---
title: "Resource Manager 지원 서비스 | Microsoft Docs"
description: "리소스 관리자, 스키마, 제공되는 API 버전 및 리소스를 호스팅할 수 있는 지역을 지원하는 리소스 공급자에 대해 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7dc5143086e3a73e0536408a41468b8cdd40bd12
ms.lasthandoff: 03/24/2017


---
# <a name="resource-manager-providers-regions-api-versions-and-schemas"></a>리소스 관리자 공급자, 지역, API 버전 및 스키마
이 항목에서는 Azure Resource Manager를 지원하는 리소스 공급자 목록을 제공합니다.

리소스를 배포할 때는 이러한 리소스를 지원하는 지역 및 리소스에 사용할 수 있는 API 버전도 알아야 합니다. [지원되는 지역](#supported-regions) 섹션에서는 구독 및 리소스에 대해 작동할 지역을 확인하는 방법을 보여 줍니다. 섹션 [지원되는 API 버전](#supported-api-versions) 에서는 사용할 수 있는 API 버전을 결정하는 방법을 보여줍니다.

Azure 포털 및 클래식 포털에서 어떤 서비스가 지원되는지 알아보려면 [Azure 포털 가용성 차트](https://azure.microsoft.com/features/azure-portal/availability/)를 참조하세요. 리소스 이동을 지원하는 서비스를 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

아래 표에서는 Resource Manager를 통해 배포와 관리를 지원하거나 지원하지 않는 Microsoft 서비스의 목록을 보여 줍니다. 또한 Resource Manager를 지원하는 타사 리소스 공급자도 많이 있습니다. 모든 리소스 공급자를 표시하는 방법에 대해서는 [리소스 공급자 및 형식](#resource-providers-and-types) 섹션을 참조하세요.

## <a name="compute"></a>계산
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- |
| Batch |예 |[Batch REST](/rest/api/batchservice) |[Batch 리소스](/azure/templates/microsoft.batch/batchaccounts) |
| Container Registry |예 |[컨테이너 레지스트리 REST](/rest/api/containerregistry) |[컨테이너 레지스트리 리소스](/azure/templates/microsoft.containerregistry/registries) |
| 컨테이너 서비스 |예 |[컨테이너 서비스 REST](/rest/api/compute/containerservices) |[컨테이너 서비스 리소스](/azure/templates/microsoft.containerservice/containerservices) |
| Dynamics Lifecycle Services |예 | | |
| 크기 집합 |예 |[크기 집합 REST](/rest/api/compute/virtualmachinescalesets) |[크기 집합 리소스](/azure/templates/microsoft.compute/virtualmachinescalesets) |
| Service Fabric |예 |[서비스 패브릭 Rest](/rest/api/servicefabric) | [Service Fabric 리소스](/azure/templates/microsoft.servicefabric/clusters) |
| 가상 컴퓨터 |예 |[VM REST](/rest/api/compute/virtualmachines) |[VM 리소스](/azure/templates/microsoft.compute/virtualmachines) |
| 가상 컴퓨터(클래식) |제한적 |- |- |
| RemoteApp |아니요 |- |- |
| Cloud Services(클래식) |제한적(아래 참조) |- |- |

가상 컴퓨터(클래식)는 리소스 관리자 배포 모델 대신, 클래식 배포 모델을 통해 배포된 리소스를 참조합니다. 일반적으로 이러한 리소스는 리소스 관리자 작업을 지원하지 않지만 일부 작업은 가능합니다. 이러한 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](resource-manager-deployment-model.md)를 참조하세요. 

Cloud Services(클래식)는 다른 클래식 리소스와 함께 사용할 수 있습니다. 그러나 클래식 리소스는 모든 Resource Manager 기능을 사용하지 않으며 향후 솔루션에 적합하지 않습니다. 대신, 응용 프로그램 인프라가 Microsoft.Compute, Microsoft.Storage 및 Microsoft.Network 네임스페이스의 리소스를 사용하도록 변경하는 것이 좋습니다.

## <a name="networking"></a>네트워킹
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- |
| Application Gateway |예 |[응용 프로그램 게이트웨이 REST](https://msdn.microsoft.com/library/azure/mt684939.aspx) | [Application Gateway 리소스](/azure/templates/microsoft.network/applicationgateways) |
| DNS |예 |[DNS REST](/rest/api/dns) |[DNS 리소스](/azure/templates/microsoft.network/dnszones) |
| ExpressRoute |예 |[Express 경로 REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | [ExpressRoute 리소스](/azure/templates/microsoft.network/expressroutecircuits) |
| 부하 분산 장치 |예 |[부하 분산 장치 REST](https://msdn.microsoft.com/library/azure/mt163651.aspx) |[부하 분산 장치 리소스](/azure/templates/microsoft.network/loadbalancers) |
| 트래픽 관리자 |예 |[트래픽 관리자 REST](https://msdn.microsoft.com/library/azure/mt163667.aspx) |[Traffic Manager 리소스](/azure/templates/microsoft.network/trafficmanagerprofiles) |
| 가상 네트워크 |예 |[Virtual Network REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) |[Virtual Network 리소스](/azure/templates/microsoft.network/virtualnetworks) |
| 네트워크 게이트웨이 |예 |[네트워크 게이트웨이 REST](https://msdn.microsoft.com/library/azure/mt163859.aspx) | [연결 리소스](/azure/templates/microsoft.network/connections) <br /> [로컬 네트워크 게이트웨이 리소스](/azure/templates/microsoft.network/localnetworkgateways) <br /> [Virtual Network 게이트웨이 리소스](/azure/templates/microsoft.network/virtualnetworkgateways) |

## <a name="storage"></a>저장소
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- | --- |
| 가져오기 및 내보내기 | 예 | [가져오기 및 내보내기 REST](/rest/api/storageimportexport/) | [가져오기 및 내보내기 리소스](/azure/templates/microsoft.importexport/jobs) |
| 저장소 |예 |[저장소 REST](/rest/api/storagerp) |[저장소 리소스](/azure/templates/microsoft.storage/storageaccounts) |
| StorSimple |예 | | |

## <a name="databases"></a>데이터베이스
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- | --- |
| DocumentDB |예 |[DocumentDB REST](/rest/api/documentdbresourceprovider) |[DocumentDB 리소스](/azure/templates/microsoft.documentdb/databaseaccounts) |
| Redis 캐시 |예 | [Redis Cache REST](/rest/api/redis) |[Redis 리소스](/azure/templates/microsoft.cache/redis) |
| SQL 데이터베이스 |예 |[SQL 데이터베이스 REST](/rest/api/sql) |[SQL Database 리소스](/azure/templates/microsoft.sql/servers) |
| SQL 데이터 웨어하우스 |예 | | |

## <a name="web--mobile"></a>웹 및 모바일
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- |
| API Apps |예 | [App Service REST](/rest/api/appservice) |[웹 리소스](/azure/templates/microsoft.web/sites) |
| API Management |예 |[API 관리 REST](/rest/api/apimanagement) |[API Management 리소스](/azure/templates/microsoft.apimanagement/service) |
| 인증서 등록 | 예 | [인증서 등록 REST](/rest/api/appservice/appservicecertificateorders) | [인증서 등록 리소스](/azure/templates/microsoft.certificateregistration/certificateorders)  |
| Content Moderator |예 | | | |
| 도메인 등록 | 예 | [도메인 등록](/rest/api/appservice/domains) | [도메인 등록 리소스](/azure/templates/microsoft.domainregistration/domains)  |
| 함수 앱 |예 | [함수 앱 REST](/rest/api/appservice) | [웹 리소스](/azure/templates/microsoft.web/sites) |
| Logic Apps |예 |[Logic Apps REST](/rest/api/logic) |[Logic Apps 리소스](/azure/templates/microsoft.logic/workflows) |
| Mobile Apps |예 | [App Service REST](/rest/api/appservice) | [웹 리소스](/azure/templates/microsoft.web/sites) |
| 모바일 고객 관리 |예 |[Mobile Engagement REST](https://msdn.microsoft.com/library/azure/mt683754.aspx) | |
| 검색 |예 |[검색 REST](/rest/api/searchservice) | [검색 리소스](/azure/templates/microsoft.search/searchservices) |
| 웹앱 |예 | [Web Apps REST](/rest/api/appservice/webapps) | [웹 리소스](/azure/templates/microsoft.web/sites) |

## <a name="intelligence--analytics"></a>인텔리전스 + 분석
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 | 
| --- | --- | --- | --- |
| Analysis Services | 예 | [Analysis Service REST](/rest/api/analysisservices) | [Analysis Services 리소스](/azure/templates/microsoft.analysisservices/servers) |
| Cognitive Services |예 | [Cognitive Services REST](/rest/api/cognitiveservices) |[Cognitive Services 리소스](/azure/templates/microsoft.cognitiveservices/accounts) |
| 데이터 카탈로그 |예 |[데이터 카탈로그 REST](/rest/api/datacatalog) |[Data Catalog 스키마](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2016-03-30/Microsoft.DataCatalog.json) |
| 데이터 팩터리 |예 |[데이터 팩터리 REST](/rest/api/datafactory) | |
| 데이터 레이크 분석 |예 | [Data Lake REST](/rest/api/datalakeanalytics) |[Data Lake Analytics 리소스](/azure/templates/microsoft.datalakeanalytics/accounts) |
| 데이터 레이크 저장소 |예 |[Data Lake Store 저장소 REST](/rest/api/datalakestore) |[Data Lake Store 리소스](/azure/templates/microsoft.datalakestore/accounts) |
| HDInsights |예 |[HDInsights REST](/rest/api/hdinsight) | |
| Machine Learning |예 |[기계 학습 REST](/rest/api/machinelearning) |[Machine Learning 리소스](/azure/templates/microsoft.machinelearning/commitmentplans) |
| Stream Analytics |예 |[스트림 분석 REST](/rest/api/streamanalytics) | |
| Power BI |예 |[Power BI Embedded REST](/rest/api/powerbiembedded) |[Power BI 리소스](/azure/templates/microsoft.powerbi/workspacecollections) |


## <a name="internet-of-things"></a>사물 인터넷
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- |
| 이벤트 허브 |예 |[이벤트 허브 REST](/rest/api/eventhub) |[Event Hub 리소스](/azure/templates/microsoft.eventhub/namespaces) |
| IoTHubs |예 |[IoT Hub REST](/rest/api/iothub) |[IoT Hub 리소스](/azure/templates/microsoft.devices/iothubs) |
| Notification Hubs |예 |[알림 허브 REST](/rest/api/notificationhubs) |[Notification Hub REST](/azure/templates/microsoft.notificationhubs/namespaces) |

## <a name="media--cdn"></a>미디어 및 CDN
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- |
| CDN |예 |[CDN REST](/rest/api/cdn) |[CDN 리소스](/azure/templates/microsoft.cdn/profiles) |
| 미디어 서비스 |예 |[미디어 서비스 REST](/rest/api/media) |[미디어 리소스](/azure/templates/microsoft.media/mediaservices) |

## <a name="enterprise-integration"></a>엔터프라이즈 통합
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- |
| BizTalk 서비스 |예 | |[BizTalk 스키마](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| 릴레이 | 예 |  | [Relay 리소스](/azure/templates/microsoft.relay/namespaces) |
| Service Bus |예 |[Service Bus REST](/rest/api/servicebus) |[Service Bus 리소스](/azure/templates/microsoft.servicebus/namespaces) |

## <a name="identity--access-management"></a>ID 및 액세스 관리
Azure Active Directory는 구독에 대해 리소스 관리자로 작동하므로 역할 기반 액세스 제어가 가능합니다. 역할 기반 액세스 제어 및 Active Directory를 사용하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.

## <a name="developer-services"></a>개발자 서비스
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- |
| 모니터 |예 |[Monitor REST](/rest/api/monitor) |[Insights 리소스](/azure/templates/microsoft.insights/alertrules) |
| Bing 지도 |예 | | |
| DevTest Labs |예 | [DevTest REST](/rest/api/dtl) |[DevTest Lab 리소스](/azure/templates/microsoft.devtestlab/labs) |
| Visual Studio 계정 |예 | |[Visual Studio 스키마](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## <a name="management-and-security"></a>관리 및 보안
| 부여 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- |
| Advisor | 예 | [Advisor REST](/rest/api/advisor/) | - |
| 자동화 |예 |[자동화 REST](https://msdn.microsoft.com/library/azure/mt662285.aspx) |[Automation 리소스](/azure/templates/microsoft.automation/automationaccounts) |
| 결제 | 예 | [청구 REST](/rest/api/billing/) | - |
| 키 자격 증명 모음 |예 |[키 자격 증명 모음 REST](/rest/api/keyvault) |[Key Vault 리소스](/azure/templates/microsoft.keyvault/vaults) |
| Operational Insights |예 | | |
| 복구 서비스 |예 |[Recovery Services REST](/rest/api/recoveryservices) |[Recovery Services 리소스](/azure/templates/microsoft.recoveryservices/vaults) |
| 스케줄러 |예 |[스케줄러 REST](/rest/api/scheduler) |[Scheduler 리소스](/azure/templates/microsoft.scheduler/jobcollections) |
| 보안 |예 |[보안 REST](https://msdn.microsoft.com/library/azure/mt704034.aspx) | |
| 서버 관리 | 예 | [Server Management REST](/rest/api/servermanagement/) | [Server Management 리소스](/azure/templates/microsoft.servermanagement/gateways) |

## <a name="resource-manager"></a>리소스 관리자
| 기능 | 리소스 관리자 사용 | REST API | 템플릿 형식 |
| --- | --- | --- | --- | --- |
| 권한 부여 |예 |[권한 부여 REST](/rest/api/authorization) |[권한 부여 리소스](/azure/templates/microsoft.authorization/locks) |
| 리소스 |예 |[리소스 REST](/rest/api/resources) |[배포 리소스](/azure/templates/microsoft.resources/deployments) |

## <a name="resource-providers-and-types"></a>리소스 공급자 및 형식
리소스를 배포할 때는 리소스 공급자 및 형식에 대한 정보를 자주 검색하게 됩니다. REST API, Azure PowerShell 또는 Azure CLI를 통해 이 정보를 검색할 수 있습니다.

리소스 공급자로 작업하려면 해당 리소스 공급자를 계정에 등록해야 합니다. 기본적으로 리소스 공급자는 대부분 자동으로 등록되지만 일부 리소스 공급자는 수동으로 등록해야 합니다. 아래 예제에서는 리소스 공급자의 등록 상태를 가져오고 필요한 경우 리소스 공급자를 등록하는 방법을 보여 줍니다.

### <a name="portal"></a>포털
구독 블레이드에서 **리소스 공급자**를 선택하면 지원되는 리소스 공급자 목록을 쉽게 볼 수 있습니다. 구독에 리소스 공급자를 등록하려면 **등록** 링크를 선택합니다.
   
![리소스 공급자 나열](./media/resource-manager-supported-services/view-resource-providers.png)

### <a name="rest-api"></a>REST API
형식, 위치, API 버전 및 등록 상태를 비롯한 모든 사용 가능한 리소스 공급자를 가져오려면 [모든 리소스 공급자 나열](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 작업을 사용합니다. 리소스 공급자를 등록해야 하는 경우 [리소스 공급자로 구독 등록](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register)을 참조하세요.

### <a name="powershell"></a>PowerShell
다음 예제는 사용 가능한 모든 리소스 공급자를 가져오는 방법을 보여 줍니다.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```


다음 예제는 특정 리소스 공급자에 대한 리소스 형식을 가져오는 방법을 보여 줍니다.

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
```

리소스 공급자를 등록하려면 다음과 같이 네임스페이스를 제공합니다.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement
```

### <a name="azure-cli"></a>Azure CLI
다음 예제는 사용 가능한 모든 리소스 공급자를 가져오는 방법을 보여 줍니다.

```azurecli
az provider list
```

다음 명령을 사용하여 특정 리소스 공급자에 대한 정보를 볼 수 있습니다.

```azurecli
az provider show --namespace Microsoft.Web
```

리소스 공급자를 등록하려면 다음과 같이 네임스페이스를 제공합니다.

```azurecli
az provider register --namespace Microsoft.ServiceBus
```

## <a name="supported-regions"></a>지원되는 지역
리소스를 배포할 때는 일반적으로 리소스에 대한 지역을 지정해야 합니다. 리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 해당 리소스를 지원하는 일부 지역을 사용하지 못하도록 구독에 대한 제한 사항이 있을 수 있습니다. 이러한 제한은 본국에 대한 세금 문제와 관련되거나 구독 관리자가 특정 지역만 사용하도록 배치한 정책에 따른 결과일 수 있습니다. 

모든 Azure 서비스에 대해 지원되는 전체 지역 목록은 [지역별 서비스](https://azure.microsoft.com/regions/#services)를 참조하세요. 그러나 이 목록에 구독이 지원하지 않는 지역이 포함될 수 있습니다. 구독에서 포털, REST API, PowerShell 또는 Azure CLI를 통해 지원하는 특정 리소스 형식에 대한 지역을 확인할 수 있습니다.

### <a name="portal"></a>포털
다음 단계를 통해 리소스 형식에 지원되는 지역을 확인할 수 있습니다.

1. **추가 서비스** > **리소스 탐색기**를 선택합니다.
   
    ![리소스 탐색기](./media/resource-manager-supported-services/select-resource-explorer.png)
2. **공급자** 노드를 엽니다.
   
    ![공급자 선택](./media/resource-manager-supported-services/select-providers.png)
3. 리소스 공급자를 선택하고 지원되는 위치 및 API 버전을 확인합니다.
   
    ![공급자 보기](./media/resource-manager-supported-services/view-provider.png)

### <a name="rest-api"></a>REST API
구독 중인 특정 리소스 종류에 사용할 수 있는 지역을 검색하려면 [모든 리소스 공급자 나열](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 작업을 사용하세요. 

### <a name="powershell"></a>PowerShell
다음 예제에서는 웹 사이트에 대해 지원되는 지역을 가져오는 방법을 보여 줍니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

### <a name="azure-cli"></a>Azure CLI
다음 예제에서는 웹 사이트에 대해 지원되는 위치를 가져오는 방법을 보여 줍니다.

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```


## <a name="supported-api-versions"></a>지원되는 API 버전
서식 파일을 배포할 때 각 리소스를 만들기 위해 사용하는 API 버전을 지정해야 합니다. API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다. 따라서 서식 파일에 지정하는 API의 버전은 템플릿에 지정할 수 있는 속성에 영향을 줍니다. 일반적으로 새 템플릿을 만들 때 최신 버전의 API를 선택하려고 합니다. 기존 템플릿의 경우 이전 API 버전을 계속 사용할지 아니면 새 기능을 이용하도록 최신 버전에 맞게 템플릿을 업데이트할지 여하를 결정할 수 있습니다.

### <a name="portal"></a>포털
지원되는 지역을 확인하기 위해 앞에서 설명한 방식과 동일한 방식으로 지원되는 API 버전을 확인합니다.

### <a name="rest-api"></a>REST API
리소스 종류에 사용할 수 있는 API 버전을 검색하려면 [모든 리소스 공급자 나열](https://docs.microsoft.com/rest/api/resources/providers#Providers_List) 작업을 사용하세요. 

### <a name="powershell"></a>PowerShell
아래 예제에서는 특정 리소스 형식에 사용할 수 있는 API 버전을 가져오는 방법을 보여 줍니다.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

다음과 유사하게 출력됩니다.

```powershell
2015-08-01
2015-07-01
2015-06-01
2015-05-01
2015-04-01
2015-02-01
2014-11-01
2014-06-01
2014-04-01-preview
2014-04-01
```

### <a name="azure-cli"></a>Azure CLI
다음 명령을 사용하여 리소스 공급자에 대한 사용 가능한 API 버전을 가져옵니다.

```azurecli
az provider show --namespace Microsoft.Web --query "resourceTypes[?resourceType=='sites'].apiVersions"
```

## <a name="next-steps"></a>다음 단계
* 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
* 리소스 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.


