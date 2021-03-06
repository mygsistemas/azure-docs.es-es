---
title: "Streaming en vivo con codificadores locales que crean transmisiones de velocidad de bits múltiple | Microsoft Docs"
description: "En este tema se describe cómo configurar un canal que recibe una transmisión en vivo de velocidad de bits múltiple desde un codificador local. Posteriormente, la secuencia se puede enviar a aplicaciones de reproducción cliente a través de uno o más puntos de conexión de streaming, mediante uno de los siguientes protocolos de streaming adaptable: HLS, Smooth Stream y MPEG DASH."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/12/2016
ms.author: cenkd;juliako
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: d3a3204ee7690d501722031dea3f35bf55bfec00


---
# <a name="live-streaming-with-on-premise-encoders-that-create-multi-bitrate-streams"></a>Streaming en directo con codificadores locales que crean secuencias de velocidad de bits múltiple
## <a name="overview"></a>Información general
En Servicios multimedia de Azure, un **canal** representa una canalización para procesar contenido de streaming en vivo. Los **canales** reciben el flujo de entrada en directo de dos maneras posibles:

* Un codificador local en vivo envía contenido **RTMP** o **Smooth Streaming** (MP4 fragmentado) de velocidad de bits múltiple al canal que no está habilitado para realizar la codificación en directo con AMS. Las secuencias recopiladas pasan a través de **canales**sin más procesamiento. Este método se llama **paso a través**. Puede usar los siguientes codificadores en directo que generan Smooth Streaming de varias velocidades de bits: Elemental, Envivio y Cisco.  Los siguientes codificadores en directo generan RTMP: transcodificadores Tricaster, Telestream Wirecast y Adobe Flash Live.  El codificador en directo también puede enviar una secuencia de una sola velocidad de bits a un canal que no está habilitado para la codificación en directo, pero esto no es recomendable. Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.

  > [!NOTE]
  > El método de paso a través es la forma más económica de realizar un streaming en vivo.
  >
  >
* Un codificador en directo local envía una secuencia de una sola velocidad de bits al canal que está habilitado para realizar codificación en directo con Servicios multimedia, con uno de los siguientes formatos: RTP (MPEG-TS), RTMP o Smooth Streaming (MP4 fragmentado). Después, el canal codifica en directo la secuencia entrante de una sola velocidad de bits en una secuencia de vídeo de varias velocidades de bits (adaptable). Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.

A partir de la versión 2.10 de Servicios multimedia, al crear un canal, puede especificar la forma en que desea que este reciba el flujo de entrada y si quiere que el canal realice la codificación en directo de la secuencia. Tiene dos opciones:

* **Ninguna** : especifique este valor si piensa usar un codificador en directo local que genere una secuencia de varias velocidades de bits (una transmisión de paso a través). En este caso, el flujo entrante pasa hasta la salida sin codificación alguna. Este es el comportamiento de un canal antes de la versión 2.10.  En este tema se proporciona información sobre cómo trabajar con canales de este tipo.
* **Estándar** : elija este valor si piensa usar Servicios multimedia para codificar secuencias en directo con una sola velocidad de bits como secuencias de varias velocidades de bits. Tenga en cuenta que hay un impacto en la facturación para la codificación en directo y debe recordar que salir de un canal de codificación en directo en el estado "En ejecución" supondrá un coste adicional de facturación.  Se recomienda detener inmediatamente sus canales de ejecución después que se complete su evento de transmisión en directo para evitar cargos por hora adicionales.
  Cuando se solicita, Servicios multimedia entrega la secuencia a los clientes.

> [!NOTE]
> En este tema se describen los atributos de los canales no habilitados para realizar la codificación en directo (tipo de codificación**Ninguna** ). Para más información sobre cómo trabajar con los canales habilitados para realizar la codificación en directo, consulte [Codificación en directo con Servicios multimedia de Azure para crear velocidades de bits múltiple](media-services-manage-live-encoder-enabled-channels.md).
>
>

