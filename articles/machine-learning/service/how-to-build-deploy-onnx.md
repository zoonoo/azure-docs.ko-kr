---
title: ONNX 및 Azure Machine Learning | 모델 만들기 및 배포
description: ONNX의 정의와 Azure Machine Learning을 사용하여 ONNX 모델을 만들고 배포하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: d4ce2dc67b0d9229ac2605ab317594ea345c19b2
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434078"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX 및 Azure Machine Learning: 상호 운용 가능한 AI 모델 만들기 및 배포

ONNX([Open Neural Network Exchange](http://onnx.ai)) 형식은 Machine Learning 모델을 표현하기 위한 개방형 표준입니다. Microsoft를 비롯하여 호환 프레임워크와 도구를 만드는 [파트너 커뮤니티](http://onnx.ai/supported-tools)에서 ONNX를 지원합니다. Microsoft는 데이터 과학자와 개발자가 다음 작업을 수행할 수 있도록 개방형 상호 운용 가능 AI를 지원하기 위해 노력하고 있습니다.

+ 선택한 프레임워크를 사용하여 모델을 만들고 학습시키기
+ 최소한의 통합 작업만으로 플랫폼 간에 모델 배포

Microsoft는 사용자가 이러한 목표를 달성할 수 있도록 Azure와 Windows를 포함한 자사 제품에서 ONNX를 지원합니다.  

## <a name="why-choose-onnx"></a>ONNX를 선택해야 하는 이유
ONNX는 상호 운용성이 뛰어나므로 기발한 아이디어를 프로덕션 환경에서 더 빠르게 구현할 수 있습니다. 데이터 과학자는 ONNX를 통해 작업에서 원하는 프레임워크를 선택할 수 있습니다. 마찬가지로 개발자는 프로덕션 환경용으로 모델을 준비하는 시간을 줄이고 클라우드와 에지로 모델을 배포할 수 있습니다.  

PyTorch, Chainer, Microsoft Cognitive Toolkit(CNTK), MXNet, ML.Net 등의 여러 프레임워크에서 ONNX 모델을 내보낼 수 있습니다. TensorFlow, Keras, SciKit-Learn 등의 기타 프레임워크용 변환기도 제공됩니다.

ONNX 모델을 시각화하고 더 빠르게 작성하기 위한 도구 에코시스템도 갖춰져 있습니다. 일반 시나리오용으로 미리 학습된 여러 ONNX 모델도 제공됩니다.

Azure Machine Learning 및 ONNX Runtime을 사용하면 클라우드로 [ONNX 모델을 배포](#deploy)할 수 있습니다. [Windows ML](https://docs.microsoft.com/windows/ai/)을 사용해 Windows 10 장치로 ONNX 모델을 배포할 수도 있습니다. ONNX 커뮤니티에서 제공되는 변환기를 사용하면 다른 플랫폼으로도 배포가 가능합니다. 

[ ![학습, 변환기 및 배포를 보여 주는 ONNX 흐름 다이어그램](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="create-onnx-models-in-azure"></a>Azure에서 ONNX 모델 만들기

여러 가지 방법으로 ONNX 모델을 만들 수 있습니다.
+ Azure Machine Learning 서비스에서 모델을 학습시킨 다음 ONNX로 변환하거나 내보냅니다(이 문서 아래쪽의 예제 참조).

+ [ONNX Model Zoo](https://github.com/onnx/models)에서 미리 학습된 ONNX 모델을 가져옵니다.

+ [Azure Custom Vision 서비스](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)에서 사용자 지정된 ONNX 모델을 생성합니다.

## <a name="exportconvert-your-models-to-onnx"></a>ONNX로 모델 내보내기/변환

기존 모델을 ONNX로 변환할 수도 있습니다.

|모델에 대한 프레임워크|변환 예제 또는 도구|
|-----|-------|
|PyTorch|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;CNTK(Cognitive&nbsp;Toolkit&nbsp;)|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx 변환기](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Jupyter Notebook](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Learn, CoreML<br/>XGBoost 및 libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

지원되는 프레임워크 및 변환기의 최신 목록은 [ONNX 자습서 사이트](https://github.com/onnx/tutorials)에서 확인할 수 있습니다.

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Azure에서 ONNX 모델 배포

Azure Machine Learning 서비스를 사용하면 ONNX 모델을 배포, 관리 및 모니터링할 수 있습니다. 표준 [배포 워크플로](concept-model-management-and-deployment.md)와 ONNX Runtime을 사용하면 클라우드에서 호스트되는 REST 엔드포인트를 만들 수 있습니다. 이 문서 끝부분에 나와 있는 전체 예제 Jupyter Notebook을 참조하여 엔드포인트를 직접 만들어 보세요. 

### <a name="install-and-configure-the-onnx-runtime"></a>ONNX Runtime 설치 및 구성

ONNX 모델용 고성능 유추 엔진인 ONNX Runtime은 Python API를 포함하며 CPU와 GPU에서 모두 하드웨어 가속을 제공합니다. 현재 ONNX Runtime은 ONNX 1.2 모델을 지원하며 Ubuntu 16.04 Linux에서 실행됩니다. [CPU](https://pypi.org/project/onnxruntime) 및 [GPU](https://pypi.org/project/onnxruntime-gpu) 패키지 둘 다 [PyPi.org](https://pypi.org)에서 사용할 수 있습니다.

ONNX Runtime을 설치하려면 다음 코드를 사용합니다.
```python
pip install onnxruntime
```

Python 스크립트에서 ONNX Runtime을 호출하려면 다음 코드를 사용합니다.
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

전체 API 참조는 [ONNX 런타임 참조 문서](https://aka.ms/onnxruntime-python)를 참조하세요.

### <a name="example-deployment-steps"></a>예제 배포 단계

아래에는 ONNX 모델을 배포하는 예제가 나와 있습니다.

1. Azure Machine Learning 작업 영역을 초기화합니다. 아직 작업 영역이 없으면 [이 빠른 시작](quickstart-get-started.md)에서 작업 영역을 만드는 방법을 알아보세요.

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Azure Machine Learning에 모델을 등록합니다.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. 모델과 종속성이 포함된 이미지를 만듭니다.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   점수 매기기 논리가 들어 있는 `score.py` 파일을 이미지에 포함해야 합니다. 이 파일은 이미지에서 모델을 실행하는 데 사용됩니다. 점수 매기기 스크립트를 만드는 방법에 대한 지침은 이 [자습서](tutorial-deploy-models-with-aml.md#create-scoring-script)를 참조하세요. 아래에는 ONNX 모델의 예제 파일이 나와 있습니다.

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   `myenv.yml` 파일에는 이미지에 필요한 종속성에 대한 설명이 포함되어 있습니다. 이 샘플 파일과 같은 환경 파일을 만드는 방법에 대한 지침은 이 [자습서](tutorial-deploy-models-with-aml.md#create-environment-file)를 참조하세요.

   ```
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Azure Machine Learning을 사용하여 ONNX 모델을 배포합니다.
   + ACI(Azure Container Instances: [방법 배우기...](how-to-deploy-to-aci.md)

   + AKS(Azure Kubernetes Service): [방법 배우기...](how-to-deploy-to-aks.md)


## <a name="examples"></a>예
 
다음 Notebook은 Azure Machine Learning을 사용하여 ONNX 모델을 배포하는 방법을 보여 줍니다. 
+ `/onnx/onnx-inference-mnist.ipynb`
 
+ `/onnx/onnx-inference-emotion-recognition.ipynb`
 
이 Notebook을 다운로드하려면 다음 단계를 수행합니다.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>자세한 정보

ONNX에 대해 자세히 알아보거나 프로젝트에 참가합니다.
+ [ONNX 프로젝트 웹 사이트](http://onnx.ai)

+ [GitHub의 ONNX 코드](https://github.com/onnx/onnx)
