---
title: "Solución de problemas de Azure SQL Data Warehouse | Microsoft Docs"
description: "Cómo solucionar los problemas de Almacenamiento de datos SQL de Azure."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 18d3a5eca0b272a3fbe48c06e668c33aff2b3f11


---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Solución de problemas de Almacenamiento de datos SQL de Azure
En este tema se describen algunas de las preguntas de solución de problemas más comunes que oímos de nuestros clientes.

## <a name="connecting"></a>Conexión
| Problema | Resolución |
|:--- |:--- |
| Error de inicio de sesión del usuario 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, error: 18456) |Este error se produce cuando un usuario de AAD intenta conectarse a la base de datos maestra, pero no tiene un usuario ahí.  Para corregir este problema, especifique el Almacenamiento de datos SQL al que se desea conectar en el momento de la conexión o agregue el usuario a la base de datos maestra.  Para más información, vea el artículo [Introducción a la seguridad][Introducción a la seguridad]. |
| La entidad de seguridad del servidor "MyUserName" no puede obtener acceso a la base de datos "maestra" en el contexto de seguridad actual. No se puede abrir la base de datos predeterminada del usuario. Error de inicio de sesión. Error de inicio de sesión del usuario 'MyUserName'. (Microsoft SQL Server, error: 916) |Este error se produce cuando un usuario de AAD intenta conectarse a la base de datos maestra, pero no tiene un usuario ahí.  Para corregir este problema, especifique el Almacenamiento de datos SQL al que se desea conectar en el momento de la conexión o agregue el usuario a la base de datos maestra.  Para más información, vea el artículo [Introducción a la seguridad][Introducción a la seguridad]. |
| Error CTAIP |Este error puede producirse cuando se ha creado un inicio de sesión en la base de datos maestra de SQL Server, pero no en la base de datos de Almacenamiento de datos SQL.  Si se produce este error, eche un vistazo al artículo [Introducción a la seguridad][Introducción a la seguridad].  En este artículo se explica cómo crear un inicio de sesión y un usuario en una base de datos maestra y luego cómo crear un usuario en la base de datos de Almacenamiento de datos SQL. |
| Bloqueado por el firewall |Las bases de datos SQL de Azure están protegidas por firewalls de nivel de servidor y base de datos para garantizar que solo las direcciones IP conocidas tienen acceso a una base de datos. Los firewalls están protegidos de manera predeterminada, lo que significa que debe habilitar explícitamente una dirección IP o un intervalo de direcciones para poder conectarse.  Para configurar el firewall para el acceso, siga los pasos de la sección [Configurar el acceso al servidor de firewall para la dirección IP de cliente][Configurar el acceso al servidor de firewall para la dirección IP de cliente] del artículo [instrucciones de aprovisionamiento][instrucciones de aprovisionamiento]. |
| No se puede conectar con una herramienta o un controlador |SQL Data Warehouse recomienda el uso de [SSMS][SSMS], [SSDT para Visual Studio 2015][SSDT para Visual Studio 2015] o [sqlcmd][sqlcmd] para consultar los datos. Para más información sobre los controladores y conocer cómo conectarse a SQL Data Warehouse, vea los artículos [Controladores de Almacenamiento de datos SQL de Azure][Controladores de Almacenamiento de datos SQL de Azure] y [Conexión a Almacenamiento de datos SQL de Azure][Conexión a Almacenamiento de datos SQL de Azure]. |

## <a name="tools"></a>Herramientas
| Problema | Resolución |
|:--- |:--- |
| El Explorador de objetos de Visual Studio no muestra usuarios de AAD |Este es un problema conocido.  Como solución alternativa, vea los usuarios de [sys.database_principals][sys.database_principals].  Para más información sobre Azure Active Directory con SQL Data Warehouse, vea [Autenticación a Almacenamiento de datos SQL de Azure][Autenticación a Almacenamiento de datos SQL de Azure]. |

## <a name="performance"></a>Rendimiento
| Problema | Resolución |
|:--- |:--- |
| Solución de problemas de rendimiento de consultas |Si está intentando solucionar los problemas de una consulta determinada, empiece por [aprender a supervisar las consultas][aprender a supervisar las consultas]. |
| Un bajo rendimiento de las consultas y unos planes mal diseñados suelen ser el resultado de la falta de estadísticas |La causa más común del rendimiento ineficiente es la falta de estadísticas en las tablas.  Para más información sobre cómo crear estadísticas y por qué son tan importantes para el rendimiento, vea [Maintaining table statistics (Mantenimiento de estadísticas de tablas)][Estadísticas]. |
| Baja simultaneidad o consultas en cola |Para comprender el modo de equilibrar la asignación de memoria con la simultaneidad, es importante entender la [administración de la carga de trabajo][administración de la carga de trabajo]. |
| Implementación de procedimientos recomendados |El mejor lugar para empezar a aprender formas de mejorar el rendimiento de las consultas es el artículo [Procedimientos recomendados para Almacenamiento de datos SQL de Azure][Procedimientos recomendados para Almacenamiento de datos SQL de Azure]. |
| Mejora del rendimiento con el escalado |En ocasiones, la solución para mejorar el rendimiento consiste simplemente en agregar más potencia de proceso a las consultas con el [escalado de Almacenamiento de datos SQL][escalado de Almacenamiento de datos SQL]. |
| Bajo rendimiento de las consultas como resultado de poca calidad del índice |A veces, la velocidad de las consultas se puede reducir debido a la [baja calidad del índice de almacén de columnas][baja calidad del índice de almacén de columnas].  Para más información al respecto y conocer el modo de [volver a generar los índices para mejorar la calidad del segmento][volver a generar los índices para mejorar la calidad del segmento], vea este artículo. |