El siguiente diagrama representa un flujo de trabajo de streaming en vivo que usa un codificador en directo local para generar secuencias RTMP o MP4 fragmentado de velocidad de bits múltiple (Smooth Streaming).

![Flujo de trabajo activo][live-overview]

En este tema se trata lo siguiente:

* [Escenario común de streaming en vivo](media-services-live-streaming-with-onprem-encoders.md#scenario)
* [Descripción de un canal y sus componentes relacionados](media-services-live-streaming-with-onprem-encoders.md#channel)
* [Consideraciones](media-services-live-streaming-with-onprem-encoders.md#considerations)

## <a name="a-idscenarioacommon-live-streaming-scenario"></a><a id="scenario"></a>Escenario común de streaming en vivo
En los pasos siguientes se describen las tareas que intervienen en la creación de aplicaciones comunes de streaming en vivo.

1. Conecte una cámara de vídeo a un equipo. Iniciar y configurar un codificador en directo local que genera una secuencia de RTMP o MP4 fragmentado de velocidad de bits múltiple (Smooth Streaming) Para obtener más información, consulte [Compatibilidad con RTMP de Servicios multimedia de Azure y codificadores en directo](http://go.microsoft.com/fwlink/?LinkId=532824).

    Este paso también puede realizarse después de crear el canal.
2. Cree e inicie un canal.
3. Recupere la URL de ingesta de canales.

    El codificador en directo usa la URL de ingesta para enviar la secuencia al canal.
4. Recupere la URL de vista previa de canal.

    Use esta dirección URL para comprobar que el canal recibe correctamente la secuencia en vivo.
5. Cree un programa.

    Con Azure Portal, al crear un programa también se crea un recurso.

    Con el SDK de .NET o REST, debe crear un recurso y especificar que este se use al crear un programa.
6. Publique el recurso asociado al programa.   

    Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.
7. Inicie el programa cuando esté listo para iniciar el streaming y el archivo.
8. Si lo desea, puede señalar el codificador en directo para iniciar un anuncio. El anuncio se inserta en el flujo de salida.
9. Detenga el programa cuando quiera detener el streaming y el archivo del evento.
10. Elimine el programa (y, opcionalmente, elimine el recurso).     

## <a name="a-idchanneladescription-of-a-channel-and-its-related-components"></a><a id="channel"></a>Descripción de un canal y sus componentes relacionados
### <a name="a-idchannelinputachannel-input-ingest-configurations"></a><a id="channel_input"></a>Configuraciones de entrada de canal (introducción)
#### <a name="a-idingestprotocolsaingest-streaming-protocol"></a><a id="ingest_protocols"></a>Protocolo de streaming de ingesta
Servicios multimedia admite la ingesta de fuentes en directo que usan el siguiente protocolo:

* **MP4 fragmentado de velocidad de bits múltiple**
* **RTMP de velocidad de bits múltiple**

    Cuando se selecciona el protocolo de streaming de introducción **RTMP** , se crean dos extremos de introducción (entrada) para el canal:

    **URL principal**: especifica la dirección URL completa del extremo de introducción RTMP principal del canal.

    **URl secundaria** : especifica la dirección URL completa del extremo de introducción RTMP secundario del canal.

    Use la dirección URL secundaria si quiere mejorar la durabilidad y la tolerancia a errores de la secuencia de entrada, así como la conmutación por error y la tolerancia a errores del codificador, especialmente en los siguientes escenarios.

    - Un único codificador que realiza inserciones dobles en las URL principal y secundaria:

        El objetivo principal es proporcionar más resistencia a las fluctuaciones de la red y a las vibraciones. Algunos codificadores RTMP no controlan bien las desconexiones de red. Cuando se produce una desconexión de red, un codificador podría detener la codificación y no enviará los datos almacenados en búfer cuando vuelva a conectarse, lo que provoca discontinuidades y pérdida de datos. Las desconexiones de red pueden suceder por una red en mal estado o un mantenimiento inadecuado en el lado de Azure. Las URL principal y secundaria reducen los problemas de red y también proporcionan un proceso de actualización controlado. Cada vez que se produce una desconexión de red programada, Servicios multimedia administra la desconexión principal y secundaria, y proporciona un retraso entre las dos, lo que da tiempo a los codificadores a continuar enviando datos y volver a conectar. El orden de las desconexiones puede ser aleatorio, pero siempre habrá un retraso entre la principal y la secundaria, o viceversa. En este escenario, el codificador sigue siendo el único punto de error.

    - Varios codificadores, cada uno insertando en un punto dedicado:

        Este escenario proporciona redundancia tanto para el codificador como para la entrada. En este escenario, encoder1 inserta en la dirección URL principal y encoder2 inserta en la dirección URL secundaria. Cuando se produce un error en un codificador, el otro codificador puede seguir enviando datos. Aún se puede seguir manteniendo la redundancia de datos porque Servicios multimedia no desconecta el principal y el secundario al mismo tiempo. En este escenario se da por hecho que los codificadores tienen sincronización temporal y proporcionan exactamente los mismos datos.  

    - Varios codificadores que realizan una doble inserción en las URL principal y secundaria:

        En este escenario, ambos codificadores insertan datos en las URL principal y secundaria. Esto proporciona la mejor confiabilidad y tolerancia a errores, así como redundancia de datos. Puede tolerar errores en ambos codificadores, así como desconexiones si un codificador deja de funcionar. En este escenario se da por hecho que los codificadores tienen sincronización temporal y proporcionan exactamente los mismos datos.  

Para obtener información sobre los codificadores en directo de RTMP, consulte [Compatibilidad con RTMP de Servicios multimedia de Azure y codificadores en directo](http://go.microsoft.com/fwlink/?LinkId=532824).

Se aplican las siguientes consideraciones:

* Asegúrese de que tiene suficiente conectividad a Internet disponible para enviar datos a los puntos de ingesta.
* El uso de la dirección URL de ingesta secundaria requiere más ancho de banda.
* La secuencia entrante de velocidad de bits múltiple puede tener un máximo de 10 niveles de calidad de vídeo (también conocido conocidas como capas) y un máximo de 5 pistas de audio.
* La mayor velocidad de bits promedio para cualquiera de los niveles o las capas de calidad de vídeo debe ser inferior a 10 Mbps.
* La suma de las velocidades de bits promedio para todas las secuencias de audio y vídeo debe ser inferior a 25 Mbps.
* No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada.
* Puede introducir una velocidad de bits única en su canal, pero como el canal no procesa la secuencia, las aplicaciones cliente también recibirán una secuencia de una sola velocidad de bits (no se recomienda esta opción).

#### <a name="ingest-urls-endpoints"></a>Direcciones URL de ingesta (extremos)
Un canal proporciona un extremo de entrada (dirección URL de ingesta) que usted especifica en el codificador en directo, de modo que el codificador puede insertar secuencias en sus canales.   

Puede obtener las direcciones URL de ingesta al crear el canal. Para obtener estas direcciones URL, el canal no puede estar **En ejecución** . Cuando esté listo para comenzar a insertar datos en el canal, este debe estar **En ejecución** . Una vez que el canal empieza a consumir datos, puede obtener una vista previa de la secuencia a través de la dirección URL de vista previa.

Tiene la opción de consumir una secuencia en directo de MP4 fragmentado (Smooth Streaming) a través de una conexión SSL. Para introducir en SSL, asegúrese de actualizar la dirección URL de introducción a HTTPS. Actualmente, no se puede consumir RTMP a través de SSL.

#### <a name="a-idkeyframeintervalakeyframe-interval"></a><a id="keyframe_interval"></a>Intervalo de fotogramas clave
Cuando se usa un codificador en directo local para generar una secuencia de velocidad de bits múltiple, el intervalo de fotogramas clave especifica la duración de GOP (tal como la usa el codificador externo). Cuando el canal recibe esta secuencia entrante, puede entregar la secuencia en directo a las aplicaciones cliente de reproducción en cualquiera de los siguientes formatos:  Smooth Streaming, DASH y HLS. Cuando se realiza el streaming en vivo, HLS siempre se empaqueta dinámicamente. De forma predeterminada, Servicios multimedia calcula automáticamente la relación de empaquetado por segmento HLS (fragmentos por segmento) según el intervalo de fotogramas clave, también denominado grupo de imágenes, GOP, que se recibe del codificador en directo.

En la tabla siguiente se muestra cómo calcula la duración de los segmentos:

| Intervalo de fotogramas clave | Relación de empaquetado por segmento HLS (fragmento por segmento) | Ejemplo |
| --- | --- | --- |
| menor o igual a 3 segundos |3:1 |Si KeyFrameInterval (o GOP) es 2 segundos de duración, la relación de empaquetado por segmento HLS predeterminada será 3 a 1, lo que creará un segmento HLS de 6 segundos. |
| de 3 a 5 segundos |2:1 |Si KeyFrameInterval (o GOP) es 4 segundos de duración, la relación de empaquetado por segmento HLS predeterminada será 2 a 1, lo que creará un segmento HLS de 8 segundos. |
| mayor de 5 segundos |1:1 |Si KeyFrameInterval (o GOP) es 6 segundos de duración, la relación de empaquetado por segmento HLS predeterminada será 1 a 1, lo que creará un segmento HLS de 6 segundos. |

Puede cambiar la relación de fragmentos por segmento configurando la salida del canal y estableciendo FragmentsPerSegment en ChannelOutputHls.

También puede cambiar el valor del intervalo de fotogramas clave estableciendo la propiedad KeyFrameInterval en ChanneInput.

Si se establece explícitamente KeyFrameInterval, la relación de empaquetado por segmento HLS FragmentsPerSegment se calcula mediante las reglas descritas anteriormente.  

Si se establece explícitamente KeyFrameInterval y FragmentsPerSegment, Servicios multimedia usará los valores que usted introduzca.

#### <a name="allowed-ip-addresses"></a>Direcciones IP permitidas
Puede definir las direcciones IP permitidas para publicar vídeo en el canal. Dichas direcciones se pueden especificar como dirección IP individual (por ejemplo, ‘10.0.0.1’), un intervalo de direcciones IP mediante una dirección IP y una máscara de subred CIDR (por ejemplo, ‘10.0.0.1/22’) o un intervalo de direcciones IP mediante una dirección IP y una máscara de subred decimal con puntos (por ejemplo, ‘10.0.0.1(255.255.252.0)’).

Si no se especifica ninguna dirección IP y no hay ninguna definición de regla, no se permitirá ninguna dirección IP. Para permitir las direcciones IP, cree una regla y establezca 0.0.0.0/0.

### <a name="channel-preview"></a>Vista previa de canal
#### <a name="preview-urls"></a>Direcciones URL de vista previa
Los canales proporcionan un extremo de vista previa (dirección URL de vista previa) que se puede utilizar para obtener una vista previa y validar la secuencia antes de mayor procesamiento y entrega.

Puede obtener la dirección URL de vista previa al crear el canal. Para obtenerla, el canal no puede encontrarse en el estado **En ejecución** .

Una vez que el canal empieza a consumir datos, puede obtener una vista previa de la secuencia.

Tenga en cuenta que actualmente la secuencia de vista previa solo se puede entregar en formato MP4 fragmentado (Smooth Streaming) independientemente del tipo de entrada especificado. Puede usar el reproductor [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) para probar el formato Smooth Stream. También puede usar un reproductor hospedado en Azure Portal para ver la transmisión.

#### <a name="allowed-ip-addresses"></a>Direcciones IP permitidas
Puede definir las direcciones IP permitidas para conectarse al extremo de vista previa. Si no se especifica ninguna dirección IP, se permitirá cualquier dirección IP. Dichas direcciones se pueden especificar como dirección IP individual (por ejemplo, ‘10.0.0.1’), un intervalo de direcciones IP mediante una dirección IP y una máscara de subred CIDR (por ejemplo, ‘10.0.0.1/22’) o un intervalo de direcciones IP mediante una dirección IP y una máscara de subred decimal con puntos (por ejemplo, ‘10.0.0.1(255.255.252.0)’).

### <a name="channel-output"></a>Salida del canal
Para obtener más información, consulte la sección [Configuración del intervalo de fotogramas clave](#keyframe_interval) .

### <a name="channels-programs"></a>Programas del canal
Un canal está asociado a programas que le permiten controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Los canales administran los programas. La relación entre canales y programas es muy similar a los medios tradicionales, donde un canal tiene un flujo constante de contenido y un programa se enfoca algún evento programado en dicho canal.

Puede especificar la cantidad de horas que desea conservar el contenido grabado del programa en la configuración de la duración de **Ventana de archivo** . Este valor se puede establecer desde un mínimo de cinco minutos a un máximo de 25 horas. La duración de la ventana de archivo también indica el tiempo máximo que los clientes pueden buscar hacia atrás a partir de la posición en vivo actual. Los programas pueden transmitirse durante la cantidad de tiempo especificada, pero el contenido que escape de esa longitud de ventana se descartará continuamente. El valor de esta propiedad también determina durante cuánto tiempo los manifiestos de cliente pueden crecer.

Cada programa se asocia a un recurso que almacena el contenido transmitido por streaming. Un recurso se asigna a un contenedor de blobs en la cuenta de almacenamiento de Azure y los archivos del recurso se almacenan como blobs en ese contenedor. Para publicar el programa a fin de que los clientes puedan ver la secuencia, debe crear un localizador a petición para el recurso asociado. Contar con este localizador le permitirá crear una dirección URL de streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres programas en ejecución simultánea, por lo que puede crear varios archivos del mismo flujo entrante. Esto le permite publicar y archivar distintas partes de un evento, según sea necesario. Por ejemplo, el requisito de su empresa es solo archivar seis horas de un programa, pero difundir solo los últimos diez minutos. Para lograrlo, necesita crear dos programas en ejecución simultánea. Un programa está definido para archivar seis horas del evento, pero no está publicado. El otro programa está definido para archivar durante diez minutos y este programa sí se publica.

No debe volver a usar programas existentes para eventos nuevos. En su lugar, cree e inicie un programa nuevo para cada evento como se describe en la sección Programación de aplicaciones de streaming en vivo.

Inicie el programa cuando esté listo para iniciar el streaming y el archivo. Detenga el programa cuando quiera detener el streaming y el archivo del evento.

Para eliminar contenido archivado, detenga y elimine el programa y, a continuación, elimine el recurso asociado. No se puede eliminar un recurso si se usa un programa; primero se debe eliminar el programa.

Incluso después de detener y eliminar el programa, los usuarios podrán transmitir el contenido archivado como un vídeo a petición siempre que no elimine el recurso.

Si desea conservar el contenido archivado, pero no hacerlo disponible para la transmisión, elimine el localizador de streaming.

## <a name="a-idstatesachannel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Estados del canal y cómo se asignan los estados al modo de facturación
El estado actual de un canal. Los valores posibles son:

* **Detenido**. Este es el estado inicial del canal después de su creación. En este estado, se pueden actualizar las propiedades del canal pero no se permite el streaming.
* **Iniciando**. El canal se está iniciando. No se permiten actualizaciones ni streaming durante este estado. Si se produce un error, el canal vuelve al estado Detenido.
* **En ejecución**. El canal es capaz de procesar secuencias en directo.
* **Deteniéndose**. El canal se está deteniendo. No se permiten actualizaciones ni streaming durante este estado.
* **Eliminando**. El canal se está eliminando. No se permiten actualizaciones ni streaming durante este estado.

En la tabla siguiente se muestra cómo se asignan los estados del canal al modo de facturación.

| Estado del canal | Indicadores IU del portal | ¿Facturado? |
| --- | --- | --- | --- |
| Iniciando |Iniciando |No (estado transitorio) |
| En ejecución |Listo (no hay programas en ejecución)<p>o<p>Streaming (al menos un programa en ejecución) |SÍ |
| Deteniéndose |Deteniéndose |No (estado transitorio) |
| Detenido |Detenido |No |

## <a name="a-idccandadsaclosed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Subtítulos e inserción de anuncios
En la siguiente tabla se muestran los estándares de subtítulos e inserción de anuncios.

| Estándar | Notas |
| --- | --- |
| CEA-708 y EIA-608 (708/608) |CEA-708 y EIA-608 son estándares de subtítulos (CC) para Estados Unidos y Canadá.<p><p>Actualmente, solo se admiten subtítulos si se transmiten en la secuencia de entrada codificada. Debe usar un codificador multimedia en directo que pueda insertar 608 o 708 títulos en la secuencia codificada que se envía a Servicios multimedia. Servicios multimedia ofrecerá el contenido con subtítulos insertados para los usuarios. |
| TTML dentro de ismt (pistas de texto Smooth Streaming) |El empaquetado dinámico de Servicios multimedia permite a los clientes transmitir contenido de cualquiera de los siguientes formatos: MPEG DASH, HLS o Smooth Streaming. Sin embargo, si usted utiliza MP4 fragmentado (Smooth Streaming) con subtítulos dentro de .ismt (pistas de texto Smooth Streaming), solo podrá entregar la secuencia a clientes de Smooth Streaming. |
| SCTE-35 |Sistema de señalización digital utilizado para poner en cola la inserción de publicidad. Los receptores descendentes usan la señal para unir la publicidad a la secuencia por el tiempo asignado. SCTE-35 se debe enviar como una pista dispersa en la secuencia de entrada.<p><p>Tenga en cuenta que en la actualidad el único formato de secuencia de entrada admitido que transporta señales de publicidad es el formato MP4 fragmentado (Smooth Streaming). El único formato de salida admitido también es Smooth Streaming. |

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>Consideraciones
Cuando se usa un codificador en directo local para enviar una secuencia de velocidad de bits múltiple en un canal, se aplican las siguientes restricciones:

* Asegúrese de que tiene suficiente conectividad a Internet disponible para enviar datos a los puntos de ingesta.
* La secuencia entrante de velocidad de bits múltiple puede tener un máximo de 10 niveles de calidad de vídeo (10 capas) y un máximo de 5 pistas de audio.
* La mayor velocidad de bits promedio para cualquiera de los niveles o las capas de calidad de vídeo debe ser inferior a 10 Mbps.
* La suma de las velocidades de bits promedio para todas las secuencias de audio y vídeo debe ser inferior a 25 Mbps.
* No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada.

Otras consideraciones sobre el funcionamiento de los canales y los componentes relacionados:

* Cada vez que vuelva a configurar el codificador en directo, llame al método **Restablecer** en el canal. Antes de restablecer el canal, debe detener el programa. Después de restablecer el canal, reinicie el programa.
* Un canal se puede detener solo cuando está en el estado En ejecución y se han detenido todos los programas en el canal.
* De forma predeterminada solo puede agregar 5 canales a su cuenta de Servicios multimedia. Para obtener más información, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations.md).
* No se puede cambiar el protocolo de entrada mientras el canal o sus programas asociados se están ejecutando. Si necesita diferentes protocolos, debe crear canales independientes para cada protocolo de entrada.
* Solo se le cobrará cuando el canal esté en estado **En ejecución** . Para obtener más información, consulte [esta](media-services-live-streaming-with-onprem-encoders.md#states) sección.

## <a name="how-to-create-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Creación de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales
Para más información sobre los codificadores en directo locales, vea [Uso de codificadores en directo de terceros con Servicios multimedia de Azure](https://azure.microsoft.com/blog/azure-media-services-rtmp-support-and-live-encoders/).

Elija **Portal**, **.NET** o **API de REST** para ver cómo crear y administrar los canales y programas.

[!INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Temas relacionados
[Especificación de la introducción en directo de MP4 fragmentado de Servicios multimedia de Azure](media-services-fmp4-live-ingest-overview.md)

[Entrega de eventos de streaming en vivo con Servicios multimedia de Azure](media-services-overview.md)

[Conceptos de Servicios multimedia de Azure](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png



<!--HONumber=Dec16_HO2-->


