---
title: 딥 러닝 & AI 프레임워크
titleSuffix: Azure Data Science Virtual Machine
description: Azure 데이터 과학 가상 머신에서 사용할 수 있는 딥 러닝 프레임워크 및 도구입니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270070"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Azure 데이터 과학 VM을 위한 딥 러닝 및 AI 프레임워크
DSVM의 딥 러닝 프레임워크는 다음과 같습니다.

## <a name="caffe"></a>[카페](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | |
| 지원되는 DSVM 에디션      | Linux(Ubuntu)     |
| DSVM에 구성/설치 방법  | Caffe는 `/opt/caffe`에 설치됩니다.   샘플은 에 `/opt/caffe/examples`있습니다.|
| 그것을 실행하는 방법      | X2Go를 사용하여 VM에 로그인한 다음 새 터미널을 시작하고 다음을 입력합니다.<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>새 브라우저 창이 샘플 노트북과 함께 열립니다. 이진 파일은 /opt/caffe/build/install/bin에 설치됩니다.<br/><br/>Caffe의 설치 버전은 파이썬 2.7이 필요하며 기본적으로 활성화된 파이썬 3.5에서는 작동하지 않습니다. 파이썬 2.7로 전환하려면 아나콘다 `source activate root` 환경으로 전환하십시오.|    

