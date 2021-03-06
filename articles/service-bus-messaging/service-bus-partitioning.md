---
title: Temas y colas con particiones | Microsoft Docs
description: "Describe cómo realizar la partición de los temas y las colas de Service Bus mediante el uso de varios agentes de mensajes."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a0c7d5a2-4876-42cb-8344-a1fc988746e7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/02/2016
ms.author: sethm;hillaryc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 03e68436372414a25d87f50c2b057c2f8d193401


---
# <a name="partitioned-queues-and-topics"></a>Temas y colas con particiones
Bus de servicios de Azure emplea varios agentes de mensajes para procesar mensajes y varios almacenes de mensajería para almacenar mensajes. Un único agente de mensajes controla una cola o tema convencional, que se almacena en un almacén de mensajería. Service Bus también permite que las colas o temas se dividan entre varios agentes de mensajes y almacenes de mensajería. Esto significa que el rendimiento general de una cola o tema particionado ya no está limitado por el rendimiento de un solo agente o almacén de mensajería. Además, una interrupción temporal de un almacén de mensajería no hace que una cola o tema con particiones deje de estar disponible. Las colas y los temas con particiones pueden contener todas las características avanzadas de Service Bus, como la compatibilidad con transacciones y sesiones.

Para más información sobre los aspectos internos de Service Bus, consulte el tema [Arquitectura del Bus de servicio][Arquitectura del Bus de servicio].

## <a name="how-it-works"></a>Cómo funciona
Cada cola o tema con particiones consta de varios fragmentos. Cada fragmento se almacena en un almacén de mensajería diferente y se controla por un agente de mensajes diferente. Cuando se envía un mensaje a una cola o un tema con particiones, Service Bus asigna el mensaje a uno de los fragmentos. La selección se realiza de forma aleatoria mediante el Bus de servicio o una clave de partición que el remitente puede especificar.

Cuando un cliente quiere recibir un mensaje de una cola con particiones, o de una suscripción de un tema con particiones, Service Bus consulta en todos los fragmentos si hay mensajes, luego devuelve al receptor el primer mensaje que se obtiene de cualquiera de los almacenes de mensajería. Bus de servicio almacena en caché los demás mensajes y los devuelve cuando recibe solicitudes de recepción adicionales. Un cliente de recepción no es consciente de las particiones; el comportamiento de cara al cliente de una cola o tema con particiones (por ejemplo, lectura, finalización, aplazamiento, mensajes fallidos y captura previa) es idéntico al comportamiento de una entidad regular.

No hay costos adicionales cuando se envía un mensaje a una cola o tema con particiones o cuando se recibe un mensaje de ellos.

## <a name="enable-partitioning"></a>Habilitación de las particiones
Para usar colas o temas con particiones con Azure Service Bus, use el SDK de Azure versión 2.2 o posteriores, o especifique `api-version=2013-10` en sus solicitudes HTTP.

Puede crear colas y temas de Service Bus en tamaños de 1, 2, 3, 4 o 5 GB (el valor predeterminado es 1 GB). Con las particiones habilitadas, el Bus de servicio crea 16 particiones por cada GB que especifique. Por lo tanto, si crea una cola con un tamaño de 5 GB, con 16 particiones, el tamaño de cola máximo se convierte en (5 \* 16) = 80 GB. Puede ver el tamaño máximo de la cola o tema con particiones examinando su entrada en [Portal de Azure][Portal de Azure].

Hay varias maneras de crear una cola o tema con particiones. Al crear la cola o el tema desde la aplicación, puede habilitar la creación de particiones para la cola o el tema estableciendo respectivamente las propiedades [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] o [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] en **true**. Estas propiedades deben establecerse en el momento de crear la cola o el tema. No es posible cambiar estas propiedades en una cola o tema existente. Por ejemplo:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

También puede crear una cola o tema con particiones en Visual Studio o en [Portal de Azure][Portal de Azure]. Al crear una nueva cola o tema en el portal, en la hoja **Configuración general** de la cola o en la ventana **Configuración** del tema, establezca la opción **Habilitar particiones** en **true**. En Visual Studio, haga clic en la casilla **Habilitar particiones** en el cuadro de diálogo **Nueva cola** o **Nuevo tema**.

## <a name="use-of-partition-keys"></a>Uso de claves de partición
Cuando un mensaje se coloca en cola en una cola o tema con particiones, Service Bus comprueba la presencia de una clave de partición. Si encuentra una, selecciona el fragmento basado en esa clave. Si no encuentra una clave de partición, selecciona el fragmento basado en un algoritmo interno.

### <a name="using-a-partition-key"></a>Uso de una clave de partición
En algunos escenarios, como sesiones o transacciones, es necesario que los mensajes se almacenen en un fragmento específico. Todos estos escenarios requieren el uso de una clave de partición. Todos los mensajes que usan la misma clave de partición se asignan al mismo fragmento. Si el fragmento no está disponible temporalmente, Bus de servicio devuelve un error.

