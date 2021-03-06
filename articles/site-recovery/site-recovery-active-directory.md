---
title: "Protección de Active Directory y DNS con Azure Site Recovery | Microsoft Docs"
description: "Este artículo describe cómo implementar una solución de recuperación ante desastres para Active Directory con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: abhiag
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2016
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: c5e80c3cd3caac07e250d296c61fb3813e0000dd
ms.openlocfilehash: 9f3d87fe08b13f08622b4bd169240a2ec0683b00


---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Protección de Active Directory y DNS con Azure Site Recovery
Las aplicaciones empresariales, como SharePoint, Dynamics AX y SAP, dependen de la infraestructura de DNS y AD para funcionar correctamente. Cuando se crea una solución de recuperación ante desastres para aplicaciones, es importante recordar que tiene que proteger y recuperar Active Directory y DNS antes que los demás componentes de aplicación, para garantizar el correcto funcionamiento de todo cuando se produce un desastre.

Site Recovery es un servicio de Azure que proporciona funcionalidades de recuperación ante desastres mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales. Site Recovery es compatible con toda una serie de escenarios de replicación para proteger de forma coherente, y conmutar por error sin problemas máquinas virtuales y aplicaciones en nubes públicas, privadas o de proveedor de servicios de hosting.

Con Site Recovery, puede crear un plan completo de recuperación ante desastres automatizada para Active Directory. En el momento en el que se produce una interrupción, puede iniciar la conmutación por error en segundos desde cualquier lugar y hacer que Active Directory vuelva a funcionar en unos minutos. Si ha implementado Active Directory para varias aplicaciones, como SAP y SharePoint en el sitio principal, y desea conmutar por error el sitio completo, puede conmutar por error Active Directive primero mediante Site Recovery y luego conmutar por error las demás aplicaciones con los planes de recuperación específicos de la aplicación.

En este artículo se explica cómo puede crear una solución de recuperación ante desastres para Active Directory, realizar conmutaciones por error planeadas, no planeadas o de prueba con el plan de recuperación de un solo clic, las configuraciones admitidas y los requisitos previos.  Debe estar familiarizado con Active Directory y Azure Site Recovery antes de empezar.

Hay dos opciones recomendadas según la complejidad de su entorno.

### <a name="option-1"></a>Opción 1
Si tiene un pequeño número de aplicaciones y un solo controlador de dominio, y desea conmutar por error todo el sitio, se recomienda utilizar Site Recovery para replicar el controlador de dominio en el sitio secundario (ya use Azure o un sitio secundario para la conmutación por error). La misma máquina virtual replicada puede usarse también para probar la conmutación por error.

### <a name="option-2"></a>Opción 2
Si tiene un gran número de aplicaciones y hay más de un controlador de dominio en el entorno, o si planea realizar una conmutación por error de pocas aplicaciones cada vez, se recomienda que además de replicar la máquina virtual del controlador de dominio con Site Recovery configure también un controlador de dominio adicional en el sitio de destino (Azure o un sitio secundario local).

