---
title: Data Science Virtual Machines에 대한 샘플 및 연습 - Azure | Microsoft Docs
description: 이러한 샘플과 연습을 통해 Data Science Virtual Machine를 사용 하 여 일반적인 작업 및 시나리오를 처리 하는 방법을 알아봅니다.
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
ms.date: 09/24/2018
ms.author: vijetaj
ms.openlocfilehash: 628c516553c2304739229c53d725645c799d06ce
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047844"
---
# <a name="samples-on-data-science-virtual-machines"></a>Data Science Virtual Machines의 샘플

DSVMs (Azure 데이터 과학 Virtual Machines)은 포괄적인 샘플 코드 집합을 포함 합니다. 이러한 샘플에는 Python 및 R과 같은 언어의 Jupyter 노트북 및 스크립트가 포함 됩니다.
> [!NOTE]
> 데이터 과학 virtual machines에서 Jupyter 노트북을 실행 하는 방법에 대 한 자세한 내용은 [Jupyter 액세스](#access-jupyter) 섹션을 참조 하세요.

## <a name="quick-reference-of-samples"></a>샘플의 빠른 참조
| 샘플 범주 | Description | 위치 |
| ------------- | ------------- | ------------- |
| R 언어  | 샘플은 Azure 기반 클라우드 데이터 저장소에 연결 하는 방법 및 오픈 소스 R 및 Microsoft Machine Learning Server 비교 하는 방법 등의 시나리오를 보여 줍니다. 또한 Microsoft Machine Learning Server 및 SQL Server에서 모델을 운영 하는 방법을 설명 합니다. <br/> [R 언어](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 언어  | 샘플에서는 Azure 기반 클라우드 데이터 저장소에 연결 하는 방법 및 Azure Machine Learning 사용 하는 방법 등의 시나리오를 설명 합니다.  <br/> [Python 언어](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 언어  | 줄리아의 그리기 및 심층 학습에 대 한 자세한 설명을 제공 합니다. 또한 줄리아에서 C 및 Python을 호출 하는 방법을 설명 합니다. <br/> [Julia 언어](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Machine Learning를 사용 하 여 기계 학습 및 심층 학습 모델을 빌드하는 방법을 보여 줍니다. 어디서든지 모델을 배포합니다. 자동화된 기계 학습 및 지능형 하이퍼 매개 변수 조정을 사용합니다. 또한 모델 관리 및 분산 학습을 사용합니다. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch 노트북  | PyTorch 기반 신경망을 사용 하는 심층 학습 샘플. 노트북은 초급부터 고급 시나리오까지 포함합니다.  <br/> [PyTorch 노트북](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  TensorFlow framework를 사용 하 여 구현 되는 다양 한 신경망 샘플과 기술입니다. <br/> [Tensorflow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive 도구 키트 <br/>   | Microsoft의 Cognitive Toolkit 팀에서 게시 한 심층 학습 샘플입니다.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Caffe2 기반 신경망을 사용 하는 심층 학습 샘플. 여러 노트북은 Caffe2 및 it를 효과적으로 사용 하는 방법을 숙지 합니다. 예제에는 이미지 전처리 및 데이터 집합 생성이 포함 됩니다. 또한 회귀 및 미리 학습 된 모델을 사용 하는 방법을 포함 합니다. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 실제 문제 시나리오에 H2O를 사용 하는 Python 기반 샘플입니다. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 언어  | PySpark 및 MMLSpark를 통해 Apache Spark MLLib 도구 키트의 기능을 사용 하는 샘플: Microsoft Machine Learning는 Apache Spark 2.x에서 Apache Spark 합니다.  <br/> [SparkML 언어](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | 분류 및 회귀와 같은 시나리오에 대 한 XGBoost의 표준 기계 학습 샘플입니다. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>액세스 Jupyter 

Jupyter에 액세스 하려면 바탕 화면 또는 응용 프로그램 메뉴에서 **jupyter** 아이콘을 선택 합니다. DSVM의 Linux 버전에서 Jupyter에 액세스할 수도 있습니다. 웹 브라우저에서 원격으로 액세스 하려면 Ubuntu에서로 `https://<Full Domain Name or IP Address of the DSVM>:8000` 이동 합니다.

브라우저를 통해 예외를 추가 하 고 Jupyter 액세스를 사용할 수 있도록 설정 하려면 다음 지침을 사용 합니다.


![Jupyter 예외 활성화](./media/ubuntu-jupyter-exception.png)


Data Science Virtual Machine 로그인 하는 데 사용 하는 것과 동일한 암호를 사용 하 여 로그인 합니다.
<br/>

**Jupyter 홈**
<br/>![Jupyter 홈](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 언어 
<br/>![R 샘플](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 언어
<br/>![Python 샘플](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 언어 
<br/>![Julia 샘플](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning 샘플](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch 샘플](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 샘플](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK 샘플](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 샘플](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 샘플](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 샘플](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 샘플](./media/xgboost-samples.png)<br/>

