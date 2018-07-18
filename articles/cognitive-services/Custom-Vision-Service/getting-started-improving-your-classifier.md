---
title: Custom Vision Service를 사용하여 분류자 개선 - Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service 분류자의 품질을 개선하는 방법을 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374726"
---
# <a name="how-to-improve-your-classifier"></a>분류자 개선 방법

Custom Vision Service 분류자의 품질을 개선하는 방법을 알아봅니다. 분류자 품질은 분류자에 제공하는, 레이블이 지정된 데이터의 품질에 따라 결정됩니다. 

## <a name="train-more-varied-images"></a>다양한 이미지 학습

다양한 각도, 배경, 개체 크기, 사진 그룹 및 기타 변형으로 태그가 지정된 이미지를 제공하면 분류자가 개선됩니다. 컨텍스트의 사진이 보통 배경 앞의 사진보다 더 좋습니다. 일반적인 사용 중에 분류자에 제출될 항목을 대표하는 이미지를 포함합니다.

이미지 추가에 대한 자세한 내용은 [분류자 빌드](getting-started-build-a-classifier.md)를 참조하세요.

> [!IMPORTANT]
> 이미지를 추가한 후에 분류자를 학습해야 합니다.

## <a name="use-images-submitted-for-prediction"></a>예측을 위해 제출된 이미지 사용

Custom Vision Service는 예측 끝점에 제출된 이미지를 저장합니다. 이러한 이미지를 사용하여 분류자를 개선하려면 다음 단계를 사용합니다.

1. 분류자에 제출된 이미지를 보려면 [Custom Vision 웹 페이지](https://customvision.ai)를 열고 __예측__ 탭을 선택합니다.

    ![예측 탭 이미지](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > 기본 보기에는 현재 반복의 이미지가 표시됩니다. __반복__ 드롭다운 필드를 사용하여 이전 반복 중에 제출된 이미지를 볼 수 있습니다.

2. 이미지를 마우스로 가리키면 분류자가 예측한 태그가 표시됩니다.

    > [!TIP]
    > 분류자를 가장 개선할 수 있는 이미지가 맨 위에 오도록 이미지에 순위가 지정됩니다. 다른 정렬을 선택하려면 __정렬__ 섹션을 사용합니다.

    학습 데이터에 이미지를 추가하려면 이미지, 태그, __저장 후 닫기__를 차례로 선택합니다. 이미지가 __예측__에서 제거되고 학습 이미지에 추가됩니다. __학습 이미지__ 탭을 선택하면 해당 이미지를 볼 수 있습니다.

    ![태그 지정 페이지 이미지](./media/getting-started-improving-your-classifier/tag-image.png)

3. __학습__ 단추를 사용하여 분류자를 다시 학습합니다.

## <a name="visually-inspect-predictions"></a>시각적 예측 검사

이미지 예측을 검사하려면 __학습 이미지__ 탭을 선택하고 __반복 기록__을 선택합니다. 빨간색 상자가 표시된 이미지는 잘못 예측된 이미지입니다.

![반복 기록 이미지](./media/getting-started-improving-your-classifier/iteration-history.png)

때로는 시각적 검사를 통해 학습 데이터를 더 추가하여 수정할 수 있는 패턴을 식별할 수 있습니다. 예를 들어 장미와 데이지에 대한 분류자가 모든 흰색 장미의 레이블을 데이지로 잘못 지정할 수 있습니다. 태그가 지정된 흰색 장미 이미지가 포함된 학습 데이터를 추가하고 제공하면 이 문제를 해결할 수 있습니다.

## <a name="unexpected-classification"></a>예기치 않은 분류

분류자가 이미지에 공통적인 특성을 학습하는 경우도 있습니다. 예를 들어 장미와 튤립에 대한 분류자를 만들려고 합니다. 야외에 피어 있는 튤립과 파란색 벽 앞에 있는 빨간색 꽃병에 담긴 장미의 이미지를 제공합니다. 이 데이터가 제공되면 분류자가 장미와 튤립 대신 야외와 벽 + 꽃병에 대해 학습할 수도 있습니다.

이 문제를 해결하려면 다양한 이미지 학습: 다양한 각도, 배경, 개체 크기, 그룹 및 기타 변형으로 이미지 제공에 대한 지침을 사용합니다.

## <a name="negative-image-handling"></a>음화 이미지 처리

Custom Vision Service는 몇 가지 자동 음화 이미지 처리를 지원합니다. 고양이와 개 분류자를 빌드 중이며 예측을 위해 신발 이미지를 제출하는 경우, 분류자는 해당 이미지의 점수를 고양이와 개 둘 다에 대해 0%에 가깝게 지정해야 합니다. 

> [!WARNING]
> 자동 접근 방식은 명확한 음화 이미지에 대해 작동합니다. 하지만 음화 이미지가 학습에 사용된 이미지의 변형인 경우에는 제대로 작동하지 않을 수 있습니다. 
>
> 예를 들어 허스키와 코기 분류자가 있고 포메라니언 이미지를 피드하는 경우 포메라니언을 허스키로 간주하고 점수를 지정할 수 있습니다. 이러한 특성의 음화 이미지가 있는 경우 새 태그(예: “기타”)를 만들고 음화 학습 이미지에 적용합니다.

## <a name="next-steps"></a>다음 단계

[예측 API 사용](use-prediction-api.md)