En función del escenario, se usan diferentes propiedades de mensaje como clave de partición:

**SessionId**: si un mensaje tiene establecida la propiedad [BrokeredMessage.SessionId][BrokeredMessage.SessionId], Service Bus usa esta propiedad como clave de partición. De esta manera, todos los mensajes que pertenecen a la misma sesión se gestionan por el mismo agente de mensajes. Esto permite que Bus de servicio garantice la ordenación de los mensajes así como la coherencia de los estados de la sesión.

**PartitionKey**: si un mensaje tiene establecida la propiedad [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] pero no la propiedad [BrokeredMessage.SessionId][BrokeredMessage.SessionId], Service Bus usa la propiedad [PartitionKey][PartitionKey] como clave de partición. Si el mensaje tiene establecidas las propiedades [SessionId][SessionId] y [PartitionKey][PartitionKey], ambas propiedades deben ser idénticas. Si la propiedad [PartitionKey][PartitionKey] está establecida en un valor diferente al de la propiedad [SessionId][SessionId], Service Bus devuelve una excepción **InvalidOperationException**. La propiedad [PartitionKey][PartitionKey] debe usarse si un remitente envía mensajes transaccionales que no tienen en cuenta la sesión. La clave de partición asegura que todos los mensajes que se envían dentro de una transacción se gestionan por el mismo agente de mensajes.

**MessageId**: si la cola o el tema tienen la propiedad [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] establecida en **true** y las propiedades [BrokeredMessage.SessionId][BrokeredMessage.SessionId] o [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] no están establecidas, la propiedad [BrokeredMessage.MessageId][BrokeredMessage.MessageId] sirve como clave de partición. (Tenga en cuenta que las bibliotecas de Microsoft .NET y AMQP asignan automáticamente un identificador de mensaje si la aplicación emisora no lo hace). En este caso, todas las copias del mismo mensaje se controlan por el mismo agente de mensajes. Esto permite que Bus de servicio detecte y elimine los mensajes duplicados. Si la propiedad [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] no está establecida en **true**, Service Bus no considera la propiedad [MessageId][MessageId] como una clave de partición.

### <a name="not-using-a-partition-key"></a>Sin uso de una clave de partición
En ausencia de una clave de partición, Service Bus distribuye los mensajes en modo round-robin a todos los fragmentos de una cola o tema con particiones. Si el fragmento elegido no está disponible, Bus de servicio asigna el mensaje a un fragmento diferente. De esta manera, la operación de envío se realiza correctamente a pesar de la no disponibilidad temporal de un almacén de mensajería.

Para dar a Service Bus tiempo suficiente para poner en cola el mensaje en un fragmento diferente, el valor [MessagingFactorySettings.OperationTimeout][MessagingFactorySettings.OperationTimeout] especificado por el cliente que envía el mensaje debe ser superior a 15 segundos. Se recomienda establecer la propiedad [OperationTimeout][OperationTimeout] en el valor predeterminado de 60 segundos.

