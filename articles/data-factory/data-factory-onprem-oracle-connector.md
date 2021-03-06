---
title: Mover los datos de Oracle con Data Factory | Microsoft Docs
description: Obtenga información acerca de cómo mover los datos hacia y desde la base de datos de Oracle local mediante Factoría de datos de Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: jingwang

---
# Transferencia de datos de/a Oracle local mediante Data Factory de Azure
En este artículo se describe cómo se usa la actividad de copia de Data Factory para transferir datos de Oracle a otro almacén de datos y viceversa. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

## Instalación
Para que el servicio Azure Data Factory pueda conectarse a la base de datos de Oracle local, debe instalar los siguientes componentes:

* Data Management Gateway en el mismo equipo que hospede la base de datos o en un equipo independiente para evitar la competencia por los recursos con la base de datos. Data Management Gateway es un agente cliente que conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo [Mover datos entre orígenes locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway.
* Proveedor de datos de Oracle para. NET. Este componente se incluye en [Oracle Data Access Components for Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/) (Componentes de acceso a datos Oracle para Windows). Instale la versión adecuada (32/64 bits) en la máquina de host en la que está instalada la puerta de enlace. [Proveedor de datos de Oracle para NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) puede tener acceso a bases de datos Oracle 10g Release 2 o posterior.
  
    Si elige XCopy Installation (Instalación de XCopy), siga los pasos del archivo readme.htm. Se recomienda elegir el programa de instalación con la interfaz de usuario (no puede ser una de XCopy).
  
    Después de instalar al proveedor, reinicie el servicio de Host de la puerta de enlace de administración de datos en la máquina con el applet Servicios o el Administrador de configuración de puerta de enlace de administración de datos.

> [!NOTE]
> Consulte [Solución de problemas de la puerta de enlace](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obtener sugerencias para solucionar problemas de conexión o puerta de enlace.
> 
> 

## Asistente para copia de datos
La manera más fácil de crear una canalización que copie datos de una base de datos Oracle a cualquiera de los almacenes de datos receptores admitidos y viceversa es usar el Asistente para copiar datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante el [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde una base de datos de Oracle al almacenamiento de blobs de Azure y viceversa. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Data Factory de Azure.

## Ejemplo: copia de datos de Oracle a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de una base de datos Oracle local a un Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Data Factory de Azure.

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como origen y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como receptor.

En el ejemplo de copian datos de una tabla de una base de datos de Oracle local en un blob cada hora. Para más información sobre las distintas propiedades usadas en el ejemplo, consulte la documentación de las secciones que vienen a continuación de los ejemplos.

**Servicio vinculado de Oracle:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Servicio vinculado de almacenamiento de blobs de Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Conjunto de datos de entrada de Oracle:**

El ejemplo supone que ha creado una tabla "MyTable" en Oracle que contiene una columna denominada "timestampcolumn" para los datos de serie temporales.

Si se establece "external": "true", se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
            },
          "policy": {     
               "externalData": {        
                    "retryInterval": "00:01:00",    
                    "retryTimeout": "00:10:00",       
                    "maximumRetry": 3       
                }     
              }
        }
    }


**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **OracleSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **oracleReaderQuery** selecciona los datos de la última hora que se van a copiar.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }


Debe ajustar la cadena de consulta, en función de cómo estén configuradas las fechas en la base de datos de Oracle. Si aparece el siguiente mensaje de error:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

puede que sea necesario cambiar la consulta, como se muestra en el siguiente ejemplo (mediante la función to\_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## Ejemplo: copia de datos de un blob de Azure a Oracle
En este ejemplo se muestra cómo se copian datos de un Almacenamiento de blobs de Azure a una base de datos Oracle local. Sin embargo, se pueden copiar datos **directamente** desde cualquiera de los orígenes indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia de Data Factory de Azure.

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado de tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) como origen y [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) como receptor.

El ejemplo copia datos de un blob a una tabla de una base de datos de Oracle local cada hora. Para más información sobre las distintas propiedades usadas en el ejemplo, consulte la documentación de las secciones que vienen a continuación de los ejemplos.

**Servicio vinculado de Oracle:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Servicio vinculado de almacenamiento de blobs de Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Conjunto de datos de entrada de blob de Azure**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Data Factory que esta tabla es externa a la factoría y no la produce una actividad de la factoría de datos.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Conjunto de datos de salida de Oracle:**

