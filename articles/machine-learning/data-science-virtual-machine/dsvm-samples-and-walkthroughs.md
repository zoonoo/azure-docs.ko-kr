---
title: Data Science Virtual Machines에 대한 샘플 및 연습 - Azure | Microsoft Docs
description: Data Science Virtual Machine을 사용하여 일반 작업 및 시나리오를 수행하는 방법을 가르쳐 주는 샘플 및 연습에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
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
ms.author: gokuma
ms.openlocfilehash: e61f0f4ba30b29fea1b2fd5f2a2ab253d3a6710c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502865"
---
# <a name="samples-on-data-science-virtual-machines"></a>Data Science Virtual Machines의 샘플

Azure Data Science Virtual Machines에는 포괄적인 샘플 코드 집합이 있습니다. 샘플 코드는 Python 및 R과 같은 언어로 된 Jupyter 노트북 및 스크립트 형식입니다. 
> [!NOTE]
> 데이터 과학 가상 머신에서 Jupyter 노트북을 실행하는 방법에 대한 자세한 내용은 [Jupyter 액세스](#access-jupyter) 섹션을 참조하세요.

## <a name="quick-reference-of-samples"></a>샘플의 빠른 참조
| 샘플 범주 | 설명 | 위치 |
| ------------- | ------------- | ------------- |
| R 언어  | R의 샘플에서는 Azure 클라우드 데이터 저장소에 연결하는 방법과 같은 시나리오를 설명합니다. 또한 오픈 소스 R과 Microsoft R을 비교하는 방법을 설명합니다. 또한 Microsoft R Server 또는 SQL Server에서 모델을 운용하는 방법을 설명합니다. <br/> [R 언어](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 언어  | Python의 샘플에서는 Azure 클라우드 데이터 저장소에 연결하고 Azure Machine Learning에서 작업하는 방법과 같은 시나리오를 설명합니다.  <br/> [Python 언어](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 언어  | Julia의 샘플에서는 Julia로 그리기 및 딥 러닝을 자세하게 설명합니다. 또한 Julia에서 C 및 Python을 호출하는 방법을 설명합니다. <br/> [Julia 언어](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Machine Learning을 사용하여 기계 학습과 딥 러닝 모델을 빌드합니다. 어디서든지 모델을 배포합니다. 자동화된 기계 학습 및 지능형 하이퍼 매개 변수 조정을 사용합니다. 또한 모델 관리 및 분산 학습을 사용합니다. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch 노트북  | PyTorch 기반 신경망을 사용하는 딥 러닝 샘플 노트북은 초급부터 고급 시나리오까지 포함합니다.  <br/> [PyTorch 노트북](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  TensorFlow 프레임워크를 사용하여 구현된 다양한 신경망 샘플 및 기법입니다. <br/> [Tensorflow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive 도구 키트 <br/>   | Microsoft에서 Cognitive Toolkit 팀에서 게시한 심층 학습 샘플.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| caffe2 | 신경망 기반 caffe2를 사용하는 딥 러닝 샘플. 몇 가지 노트북을 사용하여 caffe2와 이를 효과적으로 사용하는 방법을 숙지할 수 있습니다. 예로는 이미지 사전 처리 및 데이터 집합 생성이 있습니다. 또한 회귀 및 미리 학습된 모델을 사용하는 방법도 포함되어 있습니다. <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 실제 시나리오 문제에 대해 H2O를 사용하는 Python 기반 샘플입니다. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 언어  | pySpark 및 MMLSpark(Apache Spark 2.x의 Apache Spark용 Microsoft Machine Learning)를 통해 Spark MLLib 도구 키트의 기능을 사용하는 샘플  <br/> [SparkML 언어](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | 분류, 회귀 등과 같은 시나리오에 대해 XGBoost에 들어 있는 표준 기계 학습 샘플 <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>액세스 Jupyter 

Jupyter에 액세스하려면 데스크톱 또는 애플리케이션 메뉴에서 `Jupyter` 아이콘을 선택하세요. 또한 Data Science Virtual Machines의 Linux 에디션에서 Jupyter에 액세스할 수 있습니다. Ubuntu에서 `https://<Full Domain Name or IP Address of the DSVM>:8000`을 방문하여 웹 브라우저에서 원격으로 액세스할 수 있습니다.

예외를 추가하고 브라우저에서 Jupyter 액세스를 사용 가능하게 하려면 다음 스크린샷을 참조하세요.


![Jupyter 예외 활성화](./media/ubuntu-jupyter-exception.png)


Data Science Virtual Machines에 대한 로그인과 동일한 암호를 사용하여 로그인하세요.
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

