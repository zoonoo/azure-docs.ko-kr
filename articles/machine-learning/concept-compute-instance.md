---
title: Azure Machine Learning 컴퓨팅 인스턴스란?
titleSuffix: Azure Machine Learning
description: 완전 관리형 클라우드 기반 워크스테이션인 Azure Machine Learning 컴퓨팅 인스턴스에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 8c03df8fb0cd8f5f092450ebe4c66266d2ff4293
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816355"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning 컴퓨팅 인스턴스란?

Azure Machine Learning 컴퓨팅 인스턴스(미리 보기)는 데이터 과학자를 위한 완전 관리형 클라우드 기반 워크스테이션입니다. 

컴퓨팅 인스턴스를 사용하면 Azure Machine Learning 개발을 손쉽게 시작할 수 있을 뿐만 아니라 IT 관리자에게 관리 및 엔터프라이즈 준비 기능을 제공할 수 있습니다.  

클라우드에서 완전 구성 및 관리형 개발 환경으로 컴퓨팅 인스턴스를 사용합니다.

컴퓨팅 인스턴스는 일반적으로 개발 환경으로 사용됩니다.  개발 및 테스트를 위한 학습 및 추론을 위한 컴퓨팅 대상으로 사용할 수도 있습니다.  대량 작업의 경우 다중 노드 크기 조정 기능이 있는 [Azure Machine Learning 컴퓨팅 클러스터](how-to-set-up-training-targets.md#amlcompute)를 컴퓨팅 대상으로 선택하는 것이 더 바람직합니다.


## <a name="why-use-a-compute-instance"></a>컴퓨팅 인스턴스를 사용하는 이유

컴퓨팅 인스턴스는 기계 학습 개발 환경에 최적화된 완전 관리형 클라우드 기반 워크스테이션입니다. 다음과 같은 이점을 제공합니다.

|주요 이점||
|----|----|
|생산성|데이터 과학자는 통합된 Notebook 및 웹 브라우저에서 다음 도구를 사용하여 모델을 빌드 및 배포할 수 있습니다.<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio|
|관리 및 보안|보안 공간을 줄이고 엔터프라이즈 보안 요구 사항에 따라 규정 준수를 추가합니다. 컴퓨팅 인스턴스는 다음과 같은 강력한 관리 정책과 보안 네트워킹 구성을 제공합니다.<br/><br/>- Resource Manager 템플릿 또는 Azure Machine Learning SDK에서 자동 프로비저닝<br/>- [RBAC(역할 기반 액세스 제어)](/azure/role-based-access-control/overview)<br/>- [가상 네트워크 지원](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH 액세스를 사용하거나 사용하지 않도록 설정하는 SSH 정책|
|사전 구성 &nbsp;또는&nbsp; ML|사전 구성된 최신 ML 패키지, 딥 러닝 프레임워크 및 GPU 드라이버를 통해 설치 작업에 드는 시간을 절약할 수 있습니다.|
|완전한 사용자 지정 기능|GPU를 비롯한 Azure VM 형식에 대한 광범위한 지원과 패키지 및 드라이버 설치와 같은 지속형의 하위 수준 사용자 지정을 통해 고급 시나리오를 간편하게 만들 수 있습니다. |

## <a name="tools-and-environments"></a><a name="contents"></a>도구 및 환경

Azure Machine Learning 컴퓨팅 인스턴스를 사용하면 작업 영역의 완전 통합형 Notebook 환경에서 모델을 작성, 학습 및 배포할 수 있습니다.


다음과 같은 도구 및 환경은 컴퓨팅 인스턴스에 설치됩니다. 

|일반적인 도구 및 환경|세부 정보|
|----|:----:|
|드라이버|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI 라이브러리||
|Azure CLI ||
|Azure Machine Learning 샘플 ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R** 도구 및 환경|세부 정보|
|----|:----:|
|RStudio Server 오픈 소스 버전||
|R 커널||
|R용 Azure Machine Learning SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 샘플|

|**PYTHON** 도구 및 환경|세부 정보|
|----|----|
|Anaconda Python||
|Jupyter 및 확장||
|Jupyterlab 및 확장||
[Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>PyPI에서|대부분의 azureml 추가 패키지가 포함됩니다.  전체 목록을 보려면 [컴퓨팅 인스턴스에서 터미널 창을 열고](how-to-run-jupyter-notebooks.md#terminal) 다음을 실행합니다. <br/> `conda list -n azureml_py36 azureml*` |
|기타 PyPI 패키지|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 패키지|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|딥 러닝 패키지|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 패키지|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python 및 R SDK 샘플||

Python 패키지는 **Python 3.6 - AzureML** 환경에 모두 설치됩니다.  

컴퓨팅 인스턴스는 일반적으로 개발 환경으로 사용됩니다.  개발 및 테스트를 위한 학습 및 추론을 위한 컴퓨팅 대상으로 사용할 수도 있습니다.  대량 작업의 경우 다중 노드 크기 조정 기능이 있는 [Azure Machine Learning 컴퓨팅 클러스터](how-to-set-up-training-targets.md#amlcompute)를 컴퓨팅 대상으로 선택하는 것이 더 바람직합니다.

### <a name="installing-packages"></a>패키지 설치

Jupyter Notebook 또는 Rstudio에 직접 패키지를 설치할 수 있습니다.

* RStudio는 오른쪽 아래에 있는 **패키지** 탭 또는 왼쪽 위의 **콘솔** 탭을 사용합니다.  
* Python: 설치 코드를 추가하고 Jupyter Notebook 셀에서 실행합니다.

또는 다음과 같은 방법으로 터미널 창에 액세스할 수 있습니다.

* RStudio: 왼쪽 위에서 **터미널** 탭을 선택합니다.
* Jupyter Lab:  시작 관리자 탭의 **기타** 제목 아래에 있는 **터미널** 타일을 선택합니다.
* Jupyter:  파일 탭에서 오른쪽 위에 있는 **새로 만들기 > 터미널**을 선택합니다.
* SSH를 컴퓨터에 실행합니다.  그런 다음, Python 패키지를 **Python 3.6 - AzureML** 환경에 설치합니다.  **R** 환경에 R 패키지를 설치합니다.

## <a name="accessing-files"></a>파일에 액세스

Notebook 및 R 스크립트는 Azure 파일 공유에서 작업 영역의 기본 스토리지 계정에 저장됩니다.  이러한 파일은 “사용자 파일” 디렉터리 아래에 있습니다. 이 스토리지를 사용하면 컴퓨팅 인스턴스 간에 Notebook을 쉽게 공유할 수 있습니다. 스토리지 계정은 컴퓨팅 인스턴스를 중지하거나 삭제하는 경우에도 Notebook을 안전하게 유지합니다.

작업 영역의 Azure 파일 공유 계정은 컴퓨팅 인스턴스에 드라이브로 탑재됩니다. 이 드라이브는 Jupyter, Jupyter Labs 및 RStudio의 기본 작업 디렉터리입니다.

파일 공유의 파일은 동일한 작업 영역의 모든 컴퓨팅 인스턴스에서 액세스할 수 있습니다. 컴퓨팅 인스턴스에서 이러한 파일에 대한 모든 변경 내용은 안정적으로 파일 공유에 다시 저장됩니다.

또한 작업 영역 파일 공유의 사용자 파일 디렉터리 아래에 있는 폴더에 최신 Azure Machine Learning 샘플을 복제할 수 있습니다.

네트워크 드라이브에서 작은 파일 작성은 VM 자체에 작성하는 것에 비해 속도가 느릴 수 있습니다.  많은 작은 파일을 작성하는 경우 `/tmp` 디렉터리와 같이 컴퓨팅 인스턴스에서 직접 디렉터리를 사용해보세요. 이러한 파일은 작업 영역의 다른 컴퓨팅 인스턴스에서는 액세스할 수 없습니다.

## <a name="managing-a-compute-instance"></a>컴퓨팅 인스턴스 관리

Azure Machine Learning Studio의 작업 영역에서 **컴퓨팅**을 선택한 다음, 맨 위에 있는 **컴퓨팅 인스턴스**를 선택합니다.

![컴퓨팅 인스턴스 관리](./media/concept-compute-instance/manage-compute-instance.png)

다음 작업을 수행할 수 있습니다.

* 컴퓨팅 인스턴스를 만듭니다. 이름을 지정하고, GPU를 포함한 Azure VM 형식(VM 형식은 만든 후에 변경할 수 없음)을 지정하고, SSH 액세스를 사용/사용하지 않도록 설정하고, 선택적으로 가상 네트워크 설정을 구성합니다. 통합 Notebook, Azure Portal, Resource Manager 템플릿 또는 Azure Machine Learning SDK에서 직접 인스턴스를 만들 수도 있습니다. 컴퓨팅 인스턴스 생성에 적용되는 지역당 전용 코어 할당량은 통합되어 Azure Machine Learning 컴퓨팅 클러스터 할당량과 공유됩니다.
* 컴퓨팅 인스턴스 탭 새로 고침
* 컴퓨팅 인스턴스를 시작, 중지 및 다시 시작합니다. 비용을 줄이기 위해 사용하지 않을 때는 VM을 중지합니다. 그런 다음, 필요할 때 다시 시작합니다.
* 컴퓨팅 인스턴스 삭제

작업 영역의 각 컴퓨팅 인스턴스에 대해 다음을 수행할 수 있습니다.

* 컴퓨팅 인스턴스에서 Jupyter, JupyterLab, RStudio에 액세스합니다.
* SSH를 컴퓨팅 인스턴스로 실행합니다. SSH 액세스는 기본적으로 사용하지 않도록 설정되어 있지만 컴퓨팅 인스턴스 생성 시 사용하도록 설정할 수 있습니다. SSH 액세스에는 공개/프라이빗 키 메커니즘이 사용됩니다. 탭에 IP 주소, 사용자 이름 및 포트 번호와 같은 SSH 연결에 대한 세부 정보가 제공됩니다.
* 특정 컴퓨팅 인스턴스에 대한 세부 정보(예: IP 주소 및 지역)를 가져옵니다.

[RBAC](/azure/role-based-access-control/overview)를 사용하면 작업 영역에서 컴퓨팅 인스턴스를 만들고, 삭제, 시작, 중지 및 다시 시작할 수 있는 사용자를 제어할 수 있습니다. 작업 영역 기여자 및 소유자 역할의 모든 사용자는 작업 영역에서 컴퓨팅 인스턴스를 만들고, 삭제, 시작, 중지 및 다시 시작할 수 있습니다. 그러나 특정 컴퓨팅 인스턴스의 작성자만 해당 컴퓨팅 인스턴스의 Jupyter, JupyterLab 및 RStudio에 액세스할 수 있습니다. 컴퓨팅 인스턴스의 작성자에게는 전용 컴퓨팅 인스턴스가 있고, 루트 액세스 권한이 있으며, Jupyter를 통해 터미널을 사용할 수 있습니다. 컴퓨팅 인스턴스는 작성자의 단일 사용자 로그인을 사용하며, 모든 작업에는 RBAC에 대한 해당 사용자의 ID 및 실험 실행의 특성이 사용됩니다. SSH 액세스는 공개/프라이빗 키 메커니즘을 통해 제어됩니다.

인스턴스를 만들 수도 있습니다.
* 통합 Notebook 환경에서 직접
* Azure Portal에서
* Azure Resource Manager 템플릿에서
* Azure Machine Learning SDK 사용

컴퓨팅 인스턴스 생성에 적용되는 지역당 전용 코어 할당량은 통합되어 Azure Machine Learning 학습 클러스터 할당량과 공유됩니다. 

## <a name="compute-target"></a>컴퓨팅 대상

컴퓨팅 인스턴스는 Azure Machine Learning 컴퓨팅 학습 클러스터와 유사하게 [학습 컴퓨팅 대상](concept-compute-target.md#train)으로 사용할 수 있습니다. TensorFlow/PyTorch 예측 도구를 사용하여 분산 학습 작업을 실행할 다중 GPU VM을 프로비저닝합니다. 실행 구성을 만들고 이를 사용하여 컴퓨팅 인스턴스에서 실험을 실행할 수도 있습니다. 컴퓨팅 인스턴스는 테스트/디버깅 시나리오에 대한 로컬 추론 배포 대상으로 사용할 수 있습니다.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Notebook VM의 변경 내용

컴퓨팅 인스턴스가 Notebook VM을 대체하고 있습니다.  

작업 영역 파일 공유에 저장된 모든 Notebook 파일과 작업 영역 데이터 저장소의 데이터는 컴퓨팅 인스턴스에서 액세스할 수 있습니다. 그러나 Notebook VM에 이전에 설치된 모든 사용자 지정 패키지는 컴퓨팅 인스턴스에 다시 설치해야 합니다. 컴퓨팅 클러스터 생성에 적용되는 할당량 제한은 컴퓨팅 인스턴스 생성에도 적용됩니다. 

새 Notebook VM을 만들 수 없습니다. 그러나 전체 기능을 사용하여 만든 Notebook VM에는 계속 액세스하여 사용할 수 있습니다. 컴퓨팅 인스턴스를 기존 Notebook VM과 동일한 작업 영역에 만들 수 있습니다. 


## <a name="next-steps"></a>다음 단계

 * [자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md)에서는 통합 Notebook으로 컴퓨팅 인스턴스를 사용하는 방법을 보여 줍니다.