En el ejemplo se supone que ha creado una tabla "MyTable" en Oracle. Cree la tabla en Oracle con el mismo número de columnas que espera que contenga el archivo CSV de Blob. Se agregan nuevas filas a la tabla cada hora.

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **BlobSource** y el tipo **sink**, en **OracleSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }


## Propiedades del servicio vinculado de Oracle
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado de Oracle.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **OnPremisesOracle** |Sí |
| connectionString |Especifique la información necesaria para conectarse a la instancia de Base de datos de Oracle para la propiedad connectionString. |Sí |
| gatewayName |Nombre de la puerta de enlace que se usa para conectarse al servidor de Oracle local |Sí |

Consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obtener más información acerca de cómo configurar las credenciales para un origen de datos de Oracle local.

## Propiedades del tipo de conjunto de datos de Oracle
Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (Oracle, Azure Blob, Azure Table, etc.).

La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo OracleTable tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| tableName |Nombre de la tabla en la instancia de Base de datos de Oracle a la que hace referencia el servicio vinculado. |No (si se especifica **oracleReaderQuery** de **OracleSource**) |

## Propiedades de tipo de actividad de copia de Oracle
Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

> [!NOTE]
> La actividad de copia toma solo una entrada y genera una única salida.
> 
> 

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

### OracleSource
En la actividad de copia, si el origen es de tipo **OracleSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| oracleReaderQuery |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: select * from MyTable <br/><br/>Si no se especifica, la instrucción SQL que se ejecuta: select * from MyTable |No (si se especifica **tableName** de **dataset**) |

### OracleSink
**OracleSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. |timespan<br/><br/> Ejemplo: 00:30:00 (30 minutos). |No |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize. |Entero (número de filas) |No (valor predeterminado = 10000) |
| sqlWriterCleanupScript |Especifique una consulta para que se ejecute la actividad de copia de tal forma que se limpien los datos de un segmento específico. |Una instrucción de consulta. |No |
| sliceIdentifierColumnName |Especifique el nombre de columna para que la rellene la actividad de copia con un identificador de segmentos generado automáticamente, que se usará para limpiar los datos de un segmento específico cuando se vuelva a ejecutar. |Nombre de columna de una columna con el tipo de datos binarios (32). |No |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Asignación de tipos para Oracle
Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos de Oracle, se usan las siguientes asignaciones del tipo de datos de Oracle al tipo de .NET, y viceversa.

| Tipo de datos de Oracle | Tipo de datos de .NET Framework |
| --- | --- |
| BFILE |Byte |
| BLOB |Byte |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal |
| INTEGER |Decimal |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |TimeSpan |
| LONG |String |
| LONG RAW |Byte |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal |
| NVARCHAR2 |String |
| RAW |Byte |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

## Sugerencias de solución de problemas
**Problema:** se visualiza el siguiente **mensaje de error**: La actividad de copia detectó parámetros no válidos: "UnknownParameterName". Mensaje detallado: No se encontró el proveedor de datos de .Net Framework solicitado. Puede que no esté instalado".

**Causas posibles:**

1. No se instaló el proveedor de datos de .NET Framework para Oracle.
2. El proveedor de datos de .NET Framework para Oracle se instaló en .NET Framework 2.0 y no se encuentra en las carpetas de .NET Framework 4.0.

**Resolución o solución alternativa:**

1. Si no ha instalado el proveedor de .NET para Oracle, [instálelo](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e intente de nuevo este escenario.
2. Si recibe el mensaje de error incluso después de instalar el proveedor, lleve a cabo los siguientes pasos:
   1. Abra la configuración de máquina de .NET 2.0 desde la carpeta: <disco del sistema>:\\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config.
   2. Busque **Proveedor de datos de Oracle para .NET** y encontrará una entrada como la que se muestra en el ejemplo siguiente en **system.data**: **DbProviderFactories**: “<add name="Proveedor de datos de Oracle para .NET" invariant="Oracle.DataAccess.Client" description="Proveedor de datos de Oracle para .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”.
3. Copie esta entrada en el archivo machine.config de la siguiente carpeta v4.0: <disco del sistema>:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config, and change the version to 4.xxx.x.x.
4. Instale <Ruta de instalación de ODP.NET>\\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll en la caché global de ensamblados (GAC) ejecutando `gacutil /i [provider path]`.

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory de Azure y las diversas formas de optimizarlo.

<!---HONumber=AcomDC_0928_2016-->