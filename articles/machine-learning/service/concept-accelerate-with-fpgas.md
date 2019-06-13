---
title: 현장에서 프로그래밍 FPGA (gate array) 란
titleSuffix: Azure Machine Learning service
description: Azure에서 FPGA를 사용하여 모델 및 심층 신경망을 가속화하는 방법을 알아봅니다. 이 문서에서는 FPGA(Field-programmable Gate Arrays)를 소개하고, Azure FPGA에 모델을 배포할 때 Azure Machine Learning 서비스로 실시간 AI(인공 지능)를 제공하는 방법을 설명합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 9204b0d8513d4adbb9c93be94284d7d46de41e06
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479571"
---
# <a name="what-are-field-programmable-gate-arrays-fpga"></a>현장에서 프로그래밍 FPGA (gate array) 란

이 문서에서는 FPGA(Field-programmable Gate Arrays)를 소개하고, Azure FPGA에 모델을 배포할 때 Azure Machine Learning 서비스로 실시간 AI(인공 지능)를 제공하는 방법을 설명합니다.

FPGA는 프로그래밍 가능한 논리 블록 배열과 재구성 가능한 상호 연결 계층 구조를 포함하고 있습니다. 제조 후 상호 연결을 통해 이러한 블록을 다양한 방법으로 구성할 수 있습니다. 다른 칩과 비교해서, FPGA는 프로그래밍 기능 및 성능 조합을 제공합니다.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA 대 CPU, GPU 및 ASIC 비교

다음 다이어그램 및 표에서는 FPGA와 다른 프로세서를 비교해서 보여 줍니다.

![Azure Machine Learning Service FPGA 비교 다이어그램](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|프로세서||설명|
|---|:-------:|------|
|애플리케이션 관련 집적 회로|ASIC|Google의 TPU(TensorFlow Processor Units) 같은 사용자 지정 회로는 가장 높은 효율성을 제공합니다. 이러한 회로는 변하는 요구 사항에 따라 재구성할 수 없습니다.|
|Field-programmable Gate Arrays|FPGA|Azure에서 사용할 수 있는 것과 같은 FPGA는 ASIC에 가까운 성능을 제공합니다. 또한 유연하고, 시간 경과에 따라 새 논리를 구현하기 위해 다시 구성할 수 있습니다.|
|그래픽 처리 장치|GPU|GPU는 AI 계산에 널리 사용되며, CPU보다 이미지 렌더링 속도가 빠른 병렬 처리 기능을 제공합니다.|
|중앙 처리 장치|CPU|범용 프로세서이며, 성능이 그래픽 및 비디오 처리에 적합하지 않습니다.|

Azure에서 Fpga 실시간 AI 계산을 가속화 하는 데이터 과학자와 개발자가 사용 되는 Intel FPGA 장치를 기반으로 합니다. 이 FPGA 지원 아키텍처는 강력한 성능, 유연성 및 확장성을 제공하며 Azure에서 사용할 수 있습니다.

Fpga를 사용 하면 실시간 유추 (또는 모델 점수 매기기)에 대 한 대기 시간이 짧은 요청 목표를 달성할 수 있습니다. 비동기 요청(일괄 처리)이 필요하지 않습니다. 일괄 처리를 사용하면 더 많은 데이터를 처리해야 하므로 대기 시간이 길어질 수 있습니다. 일괄 처리; 신경망 처리 단위 구현의 필요 하지 않습니다. 따라서 대기 시간이 낮은 횟수에 비해 수 있습니다 CPU 및 GPU 프로세서.

### <a name="reconfigurable-power"></a>재구성 가능한 전원
여러 형식의 기계 학습 모델에 대해 FPGA를 다시 구성할 수 있습니다. 이러한 유연성 덕분에 사용되는 최적의 숫자 정밀도 및 메모리 모델에 따라 애플리케이션을 가속화할 수 있습니다. FPGA는 다시 구성할 수 있으므로 빠르게 변화하는 AI 알고리즘의 요구 사항에 맞게 최신 상태를 유지할 수 있습니다.

### <a name="whats-supported-on-azure"></a>Azure에서 지원되는 내용
Microsoft Azure는 FPGA 부문에서 세계 최대의 클라우드 투자 규모를 자랑합니다. Azure 지원에서 Fpga:

+ 이미지 분류 및 인식 시나리오
+ TensorFlow 배포
+ DNN: ResNet 50, ResNet 152, VGG-16, SSD-VGG 및 DenseNet-121
+ Intel FPGA 하드웨어 

이 FPGA 지원 하드웨어 아키텍처를 사용하면 학습된 신경망이 낮은 대기 시간으로 신속하게 실행됩니다. Azure는 서비스를 규모 확장할을 Fpga에서 미리 학습 된 DNN 심층 신경망 () 병렬화 할 수 있습니다. DNN은 전송 학습을 위한 심층 기능 기능화기로 미리 학습하거나 업데이트된 가중치를 사용하여 미세 조정할 수 있습니다.

### <a name="scenarios-and-applications"></a>시나리오 및 애플리케이션

Azure Fpga는 Azure Machine Learning과 통합 됩니다. Microsoft에서는 DNN 평가, Bing 검색 순위, SDN(소프트웨어 정의 네트워킹) 가속화에 FPGA를 사용하여 대기 시간을 줄이는 한편, CPU 리소스를 회수하여 다른 작업에 사용합니다.

다음 시나리오는 Fpga를 사용합니다.
+ [자동화된 광학 검사 시스템](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [토지 커버 매핑](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Azure 기반의 FPGA에 배포

Azure에서 이미지 인식 서비스를 만들기 위해 지원되는 DNN을 Azure FPGA에 배포하기 위한 기능화기로 사용할 수 있습니다.

1. [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk)를 사용하여 서비스 정의를 만듭니다. 서비스 정의는 TensorFlow를 기반으로 그래프(입력, 기능화기 및 분류자) 파이프라인을 설명하는 파일입니다. 배포 명령은 자동으로 정의 및 그래프를 ZIP 파일로 압축하고, ZIP 파일을 Azure Blob Storage에 업로드합니다. DNN은 FPGA에서 실행 되도록 이미 배포 되었습니다.

1. Azure Blob Storage에서 ZIP 파일과 함께 SDK를 사용하여 모델을 등록합니다.

1. SDK를 사용하여 등록된 모델이 포함된 서비스를 배포합니다.

Azure 클라우드의 FPGA에 학습된 DNN 모델 배포를 시작하려면 [모델을 FPGA에 웹 서비스로 배포](how-to-deploy-fpga-web-service.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

다음 비디오와 블로그를 확인하세요.

+ [하이퍼스케일 하드웨어: Azure + FPGA 기반의 대규모 ML: 빌드 2018(비디오)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Microsoft FPGA 기반 구성 가능 클라우드 살펴보기(비디오)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [실시간 AI용 Project Brainwave: 프로젝트 홈페이지](https://www.microsoft.com/research/project/project-brainwave/)
