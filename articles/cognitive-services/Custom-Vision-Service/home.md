---
title: Azure Custom Vision이란?
titlesuffix: Azure Cognitive Services
description: Custom Vision Service를 사용하여 Azure 클라우드에서 사용자 지정 이미지 분류자를 빌드하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 6cbc6e351147ed5b4c31463b5cf319417f34da34
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456778"
---
# <a name="what-is-azure-custom-vision"></a>Azure Custom Vision이란?

Azure Custom Vision은 사용자 고유의 이미지 분류자를 빌드, 배포 및 향상시킬 수 있는 인지 서비스입니다. 이미지 분류자는 이미지의 시각적 특성에 따라 레이블(_클래스_를 나타냄)을 해당 이미지에 적용하는 AI 서비스입니다. [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) 서비스와 달리 Custom Vision에서는 적용할 레이블을 결정할 수 있습니다.

## <a name="what-it-does"></a>기능

Custom Vision 서비스는 기계 학습 알고리즘을 사용하여 레이블을 이미지에 적용합니다. 개발자는 문제가 없는 특징만 있는 이미지 그룹을 제출해야 합니다. 이미지를 제출할 때 직접 레이블을 지정합니다. 그런 다음, 알고리즘에서 이 데이터를 학습하고 동일한 해당 이미지에서 자체적으로 테스트하여 정확도를 계산합니다. 알고리즘이 학습되면 앱의 요구 사항에 따라 새 이미지를 테스트 및 재학습하고, 궁극적으로 분류할 수 있습니다. 모델 자체를 내보내서 오프라인에서 사용할 수도 있습니다.

## <a name="classification-and-object-detection"></a>분류 및 개체 검색

Custom Vision 기능을 두 가지로 나눌 수 있습니다. **이미지 분류**는 하나 이상의 레이블을 이미지에 적용합니다. **개체 감지**도 이와 비슷하지만, 적용된 레이블이 있는 이미지의 좌표를 반환합니다.

## <a name="optimization"></a>최적화

Custom Vision 서비스는 이미지 간의 주요 차이를 빠르게 인식할 수 있도록 최적화되어 있습니다. 이렇게 하면 소량의 데이터를 사용하여 모델 프로토타입을 시작할 수 있습니다. 일반적으로 레이블당 50개의 이미지로 시작하는 것이 좋습니다. 그러나 이렇게 하면 서비스가 이미지의 미세한 차이를 검색하는 데 최적화되지 않습니다(예: 품질 보증 시나리오에서 작은 금 또는 패인 자국 검색).

또한 특정 주체 자료(예: 랜드마크 또는 소매 항목)가 있는 이미지에 최적화된 여러 종류의 Custom Vision 알고리즘 중에서 선택할 수 있습니다 자세한 내용은 [분류자 빌드](getting-started-build-a-classifier.md) 가이드를 참조하세요.

## <a name="what-it-includes"></a>포함되는 항목
Custom Vision Service는 네이티브 SDK 집합뿐 아니라 [Custom Vision 홈페이지](https://customvision.ai/)의 웹 기반 인터페이스를 통해 제공됩니다. 인터페이스를 통하거나 둘 모두를 사용하여 모델을 만들고, 테스트하고, 학습할 수 있습니다.

![Chrome 브라우저 창의 Custom Vision 홈페이지](media/browser-home.png)

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Custom Vision 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft의 정책에 대해 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[분류자 빌드](getting-started-build-a-classifier.md) 가이드에 따라 웹에서 Custom Vision 사용을 시작하거나, [이미지 분류 자습서](csharp-tutorial.md)를 수행하여 코드에서 기본 시나리오를 구현합니다.
