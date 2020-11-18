---
title: Azure 서비스의 리소스 공급자
description: Azure Resource Manager에 대 한 모든 리소스 공급자 네임 스페이스를 나열 하 고 해당 네임 스페이스에 대 한 Azure 서비스를 표시 합니다.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: c447a08b4422929f042315370b5888f3db0626e4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697733"
---
# <a name="resource-providers-for-azure-services"></a>Azure 서비스의 리소스 공급자

이 문서에서는 리소스 공급자 네임 스페이스를 Azure 서비스에 매핑하는 방법을 보여 줍니다.

## <a name="match-resource-provider-to-service"></a>리소스 공급자를 서비스에 매칭

| 리소스 공급자 네임 스페이스 | Azure 서비스 |
| --------------------------- | ------------- |
| Microsoft.AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| ADHybridHealthService<sup>1</sup> | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [API Management](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Azure 증명 서비스 |
| Microsoft 인증<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automation](../../automation/index.yml) |
| AutonomousSystems | [자치 시스템](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Azure VMware 솔루션](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft.AzureData | SQL Server 레지스트리 |
| Microsoft.AzureStack | core |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft 청구<sup>1</sup> | [Cost Management 및 청구](/azure/billing/) |
| Microsoft.BingMaps | [Bing 지도](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure Blockchain 서비스](../../blockchain/workbench/index.yml) |
| Microsoft.BlockchainTokens | [Azure Blockchain 토큰](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft.Blueprint | [Azure Blueprints](../../governance/blueprints/index.yml) |
| Microsoft.BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft.Cache | [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [인증서 App Service](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | 클래식 배포 모델 가상 컴퓨터 |
| Microsoft.ClassicInfrastructureMigrate | 클래식 배포 모델 마이그레이션 |
| Microsoft.ClassicNetwork | 클래식 배포 모델 가상 네트워크 |
| Microsoft.ClassicStorage | 클래식 배포 모델 저장소 |
| ClassicSubscription<sup>1</sup> | 클래식 배포 모델 |
| Microsoft.CognitiveServices | [Cognitive Services](../../cognitive-services/index.yml) |
| Microsoft 상거래<sup>1</sup> | core |
| Microsoft.Compute | [Virtual Machines](../../virtual-machines/index.yml)<br />[Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) |
| Microsoft 사용<sup>1</sup> | [Cost Management](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Container Instances](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft.ContainerService | [AKS(Azure Kubernetes Service)](../../aks/index.yml) |
| CostManagement<sup>1</sup> | [Cost Management](/azure/cost-management/) |
| Microsoft.CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [Microsoft Azure에 대한 고객 Lockbox](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft.CustomProviders | [Azure 사용자 지정 공급자](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Data Catalog](../../data-catalog/index.yml) |
| Microsoft.DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Azure Database Migration Service](../../dms/index.yml) |
| Microsoft.DataProtection | 데이터 보호 |
| Microsoft.DataShare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft.DBforMariaDB | [Azure Database for MariaDB](../../mariadb/index.yml) |
| Microsoft.DBforMySQL | [Azure Database for MySQL](../../mysql/index.yml) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](../../postgresql/index.yml) |
| Microsoft.DeploymentManager | [Azure 배포 관리자](../templates/deployment-manager-overview.md) |
| Microsoft.DesktopVirtualization | [Windows Virtual Desktop](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT Hub](../../iot-hub/index.yml)<br />[Azure IoT Hub Device Provisioning Service](../../iot-dps/index.yml) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [App Service](../../app-service/index.yml) |
| Microsoft.DynamicsLcs | [수명 주기 서비스](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft.EnterpriseKnowledgeGraph | 엔터프라이즈 지식 그래프 |
| Microsoft.EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft.EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft 기능<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [Azure의 SAP HANA(대규모 인스턴스)](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [Azure Dedicated HSM](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [FHIR용 Azure API](../../healthcare-apis/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](../../storsimple/index.yml) |
| HybridNetwork  | [개인에 지 영역](../../networking/edge-zones-overview.md) |
| Microsoft.ImportExport | [Azure Import/Export](../../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft.IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [AKS(Azure Kubernetes Service)](../../aks/index.yml) |
| Microsoft.KubernetesConfiguration | [AKS(Azure Kubernetes Service)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Azure 유지 관리](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedNetwork | PaaS 서비스에서 관리 하는 가상 네트워크 |
| Microsoft.ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft.Management | [관리 그룹](../../governance/management-groups/index.yml) |
| Microsoft.Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| MarketplaceOrdering<sup>1</sup> | core |
| Microsoft.Media | [Media Services](../../media-services/index.yml) |
| Microsoft.Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Virtual WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. 전자 필기장 | [Azure 노트](https://notebooks.azure.com/help/introduction) |
| Microsoft.NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | 개체 저장소 |
| Microsoft.OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft 포털<sup>1</sup> | [Azure Portal](../../azure-portal/index.yml) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft.ProjectBabylon | [Azure Data Catalog](../../data-catalog/overview.md) |
| Microsoft.Quantum | [Azure 퀀텀](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Azure Relay](../../azure-relay/relay-what-is-it.md) |
| Microsoft ResourceGraph<sup>1</sup> | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft .Resources<sup>1</sup> | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft.Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Security Center](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| SerialConsole<sup>1</sup> | [Windows의 Azure 직렬 콘솔](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft.Services | core |
| Microsoft.SignalRService | [Azure SignalR Service](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | 라이선스 |
| Microsoft.Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Azure SQL Managed Instance](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [Azure Virtual Machines의 SQL Server](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [스토리지](../../storage/index.yml) |
| Microsoft.StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft.StorageSync | [스토리지](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft. 지원<sup>1</sup> | core |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.Token | 토큰 |
| Microsoft.VirtualMachineImages | [Azure 이미지 작성기](../../virtual-machines/linux/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/) |
| Microsoft.VMware | [Azure VMware 솔루션](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft.VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft.Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Microsoft Defender Advanced Threat Protection](../../security-center/security-center-wdatp.md) |
| Microsoft.WindowsESU | 확장 된 보안 업데이트 |
| Microsoft.WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| WorkloadMonitor<sup>1</sup> | [Azure Monitor](../../azure-monitor/index.yml) |

<sup>1</sup> 기본적으로 등록 됨

## <a name="next-steps"></a>다음 단계

리소스 공급자를 등록 하는 방법을 비롯 하 여 리소스 공급자에 대 한 자세한 내용은 [Azure 리소스 공급자 및 형식](resource-providers-and-types.md)을 참조 하세요.