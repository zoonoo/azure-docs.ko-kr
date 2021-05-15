---
title: 딥 러닝 및 AI 프레임워크
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine에서 사용할 수 있는 딥 러닝 프레임워크 및 도구입니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 43a8004b6cdfe2e074304569b83360101784a87e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568004"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Azure Data Science VM의 딥 러닝 및 AI 프레임워크
DSVM의 딥 러닝 프레임워크는 아래에 나열되어 있습니다.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | |
| 지원되는 DSVM 버전      | Ubuntu 16.04    |
| DSVM에 구성/설치 방법  | Caffe는 `/opt/caffe`에 설치됩니다.   샘플은 `/opt/caffe/examples`에 있습니다.|
| 실행 방법      | X2Go를 사용하여 VM에 로그인한 다음 새 터미널을 시작하고 다음을 입력합니다.<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>새 브라우저 창이 샘플 노트북과 함께 열립니다. 이진 파일은 /opt/caffe/build/install/bin에 설치됩니다.<br/><br/>설치된 Caffe 버전은 Python 2.7이 필요하며 기본적으로 활성화되는 Python 3.6에서는 작동하지 않습니다. Python 2.7로 전환하려면 `source activate root`를 실행하여 Anaconda 환경으로 전환합니다.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | |
| 지원되는 DSVM 버전      | Ubuntu 16.04     |
| DSVM에 구성/설치 방법  | Caffe2는 [Python 2.7(루트) conda 환경]에 설치됩니다. |
| 실행 방법      | 터미널: Python을 시작하고 Caffe2를 가져옵니다. <br/> * JupyterHub: [JupyterHub에 연결](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)한 다음, 샘플 노트북을 찾으려면 Caffe2 디렉터리로 이동합니다. 일부 노트북은 Python 코드에 Caffe2 루트를 설정해야 합니다; enter /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 5.2 |
| 지원되는 DSVM 버전      | Ubuntu 16.04    |
| DSVM에 구성/설치 방법  | Chainer는 Python 3.5에 설치됩니다. |
| 실행 방법      | 터미널: Python 3.5 환경을 활성화하고 `python`을 실행한 다음 `import chainer`를 실행합니다. <br/> * JupyterHub: [JupyterHub에 연결](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)한 다음, 샘플 노트북을 찾으려면 Chainer 디렉터리로 이동합니다.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, NVIDIA 드라이버](https://developer.nvidia.com/cuda-toolkit)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 10.0.130|
| 지원되는 DSVM 버전      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04  |
| DSVM에 구성/설치 방법  |_nvidia smi_ 는 시스템 경로에서 사용할 수 있습니다.  |
| 실행 방법      | 명령 프롬프트(Windows) 또는 터미널(Linux)을 시작한 후 _nvidia smi_ 를 실행합니다. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 0.16.1|
| 지원되는 DSVM 버전      | Ubuntu 18.04<br> Ubuntu 16.04   |
| DSVM에 구성/설치 방법  | Horovod는 Python 3.5에 설치됩니다. |
| 실행 방법      | 터미널에서 올바른 환경을 활성화한 다음 Python을 실행합니다. |

## <a name="keras"></a>[Keras](https://keras.io/)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 2.2.4 |
| 지원되는 DSVM 버전      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04   |
| DSVM에 구성/설치 방법  | Keras는 Windows의 Python 3.6 및 Linux의 Python 3.5에 설치됩니다. |
| 실행 방법      | 터미널에서 올바른 환경을 활성화한 다음 Python을 실행합니다. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit(CNTK)](/cognitive-toolkit/)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 2.5.1 |
| 지원되는 DSVM 버전      | Windows 2016 <br> Ubuntu 16.04   |
| DSVM에 구성/설치 방법  | CNTK는 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 및 [Linux](./dsvm-tools-languages.md#python-linux-edition)의 python 3.5에서 python 3.6에 설치됩니다. |
| 실행 방법      | 터미널: 올바른 환경을 활성화한 다음 Python을 실행합니다. <br/>\* Jupyter: [Jupyter](provision-vm.md) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)에 연결한 다음, 샘플에 대한 CNTK 디렉터리를 엽니다. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 1.3.0 |
| 지원되는 DSVM 버전      | Windows 2016 <br> Ubuntu 16.04    |
| DSVM에 구성/설치 방법  | MXNet은 Windows에서 `C:\dsvm\tools\mxnet`에 그리고 Ubuntu에서 `/dsvm/tools/mxnet`에 설치됩니다. Python 바인딩은 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 의 python 3.6 및 [Linux](./dsvm-tools-languages.md#python-linux-edition)의 Python 3.5에 설치됩니다. R 바인딩도 Ubuntu DSVM에 포함됩니다. |
| 실행 방법      | 터미널: 올바른 conda 환경을 활성화한 다음, `import mxnet`을 실행합니다. <br/>\* Jupyter: [Jupyter](provision-vm.md#access-the-dsvm) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)에 연결한 다음, 샘플에 대한 `mxnet` 디렉터리를 엽니다. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 1.0.1 |
| 지원되는 DSVM 버전      | Windows 2016 <br> Ubuntu 16.04    |
| DSVM에 구성/설치 방법  | MXNet Model Server는 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition)의 Phthon 3.6 및 [Linux](./dsvm-tools-languages.md#python-linux-edition)의 Python 3.5에 설치됩니다. |
| 실행 방법      | 터미널: 둘 다 동일한 포트에서 수신하기 때문에 `sudo systemctl stop jupyterhub`를 실행하여 JupyterHub 서비스를 먼저 중지합니다. 그런 다음 올바른 conda 환경을 활성화하고 다음을 실행합니다. `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management 인터페이스(nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 |  |
| 지원되는 DSVM 버전      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04   |
| 용도 정의 | GPU 작업을 쿼리하기 위한 NVIDIA 도구 |
| DSVM에 구성/설치 방법  | `nvidia-smi`는 시스템 경로에 있습니다. |
| 실행 방법      | **GPU가 있는** 가상 머신에서 명령 프롬프트(Windows) 또는 터미널(Linux)을 연 다음 `nvidia-smi`를 실행합니다. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 1.2.0(Ubuntu 16.04), 1.7.1(Ubuntu 18.04, Windows 2019) |
| 지원되는 DSVM 버전      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| DSVM에 구성/설치 방법  | Py37_pytorch conda 환경에서 [Python 3.7](dsvm-tools-languages.md#python-linux-edition)에 설치됩니다. 샘플 Jupyter 노트북이 포함되며 샘플은/dsvm/samples/pytorch에 있습니다. |
| 실행 방법      | 터미널: 올바른 환경을 활성화한 다음, Python을 실행합니다.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): 샘플에 대한 PyTorch 디렉터리에 연결하여 엽니다.  |

## <a name="tensorflow"></a>[Tensorflow](https://www.tensorflow.org/)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 1.13 |
| 지원되는 DSVM 버전      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04 |
| DSVM에 구성/설치 방법  | [Linux](dsvm-tools-languages.md#python-linux-edition)의 Python 3.5와 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition)의 Python 3.6에 설치됩니다. |
| 실행 방법      | 터미널: 올바른 환경을 활성화한 다음, Python을 실행합니다. <br/> * Jupyter: [Jupyter](provision-vm.md) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)에 연결한 다음, 샘플에 대한 TensorFlow 디렉터리를 엽니다.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 1.12 |
| 지원되는 DSVM 버전      | Ubuntu 16.04 |
| DSVM에 구성/설치 방법  | tensorflow_model_server는 터미널에서 제공됩니다. |
| 실행 방법      |  샘플은 [온라인](https://www.tensorflow.org/tfx/guide/serving)으로 제공됩니다.   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 1.0.3 |
| 지원되는 DSVM 버전      | Ubuntu 16.04 |
| DSVM에 구성/설치 방법  |Theano는 Python 2.7(_루트_)과 Python 3.5(_py35_) 환경에 설치됩니다. |
| 실행 방법      |  터미널: 원하는 Python 버전(루트 또는 py35)을 활성화하고, Python을 실행한 다음, Theano를 가져옵니다.<br/>* Jupyter: Python 2.7 또는 3.5 커널을 선택한 다음, Theano를 가져옵니다.  <br/>최근 MKL(Math Kernel Library) 버그를 해결하려면 먼저 다음과 같이 MKL 스레딩 계층을 설정해야 합니다.<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
