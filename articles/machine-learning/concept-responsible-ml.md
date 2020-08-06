---
title: 담당 machine learning (미리 보기) 이란?
titleSuffix: Azure Machine Learning
description: 담당 기계 학습 이란 무엇 이며 Azure Machine Learning에서 사용 하는 방법을 알아보세요.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 08/05/2020
ms.openlocfilehash: 689b90fc1f45faad72640f47e5eebe936d2dc8b7
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829393"
---
# <a name="what-is-responsible-machine-learning-preview"></a>담당 기계 학습 이란 무엇 인가요? (미리 보기)

이 문서에서는 기계 학습 (기계 학습)과 Azure Machine Learning를 사용 하 여이를 구현 하는 방법에 대해 알아봅니다.

## <a name="responsible-machine-learning-principles"></a>담당 기계 학습 원리

AI 시스템을 개발하고 사용하는 동안에는 신뢰가 중심이 되어야 합니다. 플랫폼, 프로세스 및 모델을 신뢰하세요. Microsoft에서 담당 machine learning에는 다음 값과 원칙이 포함 됩니다.

- 기계 학습 모델 이해
  - 모델 동작 해석 및 설명
  - 모델 평가 및 완화
- 사용자 및 데이터 보호
  - 차등 프라이버시를 통한 데이터 노출 방지
  - Homomorphic 암호화를 사용 하 여 암호화 된 데이터 작업
- 클라우드에서 엔드투엔드 기계 학습 프로세스 제어
  - 데이터 시트를 사용하여 기계 학습 수명 주기 문서화

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="담당 ML 핵심 요소-interpretability, 차등 개인 정보, homomorphic 암호화, 감사 내역-Azure Machine Learning":::

AI 및 자치 시스템이 사회 구조에 더 많이 통합됨에 따라 이러한 기술의 의도치 않은 결과를 예측하고 완화하기 위해 사전에 노력하는 것이 중요합니다.

## <a name="interpret-and-explain-model-behavior"></a>모델 동작 해석 및 설명

설명 하거나 불투명 한 시스템 시스템 개발자, 조정기, 사용자 및 비즈니스 의사 결정권자가 특정 결정을 내리는 이유를 이해 하기 위해 시스템 개발자, 조정기, 사용자 및 비즈니스 의사 결정권자에 게 어려운 문제가 발생할 수 있습니다. 일부 AI 시스템은 다른 시스템보다 더 설명하기 쉬우며, 정확도와 설명 가능성이 균형을 이루는 시스템도 있습니다.