## <a name="system-management"></a>Administración del sistema
| Problema | Resolución |
|:--- |:--- |
| Mens. 40847: No se pudo realizar la operación porque el servidor superaría la cuota de la unidad de transacción de la base de datos permitida de 45000. |Reduzca la unidad [DWU][DWU] de la base de datos que intenta crear o [Pedir un aumento de la cuota][Pedir un aumento de la cuota]. |
| Investigación del uso del espacio |Vea los [tamaños de tabla][tamaños de tabla] para comprender el uso del espacio del sistema. |
| Ayuda con la administración de tablas |Para obtener ayuda con la administración de las tablas, vea el artículo [Table overview (Información general sobre tablas)][Información general].  En este artículo también se incluyen vínculos a temas más detallados como [Table data types (Tipos de datos de tabla)][Tipos de datos], [Distributing a table (Distribución de una tabla)][Distribución], [Indexing a table (Indexar una tabla)][Índice], [Partitioning a table (Crear particiones en una tabla)][Partition], [Maintaining table statistics (Mantenimiento de las estadísticas de tabla)][Estadísticas] y [Temporary tables (Tablas temporales)][Temporal]. |

## <a name="polybase"></a>PolyBase
| Problema | Resolución |
|:--- |:--- |
| Error de UTF-8 |Actualmente, PolyBase solo admite la carga de archivos de datos que se han codificado con UTF-8.  Vea [Evitar el requisito UTF-8 de PolyBase][Evitar el requisito UTF-8 de PolyBase] para obtener instrucciones sobre cómo solucionar esta limitación. |
| Se produce un error en la carga porque hay filas de gran tamaño |Actualmente la compatibilidad con filas de gran tamaño no está disponible en Polybase.  Esto significa que si su tabla contiene VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX), no se pueden utilizar tablas externas para cargar los datos.  Las cargas de filas de gran tamaño solo se admiten mediante Data Factory de Azure (con BCP), Análisis de transmisiones de Azure, SSIS, BCP o la clase SQLBulkCopy de .NET. La compatibilidad con filas de gran tamaño en PolyBase se agregará en una versión futura. |
| La carga de la tabla bcp con el tipo de datos MAX está dando error |Existe un problema conocido que requiere la colocación de VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX) al final de la tabla en algunos escenarios.  Intente mover las columnas MAX al final de la tabla. |

## <a name="differences-from-sql-database"></a>Diferencias con respecto a Base de datos SQL
| Problema | Resolución |
|:--- |:--- |
| Características de Base de datos SQL no admitidas |Vea [Características no compatibles de las tablas][Características no compatibles de las tablas]. |
| Tipos de datos de Base de datos SQL no admitidos |Vea [Tipos de datos no admitidos][Tipos de datos no admitidos]. |
| Limitaciones de DELETE y UPDATE |Vea [Soluciones alternativas para UPDATE][Soluciones alternativas para UPDATE], [Soluciones alternativas para DELETE][Soluciones alternativas para DELETE] y [uso de CTAS para resolver la sintaxis de UPDATE y DELETE no admitida][uso de CTAS para resolver la sintaxis de UPDATE y DELETE no admitida]. |
| No se admite la instrucción MERGE |Vea [soluciones alternativas para MERGE][soluciones alternativas para MERGE]. |
| Limitaciones de procedimientos almacenados |Vea la sección [Limitaciones de procedimientos almacenados][Limitaciones de procedimientos almacenados] para conocer algunas de las limitaciones de los procedimientos almacenados. |
| Los UDF no admiten instrucciones SELECT |Se trata de una limitación actual de nuestros UDF.  Vea [CREATE FUNCTION][CREATE FUNCTION] para comprobar la sintaxis que se admite. |

## <a name="next-steps"></a>Pasos siguientes
Si no ha podido encontrar una solución a su problema con estos pasos, estos son otros recursos que puede probar.

* [Blogs]
* [Solicitud de función]
* [Vídeos]
* [Blogs del equipo de CAT]
* [Creación de una incidencia de soporte técnico]
* [Foro de MSDN]
* [Foro Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Introducción a la seguridad]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT para Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Controladores de Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-connection-strings.md
[Conexión a Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-connect-overview.md
[Creación de una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[escalado de Almacenamiento de datos SQL]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Pedir un aumento de la cuota]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[aprender a supervisar las consultas]: ./sql-data-warehouse-manage-monitor.md
[instrucciones de aprovisionamiento]: ./sql-data-warehouse-get-started-provision.md
[Configurar el acceso al servidor de firewall para la dirección IP de cliente]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Procedimientos recomendados para Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-best-practices.md
[tamaños de tabla]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Características no compatibles de las tablas]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Tipos de datos no admitidos]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Información general]: ./sql-data-warehouse-tables-overview.md
[Tipos de datos]: ./sql-data-warehouse-tables-data-types.md
[Distribución]: ./sql-data-warehouse-tables-distribute.md
[Índice]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporal]: ./sql-data-warehouse-tables-temporary.md
[baja calidad del índice de almacén de columnas]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[volver a generar los índices para mejorar la calidad del segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[administración de la carga de trabajo]: ./sql-data-warehouse-develop-concurrency.md
[uso de CTAS para resolver la sintaxis de UPDATE y DELETE no admitida]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[Soluciones alternativas para UPDATE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Soluciones alternativas para DELETE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[soluciones alternativas para MERGE]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitaciones de procedimientos almacenados]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Autenticación a Almacenamiento de datos SQL de Azure]: ./sql-data-warehouse-authentication.md
[Evitar el requisito UTF-8 de PolyBase]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs del equipo de CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitud de función]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Foro Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse




<!--HONumber=Nov16_HO3-->


