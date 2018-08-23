---
title: AMLPCV(Azure Machine Learning Package for Computer Vision) 및 TDSP(팀 데이터 과학 프로세스)를 사용한 이미지 분류 | Microsoft Docs
description: 이미지 분류를 위한 AMLPCV(Azure Machine Learning Package for Computer Vision) 및 TDSP(팀 데이터 과학 프로세스)의 사용에 대해 설명합니다.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
ms.author: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 427ea1f3f22855b2c54beacbfb89a8f7fd37cce0
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246650"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>AMLPCV(Azure Machine Learning Package for Computer Vision) 및 TDSP(팀 데이터 과학 프로세스)를 사용한 피부암 이미지 분류

이 문서에서는 [AMLPCV(Azure Machine Learning Package for Computer Vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest))를 사용하여 *이미지 분류* 모델을 학습, 테스트 및 배포하는 방법을 보여 줍니다. 샘플은 [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)의 TDSP(팀 데이터 과학 프로세스) 구조와 템플릿을 사용합니다. 이 연습은 전체 샘플을 제공합니다. [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)을 딥 러닝 프레임워크로 사용하므로 [데이터 과학 가상 머신](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview) GPU 컴퓨터에서 학습이 수행됩니다. 배포에서는 Azure Machine Learning Operationalization CLI를 사용합니다.

Computer Vision 도메인의 많은 응용 프로그램은 이미지 분류 문제로 구분될 수 있습니다. 여기에는 “이미지에 개체가 있습니까?”(개체는 개, 자동차 또는 배일 수 있음)와 같은 질문에 대답하는 모델 작성이 포함됩니다. 또한 “이 환자의 망막 스캔을 통해 안과 질환에 대해 어떤 종류의 심각도가 표시됩니까?”와 같은 보다 복잡한 질문에 대한 대답도 포함됩니다. AMLPCV(Azure Machine Learning Package for Computer Vision)는 이미지 분류 데이터 처리 및 모델링 파이프라인을 간소화합니다. 

## <a name="link-to-the-github-repository"></a>GitHub 리포지토리에 대한 링크
이 문서는 샘플에 대한 요약 문서입니다. [GitHub 사이트](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification)에서 더 광범위한 설명서를 찾을 수 있습니다.

## <a name="team-data-science-process-walkthrough"></a>팀 데이터 과학 프로세스 연습

이 연습에서는 [팀 데이터 과학 프로세스](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 수명 주기를 사용합니다. 이 연습에서 다루는 수명 주기 단계는 다음과 같습니다.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. 데이터 취득](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
이미지 분류 작업에 ISIC(International Skin Imaging Collaboration) 데이터 집합이 사용됩니다. ISIC는 디지털 피부 영상 응용 프로그램의 사용을 용이하게 하여 흑색종 사망을 연구하고 이를 줄이는 데 도움을 주기 위해 설립된 산학 협력 단체입니다. [ISIC Archive](https://isic-archive.com/#images)에는 양성 또는 악성 레이블이 지정된 13,000가지를 초과하는 피부 병변 이미지가 포함되어 있습니다. 이미지 샘플은 ISIC Archive에서 다운로드합니다.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. 모델링](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
모델링 단계에서는 다음 하위 단계를 수행합니다.

#### <a name="dataset-creation"></a>데이터 집합 만들기

로컬 디스크에 이미지의 루트 디렉터리를 제공하여 AMLPCV(Azure Machine Learning Package for Computer Vision)에서 데이터 집합 개체를 생성합니다. 

#### <a name="image-visualization-and-annotation"></a>이미지 시각화 및 주석

데이터 집합 개체에서 이미지를 시각화하고 필요한 경우 레이블을 수정합니다.

#### <a name="image-augmentation"></a>이미지 증강

[imgaug](https://github.com/aleju/imgaug) 라이브러리에 설명된 변환을 사용하여 데이터 집합 개체를 증강합니다.

#### <a name="dnn-model-definition"></a>DNN 모델 정의

학습 단계에서 사용되는 모델 아키텍처를 정의합니다. AMLPCV(Azure Machine Learning Package for Computer Vision)에서는 6가지의 미리 학습된 심층 신경망 모델(AlexNet, Resnet-18, Resnet-34, Resnet-50, Resnet-101, Resnet-152)이 지원됩니다.

#### <a name="classifier-training"></a>분류자 학습

기본 또는 사용자 지정 매개 변수를 사용하여 신경망을 학습합니다.

#### <a name="evaluation-and-visualization"></a>평가 및 시각화

독립적인 테스트 데이터 집합에서 학습된 모델의 성능을 평가할 수 있는 기능을 제공합니다. 평가 메트릭에는 정확도, 정밀도, 재현율 및 ROC 곡선이 포함됩니다.

이러한 하위 단계는 해당 Jupyter Notebook에 자세히 설명되어 있습니다. Notebook은 또한 학습 속도, 미니 배치 크기 및 드롭아웃 속도와 같은 매개 변수를 설정하여 모델 성능을 향상시키는 방법에 대한 지침을 제공합니다.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. 배포](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

이 단계에서는 모델링 단계에서 생성된 모델을 운용합니다. 필수 구성 요소 및 필요한 설정을 소개합니다. 웹 서비스 사용에 대해서도 설명합니다. 이 자습서에서는 AMLPCV(Azure Machine Learning Package for Computer Vision)를 통해 심층 학습 모델을 작성하고 Azure에서 모델을 운영하는 방법에 대해 배웁니다.

## <a name="next-steps"></a>다음 단계
- [AMLPCV(Azure Machine Learning Package for Computer Vision)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)에 대한 추가 설명서를 읽어 보세요.
- 시작하려면 [팀 데이터 과학 프로세스](https://aka.ms/tdsp) 설명서를 읽어보세요.


## <a name="references"></a>참조

* [AMLPCV(Azure Machine Learning Package for Computer Vision)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [데이터 과학 가상 머신](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

