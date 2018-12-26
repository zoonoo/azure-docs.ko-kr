---
title: Azure Machine Learning Package for Computer Vision을 사용하여 이미지 유사성 모델을 빌드하고 배포합니다.
description: Azure Machine Learning Package for Computer Vision을 사용하여 컴퓨터 비전 이미지 유사성 모델을 빌드, 학습, 테스트 및 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b1b4e3e19adb38a69e16aaa98300972df3bb71a8
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093652"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 이미지 유사성 모델 빌드 및 배포

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


이 문서에서는 AMLPCV(Azure Machine Learning Package for Computer Vision)를 사용하여 이미지 유사성 모델을 학습, 테스트 및 배포하는 방법을 알아봅니다. 이 패키지에 대한 개요와 자세한 참조 설명서는 https://aka.ms/aml-packages/vision의 내용을 참조하세요.

컴퓨터 비전 도메인의 많은 문제는 이미지 유사성을 사용하여 해결할 수 있습니다. 이러한 문제에는 다음과 같은 질문에 답하는 모델 구축이 포함됩니다.
+ _이미지에 개체가 있습니까? 예: "dog", "car", "ship" 등등_
+ _이 환자의 망막 스캔을 통해 안과 질환에 대해 어떤 종류의 심각도가 표시됩니까?_

AMLPCV를 사용하여 이 모델을 구축하고 배포하는 경우 다음 단계를 수행하십시오.
1. 데이터 세트 만들기
2. 이미지 시각화 및 주석
3. 이미지 증강
4. DNN(심층 신경망) 모델 정의
5. 분류자 학습
6. 평가 및 시각화
7. 웹 서비스 배포
8. 웹 서비스 부하 테스트

[CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/)는 딥 러닝 프레임워크로 사용되며 학습은 ([딥 러닝 데이터 과학 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview))과 같은 GPU 기반 컴퓨터에서 로컬로 수행되고 배포에는 Azure ML Operationalization CLI가 사용됩니다.

## <a name="prerequisites"></a>필수 조건

1. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

1. 다음 계정 및 응용 프로그램을 설정하고 설치해야 합니다.
   - Azure Machine Learning 실험 계정 
   - Azure Machine Learning 모델 관리 계정
   - Azure Machine Learning Workbench 설치

   이 세 가지를 아직 만들거나 설치하지 않은 경우 [Azure Machine Learning 빠른 시작 및 Workbench 설치](../desktop-workbench/quickstart-installation.md) 문서를 참조하세요. 

1. Azure Machine Learning Package for Computer Vision을 설치해야 합니다. https://aka.ms/aml-packages/vision에 설명된 대로 이 패키지를 설치하는 방법을 알아봅니다.

## <a name="sample-data-and-notebook"></a>샘플 데이터 및 노트북

### <a name="get-the-jupyter-notebook"></a>Jupyter Notebook 가져오기

여기에 설명된 샘플을 직접 실행하려면 노트북을 다운로드하세요.

> [!div class="nextstepaction"]
> [Jupyter Notebook 가져오기](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>샘플 데이터 로드

다음 예제에서는 63개의 식기류 이미지로 구성된 데이터 세트를 사용합니다. 각 이미지에는 4 개의 다른 클래스(bowl, cup, cutlery, plate) 중 하나에 속한 것으로 레이블이 지정됩니다. 이 예제에서는 샘플을 신속하게 실행할 수 있도록 이미지 수가 작습니다. 실제로는 클래스당 100개 이상의 이미지가 제공되어야 합니다. 모든 이미지는 "bowl", "cup", "cutlery", "plate"라는 하위 디렉터리의 *"../sample_data/imgs_recycling/"* 에 있습니다.

![Azure Machine Learning 데이터 세트](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>다음 단계

아래 문서에서 Azure Machine Learning Package for Computer Vision에 대해 알아봅니다.

+ [이 모델의 정확도를 높이는 방법](how-to-improve-accuracy-for-computer-vision-models.md)을 알아봅니다.

+ [패키지 개요](https://aka.ms/aml-packages/vision)를 확인합니다.

+ 이 패키지에 대한 [참조 설명서](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)를 탐색합니다.

+ [Azure Machine Learning용 다른 Python 패키지](reference-python-package-overview.md)를 자세히 알아봅니다.
