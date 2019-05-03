---
title: ONNX 사용 하 여 유추 플랫폼 간 고성능
titleSuffix: Azure Machine Learning service
description: ONNX 및 ONNX 런타임 모델을 가속화 하는 것에 대 한에 대해 알아봅니다
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6bda1af8095e7026c79b7d8ffe45b39f6cbb3508
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028697"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 및 Azure Machine Learning: 만들기 및 ML 모델을 가속화

에 대해 알아봅니다 방법을 사용 하 여 합니다 [열고 신경망 네트워크 Exchange](https://onnx.ai) (ONNX) 기계 학습 모델을 최적화할 수 있습니다.

모델 및 하드웨어 기능을 최대한 활용 하려면 유추 라이브러리를 조정 해야 하므로 최적화 기계 학습 유추를 위한 모델 어렵습니다. 문제가 어려워집니다 매우 다양 한 종류의 플랫폼 (클라우드/에 지, CPU/GPU 등)에서 최적의 성능을 제공 하려는 경우 각 이후 가지 다양 한 기능 및 특징. 다양 한 다양 한 플랫폼에서 실행 해야 하는 프레임 워크에서에서 모델이 있는 경우 복잡성이 증가 합니다. 시간이 많이 프레임 워크 및 하드웨어의 다른 모든 조합 최적화 하는 것입니다. 기본 프레임 워크에서 한 번 학습 하 고 클라우드 또는 지 모든 곳에서 실행 하는 솔루션에 필요 합니다. 이 경우 ONNX 살펴보기

Microsoft 및 파트너 커뮤니티 ONNX 기계 학습 모델을 나타내기 위한 공개 표준으로 만들어집니다. 모델 [여러 프레임 워크](https://onnx.ai/supported-tools) TensorFlow, PyTorch, Scikit-learn, Keras, Chainer, MXNet, 및 MATLAB를 포함 하 여 수 내보내거나 표준 ONNX 형식으로 변환 합니다. 모델을 ONNX 형식으로 되 면 다양 한 플랫폼 및 장치에서 실행할 수 있습니다.

[ONNX 런타임](https://github.com/Microsoft/onnxruntime) 는 고성능 유추 엔진 ONNX 모델을 프로덕션에 배포 합니다. 클라우드 및에 지 모두 적합 하 고 Linux, Windows, Mac.에서 작동 작성 된 C++, C, Python, 역시 및 C# Api입니다. ONNX 런타임 ONNX ML 사양의 모든에 지 및 NVidia gpu TensorRT 같은 다른 하드웨어에서 가속기와도 통합 됩니다.

ONNX 런타임에서 Bing, Office 및 Cognitive Services와 같은 대규모 Microsoft 서비스에 사용 됩니다. 성능 향상은 다양 한 요인에 따라 달라 집니다 하지만 이러한 Microsoft 서비스 살펴본를 __2 배의 성능 향상 cpu 평균__합니다. ONNX 런타임 수백만의 장치와 수백 개의 Windows 기계 학습의 일부로 사용 됩니다. Azure Machine Learning 서비스를 사용 하 여 런타임이 사용할 수 있습니다. ONNX 런타임을 사용 하 여 광범위 한 프로덕션 급 최적화, 테스트, 및 지속적으로 향상 시킬 수 있습니다.

[![교육, 변환기 및 배포를 보여 주는 ONNX 흐름 다이어그램](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX 모델 가져오기

여러 가지 방법으로 ONNX 모델을 가져올 수 있습니다.
+ Azure Machine Learning 서비스에서 새 ONNX 모델을 학습 (이 문서의 맨 아래에 있는 예제 참조)
+ 기존 모델을 ONNX를 다른 형식에서 변환 (참조를 [자습서](https://github.com/onnx/tutorials)) 
+ 미리 학습 된 ONNX 모델을 가져오기 합니다 [ONNX 모델 Zoo](https://github.com/onnx/models) (이 문서의 맨 아래에 있는 예제 참조)
+ [Azure Custom Vision 서비스](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)에서 사용자 지정된 ONNX 모델을 생성합니다. 

이미지 분류, 개체 감지 및 텍스트 처리를 포함 하 여 여러 모델을 ONNX 모델로 나타낼 수 있습니다. 그러나 일부 모델은 성공적으로 변환 될 수 있습니다. 이 경우 실행 하는 경우 사용 하는 각 변환기의 GitHub에서 문제를 제출 하세요. 문제를 해결 될 때까지 기존 형식 모델을 사용 하 여 계속할 수 있습니다.

## <a name="deploy-onnx-models-in-azure"></a>Azure에서 ONNX 모델 배포

Azure Machine Learning 서비스를 사용하면 ONNX 모델을 배포, 관리 및 모니터링할 수 있습니다. 표준 [배포 워크플로](concept-model-management-and-deployment.md)와 ONNX Runtime을 사용하면 클라우드에서 호스트되는 REST 엔드포인트를 만들 수 있습니다. 직접 사용해이 문서의 끝에서 Jupyter notebook 예제를 참조 하세요. 

### <a name="install-and-use-onnx-runtime-with-python"></a>설치 및 Python을 사용 하 여 ONNX 런타임 사용

ONNX 런타임에 대 한 Python 패키지에서 사용할 수 있는 [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime)하십시오 [GPU](https://pypi.org/project/onnxruntime-gpu)). 읽어보세요 [시스템 요구 사항](https://github.com/Microsoft/onnxruntime#system-requirements) 설치 하기 전에 합니다. 

 Python 용를 ONNX 런타임을 설치 하려면 다음 명령 중 하나를 사용 합니다. 
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
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})   
results = session.run([], {"input1": indata1, "input2": indata2})   
``` 

전체 Python API 참조는 [ONNX 런타임 참조 문서](https://aka.ms/onnxruntime-python)를 참조하세요.    

## <a name="examples"></a>예

ONNX 모델을 만들고 배포하는 예제 노트는 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)를 참조하세요.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>자세한 정보

ONNX에 대해 자세히 알아보거나 프로젝트에 참가합니다.
+ [ONNX 프로젝트 웹 사이트](https://onnx.ai)
+ [GitHub의 ONNX 코드](https://github.com/onnx/onnx)

ONNX Runtime에 대해 자세히 알아보거나 프로젝트에 참가합니다.
+ [ONNX Runtime GitHub Repo](https://github.com/Microsoft/onnxruntime)


