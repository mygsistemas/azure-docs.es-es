---
title: Acerca de VPN Gateway | Microsoft Docs
description: "Obtenga información acerca de las conexiones de Puerta de enlace de VPN para redes virtuales de Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f74d531006a2c2cc4b12aac846c20c30317cc013


---
# <a name="about-vpn-gateway"></a>Acerca de Puerta de enlace de VPN
Se usa una puerta de enlace de red virtual para enviar tráfico de red entre redes virtuales de Azure y ubicaciones locales, así como entre redes virtuales dentro de Azure (de red virtual a red virtual). Cuando se configura una puerta de enlace de VPN, debe crear y configurar una puerta de enlace de red virtual y una conexión de puerta de enlace de red virtual.

En el modelo de implementación de Resource Manager, cuando se crea un recurso de puerta de enlace de red virtual, se especifican varias configuraciones. Una de las configuraciones necesarias es "-GatewayType". Existen dos tipos de puerta de enlace de red virtual: Vpn y ExpressRoute. 

Cuando se envíe el tráfico de red en una conexión privada dedicada, use el tipo de puerta de enlace "ExpressRoute". Esto también se conoce como puerta de enlace de ExpressRoute. Cuando se envíe el tráfico de red cifrado a través de una conexión pública, use el tipo de puerta de enlace "Vpn". Esto se conoce como puerta de enlace de VPN. Las conexiones de sitio a sitio, de punto a sitio y de red virtual a red virtual utilizan una puerta de enlace de VPN.