Tenga en cuenta que una clave de partición “ancla” un mensaje a un fragmento específico. Si el almacén de mensajería que contiene este fragmento no está disponible, Bus de servicio devuelve un error. En ausencia de una clave de partición, Bus de servicio puede elegir un fragmento diferente y la operación se realiza correctamente. Por lo tanto, se recomienda no suministrar una clave de partición a menos que sea necesario.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Temas avanzados: uso de transacciones con entidades con particiones
Los mensajes que se envían como parte de una transacción deben especificar una clave de partición. Puede ser una de las siguientes propiedades: [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] o [BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Todos los mensajes que se envían como parte de la misma transacción deben especificar la misma clave de partición. Si intenta enviar un mensaje sin una clave de partición dentro de una transacción, Bus de servicio devuelve una excepción **InvalidOperationException**. Si intenta enviar varios mensajes dentro de la misma transacción que tienen claves de partición diferentes, Bus de servicio devuelve una excepción **InvalidOperationException**. Por ejemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Si se establece alguna de las propiedades que sirven de clave de partición, Bus de servicio ancla el mensaje a un fragmento específico. Este comportamiento se produce independientemente de si se usa una transacción o no. Se recomienda que no especifique una clave de partición si no es necesario.

## <a name="using-sessions-with-partitioned-entities"></a>Uso de sesiones con entidades con particiones
Para enviar un mensaje transaccional a un tema o cola que tienen en cuenta la sesión, el mensaje debe tener establecida la propiedad [BrokeredMessage.SessionId][BrokeredMessage.SessionId]. Si también se especifica la propiedad [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], debe ser idéntica a la propiedad [SessionId][SessionId]. Si difieren, Bus de servicio devuelve una excepción **InvalidOperationException**.

A diferencia de las colas o temas regulares (sin particiones), no es posible usar una única transacción para enviar varios mensajes a diferentes sesiones. Si se intenta, Service Bus devuelve una excepción **InvalidOperationException **. Por ejemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Reenvío automático de mensajes en entidades con particiones
Bus de servicio de Azure admite el reenvío automático de mensajes desde entidades con particiones, así como a ellas y entre ellas. Para habilitar el reenvío automático de mensajes, establezca la propiedad [QueueDescription.ForwardTo][QueueDescription.ForwardTo] en la cola o la suscripción de origen. Si el mensaje especifica una clave de partición ([SessionId][SessionId], [PartitionKey][PartitionKey] o [MessageId][MessageId]), esa clave de partición se usa para la entidad de destino.

## <a name="considerations-and-guidelines"></a>Consideraciones e instrucciones
* **Características de alta coherencia**: si una entidad utiliza características como sesiones, detección de duplicados o control explícito de la clave de creación de particiones, las operaciones de mensajería se enrutarán siempre a fragmentos específicos. Si cualquiera de estos fragmentos experimenta un tráfico elevado o el almacén subyacente es incorrecto, se producirá un error en las operaciones y se reducirá la disponibilidad. En general, la coherencia es todavía mucho mayor que en las entidades sin particiones ya que solo un subconjunto del tráfico tiene problemas, en lugar de todo el tráfico.
* **Administración**: las operaciones Create, Update y Delete se deben realizar en todos los fragmentos de la entidad. Si algún fragmento es incorrecto, podría provocar errores en estas operaciones. Para la operación Get, la información como el número de mensajes se debe agregar desde todos los fragmentos. Si algún fragmento es incorrecto, se notifica el estado de disponibilidad de la entidad como limitado.
* **Escenarios de bajo volumen de mensajes**: para tales escenarios, especialmente cuando se usa el protocolo HTTP, tendrá que realizar varias operaciones de recepción con el fin de obtener todos los mensajes. Para las solicitudes de recepción, el equipo front-end realiza una operación de recepción en todos los fragmentos y almacena en caché todas las respuestas recibidas. Una solicitud de recepción posterior en la misma conexión se beneficiaría de este almacenamiento en caché y las latencias de recepción serían menores. Sin embargo, si tiene varias conexiones o utiliza el protocolo HTTP, se establecerá una conexión nueva para cada solicitud. Por lo tanto, no hay ninguna garantía de que llegará al mismo nodo. Si todos los mensajes existentes están bloqueados y almacenados en caché en otro equipo front-end, la operación de recepción devolverá un valor **null**. Finalmente, los mensajes caducan y podrá volver a recibirlos. Se recomienda mantener la conexión HTTP.
* **Examinar o buscar mensajes**: PeekBatch no siempre devuelve el número de mensajes especificado en la [propiedad MessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Esto tiene dos motivos habituales. Uno de ellos es que el tamaño agregado de la colección de mensajes supera el tamaño máximo de 256 KB. El otro es que si la cola o tema tiene la [propiedad EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) establecida en **true**, puede que una partición no tenga suficientes mensajes para completar el número solicitado de mensajes. En general, si una aplicación desea recibir un número específico de mensajes, debe llamar a [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) repetidamente hasta que obtenga ese número de mensajes o no habrá más mensajes para inspeccionar. Para más información, incluidos algunos ejemplos de código, consulte [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) o [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Características agregadas más recientes
* La opción Agregar o quitar regla ya es compatible con las entidades con particiones. A diferencia de las entidades sin particiones, estas operaciones no se admiten en las transacciones. 
* Ahora se admite AMQP para enviar mensajes a una entidad con particiones o recibirlos de ella.
* Ahora se admite AMQP para las siguientes operaciones: [Batch Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Batch Receive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [Receive by Sequence Number](https://msdn.microsoft.com/library/azure/hh330765.aspx), [Peek](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Renew Lock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Schedule Message](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Cancel Scheduled Message](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Add Rule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Remove Rule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Session Renew Lock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Set Session State](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Get Session State](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Peek Session Messages](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx) y [Enumerate Sessions](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Limitaciones de las entidades con particiones
En su implementación actual, Service Bus impone las siguientes limitaciones en colas y temas con particiones:

* Las colas y los temas con particiones no admiten el envío de mensajes que pertenecen a sesiones diferentes en una sola transacción.
* Actualmente Bus de servicio permite hasta 100 colas o temas particionados por espacio de nombres. Cada cola o tema con particiones se tiene en cuenta para la cuota de 10.000 entidades por espacio de nombres (no se aplica al nivel Premium).

## <a name="next-steps"></a>Pasos siguientes
Consulte la explicación en [Compatibilidad de AMQP 1.0 con los temas y las colas con particiones de Service Bus][Compatibilidad de AMQP 1.0 con los temas y las colas con particiones de Service Bus] para más información sobre las entidades de mensajería con particiones. 

[Arquitectura del Bus de servicio]: service-bus-architecture.md
[Portal de Azure]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
[TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
[BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
[BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
[SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
[PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
[QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
[BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
[MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
[QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
[MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
[Compatibilidad de AMQP 1.0 con los temas y las colas con particiones de Service Bus]: service-bus-partitioned-queues-and-topics-amqp-overview.md



<!--HONumber=Nov16_HO3-->


