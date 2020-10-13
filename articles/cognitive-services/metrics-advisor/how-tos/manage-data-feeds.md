---
title: 메트릭 관리자에서 데이터 피드 관리
titleSuffix: Azure Cognitive Services
description: 메트릭 Advisor에 추가한 데이터 피드를 관리 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: aahi
ms.openlocfilehash: 22a1be7231b730c83ca97b0f128e43258ed24533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937810"
---
# <a name="how-to-manage-your-data-feeds"></a>방법: 데이터 피드 관리

메트릭 관리자에서 등록 데이터 피드를 관리 하는 방법에 대해 알아봅니다. 이 문서에서는 메트릭 모니터에서 데이터 피드를 관리 하는 과정을 안내 합니다.

## <a name="edit-a-data-feed"></a>데이터 피드 편집

> [!NOTE]
> 데이터 피드를 만든 후에는 다음 세부 정보를 변경할 수 없습니다. 
> * 데이터 피드 ID
> * 만든 시간
> * 차원
> * 원본 유형
> * 세분성

데이터 피드의 관리자만이 파일을 변경할 수 있습니다. 

데이터 피드를 일시 중지 하거나 다시 활성화 하려면:

1. 데이터 피드 목록 페이지에서 데이터 피드에 대해 수행 하려는 작업을 클릭 합니다.

2. 데이터 피드 세부 정보 페이지에서 **상태** 전환 단추를 클릭 합니다.

데이터 피드를 삭제 하려면: 

1. 데이터 피드 목록 페이지에서 데이터 피드에서 **삭제** 를 클릭 합니다.

2. 데이터 피드 세부 정보 페이지에서 **삭제**를 클릭 합니다.

시작 시간을 변경 하는 경우 스키마를 다시 확인 해야 합니다. **매개 변수 편집**을 사용 하 여 변경할 수 있습니다.

##  <a name="backfill-your-data-feed"></a>데이터 피드 백필

**백필** 단추를 선택 하 여 타임 스탬프에 대 한 즉각적인 수집을 트리거하고 실패 한 수집을 수정 하거나 기존 데이터를 재정의 합니다.
- 시작 시간은 포함입니다.
- 종료 시간은 배타적입니다.
- 변칙 검색은 선택한 범위 에서만 다시 트리거됩니다.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="백필 데이터 피드":::

## <a name="manage-permission-of-a-data-feed"></a>데이터 피드의 사용 권한 관리

작업 영역 액세스는 인증을 위해 Azure Active Directory를 사용 하는 메트릭 관리자 리소스에 의해 제어 됩니다. 사용 권한 제어의 다른 계층은 메트릭 데이터에 적용 됩니다.

메트릭 관리자를 사용 하면 서로 다른 데이터 피드의 여러 사용자 그룹에 권한을 부여할 수 있습니다. 역할에는 다음과 같은 두 가지 유형이 있습니다. 

- 관리자: 수정 및 삭제를 포함 하 여 데이터 피드를 관리할 수 있는 모든 권한을 보유 합니다.
- 뷰어: 데이터 피드의 읽기 전용 보기에 대 한 액세스 권한이 있는 사용자입니다.
 

## <a name="advanced-settings"></a>고급 설정

새 데이터 피드를 만들 때 여러 선택적인 고급 설정이 있습니다. 이러한 설정은 데이터 피드 세부 정보 페이지에서 수정할 수 있습니다.

### <a name="ingestion-options"></a>수집 옵션

* 수집 **시간 오프셋**: 기본적으로 데이터는 지정 된 세분성에 따라 수집 됩니다. 예를 들어 *일일* 타임 스탬프를 포함 하는 메트릭은 타임 스탬프 수집 1 일 후에 수행 됩니다. 오프셋을 사용 하 여 *양수* 를 사용 하 여 수집 시간을 지연 하거나 *음수* 를 사용 하 여 값을 앞으로 이동할 수 있습니다.

* **최대 동시성**: 데이터 소스에서 제한 된 동시성을 지 원하는 경우이 매개 변수를 설정 합니다. 그렇지 않으면 기본 설정으로 둡니다.

* **다음 이후에 다시 시도 중지**: 데이터 수집에 실패 한 경우에는 일정 기간 내에 자동으로 다시 시도 됩니다. 기간의 시작은 첫 번째 데이터 수집이 발생 한 시간입니다. 기간의 길이는 세분성에 따라 정의 됩니다. 기본값 (-1)을 유지 하는 경우 값은 아래와 같이 세분성에 따라 결정 됩니다.
    
    | 세분성       | 후 다시 시도 중지           |
    | :------------ | :--------------- |
    | 매일, 사용자 지정 (>= 1 일), 매주, 매월, 매년     | 7 일          |
    | 매시간, 사용자 지정 (< 1 일)       | 72 시간 |

* **최소 다시 시도 간격**: 원본에서 데이터를 다시 가져오는 경우 최소 간격을 지정할 수 있습니다. 기본값 (-1)을 벗어나면 재시도 간격은 아래와 같이 세분성에 따라 결정 됩니다.
    
    | 세분성       | 최소 재시도 간격           |
    | :------------ | :--------------- |
    | 매일, 사용자 지정 (>= 1 일), 매주, 매월     | 30분          |
    | 매시간, 사용자 지정 (< 1 일)      | 10분 |
    | 매년 | 1일          |
 
