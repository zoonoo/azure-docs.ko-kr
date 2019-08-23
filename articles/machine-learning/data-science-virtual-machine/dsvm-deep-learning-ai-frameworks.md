---
title: 심층 학습 & AI 프레임 워크
titleSuffix: Azure Data Science Virtual Machines
description: TensorFlow, PyTorch, Keras, Caffe, MXNet, Horovod, Theano, 체 이너 등을 비롯 한 Azure Data Science Virtual Machine의 심층 학습 프레임 워크 및 도구입니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: adf6a68d6da4236874dce403bb4d7043087049b2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970354"
---
# <a name="deep-learning-and-ai-frameworks"></a>심층 학습 및 AI 프레임워크
Dsvm ( [Data Science Virtual Machine](https://aka.ms/dsvm) )은 이미지 및 언어 이해와 같은 예측 분석 및 인식 기능을 통해 AI (인공 지능) 응용 프로그램을 빌드하는 데 도움이 되는 다양 한 심층 학습 프레임 워크를 지원 합니다.

DSVM에서 사용할 수 있는 심층 학습 프레임 워크는 다음과 같습니다.

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet 모델 서버
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Deep Water
+ NVIDIA DIGITS
+ nvidia-smi
+ TensorFlow 서비스
+ TensorRT
+ Microsoft Cognitive 도구 키트

|&nbsp;Dsvm&nbsp;의&nbsp;DL 도구|Windows|Linux|사용&nbsp;메모|
|---------|:-------------------:|:------------------:|-----|
|[Tensorflow](https://www.tensorflow.org/) | 예 (Windows 2016) | 예 |[Linux 및 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 의 python 3.5 및 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)의 python 3.6에 설치 되어 있습니다. 샘플 Jupyter 노트북은 DSVM에 포함 되어 있습니다.<br/><br/>**실행 하려면 다음을 수행 합니다**.<br/>* 터미널: 올바른 환경을 활성화 한 다음 Python을 실행 합니다. <br/> Jupyter [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플에 대한 TensorFlow 디렉터리를 엽니다.  |
|[PyTorch](https://pytorch.org/)| 아니요 | 예 |[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에 설치 됩니다. 샘플 Jupyter 노트북이 포함 되어 있고 샘플이/dsvm/samples/pytorch.에 있습니다.    <br/><br/>**실행 하려면**<br/>* 터미널: 올바른 환경을 활성화 한 다음 Python을 실행 합니다.<br/>* [Jupyterhub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): 연결 하 고 샘플에 대 한 PyTorch 디렉터리를 엽니다.  |
|[Keras](https://keras.io/)| 예 | 예 |API는 [Linux 및 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 의 python 3.5 및 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)의 python 3.6에 설치 됩니다. [샘플](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**실행 하려면 다음을 수행 합니다**.<br/>* 터미널: 올바른 환경을 활성화 한 다음 Python을 실행 합니다. <br/> Jupyter GitHub 위치에서 샘플을 다운로드하고, [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플 디렉터리를 엽니다. |
|[Caffe](https://github.com/caffe2/caffe2) | 아니요 |예 (Ubuntu)|Caffe는 `/opt/caffe`에 설치됩니다.   샘플은에 `/opt/caffe/examples`있습니다. <br/><br/>**이를 실행 하려면**X2Go를 사용 하 여 VM에 로그인 한 다음 새 터미널을 시작 하 고 다음을 입력 합니다.<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>새 브라우저 창이 샘플 노트북과 함께 열립니다. 이진 파일은 /opt/caffe/build/install/bin에 설치됩니다.<br/><br/>설치된 Caffe 버전은 Python 2.7이 필요하며 기본적으로 활성화되는 Python 3.5에서는 작동하지 않습니다. Python 2.7로 전환 하려면를 실행 `source activate root` 하 여 Anaconda 환경을 전환 합니다.|
|[Caffe2](https://github.com/caffe2/caffe2) | 아니요 |예 (Ubuntu)|Caffe2는 [Python 2.7(루트) conda 환경](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에 설치됩니다. 원본은 `/opt/caffe2`에 있습니다.<br/>샘플 노트북은 JupyterHub에 포함되어 있습니다.<br/><br/>**실행 하려면 다음을 수행 합니다**.<br/>* 터미널에서: [루트 Python 환경](dsvm-languages.md#python-linux-and-windows-server-2012-edition)을 활성화하고, Python을 시작하고, caffe2를 가져옵니다. <br/> * JupyterHub에서: [JupyterHub에 연결](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)한 다음, 샘플 노트북을 찾으려면 Caffe2 디렉터리로 이동합니다. 일부 노트북은 Python 코드에 Caffe2 루트를 설정해야 합니다; enter /opt/caffe2. |
|[Torch](http://torch.ch/) | 아니요 |예 (Ubuntu)|Torch는 `/dsvm/tools/torch`에 설치됩니다. PyTorch는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다. Torch 샘플은 `/dsvm/samples/torch` 에 있으며 PyTorch 샘플은에 `/dsvm/samples/pytorch`있습니다. |
|[MXNet](https://mxnet.io/) | 예 (Windows 2016) | 예|MXNet은 Windows에서 `C:\dsvm\tools\mxnet`에 그리고 Linux에서 `/dsvm/tools/mxnet`에 설치됩니다. Python 바인딩은 [Linux 및 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)의 Python 3.5에 설치되고, [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)의 Python 3.6에 설치됩니다. 또한 R 바인딩도 Ubuntu에 설치됩니다.<br/><br/>샘플 Jupyter 노트북이 포함됩니다. <br/><br/>**실행 하려면 다음을 수행 합니다**.<br/>터미널과 올바른 환경을 활성화 한 다음 Python을 실행 합니다. <br/> Jupyter [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플에 대한 mxnet 디렉터리를 엽니다.|
|[MXNet Model 서버](https://github.com/awslabs/mxnet-model-server) | 아니요 | 예 |MXNet 및 ONNX 모델에 대 한 HTTP 끝점을 만드는 서버입니다. _mxnet-model-server_는 터미널에서 사용할 수 있습니다. [MXNet Model 서버 페이지](https://github.com/awslabs/mxnet-model-server)의 샘플.|
|[Horovod](https://github.com/uber/horovod) | 아니요 | 예 (Ubuntu) |TensorFlow에 대 한 분산형 심층 학습 프레임 워크입니다. Horovod는 [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에서 Python 3.5에 설치됩니다.  [샘플 참조](https://github.com/uber/horovod/tree/master/examples)<br/><br/>**이를 실행 하려면**터미널에서 올바른 환경을 활성화 한 다음 Python을 실행 합니다. |
|[Theano](https://github.com/Theano/Theano) | 아니요 | 예 (Ubuntu) |Theano는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다.<br/><br/>**실행 하려면 다음을 수행 합니다**. <br/>터미널과 원하는 Python 버전 (root 또는 py35)을 활성화 하 고 python을 실행 한 다음 theano를 가져옵니다.<br/>Jupyter Python 2.7 또는 3.5 커널을 선택 하 고 theano 가져오기를 선택 합니다.  <br/>최근 MKL 버그를 해결하려면 먼저 MKL 스레딩 계층을 설정해야 합니다.<br/><br/>_export MKL_THREADING_LAYER=GNU_|
|[Chainer](https://chainer.org/) |아니요 | 예 |Chainer는 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에 설치됩니다. ChainerRL 및 ChainerCV도 설치됩니다. <br/><br/>샘플 노트북은 JupyterHub에 포함되어 있습니다.<br/><br/>**실행 하려면 다음을 수행 합니다**. <br/>터미널과 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 환경을 활성화 하 고, _python_을 실행 한 다음, 체 이너를 가져옵니다. <br/> JupyterHub [JupyterHub에 연결한](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)다음, 체 이너 디렉터리로 이동 하 여 샘플 노트북을 찾습니다.|
|[NVidia 숫자](https://github.com/NVIDIA/DIGITS) | 아니요 | 예 (Ubuntu) |심층 학습 모델을 신속 하 게 학습 하기 위한 NVIDIA의 심층 학습 시스템입니다. DIGITS은 `/dsvm/tools/DIGITS`에 설치되며 _digits_이란 서비스로 사용할 수 있습니다.  <br/><br/>**실행 하려면 다음을 수행 합니다**. <br/>X2Go를 사용하여 VM에 로그인합니다. 터미널에서 서비스 ```sudo systemctl start digits```를 시작 합니다. <br/><br/>서비스는 시작하는 데 약 1분이 걸립니다. 웹 브라우저를 시작하여 `http://localhost:5000` 로 이동합니다. DIGITS는 보안 로그인을 제공하지 않으며 VM 외부에 공개하면 안 됩니다.|
|[CuDNN, NVIDIA 드라이버](https://developer.nvidia.com/cuda-toolkit) |예 | 예 | |
|nvidia-smi|예 | 예 |GPU 작업을 쿼리 하기 위한 NVIDIA 도구입니다. _nvidia smi_는 시스템 경로에서 사용할 수 있습니다. <br/><br/>명령 프롬프트(Windows) 또는 터미널(Linux)을 시작한 후 _nvidia smi_를 실행합니다.|
|[TensorFlow 서비스](https://www.tensorflow.org/serving/) | 아니요 | 예 |TensorFlow 모델에서 유추할 서버입니다. _tensorflow_model_server_는 터미널에서 제공됩니다. 샘플은 [온라인](https://www.tensorflow.org/serving/)으로 제공됩니다.|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  아니요 | 예 (Ubuntu) |NVIDIA의 딥 러닝 유추 서버입니다. TensorRT는 _apt_ 패키지로 설치됩니다. 샘플은 [온라인](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples)으로 제공됩니다.|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|예 | 예 | [Linux 및 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 의 python 3.5 및 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)의 python 3.6에 설치 되어 있습니다. 샘플 Jupyter 노트북은 DSVM에 포함 되어 있습니다. <br/><br/>**실행 하려면 다음을 수행 합니다**. <br/>터미널과 올바른 환경을 활성화 하 고 Python을 실행 합니다. <br/>Jupyter [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플에 대한 CNTK 디렉터리를 엽니다. |
|Deep Water|아니요 | 예 (Ubuntu) |H2O에 대 한 심층 학습 프레임 워크는 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 에 설치 되어 있으며에서 `/dsvm/tools/deep_water`사용할 수도 있습니다. 샘플 노트북은 JupyterHub에 포함되어 있습니다. Deep Water는 cuDNN 5.1의 CUDA 8이 필요합니다. 다른 딥 러닝 프레임워크는 CUDA 9 및 cuDNN 7을 사용하므로 기본적으로 라이브러리 경로에 있지 않습니다. Deep Water에 CUDA 8 + cuDNN 5.1을 사용하려면:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Deep Water를 사용하려면:<br/>* 터미널: [python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 환경을 활성화 한 다음 _python_을 실행 합니다. <br/>* JupyterHub: [JupyterHub에 연결한](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)다음 deep_water 디렉터리로 이동 하 여 샘플 노트북을 찾습니다.|
