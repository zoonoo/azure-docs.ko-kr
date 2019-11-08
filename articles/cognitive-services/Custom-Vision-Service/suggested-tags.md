---
title: 스마트 Labeler를 사용 하 여 더 빠르게 이미지 레이블
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 스마트 Labeler를 사용 하 여 이미지에 대 한 제안 된 태그를 생성 하는 방법을 알아봅니다. 이렇게 하면 Custom Vision 모델을 학습할 때 많은 수의 이미지에 더 빠르게 레이블을 지정할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647763"
---
# <a name="label-images-faster-with-smart-labeler"></a>스마트 Labeler를 사용 하 여 더 빠르게 이미지 레이블

이 가이드에서는 스마트 Labeler를 사용 하 여 이미지에 대 한 제안 된 태그를 생성 하는 방법을 알아봅니다. 이렇게 하면 Custom Vision 모델을 학습할 때 많은 수의 이미지에 더 빠르게 레이블을 지정할 수 있습니다.

Custom Vision 모델에 대 한 이미지에 태그를 만들면 서비스는 모델의 최신 학습 된 반복을 사용 하 여 태그가 없는 이미지의 레이블을 예측 합니다. 그런 다음 선택한 신뢰 임계값 및 예측 불확실성을 기준으로 이러한 예측을 제안 된 태그로 표시 합니다. 그런 다음 제안을 확인 하거나 변경 하 여 교육용 이미지에 수동으로 태그를 지정 하는 프로세스를 빠르게 수행할 수 있습니다.

## <a name="when-to-use-smart-labeler"></a>스마트 Labeler를 사용 하는 경우

다음 제한 사항을 고려 하세요.

* 콘텐츠가 이미 한 번 학습 된 이미지에 대해 제안 된 태그만 요청 해야 합니다. 학습 하기 시작 하 고 있는 새 태그를 제안 하지 않습니다.

> [!IMPORTANT]
> 스마트 Labeler 기능은 일반 예측과 동일한 [가격 책정 모델](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 을 사용 합니다. 이미지 집합에 대해 제안 된 태그를 처음으로 트리거할 때 예측 호출의 경우와 동일 하 게 청구 됩니다. 그런 다음 서비스는 선택한 이미지에 대 한 결과를 30 일간의 데이터베이스에 저장 하 고, 해당 기간 내에 언제 든 지 언제 든 지 액세스할 수 있습니다. 30 일 후에 제안 된 태그를 다시 요청 하면 요금이 청구 됩니다.

## <a name="smart-labeler-workflow"></a>스마트 Labeler 워크플로

다음 단계에서는 Smart Labeler를 사용 하는 방법을 보여 줍니다.

1. Custom Vision 프로젝트에 모든 학습 이미지를 업로드 합니다.
1. 각 태그에 대해 동일한 수의 이미지를 선택 하 여 데이터 집합의 일부를 레이블 합니다.
    > [!TIP]
    > 나중에 제안 하려는 모든 태그를 사용 해야 합니다.
1. 학습 프로세스를 시작 합니다.
1. 학습이 완료 되 면 **태그가** 없는 뷰로 이동 하 여 왼쪽 창에서 **제안 된 태그 가져오기** 단추를 선택 합니다.
    > [!div class="mx-imgBorder"]
    > 제안 된 태그 단추 ![태그 없는 이미지 탭에 표시 됩니다](./media/suggested-tags/suggested-tags-button.png)
1. 표시 되는 팝업 창에서 제안 하려는 이미지의 수를 설정 합니다. 태그가 지정 되지 않은 이미지의 일부에 대 한 초기 태그 제안만 가져와야 합니다. 이 프로세스를 반복할 때 더 나은 태그 제안을 받게 됩니다.
1. 제안 된 태그를 확인 하 여 올바르지 않은 문제를 해결 합니다.
    > [!TIP]
    > 제안 된 태그가 있는 이미지는 예측 불확실성을 기준으로 정렬 됩니다 (하위 값은 신뢰도가 높다는 것을 나타냄). **불확실성으로 정렬** 옵션을 사용 하 여 정렬 순서를 변경할 수 있습니다. 순서를 **high에서 low**로 설정 하면 높은 불확실성 예측을 먼저 수정한 다음 낮은 불확실성을 빠르게 확인할 수 있습니다.
    * 이미지 분류 프로젝트에서 배치로 태그를 선택 하 고 확인할 수 있습니다. 지정 된 제안 태그를 기준으로 뷰를 필터링 하 고 잘못 된 태그가 지정 된 이미지를 선택 취소 한 다음 일괄 처리에서 나머지를 확인 합니다.
        > [!div class="mx-imgBorder"]
        > ![제안 태그는 필터와 함께 IC 용 배치 모드로 표시 됩니다.](./media/suggested-tags/ic-batch-mode.png)

        갤러리에서 이미지를 선택 하 여 개별 이미지 모드에서 제안 된 태그를 사용할 수도 있습니다.

        ![제안 된 태그는 IC의 개별 이미지 모드에서 표시 됩니다.](./media/suggested-tags/ic-individual-image-mode.png)
    * 개체 검색 프로젝트에서 일괄 처리 확인은 지원 되지 않지만 더 구성 된 레이블 지정 환경을 위해 제안 된 태그를 기준으로 필터링 및 정렬할 수 있습니다. 태그가 없는 이미지의 축소판 그림에는 제안 된 태그의 위치를 나타내는 경계 상자의 오버레이가 표시 됩니다. 제안 된 태그 필터를 선택 하지 않은 경우에는 태그가 없는 모든 이미지가 경계 상자를 겹치지 않고 표시 됩니다.
        > [!div class="mx-imgBorder"]
        > ![제안 태그는 필터를 사용 하 여 OD에 대해 일괄 처리 모드로 표시 됩니다.](./media/suggested-tags/od-batch-mode.png)

        개체 검색 태그를 확인 하려면 갤러리의 각 개별 이미지에 해당 태그를 적용 해야 합니다.

        ![제안 된 태그는 OD의 개별 이미지 모드에서 표시 됩니다.](./media/suggested-tags/od-individual-image-mode.png)
1. 학습 프로세스를 다시 시작 합니다.
1. 제안 품질이 충족 될 때까지 위의 단계를 반복 합니다.

## <a name="next-steps"></a>다음 단계

빠른 시작을 따라 Custom Vision 프로젝트 만들기 및 학습을 시작 합니다.

* [분류자 빌드](getting-started-build-a-classifier.md)
* [개체 탐지기 빌드](get-started-build-detector.md)