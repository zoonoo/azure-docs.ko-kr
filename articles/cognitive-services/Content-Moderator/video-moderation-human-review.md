---
title: 검토 도구를 사용한 비디오 조정-Content Moderator
titleSuffix: Azure Cognitive Services
description: 기계 지원 비디오 조정 및 검토 도구를 사용 하 여 중간에 부적절 한 콘텐츠 사용
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 392cc06c6e0bce7ec2304da61033fc508d940bbb
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93143768"
---
# <a name="video-moderation-with-the-review-tool"></a>검토 도구를 사용한 비디오 조정

Content Moderator의 기계 지원 [비디오](video-moderation-api.md) 조정 및 [검토 도구](Review-Tool-User-Guide/human-in-the-loop.md) 를 사용 하 여 성인 (명시적) 및 추천 (외설) 콘텐츠에 대 한 중간 비디오 및 성적 증명서를 통해 비즈니스에 대 한 최상의 결과를 얻을 수 있습니다.

## <a name="view-videos-under-review"></a>검토 중인 비디오 보기

대시보드의 비디오 콘텐츠 형식 내에서 검토 큐를 선택 합니다. 그러면 검토가 시작 되 고 비디오 콘텐츠 중재 페이지가 열립니다.

> [!div class="mx-imgBorder"]
> ![검토 도구의 비디오 중재 자세히 보기](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>개수 검토

오른쪽 위의 슬라이더를 사용 하 여 페이지에 표시할 검토의 수를 설정 합니다.

### <a name="view-type"></a>보기 유형

다른 콘텐츠 항목을 타일로 보거나 자세히 볼 수 있습니다. **세부** 정보 보기에서는 선택한 비디오에 대 한 키 프레임 및 기타 정보를 볼 수 있습니다. 

> [!NOTE]
> 비디오 조정 서비스는 정기적으로 프레임을 출력하는 대신 잠재적으로 완전한(양호한) 프레임만 식별하여 출력합니다. 이 기능을 사용 하면 프레임 수준 성인용 및 외설 분석을 위한 효율적인 프레임 생성이 가능 합니다.

**바둑판식** 으로 표시 된 보기에는 각 비디오가 단일 타일로 표시 됩니다. 비디오 프레임 위의 확장 단추를 선택 하 여 비디오를 확대 하 고 다른 비디오를 숨깁니다.

### <a name="content-obscuring-effects"></a>콘텐츠-효과

**모두 흐리게** 및 **검은색 및 흰색** 토글을 사용 하 여 이러한 콘텐츠를 표시 하는 효과를 설정 합니다. 기본적으로 설정 되어 있습니다. **바둑판식** 보기에서 각 비디오에 대해 개별적으로 효과를 전환할 수 있습니다.

## <a name="check-video-details"></a>비디오 세부 정보 확인

**세부** 정보 보기의 오른쪽 창에는 비디오에 대 한 세부 정보를 제공 하는 여러 탭이 표시 됩니다.

* **노트** 탭을 선택 하 여 비디오에 사용자 지정 노트를 추가 합니다.
* 기록 탭 **을 선택 하 여 비디오** &mdash; 에 대 한 음성의 기록을 자동으로 추출 하는 비디오 기록을 확인 합니다. 텍스트의 섹션을 선택 하면 비디오 플레이어는 비디오의 해당 부분으로 이동 합니다.
* **메타 데이터** 탭을 선택 하 여 비디오 파일 메타 데이터를 봅니다.
* **기록** 탭을 선택 하 여 검토 기록 (예: 생성 된 시기 및 수정 된 방법)을 확인 합니다.

> [!div class="mx-imgBorder"]
> ![비디오 조정 대량 태그 단추](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>중재 태그 적용

비디오 검토의 주요 작업은 비디오 또는 비디오 파트에서 중재 태그를 적용 하거나 제거 하는 것입니다.

### <a name="bulk-tagging"></a>대량 태그 지정

**대량 태그** 도구 모음을 사용 하면 선택한 여러 비디오에 태그를 한 번에 추가할 수 있습니다. 하나 이상의 비디오를 선택한 다음 적용 하려는 태그를 선택 하 고 **제출** 을 클릭 합니다. 

> [!div class="mx-imgBorder"]
> ![비디오 조정 대량 태그 단추](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>키 프레임 태그 지정

특정 키 프레임에 중재 태그를 추가할 수도 있습니다. 키 프레임 타일 창에서 프레임을 선택 하 고 키 프레임 **태그 +** 를 선택 하 여 원하는 태그를 적용 합니다.

> [!NOTE]
> 서비스에서 키 프레임을 추출할 수 없으면 키 프레임 타일 창에 **사용할 수 있는 프레임이 표시 되지** 않고 키 프레임을 선택 하는 옵션이 회색으로 표시 됩니다. 이 경우 비디오 **태그 +** 단추를 사용 하 여 비디오에만 태그를 적용할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![검토 도구의 비디오 중재 자세히 보기](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>보류 중인 검토 배치

비디오 창 맨 아래에 있는 **보류** 단추를 사용 하면 보류 중에 검토를 수행 하 여 나중에 검색 하 고 완료할 수 있습니다. 현재 사용할 수 없는 다른 팀 구성원 또는 관리자의 참조를 요구 하는 검토에 대해이 작업을 수행할 수 있습니다. 

화면 위쪽의 **보류** 단추를 클릭 하 여 대기 중인 비디오를 볼 수 있습니다. 보류 창이 오른쪽에 나타납니다. 여기에서 보류 중인 여러 리뷰를 선택 하 고 큐로 다시 릴리스 하거나 만료 시간을 설정할 수 있습니다. 미리 구성 된 시간 후에 보류 중인 검토가 큐로 다시 릴리스됩니다. **저장** 을 선택 하 여 현재 선택 된 만료 시간에서 계산을 시작 합니다.

> [!div class="mx-imgBorder"]
> ![검토 도구의 비디오 중재 자세히 보기](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>검토 제출

태그를 적용 한 후 비디오 창 맨 아래에 있는 **제출** 단추를 선택 합니다. 여러 비디오에 태그가 지정 된 경우 단일 검토 또는 별도의 검토를 통해 제출할 수 있습니다.

## <a name="limbo-state"></a>Limbo 상태

검토를 제출한 후 비디오는 **Limbo** 상태로 이동 되며 화면 위쪽에 있는 **Limbo** 단추를 선택 하 여 볼 수 있습니다. 비디오는 미리 구성 된 시간 (아래쪽의 메뉴에서 변경할 수 있음)에 대해 Limbo 상태를 유지 하거나 다시 검토 하거나 수동으로 전송할 때까지 유지 됩니다.

비디오가 limbo에서 만료 되 면 해당 검토가 완료로 표시 됩니다.

## <a name="next-steps"></a>다음 단계

- [비디오 조정 빠른 시작](video-moderation-api.md)을 시작합니다.
- 조정된 출력에서 사용자 검토자의 [비디오 검토](video-reviews-quickstart-dotnet.md)를 생성하는 방법을 알아봅니다.
- [비디오 대본 검토](video-transcript-reviews-quickstart-dotnet.md)를 비디오 검토에 추가합니다.
- [완전한 비디오 조정 솔루션](video-transcript-moderation-review-tutorial-dotnet.md)을 개발하는 방법에 대한 자세한 자습서를 살펴봅니다.