## <a name="caffe2"></a>[카페2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | |
| 지원되는 DSVM 에디션      | Linux(Ubuntu)     |
| DSVM에 구성/설치 방법  | Caffe2는 [파이썬 2.7 (루트) 콘다 환경에 설치됩니다. |
| 그것을 실행하는 방법      | 터미널: 파이썬을 시작하고 Caffe2를 가져옵니다. <br/> * JupyterHub: [JupyterHub에 연결한](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)다음 Caffe2 디렉토리로 이동하여 샘플 노트북을 찾습니다. 일부 노트북은 Python 코드에 Caffe2 루트를 설정해야 합니다; enter /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 5.2 |
| 지원되는 DSVM 에디션      | Linux(Ubuntu)     |
| DSVM에 구성/설치 방법  | Chainer는 Python 3.5에 설치됩니다. |
| 그것을 실행하는 방법      | 터미널: 파이썬 3.5 환경을 `python`활성화한 `import chainer`다음 실행한 다음 . <br/> * JupyterHub: [JupyterHub에 연결한](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)다음 Chainer 디렉토리로 이동하여 샘플 노트북을 찾습니다.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, NVIDIA 드라이버](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 10.0.130|
| 지원되는 DSVM 에디션      | Windows 및 Linux   |
| DSVM에 구성/설치 방법  |_nvidia smi_는 시스템 경로에서 사용할 수 있습니다.  |
| 그것을 실행하는 방법      | 명령 프롬프트 (Windows에서) 또는 터미널 (리눅스에서)을 열고 _엔비디아 스미를_실행합니다. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 0.16.1|
| 지원되는 DSVM 에디션      | Linux(Ubuntu)   |
| DSVM에 구성/설치 방법  | 호로보드는 파이썬 3.5에 설치됩니다. |
| 그것을 실행하는 방법      | 터미널에서 올바른 환경을 활성화한 다음 파이썬을 실행합니다. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 2.2.4 |
| 지원되는 DSVM 에디션      | Windows 및 Linux   |
| DSVM에 구성/설치 방법  | 케라스는 파이썬에 설치 3.6 윈도우와 파이썬에 3.5 리눅스에서 |
| 그것을 실행하는 방법      | 터미널에서 올바른 환경을 활성화한 다음 파이썬을 실행합니다. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit(CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 2.5.1 |
| 지원되는 DSVM 에디션      | Windows 및 Linux   |
| DSVM에 구성/설치 방법  | CNTK는 [윈도우 2016에서](dsvm-tools-languages.md#python-windows-server-2016-edition) 파이썬 3.6에 설치되고 [리눅스에서](./dsvm-tools-languages.md#python-linux-edition)파이썬 3.5에 설치 ) |
| 그것을 실행하는 방법      | 터미널: 올바른 환경을 활성화하고 파이썬을 실행합니다. <br/>Jupyter: [Jupyter](provision-vm.md) 또는 [JupyterHub에](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)연결한 다음 샘플에 대한 CNTK 디렉토리를 엽니다. |

## <a name="mxnet"></a>[MX넷](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 1.3.0 |
| 지원되는 DSVM 에디션      | Windows 및 Linux   |
| DSVM에 구성/설치 방법  | MXNet은 윈도우와 `C:\dsvm\tools\mxnet` `/dsvm/tools/mxnet` 우분투에 설치됩니다. 파이썬 바인딩은 [윈도우 2016의](dsvm-tools-languages.md#python-windows-server-2016-edition) 파이썬 3.6에 설치되고 파이썬 3.5에서 [리눅스에서](./dsvm-tools-languages.md#python-linux-edition)) R 바인딩은 우분투 DSVM에도 포함되어 있습니다. |
| 그것을 실행하는 방법      | 터미널: 올바른 conda 환경을 활성화한 다음 실행합니다. `import mxnet` <br/>Jupyter: [Jupyter](provision-vm.md#access-the-dsvm) 또는 [JupyterHub에](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)연결한 `mxnet` 다음 샘플에 대한 디렉토리를 엽니다. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 1.0.1 |
| 지원되는 DSVM 에디션      | Windows 및 Linux   |
| DSVM에 구성/설치 방법  | MXNet 모델 서버는 파이썬에 설치 3.6 [윈도우2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 과 파이썬3.5 [리눅스에서)](./dsvm-tools-languages.md#python-linux-edition) |
| 그것을 실행하는 방법      | 터미널: `sudo systemctl stop jupyterhub` 둘 다 같은 포트에서 수신을 듣기 때문에 JupyterHub 서비스를 먼저 중지하려면 실행합니다. 그런 다음 올바른 conda 환경을 활성화하고 실행합니다.`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[엔비디아 시스템 관리 인터페이스 (엔비디아 스미)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 |  |
| 지원되는 DSVM 에디션      | Windows 및 Linux   |
| 그것은 무엇을 위해 무엇입니까? | GPU 작업을 쿼리하기 위한 NVIDIA 도구 |
| DSVM에 구성/설치 방법  | `nvidia-smi`시스템 경로에 있습니다. |
| 그것을 실행하는 방법      | **GPU가 있는**가상 컴퓨터에서 는 명령 프롬프트(Windows) 또는 터미널(Linux)을 `nvidia-smi`연 다음 실행합니다. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 1.2.0 (우분투 16.04, 윈도우 2016), 1.4.0 (우분투 18.04, 윈도우 2019) |
| 지원되는 DSVM 에디션      | Linux |
| DSVM에 구성/설치 방법  | [파이썬 3.5에](dsvm-tools-languages.md#python-linux-edition)설치 . 샘플 Jupyter 노트북이 포함되어 있으며 샘플은 /dsvm/샘플/피토치에 있습니다. |
| 그것을 실행하는 방법      | 터미널: 올바른 환경을 활성화한 다음 파이썬을 실행합니다.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): 연결한 다음 샘플에 대한 PyTorch 디렉토리를 엽니다.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 1.13 |
| 지원되는 DSVM 에디션      | Windows, Linux |
| DSVM에 구성/설치 방법  | 윈도우 [2016에서](dsvm-tools-languages.md#python-windows-server-2016-edition) [리눅스와](dsvm-tools-languages.md#python-linux-edition) 파이썬 3.6에 파이썬 3.5에 설치 |
| 그것을 실행하는 방법      | 터미널: 올바른 환경을 활성화한 다음 파이썬을 실행합니다. <br/> * Jupyter: [Jupyter](provision-vm.md) 또는 [JupyterHub에](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)연결한 다음 샘플에 대한 텐서플로우 디렉토리를 엽니다.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 1.12 |
| 지원되는 DSVM 에디션      | Linux |
| DSVM에 구성/설치 방법  | tensorflow_model_server는 터미널에서 제공됩니다. |
| 그것을 실행하는 방법      |  샘플은 [온라인](https://www.tensorflow.org/serving/)으로 제공됩니다.   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| 지원되는 버전 | 1.0.3 |
| 지원되는 DSVM 에디션      | Linux |
| DSVM에 구성/설치 방법  |테아노는 파이썬 2.7 _(루트)과_파이썬 3.5 _(py35)_ 환경에 설치됩니다. |
| 그것을 실행하는 방법      |  터미널 : 원하는 파이썬 버전 (루트 또는 py35)을 활성화한 다음 파이썬을 실행한 다음 Theano를 가져옵니다.<br/>* Jupyter: 파이썬 2.7 또는 3.5 커널을 선택한 다음 테아노를 가져옵니다.  <br/>최근 MKL(수학 커널 라이브러리) 버그를 해결하려면 먼저 MKL 스레딩 레이어를 다음과 같이 설정해야 합니다.<br/><br/>`export MKL_THREADING_LAYER=GNU`  |