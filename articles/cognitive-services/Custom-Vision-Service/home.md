---
title: Azure Custom Vision이란?
titlesuffix: Azure Cognitive Services
description: Custom Vision Service를 사용하여 Azure 클라우드에서 사용자 지정 이미지 분류자를 빌드하는 방법을 알아봅니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: 887c2fa09a1108b6254f4dd13b1ca211c80f8f70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219263"
---
# <a name="what-is-azure-custom-vision"></a>Azure Custom Vision이란?

Azure Custom Vision API는 사용자 지정 이미지 분류자를 빌드, 배포 및 개선할 수 있는 Cognitive Service입니다. 이미지 분류자는 특정 특성에 따라 이미지를 클래스(태그)로 분류하는 AI 서비스입니다. [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) 서비스와는 달리, Custom Vision은 사용자 고유의 분류를 만들 수 있습니다.

## <a name="what-it-does"></a>기능

Custom Vision Service는 기계 학습 알고리즘을 사용하여 이미지를 분류합니다. 개발자는 원하는 분류의 특징을 보이고 해당 분류가 없는 이미지 그룹을 제출해야 합니다. 제출 시 이미지의 올바른 태그를 지정해야 합니다. 그런 다음, 알고리즘이 이 데이터를 학습하고 동일한 데이터에서 이미지 자체를 테스트하여 정확도를 계산합니다. 모델을 학습시킨 후에는 앱의 요구 사항에 따라 새 이미지를 테스트 및 재교육하고 궁극적으로 새 이미지를 분류하는 데 모델을 사용할 수 있습니다. 모델 자체를 내보내서 오프라인에서 사용할 수도 있습니다.

### <a name="classification-and-object-detection"></a>분류 및 개체 검색

Custom Vision 기능을 두 가지로 나눌 수 있습니다. **이미지 분류**는 각 이미지에 분류 분포를 할당합니다. 다중 클래스(이미지당 태그 하나) 및 다중 레이블(이미지당 태그의 수) 분류 모델이 모두 지원됩니다. **개체 감지**는 다중 레이블 분류와 비슷하지만, 이미지에서 적용된 레이블을 찾을 수 있는 좌표를 반환합니다.

### <a name="optimization"></a>최적화

일반적으로 Custom Vision Service에서 사용하는 메서드는 차이에 대한 강점이 있기 때문에 아주 작은 데이터로도 프로토타이핑을 시작할 수 있습니다. 일반적으로 태그당 이미지 50개로 시작하는 것이 좋습니다. 그러나 이렇게 하면 서비스가 이미지의 미세한 차이를 검색하는 데 최적화되지 않습니다(예: 품질 보증 시나리오에서 작은 금 또는 패인 자국 검색).

뿐만 아니라 특정 대상 소재(예: 랜드마크 또는 소매 항목)에 최적화된 여러 종류의 Custom Vision 알고리즘 중에서 선택할 수 있습니다 자세한 내용은 [분류자 빌드](getting-started-build-a-classifier.md) 가이드를 참조하세요.

## <a name="what-it-includes"></a>포함되는 항목
Custom Vision Service는 네이티브 SDK 집합뿐 아니라 [Custom Vision 홈페이지](https://customvision.ai/)의 웹 기반 인터페이스를 통해 제공됩니다. 인터페이스를 통해 또는 둘 다 사용하여 모델을 만들고, 테스트하고, 학습할 수 있습니다.

![Chrome 브라우저 창의 Custom Vision 홈페이지](media/browser-home.png)

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Custom Vision 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft의 정책에 대해 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[분류자 빌드](getting-started-build-a-classifier.md) 가이드에 따라 웹에서 Custom Vision 사용을 시작하거나, [이미지 분류 자습서](csharp-tutorial.md)를 완료하여 코드로 시나리오를 구현합니다.
