---
title: "Reenvío de estados de trabajos y de transmisiones de trabajos de Automation a Log Analytics (OMS) | Microsoft Azure"
description: "Este artículo muestra cómo enviar el estado de un trabajo y transmisiones de trabajo del runbook a Log Analytics de Microsoft Operations Management Suite para ofrecer mayor información y administración."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 22a8a7541da572445ab0d6a822b56bd9a03339b7


---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Reenvío del estado de un trabajo y de transmisiones de trabajos de Automatización a Log Analytics (OMS)
Automatización puede enviar el estado de un trabajo del runbook y de transmisiones de trabajos al área de trabajo devLog Analytics de Microsoft Operations Management Suite (OMS).  Aunque esta información se puede ver en el Portal de Azure o con PowerShell por estado de trabajo individual o todos los trabajos de una cuenta de Automatización concreta, cualquier cosa avanzada que se realice para admitir sus requisitos operativos requiere que cree scripts de PowerShell personalizados.  Ahora con Log Anaytics puede:

* Obtener información sobre los trabajos de Automatización
* Desencadenar un correo electrónico o una alerta en función de en el estado de trabajo del runbook (por ejemplo, error o en suspensión)
* Escribir consultas avanzadas en las transmisiones de trabajos
* Correlacionar trabajos en cuentas de Automatización
* Visualizar el historial de trabajos a lo largo del tiempo     

## <a name="prerequisites-and-deployment-considerations"></a>Requisitos previos y consideraciones de implementación
Para empezar a enviar los registros de Automatización a Log Analytics, debe tener lo siguiente:

1. Una suscripción de OMS. Para más información, consulte [Get started with Log Analytics](../log-analytics/log-analytics-get-started.md)(Introducción a Log Analytics).  

   > [!NOTE]
   > Es preciso que el área de trabajo de OMS y la cuenta de Automatización deben estar en la misma suscripción de Azure, y en orden para esta configuración funcione correctamente.
   >
   >
2. Una [cuenta de Almacenamiento de Azure](../storage/storage-create-storage-account.md).  

   > [!NOTE]
   > La cuenta de Almacenamiento *debe* estar en la misma región que la cuenta de Automatización.
   >
   >
3. Azure PowerShell con la versión 1.0.8 o posterior de los cmdlets de Visión operativa. Para obtener información acerca de esta versión y cómo instalarla, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs).
4. Azure Diagnostic y Log Analytics de PowerShell.  Para obtener información acerca de esta versión y cómo instalarla, consulte [Azure Diagnostic y Log Analytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1).  
5. Descargar el script de PowerShell **Enable-AzureDiagnostics.ps1** de la [Galería de PowerShell](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript). Este script configurará lo siguiente:
   * Una cuenta de almacenamiento que contenga el estado de un trabajo del runbook y datos de transmisión para una cuenta de Automatización que especifique.
   * Habilite la recopilación de estos datos de su cuenta de Automatización para almacenarlos en una cuenta de Almacenamiento de blobs de Azure en formato JSON.
   * Configure la recopilación de datos de su cuenta de Almacenamiento de blobs para Log Analytics de OMS.
   * Habilite la solución Log Analytics de Automatización en su área de trabajo de OMS.   

El script **Enable-AzureDiagnostics.ps1** requiere los siguientes parámetros durante la ejecución:

* *AutomationAccountName* : el nombre de la cuenta de Automatización.
* *LogAnalyticsWorkspaceName* : el nombre del área de trabajo de OMS.

Para buscar los valores de *AutomationAccountName*, en Azure Portal seleccione su cuenta de Automation en la hoja **Cuenta de Automatización** y seleccione **Toda la configuración**.  En la hoja **Toda la configuración**, en **Configuración de la cuenta**, seleccione **Propiedades**.  En la hoja **Propiedades**, puede anotar estos valores.<br> ![Propiedades de una cuenta de Automatización](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)(Introducción a Log Analytics).

