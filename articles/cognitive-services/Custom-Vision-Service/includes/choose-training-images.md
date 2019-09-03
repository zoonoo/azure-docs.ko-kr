---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130100"
---
최소한 초기 학습 세트에서는 태그당 이미지 30개 이상을 사용하는 것이 좋습니다. 학습된 모델을 테스트하기 위해 이미지 몇 개를 추가로 수집할 수도 있습니다.

모델을 효과적으로 학습시키려면 다양한 시각적 이미지를 사용합니다. 다음에 따라 달라지는 이미지를 선택합니다.
* 카메라 각도
* 조명
* background
* 표시 스타일
* 개별/그룹화된 피사체
* size
* 형식

또한 모든 학습 이미지가 다음 기준을 충족하는지 확인합니다.
* .jpg, .png, .bmp 또는 .gif 형식
* 크기 6MB 이하(예측 이미지의 경우 4MB)
* 가장 짧은 가장자리가 256픽셀 이상. 256픽셀보다 짧은 이미지는 Custom Vision Service에서 자동으로 확장됩니다.