Cada red virtual tiene una única puerta de enlace de red virtual por cada tipo de puerta de enlace. Por ejemplo, puede tener una puerta de enlace de una red virtual que use -GatewayType ExpressRoute y otra que use -GatewayType Vpn. Este artículo se centra principalmente en VPN Gateway. Para más información sobre ExpressRoute, consulte la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Precios
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>SKU de puerta de enlace
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Para más información acerca de las SKU de puerta de enlace para VPN Gateway, consulte [SKU de puerta de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Rendimiento agregado estimado por SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="configuring-a-vpn-gateway"></a>Configuración de una puerta de enlace de aplicaciones
Cuando configure una puerta de enlace de VPN, las instrucciones que use dependen del modelo de implementación que utilizara para crear la red virtual. Por ejemplo, si creó la red virtual con el modelo de implementación clásica, use las instrucciones y directrices del modelo de implementación clásica para crear y configurar la puerta de enlace de VPN. Para más información sobre los modelos de implementación, consulte [Descripción de los modelos de implementación clásica y de Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).

Una conexión de puerta de enlace de VPN se basa en varios recursos con una configuración específica. La mayoría de los recursos puede configurarse por separado, aunque en algunos casos es necesario seguir un orden determinado. Puede empezar a crear y configurar recursos mediante una herramienta de configuración, como el portal de Azure. Después, puede decidir cambiar a otra herramienta, como PowerShell, para configurar recursos adicionales o para modificar los existentes cuando sea aplicable. Actualmente, no se pueden configurar todos los recursos ni establecer todas las configuraciones de recurso en el portal de Azure. Las instrucciones de los artículos para cada topología de configuración indican cuándo se necesita una herramienta de configuración específica. Para más información sobre los recursos individuales y la configuración de VPN Gateway, consulte [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

Las siguientes secciones contienen tablas que muestran lo siguiente:

* modelo de implementación disponible;
* herramientas de configuración disponibles;
* vínculos que llevan directamente a un artículo, si lo hay.

Use los gráficos y las descripciones como ayuda para seleccionar la topología de conexión que mejor se ajuste a sus requisitos. Los diagramas muestran las principales topologías de referencia, pero también se pueden crear configuraciones más complejas con los diagramas como guía.

## <a name="site-to-site-and-multi-site"></a>De sitio a sitio y multisitio
### <a name="site-to-site"></a>De sitio a sitio
Una conexión de puerta de enlace de VPN de sitio a sitio (S2S) es una conexión a través de un túnel VPN IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada y que no se encuentre detrás de NAT. Se pueden utilizar conexiones S2S para las configuraciones híbridas y entre locales.   

![Conexión S2S](./media/vpn-gateway-about-vpngateways/demos2s.png "sitio a sitio")

### <a name="multi-site"></a>Multisitio
Puede crear y configurar una conexión de puerta de enlace de VPN entre su red virtual y varias redes locales. Cuando trabaje con varias conexiones, debe usar una VPN de tipo RouteBased (puerta de enlace dinámica para redes virtuales clásicas). Como una red virtual solo puede tener una puerta de enlace de VPN, todas las conexiones a través de la puerta de enlace comparten el ancho de banda disponible. A menudo, esto se llama conexión "multisitio".

![Conexión multisitio](./media/vpn-gateway-about-vpngateways/demomulti.png "multisitio")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelos de implementación y métodos para conexiones de sitio a sitio y multisitio
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="vnet-to-vnet"></a>De red virtual a red virtual
La conexión de una red virtual a otra es muy parecida a la conexión de una red virtual a una ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE. Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de conexión multisitio. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales.

Las redes virtuales que conecta pueden:

* estar en la misma región o en distintas;
* pertenecer a la misma suscripción o a distintas; 
* usar el mismo modelo de implementación o dos diferentes.

![Conexión de red virtual a red virtual](./media/vpn-gateway-about-vpngateways/demov2v.png "red virtual a red virtual")

#### <a name="connections-between-deployment-models"></a>Conexiones entre modelos de implementación
Actualmente, Azure tiene dos modelos de implementación: el clásico y el de Resource Manager. Si lleva un tiempo usando Azure, es probable que tenga máquinas virtuales de Azure y roles de instancia que se ejecuten en una red virtual clásica. Es posible que sus máquinas virtuales e instancias de roles más recientes se estén ejecutando en una red virtual creada en Resource Manager. Puede crear una conexión entre las redes virtuales para permitir que los recursos de una red virtual se comuniquen directamente con los recursos de otra.

#### <a name="vnet-peering"></a>Emparejamiento de VNET
Es posible que pueda usar el emparejamiento de VNET para crear la conexión, siempre que la red virtual cumpla determinados requisitos. El emparejamiento de VNET no utiliza una puerta de enlace de red virtual. Para más información, consulte [Emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implementación y métodos para conexiones de red virtual a red virtual
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="point-to-site"></a>De punto a sitio
Una configuración de puerta de enlace de VPN de punto a sitio (P2S) permite crear una conexión segura a su red virtual desde un equipo cliente individual. P2S es una conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). Las conexiones P2S no requieren un dispositivo VPN ni una dirección IP pública para funcionar. Para establecer la conexión VPN, iníciela desde el equipo cliente. Esta solución es útil cuando desea conectarse a la red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual. Se pueden usar conexiones P2S junto con conexiones S2S a través de la misma puerta de enlace de VPN, siempre que todos los requisitos de configuración para ambas conexiones sean compatibles.

![Conexión de punto a sitio](./media/vpn-gateway-about-vpngateways/demop2s.png "punto a sitio")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Métodos y modelos de implementación disponibles para conexiones de punto a sitio
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="expressroute"></a>ExpressRoute
[!INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

En una conexión ExpressRoute, se configura una puerta de enlace de red virtual con el tipo de puerta de enlace "ExpressRoute", en lugar de "Vpn". Para más información sobre ExpressRoute, vea la [Información técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Conexiones de sitio a sitio y de ExpressRoute coexistentes
ExpressRoute es una conexión dedicada directa desde la WAN (no a través de Internet) a servicios Microsoft, incluido Azure. El tráfico VPN de sitio a sitio viaja cifrado a través de la red pública de Internet. Poder configurar las conexiones VPN de sitio a sitio y ExpressRoute para la misma red virtual tiene varias ventajas.

Puede configurar una VPN de sitio a sitio como una ruta de acceso seguro de conmutación por error para ExpressRoute, o bien usar la VPN de sitio a sitio para conectarse a sitios que no forman parte de su red, pero que están conectados a través de ExpressRoute. Observe que esto requiere dos puertas de enlace de red virtual para la misma red virtual, una con -GatewayType Vpn y la otra, con -GatewayType ExpressRoute.

![Coexistencia de conexión](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Métodos y modelos de implementación disponibles para conexiones S2S y ExpressRoute
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Planee la configuración de puerta de enlace de VPN. Consulte [Planeamiento y diseño de puerta de enlace de VPN](vpn-gateway-plan-design.md) y [Conexión de la red local a Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).




<!--HONumber=Jan17_HO1-->


