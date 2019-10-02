---
title: 심층 학습 & AI 프레임 워크
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine에서 사용할 수 있는 심층 학습 프레임 워크 및 도구입니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: fd38bf1f7741c4d610ef43a12d90533d4ac7b703
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802422"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Azure Data Science VM에 대 한 심층 학습 및 AI 프레임 워크
DSVM의 심층 학습 프레임 워크는 아래에 나열 되어 있습니다.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | |
| 지원 되는 DSVM 버전      | Linux(Ubuntu)     |
| DSVM에 구성/설치 방법  | Caffe는 `/opt/caffe`에 설치됩니다.   샘플은에 `/opt/caffe/examples`있습니다.|
| 실행 방법      | X2Go를 사용 하 여 VM에 로그인 한 다음 새 터미널을 시작 하 고 다음을 입력 합니다.<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>새 브라우저 창이 샘플 노트북과 함께 열립니다. 이진 파일은 /opt/caffe/build/install/bin에 설치됩니다.<br/><br/>설치 된 Caffe 버전에는 Python 2.7이 필요 하며, 기본적으로 활성화 되는 Python 3.5에서는 작동 하지 않습니다. Python 2.7로 전환 하려면를 실행 `source activate root` 하 여 Anaconda 환경으로 전환 합니다.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | |
| 지원 되는 DSVM 버전      | Linux(Ubuntu)     |
| DSVM에 구성/설치 방법  | Caffe2는 [Python 2.7 (root) conda 환경에 설치 됩니다. |
| 실행 방법      | 터미널과 Python을 시작 하 고 Caffe2를 가져옵니다. <br/> JupyterHub [JupyterHub에 연결한](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)다음 Caffe2 디렉터리로 이동 하 여 샘플 노트북을 찾습니다. 일부 노트북은 Python 코드에 Caffe2 루트를 설정해야 합니다; enter /opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 5.2 |
| 지원 되는 DSVM 버전      | Linux(Ubuntu)     |
| DSVM에 구성/설치 방법  | 체 이너는 Python 3.5에 설치 됩니다. |
| 실행 방법      | 터미널과 Python 3.5 환경을 활성화 하 고 `python`을 실행 한 다음-1을 @no__t 합니다. <br/> JupyterHub [JupyterHub에 연결한](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)다음, 체 이너 디렉터리로 이동 하 여 샘플 노트북을 찾습니다.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CuDNN, NVIDIA 드라이버](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 10.0.130|
| 지원 되는 DSVM 버전      | Windows 및 Linux   |
| DSVM에 구성/설치 방법  |_nvidia smi_는 시스템 경로에서 사용할 수 있습니다.  |
| 실행 방법      | 명령 프롬프트 (Windows) 또는 터미널 (Linux)을 연 다음 _nvidia-smi-s_를 실행 합니다. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 0.16.1|
| 지원 되는 DSVM 버전      | Linux(Ubuntu)   |
| DSVM에 구성/설치 방법  | Horovod는 Python 3.5에 설치 됩니다. |
| 실행 방법      | 터미널에서 올바른 환경을 활성화 한 다음 Python을 실행 합니다. |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 2.2.4 |
| 지원 되는 DSVM 버전      | Windows 및 Linux   |
| DSVM에 구성/설치 방법  | Keras는 Windows의 Python 3.6 및 Linux의 Python 3.5에 설치 됩니다. |
| 실행 방법      | 터미널에서 올바른 환경을 활성화 한 다음 Python을 실행 합니다. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 2.5.1 |
| 지원 되는 DSVM 버전      | Windows 및 Linux   |
| DSVM에 구성/설치 방법  | CNTK는 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 및 [Linux](./dsvm-languages.md#python-linux-edition)의 python 3.5에서 python 3.6에 설치 됩니다. |
| 실행 방법      | 터미널과 올바른 환경을 활성화 하 고 Python을 실행 합니다. <br/>Jupyter [Jupyter](provision-vm.md) 또는 [Jupyterhub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)에 연결한 다음 샘플에 대 한 CNTK 디렉터리를 엽니다. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 1.2.0 |
| 지원 되는 DSVM 버전      | Linux |
| DSVM에 구성/설치 방법  | [Python 3.5](dsvm-languages.md#python-linux-edition)에 설치 됩니다. 샘플 Jupyter 노트북이 포함 되어 있고 샘플이/dsvm/samples/pytorch.에 있습니다. |
| 실행 방법      | 터미널과 올바른 환경을 활성화 한 다음 Python을 실행 합니다.<br/>* [Jupyterhub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): 연결을 클릭 한 다음 샘플에 대 한 PyTorch 디렉터리를 엽니다.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[Tensorflow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 1.13 |
| 지원 되는 DSVM 버전      | Windows, Linux |
| DSVM에 구성/설치 방법  | [Linux](dsvm-languages.md#python-linux-edition) 의 python 3.5에 설치 된 및 Windows의 python 3.6 [2016](dsvm-languages.md#python-windows-server-2016-edition) |
| 실행 방법      | 터미널과 올바른 환경을 활성화 한 다음 Python을 실행 합니다. <br/> Jupyter [Jupyter](provision-vm.md) 또는 [Jupyterhub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)에 연결한 다음 샘플에 대 한 TensorFlow 디렉터리를 엽니다.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow 서비스](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 1.12 |
| 지원 되는 DSVM 버전      | Linux |
| DSVM에 구성/설치 방법  | tensorflow_model_server는 터미널에서 사용할 수 있습니다. |
| 실행 방법      |  샘플은 [온라인](https://www.tensorflow.org/serving/)으로 제공됩니다.   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| 지원 되는 버전 | 1.0.3 |
| 지원 되는 DSVM 버전      | Linux |
| DSVM에 구성/설치 방법  |Theano는 Python 2.7 (_root_) 및 python 3.5 (_py35_) 환경에 설치 됩니다. |
| 실행 방법      |  터미널과 원하는 Python 버전 (root 또는 py35)을 활성화 하 고 Python을 실행 한 다음 Theano를 가져옵니다.<br/>Jupyter Python 2.7 또는 3.5 커널을 선택한 다음 Theano를 가져옵니다.  <br/>최신 MKL MKL (math kernel library) 버그를 해결 하려면 먼저 다음과 같이 MKL 스레딩 계층을 설정 해야 합니다.<br/><br/>`export MKL_THREADING_LAYER=GNU`  |