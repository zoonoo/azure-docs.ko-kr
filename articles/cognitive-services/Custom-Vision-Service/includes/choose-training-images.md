---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 9f8eadea198bbae3de2ffc1b3aaac48925719586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100105909"
---
최소한 초기 학습 세트에서는 태그당 이미지 30개 이상을 사용하는 것이 좋습니다. 학습된 모델을 테스트하기 위해 이미지 몇 개를 추가로 수집할 수도 있습니다.

모델을 효과적으로 학습시키려면 다양한 시각적 이미지를 사용합니다. 다음에 따라 달라지는 이미지를 선택합니다.
* 카메라 각도
* 조명
* background
* 표시 스타일
* 개별/그룹화된 피사체
* 크기
* type

또한 모든 학습 이미지가 다음 기준을 충족하는지 확인합니다.
* .jpg, .png, .bmp 또는 .gif 형식
* 크기 6MB 이하(예측 이미지의 경우 4MB)
* 가장 짧은 가장자리가 256픽셀 이상. 256픽셀보다 짧은 이미지는 Custom Vision Service에서 자동으로 확장됩니다.

> [!NOTE]
> 학습을 완료하려면 보다 광범위한 이미지 세트가 필요한가요? Microsoft Garage 프로젝트인 Trove를 사용하면 학습 목적으로 이미지 세트를 수집하고 구매할 수 있습니다. 이미지를 수집한 후에는 해당 이미지를 다운로드한 다음, 일반적인 방법으로 Custom Vision 프로젝트로 가져올 수 있습니다. 자세한 내용은 [Trove 페이지](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)를 참조하세요.