> [!NOTE]
> Incluso si va a implementar la opción 2, para realizar una conmutación por error de prueba tendrá que replicar el controlador de dominio con Site Recovery. Para obtener más información, lea [Consideraciones sobre la conmutación por error de prueba](#considerations-for-test-failover) .
> 
> 

Las siguientes secciones explican cómo habilitar la protección en el controlador de dominio en Site Recovery y cómo configurar un controlador de dominio en Azure.

## <a name="prerequisites"></a>Requisitos previos
* Una implementación local del servidor DNS y Active Directory.
* Un almacén de los servicios de Azure Site Recovery en una suscripción de Microsoft Azure.
* Si va a replicar en Azure, ejecute la herramienta de Evaluación de preparación de máquinas virtuales de Azure en las máquinas virtuales para asegurarse de que son compatibles con las máquinas virtuales de Azure y los servicios de Azure Site Recovery

## <a name="enable-protection-using-site-recovery"></a>Habilitación de la protección con Site Recovery
### <a name="protect-the-virtual-machine"></a>Protección de la máquina virtual
Habilite la protección de la máquina virtual de DNS y del controlador de dominio en Site Recovery. Configure las opciones de Site Recovery en función del tipo de máquina virtual (Hyper-V o VMware). Se recomienda una frecuencia de replicación coherente frente a bloqueos de 15 minutos.

### <a name="configure-virtual-machine-network-settings"></a>Configuración de los valores de red de la máquina virtual
Para la máquina virtual de controlador de dominio o DNS, configure los valores de red en Site Recovery para que la máquina virtual se conecte a la red correcta después de una conmutación por error. Por ejemplo, si replica máquinas virtuales de Hyper-V en Azure, puede seleccionar la máquina virtual en la nube VMM o en el grupo de protección para configurar las opciones de red, como se muestra a continuación

![Configuración de red de máquina virtual](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Protección de Active Directory con la replicación de Active Directory
### <a name="site-to-site-protection"></a>Protección de sitio a sitio
Cree un controlador de dominio en el sitio secundario y especifique el nombre del mismo dominio que se utiliza en el servidor principal del sitio cuando promueve el servidor a un rol de controlador de dominio. Puede usar el complemento **Servicios y sitios de Active Directory** para configurar los valores en el objeto de vínculo del sitio al que se agregan los sitios. Al configurar los valores en un vínculo de sitio, puede controlar cuándo se produce la replicación entre dos o más sitios y con qué frecuencia se produce. Consulte [Programación de la replicación entre sitios](https://technet.microsoft.com/library/cc731862.aspx) para obtener más detalles.

### <a name="site-to-azure-protection"></a>Protección del sitio en Azure
Siga estas instrucciones para [crear un controlador de dominio en una red virtual de Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Al promover el servidor a un rol de controlador de dominio, especifique el mismo nombre de dominio que se usa en el sitio principal.

A continuación [vuelva a configurar el servidor DNS para la red virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)para usar el servidor DNS en Azure

![Red de Azure](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>Consideraciones sobre la conmutación por error de prueba
La conmutación por error de prueba se produce en una red que está aislada de la red de producción para que no haya impacto alguno en la carga de trabajo de producción.

La mayoría de las aplicaciones también requieren la presencia de un controlador de dominio y un servidor DNS para funcionar, por ello, antes de conmutar por error la aplicación, se tiene que crear un controlador de dominio en la red aislada que se usará para la conmutación por error. La manera más fácil de hacerlo es habilitar primero la protección en el controlador de dominio o la máquina virtual DNS mediante Site Recovery, y ejecutar una conmutación por error de prueba de esa máquina virtual antes de ejecutar la conmutación por error de prueba de la aplicación. Así es cómo debe hacerlo:

1. Habilite la protección de la máquina virtual de DNS y del controlador de dominio en Site Recovery.
1. Cree una red aislada. Cualquier red virtual que se cree en Azure de forma predeterminada está aislada de otras redes. Se recomienda que el intervalo de dirección IP para esta red sea el mismo que el de la red de producción. No habilite la conectividad de sitio a sitio en esta red.
1. Proporcione una dirección IP de DNS en la red que se ha creado, como la dirección IP que se espera que la máquina virtual DNS obtenga. Si realiza la replicación en Azure, proporcione la dirección IP para la máquina virtual que se usará en la conmutación por error en el valor de configuración **IP de destino** en las propiedades de la máquina virtual. Si realiza la replicación en otro sitio local y está usando DHCP, siga las instrucciones para [la configuración de DNS y DHCP para la conmutación por error en Site Recovery](site-recovery-failover.md#prepare-dhcp)

    > [!NOTE]
    > La dirección IP que se asigna a una máquina virtual en una conmutación por error de prueba es el misma dirección IP que obtendría al realizar una conmutación por error planeada o no planeada, si la dirección IP está disponible en la red de conmutación por error de prueba. Si no lo está, la máquina virtual recibe una dirección IP diferente que está disponible en la red de conmutación por error de prueba.
    > 
    > 

1. En la máquina virtual de controlador de dominio pruebe la conmutación por error del mismo en la red aislada. Use el último punto de recuperación **coherente de aplicación** disponible de la máquina virtual del controlador de dominio para realizar la conmutación por error de prueba. 
1. Ejecute una conmutación por error de prueba para un plan de recuperación de aplicación.
1. Cuando finalice la prueba, marque el trabajo de conmutación por error de prueba de la máquina virtual del controlador de dominio y del plan de recuperación como "Completo" en la pestaña **Trabajos** del portal de Site Recovery.

### <a name="removing-reference-to-other-domain-controllers"></a>Eliminación de la referencia a otros controladores de dominio
Cuando realice una prueba de conmutación por error, no podrá incluir todos los controladores de dominio en la red de prueba. Para quitar la referencia a los demás controladores de dominio que existan en su entorno de producción, tendrá que [ asumir los roles FSMO de Active Directory y realizar una limpieza de metadatos](http://aka.ms/ad_seize_fsmo) para los controladores de dominio que falten. 


### <a name="dns-and-domain-controller-on-different-machines"></a>DNS y controlador de dominio en equipos diferentes
Si el DNS no está en la misma máquina virtual que el controlador de dominio, tendrá que crear una máquina virtual de DNS para probar la conmutación por error. Si están en la misma máquina virtual, puede omitir esta sección.

Puede utilizar un servidor DNS nuevo y crear todas las zonas necesarias. Por ejemplo, si el dominio de Active Directory es contoso.com, puede crear una zona DNS con el nombre contoso.com. Las entradas correspondientes a Active Directory se deben actualizar en DNS de la forma siguiente:

1. Asegúrese de que está establecida esta configuración antes de incluir cualquier otra máquina virtual en el plan de recuperación:
   
   * El nombre de la zona depende del nombre de raíz del bosque.
   * La zona debe estar respaldada por archivos.
   * La zona debe habilitarse para actualizaciones seguras y no seguras.
   * La resolución de la máquina virtual del controlador de dominio debe apuntar a la dirección IP de la máquina virtual DNS.
2. Ejecute el siguiente comando en el directorio de la máquina virtual del controlador de dominio:
   
    `nltest /dsregdns`
3. Agregue una zona en el servidor DNS, permita actualizaciones no seguras y agregue una entrada para ello en DNS:
   
        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>Pasos siguientes
Consulte [qué cargas de trabajo puede proteger](site-recovery-workload.md) para más información sobre cómo proteger las cargas de trabajo empresariales con Azure Site Recovery.




<!--HONumber=Dec16_HO3-->


