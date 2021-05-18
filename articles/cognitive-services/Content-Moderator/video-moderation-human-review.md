---
title: 검토 도구를 사용한 비디오 조정 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 기계 지원 비디오 조정 및 검토 도구를 사용하여 부적절한 콘텐츠 조정
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 05ece4d8ea24fb9da106ec3daa2cc1a63706dab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96853501"
---
# <a name="video-moderation-with-the-review-tool"></a>검토 도구를 사용한 비디오 조정

비즈니스를 위한 최상의 결과를 얻도록 Content Moderator의 기계 지원 [비디오 조정](video-moderation-api.md) 및 [검토 도구](Review-Tool-User-Guide/human-in-the-loop.md)를 사용하여 성인(노골적) 및 외설(선정적) 콘텐츠용 비디오 및 대본을 조정합니다.

## <a name="view-videos-under-review"></a>검토 중인 비디오 보기

대시보드에서 비디오 콘텐츠 형식 내의 검토 큐를 선택합니다. 그러면 검토가 시작되고 비디오 콘텐츠 조정 페이지가 열립니다.

> [!div class="mx-imgBorder"]
> ![Content Moderator(미리 보기)에서 슬라이더가 강조 표시되고 4개의 검토로 설정됩니다. 모두 흐리기 스위치와 흑백 스위치가 강조 표시되고 두 스위치가 모두 설정됩니다.](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>개수 검토

오른쪽 위의 슬라이더를 사용하여 페이지에 표시할 검토의 개수를 설정합니다.

### <a name="view-type"></a>보기 유형

다른 콘텐츠 항목을 타일로 보거나 상세 보기로 볼 수 있습니다. **세부 정보** 보기에서는 선택한 비디오에 대한 키 프레임 및 기타 정보를 볼 수 있습니다. 

> [!NOTE]
> 비디오 조정 서비스는 정기적으로 프레임을 출력하는 대신 잠재적으로 완전한(양호한) 프레임만 식별하여 출력합니다. 이 기능을 사용하면 프레임 수준의 성인물 및 음란물 분석을 위한 효율적인 프레임 생성이 가능합니다.

**타일식** 보기에서는 각 비디오가 단일 타일로 표시됩니다. 비디오 프레임 위의 확장 단추를 선택하여 해당 비디오를 확대하고 다른 비디오를 숨깁니다.

### <a name="content-obscuring-effects"></a>콘텐츠-가리기 효과

**모두 흐리기** 및 **흑백** 토글을 사용하여 이러한 콘텐츠-가리기 효과를 설정합니다. 이러한 효과는 기본적으로 켜져 있습니다. **타일식** 보기에서 각 비디오에 대해 효과를 개별적으로 전환할 수 있습니다.

## <a name="check-video-details"></a>비디오 세부 정보 확인

**세부 정보** 보기에서는 오른쪽 창에 비디오에 대한 세부 정보를 제공하는 여러 탭이 표시됩니다.

* **메모** 탭을 선택하여 비디오에 사용자 지정 메모를 추가합니다.
* **대본** 탭을 선택하여 비디오 대본을 확인합니다&mdash;서비스에서 비디오에 있는 모든 음성을 대본으로 자동 추출합니다. 텍스트의 섹션을 선택하면 비디오 플레이어는 비디오의 해당 부분으로 이동합니다.
* **메타-데이터** 탭을 선택하여 비디오 파일의 메타데이터를 봅니다.
* **기록** 탭을 선택하여 검토 기록(예: 생성된 시기 및 수정된 방법)을 확인합니다.

> [!div class="mx-imgBorder"]
> ![오른쪽 창이 강조 표시되고 메모 탭이 선택됩니다. 메모 추가 레이블이 지정된 테스트 영역이 있습니다.](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>조정 태그 적용

비디오 검토의 주요 작업은 비디오 또는 비디오의 일부에 조정 태그를 적용하거나 제거하는 것입니다.

### <a name="bulk-tagging"></a>대량 태그 지정

**대량 태그** 도구 모음을 사용하면 선택한 여러 비디오에 대해 태그를 한 번에 추가할 수 있습니다. 하나 이상의 비디오를 선택한 다음 적용하려는 태그를 선택하고 **제출** 을 클릭합니다. 

> [!div class="mx-imgBorder"]
> ![대량 태그 창에 + 단추가 강조 표시됩니다.](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>키 프레임 태그 지정

특정 키 프레임에 조정 태그를 추가할 수도 있습니다. 키 프레임 타일 창에서 프레임을 선택한 다음 **키 프레임 태그 +** 를 선택하여 원하는 태그를 적용합니다.

> [!NOTE]
> 서비스에서 키 프레임을 추출할 수 없는 경우 키 프레임 타일 창에 **사용 가능한 프레임이 없음** 이 표시되고 키 프레임 선택 옵션이 회색으로 표시됩니다. 이 경우 **비디오 태그 +** 단추를 사용하여 비디오 전체에 대해서만 태그를 적용할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![타일 창, 비디오 플레이어, 키 프레임 태그 창, 비디오 태그 창이 모두 표시됩니다. 키 프레임 태그 + 및 비디오 태그 + 단추가 강조 표시됩니다.](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>검토를 보류 중 상태로 전환

비디오 창 맨 아래에 있는 **보류** 단추를 사용하면 검토를 보류 중 상태로 전환하여 나중에 해당 검토를 검색하고 검토를 완료할 수 있습니다. 현재 가용하지 않은 다른 팀 구성원 또는 관리자의 참조를 필요로 하는 검토에 대해 이 작업을 수행할 수 있습니다. 

화면 맨 위의 **보류** 단추를 클릭하여 보류 중인 비디오를 볼 수 있습니다. 보류 창이 오른쪽에 나타납니다. 여기에서 보류 중인 여러 검토를 선택하여 큐로 다시 릴리스하거나 만료 시간을 설정할 수 있습니다. 미리 구성된 시간이 지나면 보류 중인 검토가 큐로 다시 릴리스됩니다. **저장** 을 선택하면 현재 선택된 만료 시간에서 카운트다운을 시작합니다.

> [!div class="mx-imgBorder"]
> ![비디오 창에서 보류 단추가 강조 표시됩니다. 창 맨 아래에 보류 시간 콤보 상자가 릴리스 및 저장 단추와 함께 강조 표시됩니다.](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>검토 제출

태그를 적용한 후 비디오 창 맨 아래에 있는 **제출** 단추를 선택합니다. 여러 개의 비디오에 태그가 지정된 경우 단일 검토 아래에서 제출하거나 별도의 검토로 제출할 수 있습니다.

## <a name="limbo-state"></a>Limbo 상태

검토를 제출한 후 비디오는 **Limbo** 상태로 전환되어 화면 맨 위에 있는 **Limbo** 단추를 선택하여 볼 수 있습니다. 비디오는 다시 검토되거나 수동으로 제출될 때까지 또는 미리 구성된 시간(맨 아래 메뉴에서 변경할 수 있음) 동안 Limbo 상태로 유지됩니다.

비디오는 Limbo 상태가 만료되고 나면 검토가 완료된 것으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [비디오 조정 빠른 시작](video-moderation-api.md)을 시작합니다.
- 조정된 출력에서 사용자 검토자의 [비디오 검토](video-reviews-quickstart-dotnet.md)를 생성하는 방법을 알아봅니다.
- [비디오 대본 검토](video-transcript-reviews-quickstart-dotnet.md)를 비디오 검토에 추가합니다.
