---
title: Azure 서비스별 리소스 공급자
description: Azure 리소스 관리자에 대한 모든 리소스 공급자 네임스페이스를 나열하고 해당 네임스페이스에 대한 Azure 서비스를 표시합니다.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 55fbe4ae383e5275d185e2a03224e77660a01ef5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382505"
---
# <a name="resource-providers-for-azure-services"></a>Azure 서비스에 대한 리소스 공급자

이 문서에서는 리소스 공급자 네임스페이스가 Azure 서비스에 매핑하는 방법을 보여 주며 있습니다.

## <a name="match-resource-provider-to-service"></a>리소스 공급자를 서비스로 일치시다

| 리소스 공급자 네임스페이스 | Azure 서비스 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [API Management](../../api-management/index.yml) |
| 마이크로소프트.앱구성 | core |
| 마이크로소프트.앱플랫폼 | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure 증명 서비스 |
| Microsoft.Authorization | [Azure 리소스 관리자](../index.yml) |
| Microsoft.Automation | [Automation](../../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| 마이크로소프트.Azure데이터 | SQL 서버 레지스트리 |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft.Billing | [Cost Management 및 청구](/azure/billing/) |
| Microsoft.BingMaps | [Bing 지도](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure Blockchain 서비스](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Azure Blueprints](/azure/governance/blueprints/) |
| Microsoft.BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [앱 서비스 인증서](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| 마이크로소프트.변경분석 | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | 클래식 배포 모델 가상 머신 |
| Microsoft.ClassicInfrastructureMigrate | 클래식 배포 모델 마이그레이션 |
| Microsoft.ClassicNetwork | 클래식 배포 모델 가상 네트워크 |
| Microsoft.ClassicStorage | 클래식 배포 모델 스토리지 |
| 마이크로소프트.클래식 구독 | 클래식 배포 모델 |
| Microsoft.CognitiveServices | [Cognitive Services](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [Virtual Machines](/azure/virtual-machines/)<br />[Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) |
| Microsoft.Consumption | [비용 관리](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Container Registry](/azure/container-registry/) |
| Microsoft.ContainerService | [AKS(Azure Kubernetes Service)](/azure/aks/) |
| Microsoft.CostManagement | [비용 관리](/azure/cost-management/) |
| 마이크로소프트.코스트매니지먼트수출 | [비용 관리](/azure/cost-management/) |
| 마이크로소프트.고객 잠금 상자 | 마이크로소프트 Azure를 위한 고객 잠금 상자 |
| 마이크로소프트.사용자 지정 공급자 | [Azure 사용자 지정 공급자](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure 데이터 상자](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure Database Migration Service](/azure/dms/) |
| 마이크로소프트.데이터 쉐어 | [Azure Data Share](/azure/data-share/) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| 마이크로소프트.데스크톱 가상화 | [Windows Virtual Desktop](/azure/virtual-desktop/) |
| Microsoft.DeploymentManager | [Azure 배포 관리자](../templates/deployment-manager-overview.md) |
| Microsoft.Devices | [Azure IoT Hub](/azure/iot-hub/)<br />[Azure IoT Hub Device Provisioning Service](/azure/iot-dps/) |
| 마이크로소프트.데브옵스 | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev 공간](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| 마이크로소프트.디지털 트윈스 | [Azure Digital Twins](../../digital-twins/about-digital-twins.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service](/azure/app-service/) |
| 마이크로소프트.엔터프라이즈 지식그래프 | 엔터프라이즈 지식 그래프 |
| Microsoft.EventGrid | [Event Grid](/azure/event-grid/) |
| Microsoft.EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft.Features | [Azure 리소스 관리자](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure의 SAP HANA(대규모 인스턴스)](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure 전용 HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| 마이크로소프트.헬스케어아피 | [FHIR용 Azure API](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](/azure/storsimple/) |
| Microsoft.ImportExport | [Azure Import/Export](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| 마이크로소프트.쿠베네테스 | [AKS(Azure Kubernetes Service)](/azure/aks/) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| 마이크로소프트.유지 보수 | [Azure 유지 관리](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/index.yml) |
| 마이크로소프트.ManagedServices | [Azure Lighthouse](/azure/lighthouse/) |
| Microsoft.Management | [관리 그룹](/azure/governance/management-groups/) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [Media Services](../../media-services/index.yml) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-overview.md) |
| 마이크로소프트.혼합현실 | [Azure Spatial Anchors](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](/azure/bastion/)<br />[Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[가상 네트워크](../../virtual-network/index.yml)<br />[가상 WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft.NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| 마이크로소프트.피어링 | [Azure 피어링 서비스](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.Portal | [Azure Portal](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| 마이크로소프트.레드햇오픈시프트 | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure Relay](../../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Azure Resource Graph](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | [Azure Service Health](../../service-health/index.yml)|
| Microsoft.Resources | [Azure 리소스 관리자](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Scheduler](/azure/scheduler/) |
| Microsoft.Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Security Center](../../security-center/index.yml) |
| 마이크로소프트.시큐리티인사이트 | [Azure Sentinel](/azure/sentinel/) |
| 마이크로소프트.시리얼 콘솔 | [윈도우용 Azure 직렬 콘솔](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Azure SignalR Service](../../azure-signalr/index.yml) |
| 마이크로소프트.소프트웨어플랜 | License |
| Microsoft.Solutions | [Azure 관리되는 애플리케이션](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../sql-database/index.yml)<br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [Azure Virtual Machines의 SQL Server](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [스토리지](../../storage/index.yml) |
| 마이크로소프트.스토리지캐시 | [Azure HPC Cache](/azure/hpc-cache/) |
| Microsoft.StorageSync | [스토리지](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| 마이크로소프트.시냅스 | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.VirtualMachineImages | [Azure 이미지 작성기](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/?view=azure-devops) |
| 마이크로소프트.VM웨어클라우드심플 | [Azure VMware Solution by CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="next-steps"></a>다음 단계

리소스 공급자에 대한 자세한 내용은 [Azure 리소스 공급자 및 형식을](resource-providers-and-types.md) 참조하십시오.
