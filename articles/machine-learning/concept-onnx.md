---
title: 'ONNX: 고퍼, 크로스 플랫폼 추론'
titleSuffix: Azure Machine Learning
description: 개방형 신경망 교환(ONNX)을 사용하여 기계 학습 모델의 추론을 최적화하는 방법을 알아보십시오.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270174"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 및 Azure 기계 학습: ML 모델 생성 및 가속화

[개방형 신경망](https://onnx.ai) 교환(ONNX)을 사용하여 기계 학습 모델의 추론을 최적화하는 방법을 알아보십시오. 추론 또는 모델 점수 매기기는 배포된 모델이 예측에 가장 일반적으로 사용되는 단계입니다. 

추론(또는 모델 점수 매기기)을 위해 기계 학습 모델을 최적화하는 것은 하드웨어 기능을 최대한 활용하려면 모델과 추론 라이브러리를 조정해야 하므로 어렵습니다. 각 플랫폼(클라우드/에지, CPU/GPU 등)에서 최적의 성능을 얻으려면 각 기와 기능이 다르므로 문제가 매우 어려워집니다. 다양한 플랫폼에서 실행해야 하는 다양한 프레임워크의 모델이 있는 경우 복잡성이 증가합니다. 프레임워크와 하드웨어의 모든 조합을 최적화하는 데 는 매우 많은 시간이 소요됩니다. 원하는 프레임워크에서 한 번 학습하고 클라우드 또는 에지의 어느 곳에서나 실행할 수 있는 솔루션이 필요합니다. 이것이 ONNX가 들어오는 곳입니다.

Microsoft와 파트너 커뮤니티는 기계 학습 모델을 표현하기 위한 개방형 표준으로 ONNX를 만들었습니다. 텐서플로우, 파이토치, SciKit-Learn, 케라스, 체인러, MXNet 및 MATLAB을 포함한 [다양한 프레임워크의](https://onnx.ai/supported-tools) 모델을 표준 ONNX 형식으로 내보내거나 변환할 수 있습니다. 모델이 ONNX 형식으로 되면 다양한 플랫폼 및 장치에서 실행할 수 있습니다.

[ONNX 런타임은](https://github.com/Microsoft/onnxruntime) ONNX 모델을 프로덕션에 배포하기 위한 고성능 추론 엔진입니다. 그것은 클라우드와 가장자리에 최적화 하 고 리눅스에서 작동, 윈도우, 그리고 맥. C ++로 작성되어 C, 파이썬 및 C # API도 있습니다. ONNX 런타임은 모든 ONNX-ML 사양을 지원하며 엔비디아 GPU의 텐서RT와 같은 다른 하드웨어의 가속기와도 통합됩니다.

ONNX 런타임은 Bing, Office 및 코그너티브 서비스와 같은 대규모 Microsoft 서비스에서 사용됩니다. 성능 향상은 여러 가지 요인에 따라 달라지지만 이러한 Microsoft 서비스는 __CPU에서 평균 2배의 성능 향상을__보였습니다. ONNX 런타임은 수억 대의 장치에서 Windows ML의 일부로사용됩니다. Azure 기계 학습에서 런타임을 사용할 수 있습니다. ONNX 런타임을 사용하면 광범위한 프로덕션 등급 최적화, 테스트 및 지속적인 개선의 이점을 누릴 수 있습니다.

[![교육, 컨버터 및 배포를 보여주는 ONNX 흐름 다이어그램](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX 모델 가져오기

여러 가지 방법으로 ONNX 모델을 가져올 수 있습니다.
+ Azure 기계 학습에서 새 ONNX 모델 학습(이 문서 하단의 예제 참조)
+ 다른 형식에서 ONNX로 기존 모델을 변환 [(자습서](https://github.com/onnx/tutorials)참조) 
+ [ONNX 모델 동물원에서](https://github.com/onnx/models) 미리 학습된 ONNX 모델 받기(이 문서 하단의 예제 참조)
+ [Azure Custom Vision 서비스](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)에서 사용자 지정된 ONNX 모델을 생성합니다. 

이미지 분류, 개체 감지 및 텍스트 처리를 포함한 많은 모델을 ONNX 모델로 나타낼 수 있습니다. 그러나 일부 모델은 성공적으로 변환되지 않을 수 있습니다. 이 상황이 발생하면 사용한 각 변환기의 GitHub에 문제를 제기하십시오. 문제가 해결될 때까지 기존 형식 모델을 계속 사용할 수 있습니다.

## <a name="deploy-onnx-models-in-azure"></a>Azure에서 ONNX 모델 배포

Azure 기계 학습을 사용하면 ONNX 모델을 배포, 관리 및 모니터링할 수 있습니다. 표준 [배포 워크플로](concept-model-management-and-deployment.md)와 ONNX Runtime을 사용하면 클라우드에서 호스트되는 REST 엔드포인트를 만들 수 있습니다. 이 문서의 끝에 있는 Jupyter 노트북 예제를 참조하여 직접 사용해 보십시오. 

### <a name="install-and-use-onnx-runtime-with-python"></a>파이썬으로 ONNX 런타임 설치 및 사용

ONNX 런타임용 파이썬 패키지는[PyPi.org(CPU,](https://pypi.org/project/onnxruntime) [PyPi.org](https://pypi.org) [GPU)에서](https://pypi.org/project/onnxruntime-gpu)사용할 수 있습니다. 설치 하기 전에 [시스템 요구 사항을](https://github.com/Microsoft/onnxruntime#system-requirements) 읽어 보시기 바랍니다. 

 파이썬용 ONNX 런타임을 설치하려면 다음 명령 중 하나를 사용합니다. 
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

## <a name="examples"></a>예

ONNX 모델을 만들고 배포하는 예제 노트는 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)를 참조하세요.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>추가 정보

ONNX에 대해 자세히 알아보거나 프로젝트에 참가합니다.
+ [ONNX 프로젝트 웹 사이트](https://onnx.ai)
+ [GitHub의 ONNX 코드](https://github.com/onnx/onnx)

ONNX Runtime에 대해 자세히 알아보거나 프로젝트에 참가합니다.
+ [ONNX Runtime GitHub Repo](https://github.com/Microsoft/onnxruntime)