### <a name="fill-gap-when-detecting"></a>검색 시 채우기 간격: 

> [!NOTE]
> 이 설정은 데이터 원본에 영향을 주지 않으며 포털에 표시 되는 데이터 차트에 영향을 주지 않습니다. 자동 채우기는 변칙 검색 중에만 발생 합니다.

일부 시계열은 연속 되지 않습니다. 누락 된 데이터 요소가 있는 경우 메트릭 관리자는 보다 정확한 정확성을 위해 지정 된 값을 사용 하 여 변칙 검색 전에 값을 채웁니다.
옵션은 다음과 같습니다. 

* 이전 실제 데이터 요소의 값을 사용 합니다. 이는 기본적으로 사용 됩니다.
* 특정 값을 사용 합니다.

### <a name="action-link-template"></a>작업 링크 템플릿: 

작업 링크 템플릿은 자리 표시자,,, 및로 구성 된 조치 가능한 HTTP url을 미리 정의할 때 사용 됩니다 `%datafeed` `%metric` `%timestamp` `%detect_config` `%tagset` . 템플릿을 사용 하 여 변칙 또는 인시던트에서 특정 URL로 리디렉션하여 드릴 다운할 수 있습니다.

:::image type="content" source="../media/action-link-template.png" alt-text="백필 데이터 피드" lightbox="../media/action-link-template.png":::

작업 링크를 입력 한 후 인시던트 목록의 동작 옵션 및 인시던트 트리의 오른쪽 클릭 메뉴에서 **작업으로 이동 링크** 를 클릭 합니다. 작업 링크 템플릿의 자리 표시자를 변칙 또는 인시던트에 해당 하는 값으로 바꿉니다.

| 자리표시자 | 예 | 설명 |
| ---------- | -------- | ------- |
| `%datafeed` | - | 데이터 피드 ID |
| `%metric` | - | 메트릭 ID |
| `%detect_config` | - | 구성 ID 검색 |
| `%timestamp` | - | 영구 인시던트의 변칙 또는 종료 시간의 타임 스탬프 |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | 인시던트의 비정상 또는 상위 변칙의 차원 값입니다.   <br> 는 `filterVal` 대괄호 내에서 일치 하는 값을 필터링 하는 데 사용 됩니다.   |

예와

* 작업 링크 `https://action-link/metric/%metric?detectConfigId=%detect_config` 템플릿이 인 경우
  * 작업 링크는 `https://action-link/metric/1234?detectConfigId=2345` 메트릭 아래의 비정상 또는 인시던트로 이동 하 `1234` 고 구성을 검색 `2345` 합니다.

* 작업 링크 `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` 템플릿이 인 경우 
    * 작업 링크는 `https://action-link?Dim1=Val1&Dim2=Val2` 변칙이 인 경우입니다 `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * 작업 링크는 `https://action-link?Dim2=Val2` 변칙 인 경우입니다 `{ "Dim1": "", "Dim2": "Val2" } ` `[Dim1=***&]` .이는 차원 값의 빈 문자열에 대해를 건너뜀 때문입니다. 

* 작업 링크 `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` 템플릿이 인 경우 
    * 작업 링크는 `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` 변칙이 인 경우입니다. `{ "Dim1": "Val1", "Dim2": "Val2" }` 
    * 작업 링크는 `https://action-link?filter=Name/Dim2 eq 'Val2'` `{ "Dim1": "", "Dim2": "Val2" }` `[Name/Dim1 eq '***' and ]` 차원 값의 빈 문자열에 대해를 건너뜀 때문에 변칙이 인 경우입니다. 
   
### <a name="data-feed-not-available-alert-settings"></a>"데이터 피드를 사용할 수 없음" 경고 설정

데이터 피드는 데이터 피드가 수집을 시작한 시간에서 지정 된 유예 기간 내에 수집 데이터를 사용할 수 없는 것으로 간주 됩니다. 이 경우 경고가 트리거됩니다.

경고를 구성 하려면 먼저 [후크를 만들어야](alerts.md#create-a-hook) 합니다. 경고는 구성 된 후크를 통해 전송 됩니다.

* **유예 기간**: 유예 기간 설정은 데이터 요소가 수집 경우 경고를 보내는 시기를 결정 하는 데 사용 됩니다. 참조 지점은 첫 번째 수집 시간입니다. 수집이 실패 하면 메트릭 Advisor는 세분성으로 지정 된 정기적인 간격으로 계속 시도 합니다. 유예 기간이 지난 후에도 계속 실패 하는 경우 경고가 전송 됩니다.

* **자동 다시 알림**:이 옵션을 0으로 설정 하면 *사용할 수 없는* 타임 스탬프 마다 경고가 트리거됩니다. 0이 아닌 설정을 지정 하면 지정 된 설정에 따라 첫 번째 타임 스탬프 ( *사용할 수 없음* ) 이후의 연속 타임 스탬프가 트리거되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [메트릭을 구성하고 구성 감지 미세 조정](configure-metrics.md)
- [피드백을 사용하여 변칙 검색 조정](anomaly-feedback.md)
- [인시던트를 진단](diagnose-incident.md)합니다.
