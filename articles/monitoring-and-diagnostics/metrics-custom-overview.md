---
title: Azure Monitor의 사용자 지정 메트릭
description: Azure Monitor의 사용자 지정 메트릭 및 모델링하는 방법을 알아봅니다.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 1bdf1e1f5e58ecb0939d5876e0cef349e32de517
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344753"
---
# <a name="custom-metrics-in-azure-monitor"></a>Azure Monitor의 사용자 지정 메트릭

Azure에서 리소스 및 응용 프로그램을 배포하는 동안 성능 및 상태에 대한 정보를 얻기 위해 원격 분석 수집을 시작할 수 있습니다. Azure는 리소스를 배포할 때 사용할 수 있도록 몇 가지 메트릭을 기본적으로 제공합니다. 이를 표준 또는 플랫폼 메트릭이라고 합니다. 단, 이러한 메트릭은 본질적으로 제한됩니다. 더욱 심층적인 정보를 제공하기 위해 일부 사용자 지정 성능 지표 또는 비즈니스 관련 메트릭을 수집하고자 할 수 있습니다.
이러한 “사용자 지정” 메트릭은 응용 프로그램 원격 분석, Azure 리소스 또는 외부 모니터링 시스템을 통해서도 수집할 수 있으며 Azure Monitor로 직접 전송됩니다. Azure Monitor에 게시한 후에는 Azure에서 내보낸 표준 메트릭과 함께 Azure 리소스 및 응용 프로그램에 대한 사용자 지정 메트릭에서 찾아보기, 쿼리 및 경고를 수행할 수 있습니다.

## <a name="send-custom-metrics"></a>사용자 지정 메트릭 보내기
사용자 지정 메트릭은 다양한 방법을 통해 Azure Monitor로 보낼 수 있습니다.
- Application Insights SDK를 사용하여 응용 프로그램 계측 및 Azure Monitor에 사용자 지정 원격 분석 보내기 
- [Azure VM](metrics-store-custom-guestos-resource-manager-vm.md), [Virtual Machine Scale set](metrics-store-custom-guestos-resource-manager-vmss.md), [클래식 VM](metrics-store-custom-guestos-classic-vm.md) 또는 [클래식 클라우드 서비스](metrics-store-custom-guestos-classic-cloud-service.md)에 Windows 진단 확장 설치 및 Azure Monitor에 성능 카운터 보내기 
- Azure Linux VM에 [InfluxDB Telegraf 에이전트](metrics-store-custom-linux-telegraf.md)설치 및 Azure Monitor 출력 플러그 인을 사용하여 메트릭 보내기
- [Azure Monitor REST API에 직접](metrics-store-custom-rest-api.md) 사용자 지정 메트릭 보내기 https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics

Azure Monitor에 사용자 지정 메트릭을 보내면 보고되는 각 데이터 요소(또는 값)는 다음 정보를 포함해야 합니다.

### <a name="authentication"></a>인증
Azure Monitor에 사용자 지정 메트릭을 전송하려면 메트릭을 전송하는 엔터티는 요청의 “전달자” 헤더에 유효한 Azure Active Directory 토큰이 있어야 합니다. 유효한 전달자 토큰을 획득하기 위한 지원되는 몇 가지 방법이 있습니다.
1. [Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) - Azure 리소스 자체(예: VM)에 ID를 제공합니다. MSI는 리소스가 리소스 자체에 대한 메트릭을 내보내도록 허용하는 등의 특정 작업을 수행하는 권한을 리소스에 부여하도록 설계되었습니다. 다른 리소스에 대한 “메트릭 게시자 모니터링” 권한을 리소스(또는 해당 MSI)에 부여할 수 있습니다. 따라서 다른 리소스에 대한 메트릭을 내보내도록 MSI를 설정할 수 있습니다.
2. [AAD 서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) - 여기에 나온 시나리오는 Azure 리소스에 대한 메트릭을 내보내는 권한을 할당할 수 있는 AAD 응용 프로그램(서비스)입니다.
요청을 인증하기 위해 Azure Monitor가 AAD 공개 키를 사용하여 응용 프로그램 토큰의 유효성을 검사합니다. 기존 “메트릭 게시자 모니터링” 역할에는 이미 이 권한이 있으며 Azure Portal에서 사용할 수 있습니다. 서비스 주체는 사용자 지정 메트릭을 내보낼 리소스에 따라 필요한 범위(구독, 리소스 그룹 또는 특정 리소스)에서 “메트릭 게시자 모니터링” 역할을 부여받을 수 있습니다.

