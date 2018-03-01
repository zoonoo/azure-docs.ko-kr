---
title: "심층 학습 및 AI 프레임워크 - Azure | Microsoft Docs"
description: "심층 학습 및 AI 프레임워크"
keywords: "데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 89a8cde0dbb7fe7ccfbb6d068411aaf5488c532f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>심층 학습 및 AI 프레임워크
[데이터 과학 Virtual Machine](http://aka.ms/dsvm)(DSVM) 및 [심층 학습 VM](http://aka.ms/dsvm/deeplearning)은 예측 분석 및 이미지, 언어 이해 등의 인식 기능을 갖춘 인공 지능(AI) 응용 프로그램을 빌드하는 데 유용한 여러 심층 학습 프레임워크를 지원합니다. 

여기에 DSVM에서 사용할 수 있는 모든 심층 학습에 대한 자세한 내용이 있습니다.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive 도구 키트

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | Microsoft Cognitive 도구 키트(CNTK)는 _루트_ 환경에서 Python 2.7은 물론 _py35_ 환경에서 Python 3.5에 설치됩니다.   |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함됩니다.     |
| DSVM의 관련 도구      | Keras      |
| 사용/실행 방법    | Jupyter를 연 다음 CNTK 폴더를 찾습니다  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | Linux에서 TensorFlow는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다. Windows에서 Tensorflow는 Python 3.5(_py35_) 환경에 설치됩니다.  |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함됩니다.     |
| DSVM의 관련 도구      | Keras      |
| 사용/실행 방법    | Jupyter를 연 다음 TensorFlow 폴더를 찾습니다.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | Keras는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다.   |
| 샘플에 대한 링크      | https://github.com/fchollet/keras/tree/master/examples      |
| DSVM의 관련 도구      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| 사용/실행 방법    | 샘플을 Github 위치에서 다운로드하여, ~/notebooks 아래의 하위 디렉터리에 복사한 후 Jupyter에서 엽니다.   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Caffe는 `/opt/caffe`에 설치됩니다.    |
| 샘플에 대한 링크      | 샘플은 `/opt/caffe/examples`에 포함됩니다.      |
| DSVM의 관련 도구      | Caffe2      |
### <a name="how-to-use--run-it"></a>사용/실행 방법  

X2Go를 사용하여 VM에 로그인한 다음 새 터미널을 시작하고 입력합니다

```
cd /opt/caffe/examples
jupyter notebook
```

새 브라우저 창이 샘플 노트북과 함께 열립니다.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Caffe2는 `/opt/caffe2`에 설치됩니다. 또한 Python 2.7(_루트_) Conda 환경에 대해 사용할 수 있습니다.     |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함됩니다     |
| DSVM의 관련 도구      | Caffe      |
| 사용/실행 방법    | Jupyter를 연 후, 샘플 노트북을 찾으려면 Caffe2 디렉터리로 이동합니다. 일부 노트북은 Python 코드에 Caffe2 루트를 설정해야 합니다; enter /opt/caffe2.   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | Chainer는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다. ChainerRL 및 ChainerCV도 설치됩니다.   |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함됩니다.      |
| DSVM의 관련 도구      | Caffe      |

### <a name="how-to-use--run-it"></a>사용/실행 방법  

터미널에서 원하는 Python 버전(_루트_ 또는 _py35_)을 활성화하고, _python_을 실행한 후, Chainer를 가져옵니다. Jupyter에서 Python 2.7 또는 3.5 커널을 선택한 다음 Chainer를 가져옵니다.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | H2O에 대한 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Deep Water은 `/dsvm/tools/deep_water`에 설치됩니다.   |
| 샘플에 대한 링크      | 샘플은 Deep Water 서버를 통해 제공됩니다.      |
| DSVM의 관련 도구      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>사용/실행 방법  

X2Go를 사용하여 VM에 연결합니다. 터미널에서 Deep Water 서버를 시작합니다.

    java -jar /dsvm/tools/deep_water/h2o.jar

그런 다음 브라우저를 열고 `http://localhost:54321`에 연결합니다.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | MXNet은 Windows에서 `C:\dsvm\tools\mxnet`에 그리고 Linux에서 `/dsvm/tools/mxnet`에 설치됩니다. Python 바인딩은 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다. 또한 R 바인딩도 설치됩니다.   |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함됩니다.    |
| DSVM의 관련 도구      | Keras      |
| 사용/실행 방법    | Jupyter를 연 다음 MXNet 폴더를 찾습니다  |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 모델을 빠르게 훈련하기 위한 NVIDIA의 심층 교육 시스템      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | DIGITS은 `/dsvm/tools/DIGITS`에 설치되며 _digits_이란 서비스로 사용할 수 있습니다.   |
### <a name="how-to-use--run-it"></a>사용/실행 방법  

X2Go를 사용하여 VM에 로그인합니다. 터미널에서 서비스를 시작합니다.

    sudo systemctl start digits

서비스는 시작하는 데 약 1분이 걸립니다. 웹 브라우저를 시작하여 `http://localhost:5000`로 이동합니다.



## <a name="nvdia-smi"></a>nvdia smi

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | GPU 작업을 쿼리하기 위한 NVIDIA 도구      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | _nvidia smi_는 시스템 경로에서 사용할 수 있습니다.   |
| 사용/실행 방법 | 명령 프롬프트(Windows) 또는 터미널(Linux)을 시작한 후 _nvidia smi_를 실행합니다.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Theano는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다.   |
| DSVM의 관련 도구      | Keras      |
| 사용/실행 방법    | 터미널에서 원하는 Python 버전(루트 또는 py35)을 활성화하고, Python을 실행한 후, Theano를 가져옵니다. Jupyter에서 Python 2.7 또는 3.5 커널을 선택한 다음 Theano를 가져옵니다.  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Torch는 `/dsvm/tools/torch`에 설치됩니다. PyTorch는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다.   |
| 샘플에 대한 링크      | Torch 샘플은 `/dsvm/samples/torch`에 있습니다. PyTorch 샘플은 `/dsvm/samples/pytorch`에 있습니다.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Linux     |
| DSVM에 구성/설치 방법  | PyTorch는 Python 3.5(_py35_) 환경에 설치됩니다.   |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함되며 /dsvm/samples/pytorch에도 샘플이 있습니다.      |
| DSVM의 관련 도구      | Torch      |

### <a name="how-to-use--run-it"></a>사용/실행 방법  

터미널에서 _python_을 실행한 다음, torch를 가져옵니다. Jupyter에서 Python 3.5 커널을 선택한 다음, torch를 가져옵니다.


## <a name="mxnet-model-server"></a>MXNet 모델 서버

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | MXNet 및 ONNX 모델에 대한 HTTP 엔드포인트를 만들 서버      |
| 지원되는 DSVM 버전      | Linux     |
| DSVM에 구성/설치 방법  | _mxnet-model-server_는 터미널에서 사용할 수 있습니다.   |
| 샘플에 대한 링크      | [MXNet 모델 서버 페이지](https://github.com/awslabs/mxnet-model-server)에서 최신 샘플을 확인할 수 있습니다.    |
| DSVM의 관련 도구      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow 서비스

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | TensorFlow 모델에서 유추를 실행하는 서버      |
| 지원되는 DSVM 버전      | Linux     |
| DSVM에 구성/설치 방법  | _tensorflow_model_server_는 터미널에서 제공됩니다.   |
| 샘플에 대한 링크      | 샘플은 [온라인](https://www.tensorflow.org/serving/)으로 제공됩니다.      |
| DSVM의 관련 도구      | TensorFlow      |
