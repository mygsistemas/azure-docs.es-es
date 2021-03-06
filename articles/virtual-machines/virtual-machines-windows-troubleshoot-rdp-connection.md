---
title: Solución de problemas con la conexión del Escritorio remoto a una máquina virtual de Azure | Microsoft Docs
description: Si no puede acceder a la máquina virtual de Azure, consulte pasos rápidos para solucionar problemas de RDP, ayuda para mensajes de error y pasos detallados para solucionar problemas de red.
keywords: Error de escritorio remoto, error de conexión del escritorio remoto, no se puede conectar a la máquina virtual, solución de problemas con el escritorio remoto
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: iainfou

---
# Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows
La conexión de Protocolo de escritorio remoto (RDP) a la máquina virtual (VM) de Azure basada en Windows puede presentar errores por varios motivos que le impedirán acceder a la máquina virtual. El problema puede originar en el servicio de Escritorio remoto de la máquina virtual, la conexión de red o el cliente de Escritorio remoto en el equipo host. Este artículo le guía por algunos de los métodos más comunes para resolver problemas de conexión de RDP. Si su problema no aparece en esta lista o sigue sin poderse conectarse a su máquina virtual mediante RDP, puede leer [pasos y conceptos de solución de problemas de RDP más detallados](virtual-machines-windows-detailed-troubleshoot-rdp.md).

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**.

<a id="quickfixrdp"></a>

## Pasos rápidos para solucionar problemas
Después de cada paso de solución de problemas, intente volver a conectarse a la máquina virtual:

1. Restablezca el acceso remoto mediante el Portal de Azure o Azure PowerShell
2. Reinicie la máquina virtual
3. Vuelva a implementar la máquina virtual
4. Compruebe las reglas de grupo de seguridad de red / punto de conexión de Servicios en la nube
5. Revise los registros de la consola de la máquina virtual en el Portal de Azure o en Azure PowerShell
6. Compruebe el estado del recurso de máquina virtual en el Portal de Azure
7. Restablezca la contraseña de la máquina virtual

Siga leyendo si necesita pasos más detallados y explicaciones para los modelos de implementación de Resource Manager y Clásico.

<a id="fix-common-remote-desktop-errors"></a>

## Solución de problemas de máquinas virtuales creadas con el modelo de implementación de Resource Manager
Después de cada paso de solución de problemas, intente volver a conectarse a la máquina virtual.

> [!TIP]
> Si el botón "Conectar" del portal está atenuado y no está conectado a Azure a través de una conexión [Express Route](../expressroute/expressroute-introduction.md) o [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), deberá crear y asignar a la máquina virtual una dirección IP pública antes de poder usar RDP. Aquí puede leer más sobre [direcciones IP públicas en Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).
> 
> 

1. Restablezca el acceso remoto con PowerShell.
   
   * Si aún no lo ha hecho, [instale y configure la última versión de Azure PowerShell](../powershell-install-configure.md).
   * Restablezca la conexión de RDP usando cualquiera de los siguientes comandos de PowerShell. Reemplace `myRG`, `myVM`, `myVMAccessExtension` y la ubicación por valores pertinentes para la instalación.
     
     ```
     Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
       -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" `
       -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" `
       -Location Westus
     ```
     OR
     
     ```
     Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" `
       -VMName "myVM" -Name "myVMAccess" -Location Westus
     ```
     
     > [!NOTE]
     > En los ejemplos anteriores, `myVMAccessExtension` o `MyVMAccess` es un nombre que especifica para la nueva extensión que se instala como parte del proceso. A menudo, se usa el nombre de la máquina virtual. Si ha trabajado previamente con VMAccessAgent, use `Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"` para obtener el nombre de la extensión existente y comprobar las propiedades de la máquina virtual. Busque en la sección "Extensiones" de la salida para ver el nombre. Puesto que en una máquina virtual solo puede existir un VMAccessAgent, es necesario agregar el parámetro `-ForceReRun True` al usar `Set-AzureRmVMExtension` para volver a registrarse el agente.
     > 
     > 
2. Reinicie la máquina virtual para resolver otros problemas de inicio. Seleccione **Examinar** > **Máquinas virtuales** > *la máquina virtual* > **Reiniciar**.
3. [Vuelva a implementar la máquina virtual en un nuevo nodo de Azure](virtual-machines-windows-redeploy-to-new-node.md).
   
    Cuando finalice esta operación, se pierden datos de disco efímeros y se actualizan las direcciones IP dinámicas que están asociadas a la máquina virtual.