> [!NOTE]
> 사용자 지정 메트릭을 내보내기 위해 AAD 토큰을 요청하는 경우 토큰이 요청되는 대상/리소스가 https://monitoring.azure.com/인지 확인합니다(후행 ‘/’을 포함해야 함).

### <a name="subject"></a>제목
이 속성은 사용자 지정 메트릭이 보고되는 Azure 리소스 ID를 캡처합니다. 이 정보는 수행되는 API 호출의 URL에 인코딩됩니다. 각 API는 단일 Azure 리소스에 대한 메트릭 값만 전송할 수 있습니다.

> [!NOTE]
> 리소스 그룹 또는 구독의 리소스 ID에 대해 사용자 지정 메트릭을 내보낼 수 없습니다.
>
>

### <a name="region"></a>지역
이 속성은 메트릭을 내보내는 리소스가 배포된 Azure 지역을 캡처합니다. 메트릭은 리소스가 배포되는 지역과 동일한 Azure Monitor 지역 엔드포인트에 배포되어야 합니다. 예를 들어 미국 서부에 배포된 VM에 대한 사용자 지정 메트릭은 WestUS 지역별 Azure Monitor 엔드포인트에 전송되어야 합니다. 또한 지역 정보는 API 호출의 URL에 인코딩됩니다.

> [!NOTE]
> 공개 미리 보기 중 사용자 지정 메트릭은 Azure 지역의 하위 집합에서만 사용할 수 있습니다. 지원되는 지역 목록은 이 문서의 뒷부분에 설명되어 있습니다.
>
>

### <a name="timestamp"></a>타임 스탬프
Azure Monitor에 전송되는 각 데이터 요소는 타임스탬프를 사용하여 표시되어야 합니다. 이 타임스탬프는 메트릭 값이 측정/수집되는 날짜/시간을 캡처합니다. Azure Monitor는 과거 20분 및 미래 5분까지의 타임스탬프를 사용하여 메트릭 데이터를 수락합니다.

### <a name="namespace"></a>네임스페이스
네임스페이스는 유사한 메트릭을 함께 분류 또는 그룹화하는 방법입니다. 네임스페이스를 사용하면 다른 정보 또는 성과 지표를 수집할 수도 있는 메트릭의 그룹 사이를 격리할 수 있습니다. 예를 들어 추적 메모리가 앱을 프로파일링하는 메트릭을 사용하는 데 사용되는 *ContosoMemoryMetrics*라는 네임스페이스와 사용자의 응용 프로그램에서 사용자 트랜잭션에 대한 모든 메트릭을 추적하는 *ContosoAppTransaction*이라는 다른 네임스페이스가 있을 수 있습니다.

### <a name="name"></a>이름
보고되는 메트릭의 이름입니다. 일반적으로 측정 대상을 식별하는 데 충분한 설명하는 이름입니다. 예를 들어 제공된 VM에서 활용되는 메모리의 바이트 수를 측정하는 메트릭의 이름은 “Memory Bytes In Use(사용 중인 메모리 바이트)”와 같을 수 있습니다.

### <a name="dimension-keys"></a>차원 키
차원은 메트릭을 수집하는 메트릭에 관한 추가적인 특성을 설명하는 데 유용한 키/값 쌍입니다. 추가적인 특성은 더욱 심층적인 정보를 얻을 수 있도록 메트릭에 관한 자세한 정보를 수집합니다. 예를 들어, “Memory Bytes In Use(사용 중인 메모리 바이트)” 메트릭에는 VM의 각 프로세스가 사용 중인 메모리의 바이트 수를 캡처하는 “Process(프로세스)”라는 차원 키가 있을 수 있습니다. 이를 통해 얼마나 많은 메모리 관련 프로세스가 사용 중인지 확인하거나 메모리 사용량에 따른 상위 5개 프로세스를 확인하는 메트릭을 필터링할 수 있습니다.
각 사용자 지정 메트릭에는 최대 10개의 차원이 있을 수 있습니다.

