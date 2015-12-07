<properties
   pageTitle="리소스 관리자 지원 서비스 및 지원되는 지역 | Microsoft Azure"
   description="리소스 관리자 및 리소스를 호스팅할 수 있는 지역을 지원하는 리소스 공급자에 대해 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="tomfitz"/>

# 서비스, 지역 및 API 버전에 대한 리소스 관리자 지원

Azure 리소스 관리자는 응용 프로그램을 구성하는 서비스를 배포하고 관리하는 새로운 방법을 제공합니다. 모두는 아니지만, 대부분의 서비스는 리소스 관리자를 지원하고 일부 서비스는 리소스 관리자를 부분적으로만 지원합니다. Microsoft는 향후 솔루션을 위해 중요한 모든 서비스에 리소스 관리자를 사용하도록 할 예정이지만 일관적으로 지원될 때까지 각 서비스의 현재 상태를 알아야 합니다. 이 항목에서는 Azure 리소스 관리자에 대해 지원되는 리소스 공급자 목록을 제공합니다.

리소스를 배포할 때는 이러한 리소스를 지원하는 지역 및 리소스에 사용할 수 있는 API 버전도 알아야 합니다. [지원되는 지역](#supported-regions) 섹션에서는 구독 및 리소스에 대해 작동할 지역을 확인하는 방법을 보여줍니다. 섹션 [지원되는 API 버전](#supported-api-versions)에서는 사용할 수 있는 API 버전을 결정하는 방법을 보여줍니다.

다음 표에서는 리소스 관리자를 통해 배포 및 관리를 지원 또는 지원하지 않는 서비스 목록을 보여줍니다. **리소스 이동**이라는 열은 새 리소스 그룹 및 새 구독 모두에 이 유형의 리소스를 이동할 수 있는지 여부를 나타냅니다. **Preview 포털**이라는 열은 Preview 포털을 통해 서비스를 만들 수 있는지 여부를 나타냅니다.


## 계산

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| 가상 컴퓨터 | 예 | 예, 다양한 옵션 | 아니요 | [VM 만들기](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| 배치 | 예 | [예(클래식에만 해당)](https://portal.azure.com/#create/Microsoft.BatchAccount) | 예 | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Dynamics Lifecycle Services | 예 | 아니요 | | | |
| 가상 컴퓨터(클래식) | 제한됨 | 예, 다양한 옵션 | 부분적(아래 참조) | - | - |
| RemoteApp | 아니요 | 아니요 | - | - | - |
| 서비스 패브릭 | 아니요 | 아니요 | - | - | - |

가상 컴퓨터(클래식)는 리소스 관리자 배포 모델 대신, 클래식 배포 모델을 통해 배포된 리소스를 참조합니다. 일반적으로 이러한 리소스는 리소스 관리자 작업을 지원하지 않지만 일부 작업은 가능합니다. 이러한 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](resource-manager-deployment-model.md)를 참조하세요.

가상 컴퓨터(클래식) 리소스를 새 구독이 아닌, 새 리소스 그룹으로 이동할 수 있습니다.

## 네트워킹

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| 응용 프로그램 게이트웨이 | 예 | | | | |
| DNS | 예 | | | [DNS 영역 만들기](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 부하 분산 장치 | 예 | | | [부하 분산 장치 만들기](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 가상 네트워크 | 예 | [예](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) | 아니요 | [가상 네트워크 만들기](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 트래픽 관리자 | 예 | 아니요 | | [트래픽 관리자 프로필 만들기](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| Express 경로 | 예 | 아니요 | 아니요 | [Express 경로 REST](https://msdn.microsoft.com/library/azure/mt586720.aspx) | |

## 데이터 및 저장소

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | 예 | [예](https://portal.azure.com/#create/Microsoft.DocumentDB) | 예 | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| 저장소 | 예 | [예](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) | 아니요 | [저장소 만들기](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [저장소 계정](resource-manager-template-storage.md) |
| Redis 캐시 | 예 | [예](https://portal.azure.com/#create/Microsoft.Cache.1.0.4) | 예 | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| SQL 데이터베이스 | 예 | [예](https://portal.azure.com/#create/Microsoft.SQLDatabase.0.5.9-preview) | 예 | [데이터베이스 만들기](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| 검색 | 예 | [예](https://portal.azure.com/#create/Microsoft.Search) | 예 | [검색 REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL 데이터 웨어하우스 | 예 | [예](https://portal.azure.com/#create/Microsoft.SQLDataWarehouse.0.1.12-preview) | | | |
| StorSimple | 아니요 | 아니요 | - | - | - | | 관리된 캐시 | 아니요 | 아니요 | - | - | - |

## 웹 및 모바일

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| API 관리 | 예 | 아니요 | 예 | [API 만들기](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| API 앱 | 예 | [예](https://portal.azure.com/#create/microsoft_com.ApiApp.0.2.0-preview) | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| 웹앱 | 예 | [예](https://portal.azure.com/#create/Microsoft.WebSite) | 예, 제한 있음(아래 참조) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| 알림 허브 | 예 | [예](https://portal.azure.com/#create/Microsoft.NotificationHub) | 예 | [알림 허브 만들기](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| 논리 앱 | 예 | [예](https://portal.azure.com/#create/Microsoft.EmptyWorkflow.0.2.0-preview) | 예 | | |
| 모바일 고객 관리 | 예 | 아니요 | 예 | | |

웹앱으로 작업할 경우에는 앱 서비스 계획만 이동할 수 없습니다. 웹앱을 이동하려면 옵션은 다음과 같습니다.

- 대상 리소스 그룹에 아직 Microsoft.Web 리소스가 없는 경우 하나의 리소스 그룹에 있는 모든 리소스를 다른 리소스 그룹으로 이동합니다.
- 웹앱을 다른 리소스 그룹으로 이동하지만 앱 서비스는 원래 리소스 그룹에 유지합니다.

## 분석

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| 이벤트 허브 | 예 | 아니요 | | [이벤트 허브 만들기](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| 스트림 분석 | 예 | [예](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) | | | |
| HDInsights | 예 | [예](https://portal.azure.com/#create/Microsoft.HDInsightCluster) | 예 | | |
| 데이터 팩터리 | 예 | [예](https://portal.azure.com/#create/Microsoft.DataFactory) | 예 | [데이터 팩터리 만들기](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| 기계 학습 | 아니요 | 아니요 | - | - | - | | 데이터 카탈로그 | 아니요 | 아니요 | - | - | - |

## 미디어 및 CDN

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| CDN | 예(Preview) | 아니요 | | | |
| 미디어 서비스 | 아니요 | 아니요 | | | |


## 하이브리드 통합

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| BizTalk 서비스 | 예 | 아니요 | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| 서비스 버스 | 예 | 아니요 | | [서비스 버스 REST](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |
| 백업 | 아니요 | 아니요 | - | - | - | | 사이트 복구 | 아니요 | 아니요 | - | - | - |

## ID 및 액세스 관리 

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | 아니요 | 아니요 | - | - | - |
| Azure Active Directory B2C | 아니요 | 아니요 | - | - | - |
| Multi-Factor Authentication | 아니요 | 아니요 | - | - | - |

## 개발자 서비스 

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | 예 | [예](https://portal.azure.com/#create/Microsoft.AppInsights.0.2.3-preview) | 아니요 | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing 지도 | 예 | [예](https://portal.azure.com/#create/bingmaps.mapapis.1.0.4) | | | |
| Visual Studio 계정 | 예 | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## 관리 

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| 자동화 | 예 | [예](https://portal.azure.com/#create/Microsoft.AutomationAccount.1.0.2-preview) | 예 | | |
| 키 자격 증명 모음 | 예 | 아니요 | 예 | [키 자격 증명 모음 REST](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| 스케줄러 | 예 | 아니요 | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | 예 | 아니요 | 예 | | |
| IoTHubs | 예 | [예](https://portal.azure.com/#create/Microsoft.IotHub) | | | |

## 리소스 관리자

| 기능 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| 권한 부여 | 예 | 해당 없음 | 해당 없음 | [관리 잠금](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[역할 기반 액세스 제어](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [리소스 잠금](resource-manager-template-lock.md)<br />[역할 할당](resource-manager-template-role.md) |
| 리소스 | 예 | 해당 없음 | 해당 없음 | [연결된 리소스](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [리소스 링크](resource-manager-template-links.md) |


## 지원되는 지역

리소스를 배포할 때는 일반적으로 리소스에 대한 지역을 지정해야 합니다. 리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 해당 리소스를 지원하는 일부 지역을 사용하지 못하게 하는 구독에 대한 제한 사항이 있을 수 있습니다. 이러한 제한은 본국에 대한 세금 문제와 관련되거나 구독 관리자가 특정 지역만 사용하도록 배치한 정책에 따른 결과일 수 있습니다.

모든 Azure 서비스에 대해 지원되는 모든 지역의 전체 목록은 [지역별 서비스](https://azure.microsoft.com/regions/#services)를 참조하세요. 그러나 이 목록에는 구독에서 지원하지 않는 지역이 포함될 수 있습니다. 다음 명령 중 하나를 실행하여 구독에서 지원하는 특정 리소스 종류에 대한 지역을 확인할 수 있습니다.

### REST API

구독 중인 특정 리소스 종류에 사용할 수 있는 지역을 검색하려면 [모든 리소스 공급자 나열](https://msdn.microsoft.com/library/azure/dn790524.aspx) 작업을 사용하세요.

### PowerShell

다음 예에서는 Azure PowerShell 1.0 미리 보기를 사용하여 웹 사이트에 대해 지원되는 지역을 가져오는 방법을 보여줍니다. 1.0 Preview 릴리스에 대한 자세한 내용은 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)를 참조하세요.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
다음과 유사하게 출력됩니다.

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

Azure PowerShell 0.9.8의 경우 다음 명령을 사용합니다.

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Azure CLI

다음 예에서는 각 리소스 유형에 대해 지원되는 모든 지역을 반환합니다.

    azure location list

또한 **jq**와 같은 도구를 사용하여 위치 결과를 필터링할 수도 있습니다. jq와 같은 도구에 대해 자세히 알아보려면 [Azure를 조작하는 데 유용한 도구](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure)를 참조하세요.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

반환하는 내용은 다음과 같습니다.

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## 지원되는 API 버전

서식 파일을 배포할 때 각 리소스를 만들기 위해 사용하는 API 버전을 지정해야 합니다. API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 REST API의 새 버전을 릴리스합니다. 따라서 서식 파일에 지정하는 API의 버전은 템플릿에 지정할 수 있는 속성에 영향을 줍니다. 일반적으로 새 템플릿을 만들 때 가장 최근의 API 버전을 선택하려고 합니다. 기존 템플릿의 경우 이전 API 버전을 계속 사용할지 아니면 새 기능을 이용하도록 최신 버전에 맞게 템플릿을 업데이트할지 여하를 결정할 수 있습니다.

### REST API

리소스 종류에 사용할 수 있는 API 버전을 검색하려면 [모든 리소스 공급자 나열](https://msdn.microsoft.com/library/azure/dn790524.aspx) 작업을 사용하세요.

### PowerShell

다음 예제에서는 Azure PowerShell 1.0 Preview를 사용하여 특정 리소스에 사용할 수 있는 API 버전을 가져오는 방법을 보여줍니다.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
다음과 유사하게 출력됩니다.
    
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

Azure PowerShell 0.9.8의 경우 다음을 사용합니다.

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

다음 명령을 사용하여 리소스 공급자에 대한 정보(사용할 수 있는 API 버전 포함)를 파일로 저장할 수 있습니다.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

파일을 열고 **apiVersions** 요소를 찾을 수 있습니다.

## 다음 단계

- 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- 리소스 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_1125_2015-->