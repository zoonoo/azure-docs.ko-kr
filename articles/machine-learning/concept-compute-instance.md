---
title: Azure 기계 학습 계산 인스턴스란 무엇입니까?
titleSuffix: Azure Machine Learning
description: 완전히 관리되는 클라우드 기반 워크스테이션인 Azure 기계 학습 계산 인스턴스에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283928"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Azure 기계 학습 계산 인스턴스란 무엇입니까?

Azure 기계 학습 계산 인스턴스(미리 보기)는 데이터 과학자를 위한 완전히 관리되는 클라우드 기반 워크스테이션입니다. 

계산 인스턴스를 사용하면 Azure 기계 학습 개발을 쉽게 시작할 수 있으며 IT 관리자를 위한 관리 및 엔터프라이즈 준비 기능을 제공할 수 있습니다.  

컴퓨팅 인스턴스를 클라우드에서 완전히 구성되고 관리되는 개발 환경으로 사용합니다.

계산 인스턴스는 일반적으로 개발 환경으로 사용됩니다.  또한 개발 및 테스트를 위한 교육 및 추론을 위한 계산 대상으로사용할 수도 있습니다.  대규모 작업의 경우 다중 노드 크기 조정 기능이 있는 [Azure 기계 학습 계산 클러스터가](how-to-set-up-training-targets.md#amlcompute) 더 나은 계산 대상 선택입니다.


## <a name="why-use-a-compute-instance"></a>계산 인스턴스를 사용하는 이유는 무엇입니까?

계산 인스턴스는 기계 학습 개발 환경에 최적화된 완전히 관리되는 클라우드 기반 워크스테이션입니다. 가비지 수집은 다음과 같은 이점을 제공합니다.

|주요 이점||
|----|----|
|생산성|데이터 과학자는 통합 노트북과 다음 도구를 사용하여 모델을 웹 브라우저에서 빌드하고 배포할 수 있습니다.<br/>- 주피터<br/>- 주피터랩<br/>- RStudio|
|관리되는 & 보안|보안 공간을 줄이고 엔터프라이즈 보안 요구 사항을 준수할 수 있습니다. 계산 인스턴스는 다음과 같은 강력한 관리 정책과 보안 네트워킹 구성을 제공합니다.<br/><br/>- 리소스 관리자 템플릿 또는 Azure 기계 학습 SDK에서 자동 프로비전<br/>- [역할 기반 액세스 제어(RBAC)](/azure/role-based-access-control/overview)<br/>- [가상 네트워크 지원](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH 액세스를 활성화/비활성화하는 SSH 정책|
|미리 구성된&nbsp;&nbsp;또는 ML|사전 구성된 최신 ML 패키지, 딥 러닝 프레임워크, GPU 드라이버로 설치 작업에 시간을 절약할 수 있습니다.|
|완벽하게 사용자 정의 가능|GPU를 비롯한 Azure VM 형식에 대한 광범위한 지원과 패키지 및 드라이버 설치와 같은 지속적인 하위 수준 사용자 지정은 고급 시나리오를 간편하게 만듭니다. |

## <a name="tools-and-environments"></a><a name="contents"></a>도구 및 환경

Azure 기계 학습 계산 인스턴스를 사용하면 작업 공간에서 완전히 통합된 전자 필기장 환경에서 모델을 작성, 학습 및 배포할 수 있습니다.


이러한 도구 및 환경은 계산 인스턴스에 설치됩니다. 

|환경에 & 일반적인 도구|세부 정보|
|----|:----:|
|드라이버|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|인텔 MPI 라이브러리||
|Azure CLI ||
|Azure 기계 학습 샘플 ||
|Azure 기계 학습 EDAT 엔진 ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|프로토부프|| 

|**R** 도구 & 환경|세부 정보|
|----|:----:|
|RStudio 서버 오픈 소스 에디션||
|R 커널||
|R용 Azure 기계 학습 SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 샘플|

|**파이썬** 도구 & 환경|세부 정보|
|----|----|
|Anaconda Python||
|주피터 및 확장||
|주피터랩 및 확장||
|Visual Studio Code ||
[파이썬을 위한 Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>PyPI에서|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|기타 PyPI 패키지|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|콘다 패키지|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|딥 러닝 패키지|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 패키지|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure 기계 학습 파이썬 & R SDK 샘플||

파이썬 패키지는 모두 **파이썬 3.6 - AzureML** 환경에 설치됩니다.  

계산 인스턴스는 일반적으로 개발 환경으로 사용됩니다.  또한 개발 및 테스트를 위한 교육 및 추론을 위한 계산 대상으로사용할 수도 있습니다.  대규모 작업의 경우 다중 노드 크기 조정 기능이 있는 [Azure 기계 학습 계산 클러스터가](how-to-set-up-training-targets.md#amlcompute) 더 나은 계산 대상 선택입니다.

### <a name="installing-packages"></a>패키지 설치

Jupyter 노트북 또는 Rstudio에 직접 패키지를 설치할 수 있습니다.

* RStudio 오른쪽 하단의 **패키지** 탭 또는 왼쪽 상단의 **콘솔** 탭을 사용합니다.  
* 파이썬: 설치 코드를 추가하고 Jupyter 노트북 셀에서 실행합니다.

또는 다음과 같은 방법으로 터미널 창에 액세스할 수 있습니다.

* RStudio: 왼쪽 상단에 있는 **터미널** 탭을 선택합니다.
* Jupyter Lab: 런처 탭의 **다른** 제목 아래에 있는 **터미널** 타일을 선택합니다.
* Jupyter: 파일 탭에서 오른쪽 상단의 **새>터미널을** 선택합니다.
* SSH를 컴퓨터에.  그런 다음 파이썬 패키지를 **파이썬 3.6 - AzureML** 환경에 설치합니다.  R 패키지를 **R** 환경에 설치합니다.

## <a name="accessing-files"></a>파일에 액세스

전자 필기장 및 R 스크립트는 Azure 파일 공유에서 작업 영역의 기본 저장소 계정에 저장됩니다.  이러한 파일은 "사용자 파일" 디렉토리 아래에 있습니다. 이 스토리지를 사용하면 계산 인스턴스 간에 노트북을 쉽게 공유할 수 있습니다. 또한 저장소 계정은 계산 인스턴스를 중지하거나 삭제할 때 노트북을 안전하게 보존합니다.

작업 영역의 Azure 파일 공유 계정은 계산 인스턴스의 드라이브로 탑재됩니다. 이 드라이브는 Jupyter, Jupyter Labs 및 RStudio의 기본 작업 디렉토리입니다.

파일 공유의 파일은 동일한 작업 공간의 모든 계산 인스턴스에서 액세스할 수 있습니다. 계산 인스턴스에서 이러한 파일에 대한 모든 변경 내용은 파일 공유로 안정적으로 유지됩니다.

작업 영역 파일 공유의 사용자 파일 디렉터리 아래의 폴더에 최신 Azure 기계 학습 샘플을 복제할 수도 있습니다.

네트워크 드라이브에서 작은 파일을 작성하는 것이 VM 자체에 쓰는 것보다 느려질 수 있습니다.  많은 작은 파일을 작성하는 경우 디렉터리와 같은 계산 인스턴스에서 직접 `/tmp` 디렉터리를 사용해 보십시오. 이러한 파일은 작업 영역의 다른 계산 인스턴스에서 액세스할 수 없습니다.

## <a name="managing-a-compute-instance"></a>계산 인스턴스 관리

Azure 기계 학습 스튜디오의 작업 영역에서 **계산을**선택한 다음 맨 위에 있는 **인스턴스 계산을** 선택합니다.

![계산 인스턴스 관리](./media/concept-compute-instance/manage-compute-instance.png)

다음 동작을 수행할 수 있습니다.

* 컴퓨팅 인스턴스를 만듭니다. GPU를 포함한 Azure VM 유형(생성 후 VM 유형을 변경할 수 없음), SSH 액세스를 활성화/비활성화하고, 가상 네트워크 설정을 선택적으로 구성합니다. 통합 된 노트북, Azure 포털, 리소스 관리자 템플릿 또는 Azure 기계 학습 SDK에서 직접 인스턴스를 만들 수도 있습니다. 인스턴스 만들기 계산에 적용되는 지역별 전용 코어는 Azure Machine Learning 계산 클러스터 할당량과 통합되고 공유됩니다.
* 계산 인스턴스 탭 새로 고침
* 계산 인스턴스 시작, 중지 및 다시 시작
* 계산 인스턴스 삭제

작업 공간의 각 계산 인스턴스에 대해 다음을 수행할 수 있습니다.

* 액세스 주피터, 주피터랩, RStudio 온 컴퓨팅 인스턴스
* SSH를 계산 인스턴스로 변환합니다. SSH 액세스는 기본적으로 비활성화되어 있지만 계산 인스턴스 생성 시간에 사용할 수 있습니다. SSH 액세스는 공개/개인 키 메커니즘을 통해 전송됩니다. 이 탭은 IP 주소, 사용자 이름 및 포트 번호와 같은 SSH 연결에 대한 세부 정보를 제공합니다.
* IP 주소 및 지역과 같은 특정 계산 인스턴스에 대한 세부 정보를 가져옵니다.

[RBAC를](/azure/role-based-access-control/overview) 사용하면 작업 영역에서 계산 인스턴스를 생성, 삭제, 시작, 중지, 다시 시작할 수 있는 사용자를 제어할 수 있습니다. 작업 영역 기여자 및 소유자 역할의 모든 사용자는 작업 영역 전체에서 계산 인스턴스를 생성, 삭제, 시작, 중지 및 다시 시작할 수 있습니다. 그러나 특정 계산 인스턴스의 작성자만 해당 계산 인스턴스에서 Jupyter, JupyterLab 및 RStudio에 액세스할 수 있습니다. 계산 인스턴스의 작성자는 계산 인스턴스전용 인스턴스를 가지고 있으며 루트 액세스 권한을 가지며 Jupyter를 통해 터미널에 들어갈 수 있습니다. 계산 인스턴스에는 작성자 사용자의 단일 사용자 로그인이 있으며 모든 작업은 RBAC 및 실험 실행의 기여에 해당 사용자의 ID를 사용합니다. SSH 액세스는 공개/개인 키 메커니즘을 통해 제어됩니다.

인스턴스를 만들 수도 있습니다.
* 통합 된 노트북 경험에서 직접
* Azure Portal에서
* Azure 리소스 관리자 템플릿에서
* Azure 기계 학습 SDK

인스턴스 생성 계산에 적용되는 지역별 전용 코어는 통합되고 Azure Machine Learning 학습 학습 학습 클러스터 할당량과 공유됩니다. 

## <a name="compute-target"></a>컴퓨팅 대상

계산 인스턴스는 Azure 기계 학습 계산 학습 학습 클러스터와 유사한 [학습 계산 대상으로](concept-compute-target.md#train) 사용할 수 있습니다. TensorFlow/PyTorch 추정기를 사용하여 분산 교육 작업을 실행하도록 다중 GPU VM을 프로비전합니다. 실행 구성을 만들고 이를 사용하여 계산 인스턴스에서 실험을 실행할 수도 있습니다. 계산 인스턴스를 테스트/디버깅 시나리오에 대한 로컬 추론 배포 대상으로 사용할 수 있습니다.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>노트북 VM은 어떻게 되었습니까?

계산 인스턴스가 노트북 VM을 대체하고 있습니다.  

작업 영역 파일 공유및 작업 영역 데이터 저장소에 저장된 모든 전자 필기장 파일은 계산 인스턴스에서 액세스할 수 있습니다. 그러나 이전에 노트북 VM에 설치한 사용자 지정 패키지는 계산 인스턴스에 다시 설치해야 합니다. 계산 클러스터 생성에 적용되는 할당량 제한은 인스턴스 생성 계산에도 적용됩니다. 

새 전자 필기장 VM을 만들 수 없습니다. 그러나 모든 기능을 사용하여 만든 노트북 VM에 계속 액세스하고 사용할 수 있습니다. 계산 인스턴스는 기존 노트북 VM과 동일한 작업 공간에서 만들 수 있습니다. 


## <a name="next-steps"></a>다음 단계

 * [자습서: 첫 번째 ML 모델을 학습하면](tutorial-1st-experiment-sdk-train.md) 통합 된 노트북을 사용하여 계산 인스턴스를 사용하는 방법을 보여 줍니다.