### <a name="dimension-values"></a>차원 값
메트릭 데이터 요소를 보고할 때 보고되는 메트릭의 각 차원 키의 경우 해당하는 차원 값이 있습니다. 예를 들어 VM의 ContosoApp에서 사용하는 메모리를 보고하려는 경우:

* 메트릭 이름은 *Memory Bytes in Use(사용 중인 메모리 바이트)*
* 차원 키는 *프로세스*
* 차원 값은 *ContosoApp.exe*가 됩니다.

메트릭 값을 게시하는 경우 차원 키당 단일 차원 값만 지정할 수 있습니다. VM에서 여러 프로세스에 대한 동일한 메모리 사용률을 수집하는 경우 해당 타임스탬프에 대한 여러 메트릭 값을 보고할 수 있습니다. 각 메트릭 값은 프로세스 차원 키에 대한 다양한 차원 값을 지정하게 됩니다.

### <a name="metric-values"></a>메트릭 값
Azure Monitor는 1분 단위 간격으로 모든 메트릭을 저장합니다. 지정된 시간 동안 메트릭을 여러 번 샘플링하거나(예: CPU 사용률) 여러 불연속 이벤트(예: 로그인 트랜잭션 대기 시간)에 대해 측정해야 할 수도 있습니다. 내보내야 하는 원시 값의 수를 제한하고 Azure Monitor에 대한 요금을 지불하려면 다음과 같은 값을 로컬로 미리 집계하고 내보낼 수 있습니다.

* 최소: 일정 시간 동안 모든 샘플/측정에서 관찰된 최솟값
* 최대: 일정 시간 동안 모든 샘플/측정에서 관찰된 최댓값
* 합계: 일정 시간 동안 모든 샘플/측정에서 관찰된 모든 값의 합계
* 개수: 일정 시간 동안 수행된 샘플/측정 수

예를 들어 정해진 시간 동안 앱에 4개의 로그인 트랜잭션이 있었고 각각 측정된 결과 대기 시간이 다음과 같습니다.

|트랜잭션 1|트랜잭션 2|트랜잭션 3|트랜잭션 4|
|---|---|---|---|
|7ms|4ms|13ms|16ms|
|

Azure Monitor에 대한 결과 메트릭 게시는 다음과 같습니다.
* 최소: 4
* 최대: 16
* 합계: 40
* 개수: 4

응용 프로그램이 로컬로 미리 집계할 수 없고 각 불연속 샘플 또는 이벤트를 수집되는 즉시 내보내야 하는 경우 원시 측정 값을 내보낼 수 있습니다.
예를 들어, 앱에서 로그인 트랜잭션이 발생할 때마다 단일 측정만 사용하여 메트릭을 Azure Monitor에 게시할 수 있습니다. 따라서 12밀리초가 걸리는 로그인 트랜잭션의 경우 메트릭 게시는 다음과 같습니다.
* 최소: 12
* 최대: 12
* 합계: 12
* 개수: 1

이 프로세스를 사용하면 정해진 시간 동안 동일한 메트릭과 차원 조합에 대한 여러 값을 내보낼 수 있습니다. 그런 다음, Azure Monitor는 정해진 시간 동안 내보낸 모든 원시 값을 가져와서 함께 집계합니다.

### <a name="sample-custom-metric-publication"></a>샘플 사용자 지정 메트릭 게시
다음 예제에서는 Virtual Machine에 대한 “Memory Profile(메모리 프로필)”이란 메트릭 네임스페이스에서 “Memory Bytes in Use(사용 중인 메모리 바이트)”라고 하는 사용자 지정 메트릭을 만듭니다. 메트릭에는 “Process(프로세스)”라는 단일 차원이 있습니다. 지정된 타임스탬프의 경우 두 개의 다른 프로세스에 대한 메트릭 값을 내보냅니다.

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]
> Application Insights, Windows Azure 진단 확장 및 InfluxData Telegraf 에이전트는 올바른 지역별 엔드포인트에 대해 메트릭 값을 내보내고 각 내보내기에서 위의 속성을 모두 수행하도록 이미 구성되어 있습니다.
>
>

