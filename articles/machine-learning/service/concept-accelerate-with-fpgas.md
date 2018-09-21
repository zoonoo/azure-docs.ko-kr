---
title: FPGA란? – Project Brainwave – Azure Machine Learning
description: FPGA로 모델 및 심층 신경망을 가속화하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5c8efcbb5f2e9014c9edabfc2aee37c8c29ea0b4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35638394"
---
# <a name="what-is-fpga-and-project-brainwave"></a>FPGA 및 Project Brainwave란?

이 문서에서는 FPGA(Field-Programmable Gate Arrays)를 소개하고 FPGA를 Azure 기계 학습과 통합하여 실시간 AI를 제공하는 방법을 설명합니다.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA 대 CPU, GPU 및 ASIC 비교

FPGA는 프로그래밍 가능한 논리 블록 배열, 그리고 제조 후 여러 가지 방법으로 블록을 구성할 수 있는 재구성 가능 상호 연결 계층을 포함하고 있습니다.

FPGA는 다른 칩과 비교되는 프로그래밍 기능 및 성능 조합을 제공합니다.

![Azure Machine Learning FPGA 비교](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **CPU(중앙 처리 장치)** 는 범용 프로세서입니다. CPU 성능은 그래픽 및 비디오 처리에 적합하지 않습니다.
- **GPU(그래픽 처리 장치)** 는 병렬 처리를 제공하며 AI 계산에 애용됩니다. GPU를 통해 병렬 처리하므로 CPU보다 이미지 렌더링 속도가 빠릅니다.
- Google의 TensorFlow 프로세서 장치 같은 **ASIC(응용 프로그램별 통합 회로)** 는 사용자 지정된 회로입니다. 이러한 칩은 가장 높은 효율성을 제공하지만 ASIC가 유연하지 않습니다.
- Azure에서 제공하는 **FPGA**는 ASIC와 비슷하지만 나중에 다시 구성할 수 있는 유연한 성능을 제공합니다.

이제 모든 새 Azure 서버에서 FPGA를 사용할 수 있습니다. 이미 Microsoft는 Bing 검색 순위, DNN(심층 신경망 네트워크) 평가, SDN(소프트웨어 정의 네트워킹) 가속에 FPGA를 사용하고 있습니다. 이처럼 FPGA를 구현하면 대기 시간이 감소하고 CPU를 확보하여 다른 작업에 사용할 수 있습니다.

## <a name="project-brainwave-on-azure"></a>Azure의 Project Brainwave

Project Brainwave는 Intel의 FPGA 장치를 기반으로 설계되어 실시간 AI 계산을 가속화하는 하드웨어 아키텍처입니다. 이 경제적인 FPGA 지원 아키텍처를 사용하면 학습된 신경망을 최대한 신속하게, 최대한 낮은 대기 시간으로 실행할 수 있습니다. Project Brainwave는 미리 학습된 DNN을 FPGA 전반에서 병렬로 실행하여 서비스 규모를 확장할 수 있습니다.

- 성능

    FPGA를 사용하면 실시간 유추 요청을 위한 짧은 대기 시간을 달성할 수 있습니다. 일괄 처리란 요청을 여러 작은 조각으로 나누어 프로세서로 보내 하드웨어 사용률을 높인다는 의미입니다. 일괄 처리는 비효율적이고 대기 시간이 발생할 수 있습니다. Brainwave는 일괄 처리가 필요 없기 때문에 CPU 및 GPU에 비해 대기 시간이 1/10에 불과합니다.

- 유연성

    FPGA를 다른 형식의 기계 학습에 대해 다시 구성할 수 있습니다. 이러한 유연성 덕분에 사용되는 최적의 숫자 정밀도 및 메모리 모델에 따라 응용 프로그램을 가속화할 수 있습니다.

    새로운 기계 학습 기술이 정기적으로 개발되고 있으며, Project Brainwave의 하드웨어 디자인 또한 빠르게 발전하고 있습니다. FPGA는 다시 구성할 수 있으므로 빠르게 변화하는 AI 알고리즘의 요구 사항에 맞게 최신 상태를 유지할 수 있습니다.

- 확장

    Microsoft Azure는 FPGA 부문에서 세계 최대의 클라우드 투자 규모를 자랑합니다. Azure의 확장 인프라에서 Brainwave를 실행할 수 있습니다.

현재 Azure Machine Learning과 통합된 Project Brainwave 미리 보기를 사용할 수 있습니다. 고객의 고유한 비즈니스 및 시설에서 이러한 컴퓨팅 속도를 활용할 수 있도록, Project Brainwave를 에지에 배포할 수 있는 제한된 미리 보기도 제공됩니다.

현재 미리 보기에서 Brainwave의 기능은 이미지 분류 및 인식을 위한 Intel FPGA 하드웨어에 TensorFlow를 배포하고 ResNet50 기반 신경망 네트워크를 구현하는 범위로 제한됩니다. 더 많은 갤러리 모델 및 다른 프레임워크를 지원할 계획이 있습니다.

다음 시나리오에서는 Project Brainwave 아키텍처에 FPGA를 사용합니다.

- 자동화된 광학 검사 시스템. [실시간 AI: Microsoft에서 Project Brainwave 미리 보기 발표](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)를 참조하세요.
- 토지 커버 매핑. [심층 학습 유추에 FPGA를 사용하여 수 테라바이트의 항공 이미지에 토지 커버 매핑을 수행하는 방법](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)을 참조하세요.

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>FPGA에 웹 서비스를 배포하는 방법

featurizer로 ResNet50을 사용하여 Azure에서 이미지 인식 서비스를 만드는 대략적인 흐름은 다음과 같습니다.

1. ResNet50은 Brainwave에 이미 배포되어 있습니다. TensorFlow를 사용하여 다른 그래프(날짜 입력, 분류 등)를 빌드하고, 서비스 정의 json 파일을 사용하여 파이프라인을 정의(입력 -> 기능화 -> 분류)할 수 있습니다. 정의 및 그래프를 Zip 파일로 압축하고, Zip 파일을 Azure Blob 저장소에 업로드합니다.
2. Azure ML 모델 관리 API를 사용하여 Blob 저장소의 Zip 파일에 모델을 등록합니다.
3. Azure ML 모델 관리 API를 사용하여 등록된 모델이 포함된 서비스를 배포합니다.

이 프로세스에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 FPGA에 웹 서비스로 모델 배포](how-to-deploy-fpga-web-service.md)를 참조하세요.


## <a name="start-using-fpga"></a>FPGA 사용 시작

Azure Machine Learning 하드웨어 가속 모델을 사용하면 Azure 클라우드에서 학습된 DNN 모델을 FPGA에 배포할 수 있습니다. 시작하려면 다음을 참조하십시오.

- [모델을 FPGA에 웹 서비스로 배포](how-to-deploy-fpga-web-service.md)
- [Project Brainwave 기반의 Microsoft Azure Machine Learning 하드웨어 가속 모델](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>다음 단계

[모델을 FPGA에 웹 서비스로 배포](how-to-deploy-fpga-web-service.md)

[FPGA SDK 설치 방법 알아보기](reference-fpga-package-overview.md)

[대규모 하드웨어: Azure 기반 대규모 ML + FPGA: 빌드 2018(비디오)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[Microsoft FPGA 기반 구성 가능 클라우드 살펴보기(비디오)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft에서 실시간 AI용 Project Brainwave 공개](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)