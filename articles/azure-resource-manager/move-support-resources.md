---
title: Compatibilidad con la operación de traslado por tipo de recurso de Azure
description: Enumera los tipos de recursos de Azure que se pueden trasladar a un nuevo grupo de recursos o suscripción.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 73f4b6fe4714d21c12d2c7bd387cd30f6f711d5a
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624323"
---
# <a name="move-operation-support-for-resources"></a>Compatibilidad con la operación de traslado para recursos
En este artículo se indica si un tipo de recurso de Azure es compatible con la operación de traslado. También proporciona información sobre las condiciones especiales que se deben tener en cuenta a la hora de mover un recurso.

Vaya a un espacio de nombres del proveedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Sin | Sin |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tenants | Sin | Sin |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionRules | Sí | Sí |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Sí | Sí |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| service | SÍ | Sí |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | Sí | Sí |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Sin | Sin |
| appidentities | Sin | Sin |
| gateways | Sin | Sin |

> [!IMPORTANT]
> Vea [Guía de movimiento de App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Sin | Sin |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Sí | Sí |
| automationaccounts/configurations | Sí | Sí |
| automationaccounts/runbooks | Sí | Sí |

> [!IMPORTANT]
> Los runbooks deben encontrarse en el mismo grupo de recursos que la cuenta de Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Sí | Sí |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registrations | Sí | Sí |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Sin | Sin |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Sí | Sí |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Sin | Sin |
| fileservers | Sin | Sin |
| jobs | Sin | Sin |
| workspaces | Sin | Sin |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Sin | Sin |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Sí | Sí |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Sí | Sí |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Sin | Sin |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Sí | Sí |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| redis | Sí | Sí |

> [!IMPORTANT]
> Si la instancia de Azure Redis Cache está configurada con una red virtual, la instancia no se puede mover a otra suscripción. Vea [Limitaciones de movimiento de las redes virtuales](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Sí | Sí |
| profiles/endpoints | Sí | Sí |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificateorders | Sí | Sí |

> [!IMPORTANT]
> Vea [Guía de movimiento de App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | Sí | Sin |
| virtualmachines | Sí | Sin |

> [!IMPORTANT]
> Vea [Guía de movimiento de implementación clásica](./move-limitations/classic-model-move-limitations.md). Los recursos de implementación clásica se pueden mover entre suscripciones con una operación específica de ese escenario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Sin | Sin |
| reservedips | Sin | Sin |
| virtualnetworks | Sin | Sin |

> [!IMPORTANT]
> Vea [Guía de movimiento de implementación clásica](./move-limitations/classic-model-move-limitations.md). Los recursos de implementación clásica se pueden mover entre suscripciones con una operación específica de ese escenario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Sí | Sin |

> [!IMPORTANT]
> Vea [Guía de movimiento de implementación clásica](./move-limitations/classic-model-move-limitations.md). Los recursos de implementación clásica se pueden mover entre suscripciones con una operación específica de ese escenario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sí | Sí |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Sí | Sí |
| disks | Sí | Sí |
| galleries | Sin | Sin |
| galleries/images | Sin | Sin |
| galleries/images/versions | Sin | Sin |
| hostgroups | Sin | Sin |
| hostgroups/hosts | Sin | Sin |
| images | Sí | Sí |
| proximityplacementgroups | Sin | Sin |
| restorepointcollections | Sin | Sin |
| sharedvmimages | Sin | Sin |
| sharedvmimages/versions | Sin | Sin |
| snapshots | Sí | Sí |
| virtualmachines | Sí | Sí |
| virtualmachines/extensions | Sí | Sí |
| virtualmachinescalesets | Sí | Sí |

> [!IMPORTANT]
> Vea [Guía de movimiento de Virtual Machines](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Sin | Sin |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Sin | Sin |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registries | Sí | Sí |
| registries/buildtasks | Sí | Sí |
| registries/replications | Sí | Sí |
| registries/tasks | Sí | Sí |
| registries/webhooks | Sí | Sí |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Sin | Sin |
| managedclusters | Sin | Sin |
| openshiftmanagedclusters | Sin | Sin |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | Sí | Sí |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sin | Sin |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| conectores | Sí | Sí |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hubs | Sí | Sí |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Sin | Sin |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Sin | Sin |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Sin | Sin |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| catalogs | Sí | Sí |
| datacatalogs | Sin | Sin |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Sin | Sin |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| packages | Sin | Sin |
| plans | Sin | Sin |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | Sí | Sí |
| factories | Sí | Sí |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Sin | Sin |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sí | Sí |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sí | Sí |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Sin | Sin |
| services/projects | Sin | Sin |
| slots | Sin | Sin |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Sí | Sí |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Sí | Sí |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Sin | Sin |
| servers | Sí | Sí |
| serversv2 | Sí | Sí |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Sí | Sí |
| rollouts | Sí | Sí |
| servicetopologies | Sí | Sí |
| servicetopologies/services | Sí | Sí |
| servicetopologies/services/serviceunits | Sí | Sí |
| steps | Sí | Sí |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Sin | Sin |
| elasticpools/iothubtenants | Sin | Sin |
| iothubs | Sí | Sí |
| provisioningservices | Sí | Sí |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| controllers | Sin | Sin |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Sin | Sin |
| labs | Sí | Sin |
| labs/environments | Sí | Sí |
| labs/servicerunners | Sí | Sí |
| labs/virtualmachines | Sí | Sin |
| schedules | Sí | Sí |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Sin | Sin |
| dnszones/a | Sin | Sin |
| dnszones/aaaa | Sin | Sin |
| dnszones/cname | Sin | Sin |
| dnszones/mx | Sin | Sin |
| dnszones/ptr | Sin | Sin |
| dnszones/srv | Sin | Sin |
| dnszones/txt | Sin | Sin |
| trafficmanagerprofiles | Sin | Sin |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Sí | Sí |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domains | Sí | Sí |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Sí | Sí |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domains | Sí | Sí |
| topics | Sí | Sí |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Sí | Sí |
| namespaces | Sí | Sí |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sin | Sin |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Sí | Sí |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Sí | Sí |

> [!IMPORTANT]
> Puede mover clústeres de HDInsight a una nueva suscripción o un nuevo grupo de recursos. Sin embargo, no puede mover entre suscripciones los recursos de red vinculados al clúster de HDInsight (por ejemplo, la red virtual, una NIC o un equilibrador de carga). Además, tampoco se puede mover a un nuevo grupo de recursos una NIC que está conectada a una máquina virtual del clúster.
>
> Al mover un clúster de HDInsight a una nueva suscripción, mueva primero otros recursos (por ejemplo, la cuenta de almacenamiento). Después, mover el clúster de HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Sí | Sí |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| machines | Sin | Sin |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Sí | Sí |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Sí | Sí |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sin | Sin |
| actiongroups | Sí | Sí |
| activitylogalerts | Sin | Sin |
| alertrules | Sí | Sí |
| autoscalesettings | Sí | Sí |
| components | Sí | Sí |
| guestdiagnosticsettings | Sin | Sin |
| metricalerts | Sin | Sin |
| notificationgroups | Sin | Sin |
| notificationrules | Sin | Sin |
| scheduledqueryrules | Sí | Sí |
| webtests | Sí | Sí |
| workbooks | Sí | Sí |

> [!IMPORTANT]
> Asegúrese de que el movimiento a una nueva suscripción no exceda las [cuotas de suscripción](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Sí | Sí |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Sí | Sí |
| graph | Sí | Sí |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Sin | Sin |
| vaults | Sí | Sí |

> [!IMPORTANT]
> Los almacenes de claves usados para el cifrado de discos no se pueden mover a grupos de recursos de la misma suscripción ni entre suscripciones.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Sí | Sí |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Sin | Sin |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sí | Sí |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sin | Sin |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Sin | Sin |
| integrationaccounts | Sí | Sí |
| integrationserviceenvironments | Sin | Sin |
| isolatedenvironments | Sin | Sin |
| workflows | Sí | Sí |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Sí | Sí |
| webservices | Sí | Sin |
| workspaces | Sí | Sí |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Sí | Sí |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sin | Sin |
| accounts/workspaces | Sin | Sin |
| accounts/workspaces/projects | Sin | Sin |
| teamaccounts | Sin | Sin |
| teamaccounts/workspaces | Sin | Sin |
| teamaccounts/workspaces/projects | Sin | Sin |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sí | Sí |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Sin | Sin |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Sin | Sin |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Sin | Sin |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sí | Sí |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Sin | Sin |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mediaservices | Sí | Sí |
| mediaservices/liveevents | Sí | Sí |
| mediaservices/streamingendpoints | Sí | Sí |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Sin | Sin |
| migrateprojects | Sin | Sin |
| projects | Sin | Sin |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Sin | Sin |
| netappaccounts/capacitypools | Sin | Sin |
| netappaccounts/capacitypools/volumes | Sin | Sin |
| netappaccounts/capacitypools/volumes/mounttargets | Sin | Sin |
| netappaccounts/capacitypools/volumes/snapshots | Sin | Sin |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Sin | Sin |
| applicationgatewaywebapplicationfirewallpolicies | Sin | Sin |
| applicationsecuritygroups | Sí | Sí |
| azurefirewalls | Sí | Sí |
| bastionhosts | Sin | Sin |
| connections | Sí | Sí |
| ddoscustompolicies | Sí | Sí |
| ddosprotectionplans | Sin | Sin |
| dnszones | Sí | Sí |
| expressroutecircuits | Sin | Sin |
| expressroutecrossconnections | Sin | Sin |
| expressroutegateways | Sin | Sin |
| expressrouteports | Sin | Sin |
| frontdoors | Sin | Sin |
| frontdoorwebapplicationfirewallpolicies | Sin | Sin |
| loadbalancers | Sí: SKU básico<br>No: SKU estándar | Sí: SKU básico<br>No: SKU estándar |
| localnetworkgateways | Sí | Sí |
| natgateways | Sí | Sí |
| networkintentpolicies | Sí | Sí |
| networkinterfaces | Sí | Sí |
| networkprofiles | Sin | Sin |
| networksecuritygroups | Sí | Sí |
| networkwatchers | Sí | Sí |
| networkwatchers/connectionmonitors | Sí | Sí |
| networkwatchers/lenses | Sí | Sí |
| networkwatchers/pingmeshes | Sí | Sí |
| p2svpngateways | Sin | Sin |
| privatednszones | Sí | Sí |
| privatednszones/virtualnetworklinks | Sí | Sí |
| privateendpoints | Sin | Sin |
| privatelinkservices | Sin | Sin |
| publicipaddresses | Sí: SKU básico<br>No: SKU estándar | Sí: SKU básico<br>No: SKU estándar |
| publicipprefixes | Sí | Sí |
| routefilters | Sin | Sin |
| routetables | Sí | Sí |
| securegateways | Sí | Sí |
| serviceendpointpolicies | Sí | Sí |
| trafficmanagerprofiles | Sí | Sí |
| virtualhubs | Sin | Sin |
| virtualnetworkgateways | Sí | Sí |
| virtualnetworks | Sí | Sí |
| virtualnetworktaps | Sin | Sin |
| virtualwans | Sin | Sin |
| vpngateways | Sin | Sin |
| vpnsites | Sin | Sin |
| webapplicationfirewallpolicies | Sí | Sí |

> [!IMPORTANT]
> Consulte [Guía de movimiento de redes](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Sí | Sí |
| namespaces/notificationhubs | Sí | Sí |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Sí | Sí |

> [!IMPORTANT]
> Asegúrese de que el movimiento a una nueva suscripción no exceda las [cuotas de suscripción](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Sí | Sí |
| solutions | Sí | SÍ |
| views | SÍ | Sí |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| peerings | Sin | Sin |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dashboards | Sí | Sí |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Sin | Sin |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Sí | Sí |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| capacities | Sí | Sí |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Sin | Sin |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| vaults | Sí | Sí |

> [!IMPORTANT]
> Vea [Guía de movimiento de Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Sí | Sí |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | Sí | Sin |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| flows | Sí | Sí |
| jobcollections | Sí | Sí |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Sí | Sí |

> [!IMPORTANT]
> No puede mover varios recursos de Search de regiones diferentes en una operación. En su lugar, muévalos en operaciones independientes.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Sí | Sí |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gateways | Sin | Sin |
| nodes | Sin | Sin |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Sí | Sí |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | Sin | Sin |
| clusters | Sí | Sí |
| containergroups | Sin | Sin |
| containergroupsets | Sin | Sin |
| edgeclusters | Sin | Sin |
| networks | Sin | Sin |
| secretstores | Sin | Sin |
| volumes | Sin | Sin |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | Sí | Sí |
| containergroups | Sin | Sin |
| gateways | Sí | Sí |
| networks | Sí | Sí |
| secrets | Sí | Sí |
| volumes | Sí | Sí |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| signalr | Sí | Sí |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Sin | Sin |

> [!IMPORTANT]
> Vea [Guía de movimiento de Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Sin | Sin |
| appliances | Sin | Sin |
| applicationdefinitions | Sin | Sin |
| applications | Sin | Sin |
| jitrequests | Sin | Sin |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Sin | Sin |
| managedinstances | Sin | Sin |
| managedinstances/databases | Sin | Sin |
| servers | Sí | Sí |
| servers/databases | Sí | Sí |
| servers/elasticpools | Sí | Sí |
| virtualclusters | Sí | Sí |

> [!IMPORTANT]
> La base de datos y el servidor deben residir en el mismo grupo de recursos. Cuando se mueve un servidor SQL Server, se mueven también todas sus bases de datos. Este comportamiento se aplica a las bases de datos de Azure SQL Database y Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Sí | Sí |
| sqlvirtualmachines | Sí | Sí |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Sin | Sin |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Sí | Sí |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| caches | Sin | Sin |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sí | Sí |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sin | Sin |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sin | Sin |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managers | Sin | Sin |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Sí | Sí |

> [!IMPORTANT]
> Los trabajos de Stream Analytics no se pueden mover si se encuentran en estado de ejecución.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Sin | Sin |
| environments/eventsources | Sin | Sin |
| instances | Sin | Sin |
| instances/environments | Sin | Sin |
| instances/environments/eventsources | Sin | Sin |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Sin | Sin |
| resources | Sin | Sin |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Sí | Sí |
| environments/eventsources | Sí | Sí |
| environments/referencedatasets | Sí | Sí |

## <a name="microsofttoken"></a>Microsoft.Token
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| almacenes | Sin | Sin |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Sin | Sin |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| account | SÍ | Sí |
| account/extension | Sí | Sí |
| account/project | Sí | Sí |

> [!IMPORTANT]
> Para cambiar la suscripción de Azure DevOps, vea [Cambiar la suscripción de Azure usada para la facturación](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Sí | Sí |
| dedicatedcloudservices | Sí | Sí |
| virtualmachines | Sí | Sí |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificates | Sin | Sí |
| connectiongateways | Sí | Sí |
| connections | Sí | Sí |
| customapis | Sí | Sí |
| hostingenvironments | Sin | Sin |
| serverfarms | Sí | Sí |
| sites | Sí | Sí |
| sites/premieraddons | Sí | Sí |
| sites/slots | Sí | Sí |

> [!IMPORTANT]
> Vea [Guía de movimiento de App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Sin | Sin |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Tipo de recurso | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Sin | Sin |
| hostpools | Sin | Sin |
| workspaces | Sin | Sin |

## <a name="third-party-services"></a>Servicios de terceros

Actualmente los servicios de terceros no son compatibles con la operación de traslado.

## <a name="next-steps"></a>Pasos siguientes
Para ver los comandos para trasladar recursos, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).

Para obtener los mismos datos como un archivo de valores separados por comas, descargue [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