## <a name="custom-metric-definitions"></a>사용자 지정 메트릭 정의
내보내기 전에 Azure Monitor에서 사용자 지정 메트릭을 미리 정의할 필요는 없습니다. 게시된 각 메트릭 데이터 요소에는 네임스페이스, 이름 및 차원 정보가 포함되므로 사용자 지정 메트릭을 Azure Monitor로 처음 내보낼 때 메트릭 정의가 자동으로 만들어집니다. 그러면 메트릭 정의를 통해 메트릭을 내보낸 모든 리소스에서 이 메트릭 정의를 검색할 수 있습니다.

> [!NOTE]
> Azure Monitor는 아직 사용자 지정 메트릭에 대해 “단위” 정의를 지원하지 않습니다.

## <a name="using-custom-metrics"></a>사용자 지정 메트릭 사용
사용자 지정 메트릭이 Azure Monitor에 전송되고 나면 Azure Portal을 통해 찾아보거나, Azure Monitor REST API를 통해 쿼리하거나, 특정 조건이 충족될 때 알림을 받을 수 있도록 경고를 만들 수 있습니다.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Azure Portal을 통해 사용자 지정 메트릭 찾아보기
1.  [Azure Portal](https://portal.azure.com)로 이동
2.  모니터 블레이드 선택
3.  메트릭 클릭
4.  사용자 지정 메트릭을 내보낸 리소스 선택
5.  사용자 지정 메트릭에 대한 메트릭 네임스페이스 선택
6.  사용자 지정 메트릭 선택

## <a name="supported-regions"></a>지원되는 지역
공개 미리 보기 중 사용자 지정 메트릭을 게시할 수 있는 기능은 Azure 지역의 하위 집합에서만 사용할 수 있습니다. 즉, 지원되는 지역 중 하나에 있는 리소스에 대해서만 메트릭을 게시할 수 있습니다. 아래 테이블에는 사용자 지정 메트릭에 대해 지원되는 Azure 지역의 집합 및 게시해야 하는 해당 지역에 있는 리소스에 대한 해당 엔드포인트 메트릭이 나열되어 있습니다.

|Azure 지역|지역별 엔드포인트 접두사|
|---|---|
|미국 동부|https://eastus.monitoring.azure.com/|
|미국 중남부|https://southcentralus.monitoring.azure.com/|
|미국 중서부|https://westcentralus.monitoring.azure.com/|
|미국 서부 2|https://westus2.monitoring.azure.com/|
|동남아시아|https://southeastasia.monitoring.azure.com/|
|북유럽|https://northeurope.monitoring.azure.com/|
|서유럽|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>할당량 및 한도
Azure Monitor는 사용자 지정 메트릭에서 다음과 같은 사용 제한을 적용합니다.

|Category|제한|
|---|---|
|활성 시간 시리즈/구독/지역|50,000|
|메트릭당 차원 키|10|
|메트릭 네임스페이스, 메트릭 이름, 차원 키 및 차원 값의 문자열 길이|256자|
활성 시간 시리즈는 지난 12시간 동안 게시된 메트릭 값이 있는 메트릭, 차원 키, 차원 값의 고유한 조합으로 정의됩니다.

## <a name="next-steps"></a>다음 단계
다른 서비스에서 사용자 지정 메트릭 사용 
 - [Virtual Machine](metrics-store-custom-guestos-resource-manager-vm.md)
 - [Virtual Machine Scale set](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [Virtual Machine(클래식)](metrics-store-custom-guestos-classic-vm.md)
 - [Telegraf 에이전트를 사용하는 Linux Virtual Machine](metrics-store-custom-linux-telegraf.md)
 - [REST API](metrics-store-custom-rest-api.md)
 - [클라우드 서비스(클래식)](metrics-store-custom-guestos-classic-cloud-service.md)
 