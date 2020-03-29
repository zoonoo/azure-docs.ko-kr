---
title: 스마트 라벨러로 이미지 에 더 빠르게 라벨 지정
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 스마트 라벨러를 사용하여 이미지에 대한 추천 태그를 생성하는 방법을 알아봅니다. 이렇게 하면 사용자 지정 비전 모델을 학습할 때 많은 수의 이미지에 더 빠르게 레이블을 지정할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647763"
---
# <a name="label-images-faster-with-smart-labeler"></a>스마트 라벨러로 이미지 에 더 빠르게 라벨 지정

이 가이드에서는 스마트 라벨러를 사용하여 이미지에 대한 추천 태그를 생성하는 방법을 알아봅니다. 이렇게 하면 사용자 지정 비전 모델을 학습할 때 많은 수의 이미지에 더 빠르게 레이블을 지정할 수 있습니다.

사용자 지정 비전 모델에 이미지를 태그하면 서비스는 모델의 최신 학습이터이터에 따라 태그가 지정되지 않은 이미지의 레이블을 예측합니다. 그런 다음 선택한 신뢰도 임계값 및 예측 불확실성을 기반으로 이러한 예측을 제안된 태그로 표시합니다. 그런 다음 제안을 확인하거나 변경할 수 있으므로 교육을 위해 이미지에 수동으로 태그를 지정하는 프로세스가 빨라질 수 있습니다.

## <a name="when-to-use-smart-labeler"></a>스마트 라벨러를 사용하는 경우

다음 제한 사항에 유의하십시오.

* 콘텐츠가 이미 한 번 학습된 이미지에 대해서만 제안된 태그를 요청해야 합니다. 방금 학습을 시작한 새 태그에 대한 제안을 받지 마세요.

> [!IMPORTANT]
> 스마트 라벨러 기능은 일반 예측과 동일한 [가격 책정 모델을](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 사용합니다. 이미지 집합에 대해 제안된 태그를 처음 트리거하면 예측 호출과 동일한 요금이 부과됩니다. 그 후, 서비스는 30 일 동안 데이터베이스에 선택한 이미지에 대한 결과를 저장하고, 당신은 그 기간 내에 무료로 언제든지 액세스 할 수 있습니다. 30일이 지나면 추천 태그를 다시 요청하면 요금이 청구됩니다.

## <a name="smart-labeler-workflow"></a>스마트 라벨러 워크플로우

다음 단계는 스마트 라벨러를 사용하는 방법을 보여 준다.

1. 모든 교육 이미지를 사용자 지정 비전 프로젝트에 업로드합니다.
1. 데이터 집합의 일부에 레이블을 지정하여 각 태그에 대해 동일한 수의 이미지를 선택합니다.
    > [!TIP]
    > 나중에 제안을 원하는 모든 태그를 사용해야 합니다.
1. 교육 프로세스를 시작합니다.
1. 교육이 완료되면 **태그가 지정되지 않은** 보기로 이동하여 왼쪽 창에서 **추천 태그 받기 단추를 선택합니다.**
    > [!div class="mx-imgBorder"]
    > ![추천 태그 버튼은 태그가 지정되지 않은 이미지 탭 아래에 표시됩니다.](./media/suggested-tags/suggested-tags-button.png)
1. 표시되는 팝업 창에서 제안할 이미지 수를 설정합니다. 태그가 지정되지 않은 이미지의 일부에 대해서만 초기 태그 제안을 받아야 합니다. 이 프로세스를 반복할 때 더 나은 태그 제안을 얻을 수 있습니다.
1. 제안된 태그를 확인하고 올바르지 않은 태그를 수정합니다.
    > [!TIP]
    > 제안된 태그가 있는 이미지는 예측 불확실성에 따라 정렬됩니다(값이 낮을수록 신뢰도가 높음을 나타요). **불확실성별 정렬** 옵션을 사용 하 여 정렬 순서를 변경할 수 있습니다. 순서를 **높음으로**설정하면 먼저 높은 불확실성 예측을 수정한 다음 낮은 불확실성을 빠르게 확인할 수 있습니다.
    * 이미지 분류 프로젝트에서 일괄 처리를 선택하고 태그를 확인할 수 있습니다. 지정된 제안된 태그로 뷰를 필터링하고 잘못 태그된 이미지의 선택을 취소한 다음 나머지를 일괄 처리로 확인합니다.
        > [!div class="mx-imgBorder"]
        > ![제안된 태그는 필터가 있는 IC의 일괄 처리 모드로 표시됩니다.](./media/suggested-tags/ic-batch-mode.png)

        갤러리에서 이미지를 선택하여 개별 이미지 모드에서 제안된 태그를 사용할 수도 있습니다.

        ![제안된 태그는 IC의 개별 이미지 모드로 표시됩니다.](./media/suggested-tags/ic-individual-image-mode.png)
    * 개체 검색 프로젝트에서는 일괄 처리 확인이 지원되지 않지만 제안된 태그로 필터링하고 정렬하여 보다 체계적인 레이블 지정 환경을 만들 수 있습니다. 태그가 지정되지 않은 이미지의 축소판 그림에는 추천 태그의 위치를 나타내는 경계 상자의 오버레이가 표시됩니다. 추천 태그 필터를 선택하지 않으면 경계 상자를 오버레이하지 않고 태그가 지정되지 않은 모든 이미지가 표시됩니다.
        > [!div class="mx-imgBorder"]
        > ![제안된 태그는 필터가 있는 OD에 대한 일괄 처리 모드로 표시됩니다.](./media/suggested-tags/od-batch-mode.png)

        개체 검색 태그를 확인하려면 갤러리의 각 개별 이미지에 적용해야 합니다.

        ![제안된 태그는 OD에 대한 개별 이미지 모드로 표시됩니다.](./media/suggested-tags/od-individual-image-mode.png)
1. 교육 프로세스를 다시 시작합니다.
1. 제안 품질이 만족할 때까지 이전 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

빠른 시작을 따라 사용자 지정 비전 프로젝트를 만들고 교육할 수 있습니다.

* [분류자 빌드](getting-started-build-a-classifier.md)
* [개체 감지기 빌드](get-started-build-detector.md)