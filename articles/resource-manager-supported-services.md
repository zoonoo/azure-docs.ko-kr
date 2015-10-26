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
   ms.date="10/13/2015"
   ms.author="tomfitz"/>

# 서비스 및 지역에 대한 Azure 리소스 관리자 지원

Azure 리소스 관리자는 응용 프로그램을 구성하는 서비스를 배포하고 관리하는 새로운 방법을 제공합니다. 모두는 아니지만, 대부분의 서비스는 리소스 관리자를 지원하고 일부 서비스는 리소스 관리자를 부분적으로만 지원합니다. Microsoft는 향후 솔루션을 위해 중요한 모든 서비스에 리소스 관리자를 사용하도록 할 예정이지만 일관적으로 지원될 때까지 각 서비스의 현재 상태를 알아야 합니다. 이 항목에서는 Azure 리소스 관리자에 대해 지원되는 리소스 공급자 목록을 제공합니다.

리소스를 배포할 때는 이러한 리소스를 지원하는 지역도 알아야 합니다. [지원되는 지역](#supported-regions) 섹션에서는 구독 및 리소스에 대해 작동할 지역을 확인하는 방법을 보여줍니다.

다음 표에서는 리소스 관리자를 통해 배포 및 관리를 지원 또는 지원하지 않는 서비스 목록을 보여줍니다. **리소스 이동**이라는 열은 새 리소스 그룹 및 새 구독 모두에 이 유형의 리소스를 이동할 수 있는지 여부를 나타냅니다. **Preview 포털**이라는 열은 Preview 포털을 통해 서비스를 만들 수 있는지 여부를 나타냅니다.


## 계산

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| 가상 컴퓨터 | 예 | 예 | 아니요 | [VM 만들기](https://msdn.microsoft.com/library/azure/mt163591.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) |
| 배치 | 예 | 아니요 | | [Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) | |
| Dynamics Lifecycle Services | 예 | 아니요 | | | |
| 가상 컴퓨터(클래식) | 제한됨 | 아니요 | 부분적(아래 참조) | - | - | | RemoteApp | 아니요 | - | - | - | - | | 서비스 패브릭 | 아니요 | - | - | - | - |

가상 컴퓨터(클래식)는 리소스 관리자 배포 모델 대신, 클래식 배포 모델을 통해 배포된 리소스를 참조합니다. 일반적으로 이러한 리소스는 리소스 관리자 작업을 지원하지 않지만 일부 작업은 가능합니다. 이러한 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](resource-manager-deployment-model.md)를 참조하세요.

가상 컴퓨터(클래식) 리소스를 새 구독이 아닌, 새 리소스 그룹으로 이동할 수 있습니다.

## 웹 및 모바일

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| API 관리| 예 | 아니요 | 예 | [API 만들기](https://msdn.microsoft.com/library/azure/dn781423.aspx#CreateAPI) | |
| API 앱 | 예 | 예 | | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) |
| 웹앱 | 예 | 예 | 예, 제한 있음(아래 참조) | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) |
| 알림 허브 | 예 | 예 | | [알림 허브 만들기](https://msdn.microsoft.com/library/azure/dn223269.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) |
| 논리 앱 | 예 | 예 | | | |
| 모바일 고객 관리 | 예 | 아니요 | 예 | | |

웹앱으로 작업할 경우에는 앱 서비스 계획만 이동할 수 없습니다. 웹앱을 이동하려면 옵션은 다음과 같습니다.

- 대상 리소스 그룹에 아직 Microsoft.Web 리소스가 없는 경우 하나의 리소스 그룹에 있는 모든 리소스를 다른 리소스 그룹으로 이동합니다.
- 웹앱을 다른 리소스 그룹으로 이동하지만 앱 서비스는 원래 리소스 그룹에 유지합니다.


## 데이터 및 저장소

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB | 예 | 예 | 예 | [DocumentDB REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) | |
| 저장소 | 예 | 예 | | [저장소 만들기](https://msdn.microsoft.com/library/azure/mt163564.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Storage.json) |
| Redis 캐시 | 예 | 예 | 예 | | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Cache.json) |
| SQL 데이터베이스 | 예 | 예 | 예 | [데이터베이스 만들기](https://msdn.microsoft.com/library/azure/mt163685.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) |
| 검색 | 예 | 예 | 예 | [검색 REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) | |
| SQL 데이터 웨어하우스 | 예 | 예 | | | |
| StorSimple | 아니요 | 아니요 | - | - | - | | 백업 | 아니요 | 아니요 | - | - | - | | 사이트 복구 | 아니요 | 아니요 | - | - | - | | 관리된 캐시 | 아니요 | 아니요 | - | - | - | | 데이터 카탈로그 | 아니요 | 아니요 | - | - | - |

## 분석

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| 이벤트 허브 | 예 | 아니요 | | [이벤트 허브 만들기](https://msdn.microsoft.com/library/azure/dn790676.aspx) | |
| 스트림 분석 | 예 | 예 | | | |
| HDInsights | 예 | 예 | | | |
| 데이터 팩터리 | 예 | 예 | 예 | [데이터 팩터리 만들기](https://msdn.microsoft.com/library/azure/dn906717.aspx) | |
| 기계 학습 | 아니요 | 아니요 | - | - | - |

## 네트워킹

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| 응용 프로그램 게이트웨이 | 예 | | | | |
| DNS | 예 | | | [DNS 영역 만들기](https://msdn.microsoft.com/library/azure/mt130622.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 부하 분산 장치 | 예 | | | [부하 분산 장치 만들기](https://msdn.microsoft.com/library/azure/mt163574.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 가상 네트워크 | 예 | 예 | 아니요 | [가상 네트워크 만들기](https://msdn.microsoft.com/library/azure/mt163661.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) |
| 트래픽 관리자 | 예 | 아니요 | | [트래픽 관리자 프로필 만들기](https://msdn.microsoft.com/library/azure/mt163581.aspx) | |
| Express 경로 | 아니요 | 아니요 | - | - | - |

## 미디어 및 CDN

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------- | -------------- | -------- | ------ |
| 미디어 서비스 | 아니요 | 아니요 | | | |
| CDN | 아니요 | 아니요 | | | |

## 하이브리드 통합

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| BizTalk 서비스 | 예 | 아니요 | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) |
| 서비스 버스 | 예 | 아니요 | | [서비스 버스 REST](https://msdn.microsoft.com/library/azure/hh780717.aspx) | |

## ID 및 액세스 관리 

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory | 아니요 | 아니요 | - | - | - | | Azure Active Directory B2C | 아니요 | 아니요 | - | - | - | | Multi-Factor Authentication | 아니요 | 아니요 | - | - | - |

## 개발자 서비스 

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights | 예 | 예 | | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) |
| Bing 지도 | 예 | 예 | | | |
| Visual Studio 계정 | 예 | | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) |

## 관리 

| 부여 | 리소스 관리자 사용 | Preview 포털 | 리소스 이동 | REST API | 스키마 |
| ------- | ------- | --------- | -------------- | -------- | ------ |
| 자동화 | 예 | 예 | | | |
| 키 자격 증명 모음 | 예 | 아니요 | 예 | [키 자격 증명 모음 REST](https://msdn.microsoft.com/library/azure/dn903609.aspx) | |
| 스케줄러 | 예 | 아니요 | | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) |
| Operational Insights | 예 | 아니요 | 예 | | |
| IoTHubs | 예 | 예 | | | |


## 지원되는 지역

리소스를 배포할 때는 일반적으로 리소스에 대한 지역을 지정해야 합니다. 리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 해당 리소스를 지원하는 일부 지역을 사용하지 못하게 하는 구독에 대한 제한 사항이 있을 수 있습니다. 이러한 제한은 본국에 대한 세금 문제와 관련되거나 구독 관리자가 특정 지역만 사용하도록 배치한 정책에 따른 결과일 수 있습니다.

리소스를 배포하기 전에 다음 명령 중 하나를 실행하여 리소스 유형에 대해 지원되는 지역을 확인하세요.

### REST API

특정 리소스 유형에 사용할 수 있는 지역을 검색하는 최적의 옵션은 [모든 리소스 공급자를 나열](https://msdn.microsoft.com/library/azure/dn790524.aspx)하는 작업입니다. 이 작업은 사용자의 구독 및 리소스 유형에 사용할 수 있는 지역만 반환합니다.

### PowerShell

다음 예에서는 Azure PowerShell 1.0 미리 보기를 사용하여 웹 사이트에 대해 지원되는 지역을 가져오는 방법을 보여줍니다. 1.0 미리 보기 릴리스에 대한 자세한 내용은 [Azure PowerShell 1.0 미리 보기](https://azure.microsoft.com/blog/azps-1-0-pre/)를 참조하세요.

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

<!---HONumber=Oct15_HO3-->