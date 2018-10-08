---
title: 데이터 과학 Virtual Machine에 대한 샘플 및 연습 - Azure | Microsoft Docs
description: 데이터 과학 Virtual Machine에 대한 샘플.및 연습
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392571"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>데이터 과학 Virtual Machine(DSVM)의 샘플

DSVM은 Jupyter Notebook의 형태 및 Python 및 R과 같은 언어의 스크립트 모두에서 포괄적인 샘플 코드 집합을 포함합니다.    
> [!NOTE]
> DSVM에서 Jupyter Notebook 실행에 대한 자세한 내용은 [액세스 Jupyter](#access-jupyter) 섹션을 참조하세요.

## <a name="quick-reference-of-samples"></a>샘플의 빠른 참조
| 샘플 범주 | 설명 | 위치 |
| ------------- | ------------- | ------------- |
| **R** 언어  | Azure 클라우드 데이터 저장소와 연결, 개방형 소스 R과 Microsoft R 비교 및 Microsoft R Server 또는 SQL Server에서 모델 운영과 같은 시나리오를 설명하는 **R**의 예제. <br/> [스크린샷](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| **Python** 언어  | Azure 클라우드 데이터 저장소와 연결 및 **Azure Machine Learning** 작업과 같은 시나리오를 설명하는 **Python** 예제.  <br/> [스크린샷](#python-language) | <br/>`~notebooks` <br/><br/>|
| **Julia** 언어  | Julia에서 그리기, Julia에서 심층 학습, Julia에서 C 및 Python 호출 등을 자세히 설명하는 **Julia** 샘플. <br/> [스크린샷](#julia-language) |<br/> **Windows**:<br/> `~notebooks/Julia_notebooks`<br/><br/> **Linux**:<br/> `~notebooks/julia`<br/><br/> |
| **Azure Machine Learning** AzureML  | **Azure Machine Learning** 서비스 및 배포 모델을 사용하여 ML 및 딥 러닝 모델을 어디서나 빌드합니다. 자동화된 ML, 지능형 하이퍼 매개 변수 튜닝, 모델 관리, 분산 학습과 같은 기능을 활용합니다. <br/> [스크린샷](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| **PyTorch** Notebook  | 신경망 기반 **PyTorch**를 활용하는 딥 러닝 샘플 초보자용부터 고급 시나리오에 이르기까지 다양한 노트북이 있습니다.  <br/> [스크린샷](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| **Tensorflow**  | **TensorFlow** 프레임워크를 사용하여 구현된 서로 다른 여러 신경망 샘플 및 기법 <br/> [스크린샷](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| **CNTK** <br/> (Microsoft Cognitive 도구 키트)  | Microsoft에서 Cognitive Toolkit 팀에서 게시한 심층 학습 샘플.  <br/> [스크린샷](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> **Linux**:<br/> `~notebooks/CNTK`<br/> <br/>|
| **caffe2** | 신경망 기반 **caffe2**를 활용하는 심층 학습 샘플. 사용자가 이미지 전처리, 데이터 집합 만들기, 회귀, 미리 학습된 모델 사용과 같은 예제를 포함하여 caffe2 및 그것을 효과적으로 사용하는 방법을 익히도록 디자인된 여러 노트북이 있습니다. <br/> [스크린샷](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | 많은 실제 시나리오 문제에 대해 **H2O**를 활용하는 Python 기반 샘플. <br/> [스크린샷](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| **SparkML** 언어  | **Apache Spark 2.x**에서 **pySpark** 및 **MMLSpark - Apache Spark용 Microsoft Machine Learning**을 통해 Spark의 **MLlib** 도구 집합의 기능을 활용하는 샘플  <br/> [스크린샷](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| **XGBoost** | 분류, 회귀 등과 같은 시나리오에 대해 **XGBoost**에 들어 있는 Standard Machine Learning 샘플. <br/> [스크린샷](#xgboost) | <br/>**Windows**:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>액세스 Jupyter 

바탕화면 또는 응용 프로그램 메뉴에서 `Jupyter` 아이콘을 클릭하여 Jupyter를 액세스할 수 있습니다. Ubuntu의 **`https://<Full Domain Name or IP Address of the DSVM>:8000`** 을 방문하여 웹 브라우저에서 원격으로 DSVM의 Linux 버전에서 Jupyter에 액세스할 수도 있습니다.

브라우저를 통해 예외를 추가하고 Jupyter 액세스를 활성화하려면 스크린샷을 참조하세요.


![Jupyter 예외 활성화](./media/ubuntu-jupyter-exception.png)


DSVM에 로그인할 때와 동일한 암호를 사용하여 로그인합니다.
<br/>

**Jupyter 홈**
<br/>![Jupyter 홈](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 언어 
<br/>![R 샘플](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 언어
<br/>![Python 샘플](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 언어 
<br/>![Julia 샘플](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML 샘플](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch 샘플](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 샘플](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK 샘플](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>caffe2 
<br/>![caffe2 샘플](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 샘플](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 샘플](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 샘플](./media/xgboost-samples.png)<br/>

