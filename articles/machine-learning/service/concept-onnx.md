---
title: ONNX를 사용 하는 고성능, 플랫폼 간 유추
titleSuffix: Azure Machine Learning
description: ONNX (Open 신경망 Exchange)를 사용 하 여 기계 학습 모델의 유추를 최적화 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: dc4a5984f42e87aa42c6873bb1ee63d66744e633
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053578"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 및 Azure Machine Learning: ML 모델 만들기 및 가속화

Onnx ( [Open 신경망 Exchange](https://onnx.ai) )를 사용 하 여 기계 학습 모델의 유추를 최적화 하는 방법을 알아봅니다. 유추 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계 이며 가장 일반적으로 프로덕션 데이터에 사용 됩니다. 

하드웨어 기능을 최대한 활용 하기 위해 모델 및 유추 라이브러리를 조정 해야 하기 때문에 유추를 위해 기계 학습 모델을 최적화 하거나 모델 점수를 지정 하는 것은 어렵습니다. 다양 한 종류의 플랫폼 (클라우드/edge, CPU/GPU 등)에 대해 최적의 성능을 얻기 위해 각각의 기능과 특징이 다르므로 문제가 매우 어렵습니다. 다양 한 플랫폼에서 실행 해야 하는 다양 한 프레임 워크의 모델이 있는 경우 복잡성이 증가 합니다. 프레임 워크와 하드웨어의 다양 한 조합을 모두 최적화 하는 것은 매우 시간이 많이 소요 됩니다. 선호 하는 프레임 워크에서 한 번 학습 하 고 클라우드 또는에 지의 어디에서 나 실행할 수 있는 솔루션이 필요 합니다. 여기서는 ONNX가 제공 됩니다.

Microsoft 및 파트너 커뮤니티는 기계 학습 모델을 나타내기 위해 개방형 표준으로 ONNX를 만들었습니다. TensorFlow, PyTorch, SciKit, Keras, 체 이너, MXNet 및 MATLAB를 비롯 한 [많은 프레임 워크](https://onnx.ai/supported-tools) 의 모델은 표준 onnx 형식으로 내보내거나 변환할 수 있습니다. 모델을 ONNX 형식으로 설정 하면 다양 한 플랫폼 및 장치에서 실행할 수 있습니다.

[Onnx Runtime](https://github.com/Microsoft/onnxruntime) 은 프로덕션에 onnx 모델을 배포 하기 위한 고성능 유추 엔진입니다. 클라우드 및에 지에 최적화 되어 있으며 Linux, Windows 및 Mac에서 작동 합니다. 에서 C++작성 되었으며 C, Python 및 C# api도 있습니다. ONNX Runtime은 모든 ONNX-ML 사양에 대 한 지원을 제공 하며, NVidia Gpu의 TensorRT와 같은 다른 하드웨어의 액셀러레이터와도 통합 됩니다.

ONNX 런타임은 Bing, Office, Cognitive Services 등의 대규모 Microsoft 서비스에서 사용 됩니다. 성능 향상은 여러 가지 요인에 따라 달라 지지만 이러한 Microsoft 서비스는 __CPU에 대 한 평균 2 배__향상을 보았습니다. ONNX Runtime은 수백만 개의 장치에서 Windows ML의 일부로도 사용 됩니다. Azure Machine Learning에서 런타임을 사용할 수 있습니다. ONNX 런타임을 사용 하 여 광범위 한 프로덕션 등급 최적화, 테스트 및 지속적인 개선의 이점을 누릴 수 있습니다.

[학습, 변환기 및 배포를 보여 주는![ONNX 흐름 다이어그램](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX 모델 가져오기

여러 가지 방법으로 ONNX 모델을 가져올 수 있습니다.
+ Azure Machine Learning에서 새 ONNX 모델 학습 (이 문서의 맨 아래에 있는 예제 참조)
+ 기존 모델을 다른 형식에서 ONNX로 변환 ( [자습서](https://github.com/onnx/tutorials)참조) 
+ [Onnx 모델 동물원](https://github.com/onnx/models) 에서 미리 학습 된 onnx 모델을 가져옵니다 (이 문서 맨 아래에 있는 예제 참조).
+ [Azure Custom Vision 서비스](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)에서 사용자 지정된 ONNX 모델을 생성합니다. 

이미지 분류, 개체 검색 및 텍스트 처리를 비롯 한 많은 모델을 ONNX 모델로 나타낼 수 있습니다. 그러나 일부 모델을 변환 하지 못할 수 있습니다. 이러한 상황이 발생 하는 경우 사용 하는 각 변환기의 GitHub에서 문제를 파일에 입력 하세요. 문제가 해결 될 때까지 기존 형식 모델을 계속 사용할 수 있습니다.

## <a name="deploy-onnx-models-in-azure"></a>Azure에서 ONNX 모델 배포

Azure Machine Learning를 사용 하 여 ONNX 모델을 배포, 관리 및 모니터링할 수 있습니다. 표준 [배포 워크플로](concept-model-management-and-deployment.md)와 ONNX Runtime을 사용하면 클라우드에서 호스트되는 REST 엔드포인트를 만들 수 있습니다. 이 문서 끝에 있는 Jupyter 노트북 예를 참조 하 여 직접 사용해 보세요. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Python을 사용 하 여 ONNX 런타임 설치 및 사용

ONNX 런타임에 대 한 Python 패키지는 [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu))에서 사용할 수 있습니다. 설치 하기 전에 [시스템 요구 사항을](https://github.com/Microsoft/onnxruntime#system-requirements) 읽어 보세요. 

 Python 용 ONNX 런타임을 설치 하려면 다음 명령 중 하나를 사용 합니다. 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Python 스크립트에서 ONNX Runtime을 호출하려면 다음을 사용합니다.    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

일반적으로는 모델과 함께 제공되는 설명서에 모델 사용을 위한 입력과 출력이 나와 있습니다. [Netron](https://github.com/lutzroeder/Netron) 등의 시각화 도구를 사용하여 모델을 볼 수도 있습니다. ONNX Runtime에서는 모델 메타데이터, 입력 및 출력도 쿼리할 수 있습니다.    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

모델을 유추하려면 `run`을 사용하고 반환하려는 출력 목록(모든 출력을 반환하려면 비워 둠)과 입력 값의 맵을 전달합니다. 그러면 출력 목록이 결과로 반환됩니다.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

전체 Python API 참조는 [ONNX 런타임 참조 문서](https://aka.ms/onnxruntime-python)를 참조하세요.    

## <a name="examples"></a>예시

ONNX 모델을 만들고 배포하는 예제 노트는 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)를 참조하세요.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>자세한 정보

ONNX에 대해 자세히 알아보거나 프로젝트에 참가합니다.
+ [ONNX 프로젝트 웹 사이트](https://onnx.ai)
+ [GitHub의 ONNX 코드](https://github.com/onnx/onnx)

ONNX Runtime에 대해 자세히 알아보거나 프로젝트에 참가합니다.
+ [ONNX Runtime GitHub Repo](https://github.com/Microsoft/onnxruntime)