4. Compruebe que sus [reglas del grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md) permiten tráfico RDP (puerto TCP 3389).
5. Revise la captura de pantalla o el registro de la consola de la máquina virtual para corregir problemas de arranque. Seleccione **Examinar** > **Máquinas virtuales** > *su máquina virtual de Windows* > **Soporte técnico y solución de problemas** > **Diagnósticos de arranque**.
6. [Restablezca la contraseña de la máquina virtual](virtual-machines-windows-reset-rdp.md).

Si sigue teniendo problemas con RDP, puede [abrir una solicitud de soporte técnico](https://azure.microsoft.com/support/options/) o leer [más pasos y conceptos detallados de solución de problemas de RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md).

## Solución de problemas de máquinas virtuales creadas con el modelo de implementación clásica
Después de cada paso de solución de problemas, intente volver a conectarse a la máquina virtual.

1. Restablezca el servicio de Escritorio remoto desde el [Portal de Azure](https://portal.azure.com). Seleccione **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual* > **Restablecer acceso remoto**.
2. Reinicie la máquina virtual para resolver otros problemas de inicio. Seleccione **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual* > **Reiniciar**.
3. [Vuelva a implementar la máquina virtual en un nuevo nodo de Azure](virtual-machines-windows-redeploy-to-new-node.md).
   
    Cuando finalice esta operación, se pierden datos de disco efímeros y se actualizan las direcciones IP dinámicas que están asociadas a la máquina virtual.
4. Compruebe que su [punto de conexión de Servicios en la nube permite el tráfico RDP](../cloud-services/cloud-services-role-enable-remote-desktop.md).
5. Revise la captura de pantalla o el registro de la consola de la máquina virtual para corregir problemas de arranque. Seleccione **Examinar** > **Máquinas virtuales (clásico**) > *su máquina virtual* > **Configuración** > **Diagnósticos de arranque**.
6. Compruebe el estado de los recursos de la máquina virtual por si hubiera algún problema con la plataforma. Seleccione **Examinar** > **Máquinas virtuales (clásico**) > *su máquina virtual* > **Configuración** > **Comprobar estado**.
7. [Restablezca la contraseña de la máquina virtual](virtual-machines-windows-reset-rdp.md).

Si sigue teniendo problemas con RDP, puede [abrir una solicitud de soporte técnico](https://azure.microsoft.com/support/options/) o leer [más pasos y conceptos detallados de solución de problemas de RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md).

## Solucionar problemas específicos con la conexión del Escritorio remoto
Es posible que al intentar conectarse a la máquina virtual mediante RDP, reciba un error específico. Los siguientes son los mensajes de error más comunes:

* [Se desconectó la sesión remota porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia](#rdplicense).
* [Escritorio remoto no encuentra el equipo "nombre"](#rdpname).
* [Error de autenticación. No se puede conectar con la autoridad de seguridad local](#rdpauth).
* [Error de Seguridad de Windows: Las credenciales no funcionaron](#wincred).
* [Este equipo no se puede conectar al equipo remoto](#rdpconnect).

<a id="rdplicense"></a>

### Se desconectó la sesión remota porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia.
Causa: el período de gracia de licencias de 120 días para el rol de servidor de Escritorio remoto expiró y necesita instalar licencias.

Como solución alternativa, guarde una copia local del archivo RDP desde el portal y ejecute este comando en un símbolo del sistema de PowerShell para conectarse. De este modo, se deshabilita la obtención de licencias solo de esa conexión:

        mstsc <File name>.RDP /admin

Si no necesita más de dos conexiones simultáneas de Escritorio remoto a la máquina virtual, puede usar el administrador del servidor para quitar el rol de servidor de Escritorio remoto.

Para obtener más información, consulte la entrada de blog [Azure VM fails with "No Remote Desktop License Servers available"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/) (Error de la máquina virtual de Azure: "No hay servidores de licencias de Escritorio remoto disponibles").

<a id="rdpname"></a>

### Remote Desktop can't find the computer "name" (Escritorio remoto no puede encontrar el equipo "nombre").
Causa: el cliente de Escritorio remoto del equipo no puede resolver el nombre del equipo en la configuración del archivo RDP.

Posibles soluciones:

* Si se encuentra en la intranet de una organización, asegúrese de que el equipo tenga acceso al servidor proxy y puede enviarle tráfico HTTPS.
* Si usa un archivo RDP almacenado localmente, intente usar uno generado por el portal. De esta forma se garantiza que tiene el nombre DNS correcto para la máquina virtual o el servicio en la nube y el puerto de punto de conexión de la máquina virtual. A continuación le facilitamos un archivo RDP de ejemplo generado por el portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

La parte de la dirección de este archivo RDP tiene:

* El nombre de dominio completo del servicio en la nube que contiene la máquina virtual (“tailspin-azdatatier.cloudapp.net” en este ejemplo).
* El puerto TCP externo del punto de conexión para tráfico de Escritorio remoto (55919).

<a id="rdpauth"></a>

### Error de autenticación. No se puede conectar con la autoridad de seguridad Local.
Causa: la VM de destino no encuentra la autoridad de seguridad en la parte del nombre de usuario de las credenciales.

Si el nombre de usuario tiene la forma *autoridadDeSeguridad*\\*nombreDeUsuario* (ejemplo: CORP\\User1), la parte *autoridadDeSeguridad* es el nombre del equipo de la máquina virtual (para la autoridad de seguridad local) o un nombre de dominio de Active Directory.

Posibles soluciones:

* Si su cuenta está en la propia VM, asegúrese de que el nombre de la VM está escrito correctamente.
* Si la cuenta está en un dominio de Active Directory, compruebe la ortografía del nombre de dominio.
* Si es una cuenta de dominio de Active Directory y el nombre de dominio está escrito correctamente, compruebe que hay algún controlador de dominio disponible en dicho dominio. Un problema común de las redes virtuales de Azure que contienen controladores de dominio es que un controlador de dominio no está disponible porque no se ha iniciado. Como solución alternativa, puede usar una cuenta de administrador local, en lugar de una cuenta de dominio.

<a id="wincred"></a>

### Error de Seguridad de Windows: Las credenciales no funcionaron.
Causa: la VM de destino no pudo validar el nombre y la contraseña de la cuenta.

Un equipo con Windows puede validar las credenciales de una cuenta local o de una cuenta de dominio.

* Para las cuentas locales, use la sintaxis *nombreDeEquipo*\\*nombreDeUsuario* (ejemplo: SQL1\\Admin4798).
* Para las cuentas de dominio, use la sintaxis *nombreDeDominio*\\*nombreDeUsuario* (ejemplo: CONTOSO\\peterodman).

Si promovió su máquina virtual a un controlador de dominio de un nuevo bosque de Active Directory, la cuenta de administrador local con la que inició sesión se convierte en una cuenta equivalente con la misma contraseña en el nuevo bosque y dominio. A continuación, se elimina la cuenta local.

Por ejemplo, si inició sesión con la cuenta local DC1\\DCAdmin y después promovió la máquina virtual como controlador de dominio en un bosque nuevo para el dominio corp.contoso.com, la cuenta local DC1\\DCAdmin se elimina y se crea una nueva cuenta de dominio CORP\\DCAdmin con la misma contraseña.

Asegúrese de que el nombre de la cuenta es un nombre que la máquina virtual pueda comprobar como una cuenta válida y que la contraseña sea correcta.

Si necesita cambiar la contraseña de la cuenta de administrador local, consulte [Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>

### This computer can't connect to the remote computer (Este equipo no se puede conectar al equipo remoto).
Causa: la cuenta usada para conectarse no tiene derechos de inicio de sesión de Escritorio remoto.

Todos los equipos de Windows tienen un grupo local Usuarios de escritorio remoto que contiene las cuentas y los grupos que pueden iniciar sesión en él de forma remota. Los miembros del grupo de administradores locales también tienen acceso, aunque esas cuentas no se enumeran en el grupo local Usuarios de escritorio remoto. Para los equipos unidos a un dominio, el grupo de administradores locales también contiene los administradores de dominio correspondientes al dominio.

Asegúrese de que la cuenta que usa para conectarse tiene derechos de inicio de sesión de Escritorio remoto. Como alternativa, use una cuenta de administrador local o de dominio para conectarse a través de Escritorio remoto. Use el complemento Microsoft Management Console (**Herramientas del sistema > Usuarios y grupos locales > Grupos > Usuarios de escritorio remoto**) para agregar la cuenta que quiera al grupo local de usuarios de escritorio remoto.

## Solución de errores genéricos de Escritorio remoto
Si no se ha producido ninguno de estos errores y sigue sin poder conectarse a la máquina virtual a través de Escritorio remoto, consulte [Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-windows-detailed-troubleshoot-rdp.md).

## Recursos adicionales
[Paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-rdp.md)

[Instalación y configuración de Azure PowerShell](../powershell-install-configure.md)

[Solución de problemas de conexiones de Secure Shell en una máquina virtual de Azure basada en Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0907_2016-->