## <a name="setup-integration-with-log-analytics"></a>Configuración de la integración con Log Analytics
1. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio**.  
2. Desde el shell de la línea de comandos de PowerShell, navegue hasta la carpeta que contiene el script que descargó y ejecútelo cambiando los valores de los parámetros - *AutomationAccountName* y *- LogAnalyticsWorkspaceName*.

   > [!NOTE]
   > Se le solicitará que se autentique en Azure después de ejecutar el script.  **Debe** iniciar sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.   
   >
   >

        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `
3. Tras ejecutar este script, deberá ver registros en Log Analytics unos 30 minutos después de que se escriban nuevos datos de diagnóstico en el almacenamiento.  Si los registros no están disponibles después de ese tiempo, consulte la sección sobre solución de problemas de [JSON files in blob storage](../log-analytics/log-analytics-azure-storage-json.md#troubleshooting-configuration-for-azure-diagnostic-logs)(Archivos JSON de Almacenamiento de blobs).

### <a name="verify-configuration"></a>Comprobación de la configuración
Para confirmar que el script configuró correctamente su cuenta de Automatización y el espacio de trabajo de OMS, se pueden realizar los siguientes pasos en PowerShell.  Antes de hacerlo, para buscar los valores del nombre del espacio de trabajo de OMS y el nombre del grupo de recursos, en Azure Portal navegue hasta Log Analytics (OMS) y en la hoja Log Analytics (OMS), anote el valor de **Nombre** y **Grupo de recursos**.<br> ![Lista de espacios de trabajo de Log Analytics de OMS](media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png) Estos dos valores los usaremos al comprobar la configuración del espacio de trabajo de OMS mediante el cmdlet de PowerShell [Get AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/library/mt603567.aspx).

1. En el Portal de Azure, navegue hasta Cuentas de Almacenamiento y busque la siguiente cuenta de almacenamiento, que utiliza la convención de nomenclatura *AutomationAccountNameomsstorage*.  Poco después de que se complete un trabajo del Runbook verá que se han creado dos contenedores de blobs (**insights-logs-joblogs** y **insights-logs-jobstreams**).  
2. En PowerShell, ejecute el siguiente código de PowerShell, pero cambie los valores de los parámetros **ResourceGroupName** y **WorkspaceName** que copió o anotó antes.  

   Login-AzureRmAccount Get-AzureRmSubscription -SubscriptionName 'SubscriptionName' | Set-AzureRmContext Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "OMSResourceGroupName" ` -Workspace "OMSWorkspaceName"

   Esto devolverá la información del almacenamiento del área de trabajo de OMS especificada.  Deseamos confirmar que la información del almacenamiento de la cuenta de Automation que especificamos antes existe y que el objeto **State** muestra el valor **OK**.<br> ![Resultados del cmdlet Get-AzureRmOperationalInsightsStorageInsights](media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png)(Introducción a Log Analytics).

## <a name="log-analytics-records"></a>Registros de Log Analytics
Automation crea dos tipos de registros en el repositorio de OMS.

### <a name="job-logs"></a>Registros de trabajo
| Propiedad | Description |
| --- | --- |
| Hora |Fecha y hora en que se ejecuta el trabajo de Runbook. |
| resourceId |Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook. |
| operationName |Especifica el tipo de operación realizada en Azure.  Para Automation, el valor será Job. |
| resultType |El estado del trabajo de Runbook.  Los valores posibles son:<br>Started<br>Stopped<br>Suspended<br>Con error<br>- Correcto |
| resultDescription |Describe el estado de resultado del trabajo de Runbook.  Los valores posibles son:<br>- Se inicia el trabajo<br>- Error del trabajo<br>- Trabajo completado |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| Categoría |Clasificación del tipo de datos.  Para Automation, el valor será JobLogs. |
| RunbookName |El nombre del Runbook. |
| JobId |GUID que es el identificador del trabajo de Runbook. |
| Autor de llamada |La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |

### <a name="job-streams"></a>Flujos de trabajo
| Propiedad | Description |
| --- | --- |
| Hora |Fecha y hora en que se ejecuta el trabajo de Runbook. |
| resourceId |Especifica el tipo de recurso en Azure.  Para Automation, el valor es la cuenta de Automation asociada al Runbook. |
| operationName |Especifica el tipo de operación realizada en Azure.  Para Automation, el valor será Job. |
| resultType |El estado del trabajo de Runbook.  Valores posibles son:<br>- En curso |
| resultDescription |Incluye la secuencia de salida del Runbook. |
| CorrelationId |GUID que es el identificador de correlación del trabajo de Runbook. |
| Categoría |Clasificación del tipo de datos.  Para Automation, el valor es JobStreams. |
| RunbookName |El nombre del Runbook. |
| JobId |GUID que es el identificador del trabajo de Runbook. |
| Autor de llamada |La persona que inicia la operación.  Los valores posibles son una dirección de correo electrónico o el sistema para los trabajos programados. |
| StreamType |El tipo de flujo de trabajo. Los valores posibles son:<br>progreso<br>- Salida<br>Warning (Advertencia)<br>Error<br>DEBUG<br>- Detallado |