해석 가능한 AI 시스템을 빌드하려면 Microsoft에서 개발한 오픈 소스 패키지인 [InterpretML](https://github.com/interpretml/interpret)을 사용하세요. [Azure Machine Learning 내부에 InterpretML을 사용](how-to-machine-learning-interpretability.md)하여 [자동화된 기계 학습 모델](how-to-machine-learning-interpretability-automl.md)을 포함한 [기계 학습 모델을 해석하고 설명](how-to-machine-learning-interpretability-aml.md)할 수 있습니다.

## <a name="mitigate-fairness-in-machine-learning-models"></a>기계 학습 모델의 공평 완화

AI 시스템이 사회의 일상적인 의사 결정에 점점 더 많이 관여함에 따라, 이러한 시스템이 모든 사용자에게 공정하게 결과를 제공하는 것은 매우 중요합니다.

AI 시스템이 불공정하면 다음과 같은 의도치 않은 결과가 발생할 수 있습니다.

- 개인에게 기회, 리소스 또는 정보 미제공
- 편견과 고정관념 강화

공정성의 많은 요소는 메트릭으로 캡처하거나 표현할 수 없습니다. AI 시스템의 설계 및 개발 단계에서 공정성을 개선할 수 있는 도구와 방법이 있습니다.

AI 시스템의 불공정성을 줄이기 위한 두 가지 주요 단계는 평가 및 완화입니다. AI 시스템의 잠재적 불공정성을 평가하고 완화할 수 있는 오픈 소스 패키지인 [FairLearn](https://github.com/fairlearn/fairlearn)을 권장합니다. 공정성과 FairLearn 패키지에 대해 자세히 알아보려면 [ML의 공정성 문서](./concept-fairness-ml.md)를 참조하세요.

## <a name="prevent-data-exposure-with-differential-privacy"></a>차등 프라이버시를 통한 데이터 노출 방지

데이터를 분석에 사용하는 경우 데이터를 사용하는 동안 데이터를 비공개로, 기밀로 유지하는 것이 중요합니다. 차등 프라이버시 보호는 개인의 데이터를 안전하게 비공개로 보호하는 데 도움이 되는 일련의 시스템과 방법입니다.

기존 시나리오에서는 원시 데이터가 파일 및 데이터베이스에 저장되었습니다. 사용자가 데이터를 분석할 때는 일반적으로 원시 데이터를 사용합니다. 이는 개인의 프라이버시를 침해할 수 있기 때문에 문제가 됩니다. 차등 프라이버시는 사용자가 개별 데이터 요소를 식별할 수 없도록 데이터에 "노이즈" 또는 임의성을 추가하여 이 문제를 처리합니다.

차등 비공개 시스템을 구현하기는 어렵습니다. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core)는 글로벌 차등 비공개 시스템을 빌드하기 위한 여러 구성 요소가 포함된 오픈 소스 프로젝트입니다. 차등 프라이버시 및 WhiteNoise 프로젝트에 대해 자세히 알아보려면 [차등 프라이버시 및 WhiteNoise를 사용하여 데이터 프라이버시 보호](./concept-differential-privacy.md) 문서를 참조하세요.

> [!NOTE]
> 도구 키트의 이름을 바꾸고 새 이름을 소개 하는 주입니다. 

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>Homomorphic 암호화를 사용 하 여 암호화 된 데이터에 대 한 작업

기존 클라우드 저장소 및 계산 솔루션에서는 클라우드에서 계산 하기 위해 고객 데이터에 대 한 암호화 되지 않은 액세스 권한이 있어야 합니다. 이 액세스는 클라우드 운영자에 게 데이터를 제공 합니다. 데이터 개인 정보 보호는 클라우드에서 구현 하 고 고객이 신뢰 하는 액세스 제어 정책에 의존 합니다.

동형 암호화에서는 비밀(암호 해독) 키 액세스 없이도 암호화된 데이터에 대해 계산을 수행할 수 있습니다. 계산 결과는 암호화되며 비밀 키의 소유자만 볼 수 있습니다. Homomorphic 암호화를 사용 하 여 클라우드 운영자는 저장 하 고 계산 하는 데이터에 대 한 암호화 되지 않은 액세스를 갖지 않습니다. 계산은 암호화 된 데이터에서 직접 수행 됩니다. 데이터 개인 정보는 최신 암호화에 의존 하며 데이터 소유자는 모든 정보 릴리스를 제어 합니다. Microsoft의 homomorphic encryption에 대 한 자세한 내용은 [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/)를 참조 하십시오.

Azure Machine Learning에서 homomorphic 암호화를 시작 하려면 [MICROSOFT 봉인](https://github.com/microsoft/SEAL)에 대 한 [암호화 된 유추](https://pypi.org/project/encrypted-inference/) Python 바인딩을 사용 하세요. Microsoft 봉인은 암호화 된 정수 또는 실수에 대해 추가 및 multiplications를 수행할 수 있도록 하는 오픈 소스 homomorphic 암호화 라이브러리입니다. Microsoft 봉인에 대해 자세히 알아보려면 [Azure 아키텍처 센터](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) 또는 [microsoft Research 프로젝트 페이지](https://www.microsoft.com/research/project/microsoft-seal/)를 참조 하세요.

[Azure Machine Learning에서 암호화 된 추론 웹 서비스를 배포 하는 방법을](how-to-homomorphic-encryption-seal.md)알아보려면 다음 샘플을 참조 하세요.

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>데이터 시트를 사용하여 기계 학습 수명 주기 문서화

기계 학습 프로세스에서 적절한 정보를 문서화하는 것은 각 단계에서 책임 있는 결정을 내리는 데 중요합니다. 데이터 시트는 기계 학습 수명 주기의 일부로 사용 및 생성되는 기계 학습 자산을 문서화하는 데 사용되는 방법입니다.

모델은 "불투명 상자"로 간주 되는 경향이 있으며,이에 대 한 정보는 거의 없습니다. 기계 학습 시스템은 점점 더 보편화되고 있고 의사 결정에 사용되고 있으므로 데이터 시트를 사용하면 더 책임 있는 기계 학습 시스템을 개발하는 데 한 발짝 다가갈 수 있습니다.

데이터 시트의 일부로 문서화할 수 있는 몇 가지 모델 정보는 다음과 같습니다.

- 올바른 사용법
- 모델 아키텍처
- 사용된 학습 데이터
- 사용된 평가 데이터
- 학습 모델 성능 메트릭
- 공정성 정보

Azure Machine Learning SDK를 사용하여 [모델에 대한 데이터 시트](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)를 구현하는 방법에 대해 알아보려면 다음 샘플을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- 자세한 내용은 최선의 구현 방법에 대 한 자세한 내용은 [담당 혁신 도구 키트](https://docs.microsoft.com/azure/architecture/guide/responsible-innovation/) 를 참조 하세요.
- 기계 학습 시스템 설명서의 [ML 정보](https://www.partnershiponai.org/about-ml/) 지침 세트에 대해 자세히 알아보세요.
