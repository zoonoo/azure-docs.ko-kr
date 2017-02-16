---
title: "기계 학습을 사용한 신용 위험에 대 한 예측 솔루션 | Microsoft Docs"
description: "Azure 기계 학습 스튜디오의 신용 위험 평가에 대한 예측 분석 솔루션을 만드는 방법을 보여주는 자세한 연습."
keywords: "신용 위험, 예측 분석 솔루션, 위험 평가"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>연습: Azure 기계 학습의 신용 위험 평가에 대한 예측 분석 솔루션 개발

이 연습에서는 Machine Learning Studio에서 솔루션을 개발하는 과정을 자세히 살펴보겠습니다. Machine Learning Studio에서 예측 분석 모델을 개발한 다음 이 모델에서 새로운 데이터를 사용하여 예측할 수 있는 Azure Machine Learning 웹 서비스로 배포할 것입니다. 

> [!TIP]
> 이 연습에서는 이전에 Machine Learning Studio를 사용해 본 경험이 한 번 이상 있으며 기계 학습 개념에 대해 어느 정도 이해하고 있지만 어느 쪽이든 전문가는 아니라고 가정합니다.
> 
>**Azure Machine Learning Studio**를 사용한 경험이 없는 경우 [Azure Machine Learning Studio에서 첫 번째 데이터 과학 실험 만들기](machine-learning-create-experiment.md) 자습서를 시작하는 것이 좋습니다. 이 자습서를 통해 처음으로 Machine Learning Studio를 사용할 수 있으며, 여기서는 실험에 모듈을 끌어 놓고 서로 연결하여 실험을 실행한 다음 결과를 확인할 수 있는 방법에 대한 기본 사항을 보여 줍니다.
>
>기계 학습이 처음이라면 비디오 시리즈 [초보자를 위한 데이터 과학](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md)이 시작에 좋은 출발점이 될 수 있습니다. 이 비디오 시리즈는 일상적인 언어 및 개념을 사용하여 기계 학습을 잘 소개하고 있습니다.
> 

## <a name="the-problem"></a>문제

신용대출 지원 시 제공한 정보를 기반으로 개인의 신용 위험을 예측해야 한다고 가정합니다.  

물론 신용 위험 평가는 복잡한 문제이지만 질문의 매개 변수를 약간 간소화하겠습니다. 그런 다음 Machine Learning Studio와 Machine Learning 웹 서비스가 있는 Microsoft Azure Machine Learning을 사용하여 이러한 예측 분석 솔루션을 만들 수 있는 방법의 예제로 사용할 것입니다.  

## <a name="the-solution"></a>솔루션

이 자세한 연습에서는 공개적으로 사용 가능한 신용 위험 데이터에서 시작하여 해당 데이터에 기반한 예측 모델을 개발하고 학습한 다음 신용 위험 평가를 위해 다른 사람이 사용할 수 있는 웹 서비스로 모델을 배포할 것입니다.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

신용 위험 평가 솔루션을 만들려면 다음 단계를 따릅니다.  

1. [기계 학습 작업 영역 만들기](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [기존 데이터 업로드](machine-learning-walkthrough-2-upload-data.md)
3. [새 실험 만들기](machine-learning-walkthrough-3-create-new-experiment.md)
4. [모델 학습 및 평가](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [웹 서비스 배포](machine-learning-walkthrough-5-publish-web-service.md)
6. [웹 서비스 액세스](machine-learning-walkthrough-6-access-web-service.md)

이 연습은 [Cortana Intelligence 갤러리](http://gallery.cortanaintelligence.com/)에서 [이진 분류: 신용 위험 예측](http://go.microsoft.com/fwlink/?LinkID=525270) 샘플 실험의 간소화된 버전을 기반으로 합니다.


> [!TIP]
> 기계 학습 스튜디오의 기능을 개략적으로 제공하는 다이어그램을 다운로드하고 인쇄하려면 [Azure 기계 학습 스튜디오 기능 개요](machine-learning-studio-overview-diagram.md)를 참조하세요.
> 
> 



<!--HONumber=Dec16_HO3-->