## <a name="viewing-automation-logs-in-log-analytics"></a>Visualización de registros de Automatización en Log Analytics
Ahora que ha iniciado el envío de los registros de los trabajos de Automatización a Log Analytics, veamos lo que puede hacer con dichos registros en OMS.   

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envío de un correo electrónico cuando se produce un error en un trabajo del runbook o se suspende
Uno de nuestros principales clientes pregunta por la capacidad para enviar un correo electrónico o un mensaje de texto cuando aparece algún problema en un trabajo del runbook.   

Para crear una regla de alertas, lo primero es crear una búsqueda de registros de los registros del trabajo del runbook que deben invocar la alerta.  El botón **Alerta** estará disponible para que pueda crear y configurar la regla de alertas.

1. En la página de información general de OMS, haga clic en **Búsqueda de registros**.
2. Cree una consulta de búsqueda de registros para la alerta; para ello, escriba lo siguiente en el campo de consulta: `Category=JobLogs (ResultType=Failed || ResultType=Suspended)`.  También puede agrupar por RunbookName, para lo que debe usar: `Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`.   

   Si ha configurado registros de más de una cuenta de Automatización o suscripción a su área de trabajo, también le puede interesar agrupar las alertas por suscripción o cuenta de Automatización.  El nombre de la cuenta de Automatización puede derivarse del campo Resource (Recurso) en la búsqueda de JobLogs.  
3. Haga clic en **Alerta** en la parte superior de la página para abrir la pantalla **Agregar regla de alerta**.  Para más información sobre las opciones para configurar la alerta, consulte [Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Búsqueda de todos los trabajos que se han completado con errores
Además de las alertas basadas en errores, es probable que desee saber cuándo un trabajo del runbook ha tenido un error que no conlleva su terminación (PowerShell genera una secuencia de error, pero los errores de no terminación no provocan errores en los trabajos ni que estos se suspendan).    

1. En el portal de OMS, haga clic en **Log Search**(Búsqueda de registros).
2. En el campo de consulta, escriba `Category=JobStreams StreamType_s=Error | measure count() by JobId_g` y luego haga clic en **Buscar**.

### <a name="view-job-streams-for-a-job"></a>Visualización de las transmisiones de un trabajo
Cuando se depura un trabajo, también se pueden examinar sus transmisiones.  La siguiente consulta muestra todas las transmisiones de un solo trabajo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:   

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>Visualización del estado del historial de trabajos
Por último, puede desear visualizar el historial de trabajos con el paso del tiempo.  Esta consulta se puede usar para buscar el estado de los trabajos con el paso del tiempo.

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day`  
<br> ![Gráfico del historial de trabajos de OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Resumen
Mediante el envío del estado de un trabajo de Automatización y los datos de transmisión a Log Analytics, puede obtener una mejor información del estado de los trabajos de Automatización mediante la configuración de alertas para avisarle cuando haya un problema y paneles personalizados que usen consultas avanzadas para visualizar los resultados del runbook, el estado del trabajo del runbook y otros indicadores clave o métricas relacionados.  Esto le ayudará a proporcionar mayor visibilidad de las operaciones y solucionar las incidencias más rápidamente.  

## <a name="next-steps"></a>Pasos siguientes
* Para aprender a crear diferentes consultas de búsqueda y a revisar los registros de trabajos de Automatización con Log Analytics, consulte [Búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md)
* Para aprender a crear y recuperar los mensajes de salida y de error de los runbooks en, consulte [Salidas de runbook y mensajes en la Automatización de Azure](automation-runbook-output-and-messages.md)
* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Automatización de Azure](automation-runbook-execution.md)
* Para más información acerca de Log Analytics de OMS y de los orígenes de recopilación de datos, consulte [Conexión de Almacenamiento de Azure con Log Analytics](../log-analytics/log-analytics-azure-storage.md)



<!--HONumber=Dec16_HO2-->


