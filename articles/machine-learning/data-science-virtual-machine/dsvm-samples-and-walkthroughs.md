---
title: ML 연습& 프로그램 샘플
titleSuffix: Azure Data Science Virtual Machine
description: 이러한 샘플 및 연습에서 데이터 과학 가상 머신을 사용하여 일반적인 작업 및 시나리오를 처리하는 방법을 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900042"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Azure 데이터 과학 가상 머신의 샘플

Azure 데이터 과학 가상 컴퓨터(DSVM)에는 포괄적인 샘플 코드 집합이 포함됩니다. 이 샘플에는 파이썬 및 R과 같은 언어로 된 Jupyter 노트북및 스크립트가 포함되어 있습니다.
> [!NOTE]
> 데이터 과학 가상 컴퓨터에서 Jupyter 노트북을 실행하는 방법에 대한 자세한 내용은 [주피터 액세스](#access-jupyter) 섹션을 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

이러한 샘플을 실행하려면 데이터 과학 가상 컴퓨터를 프로비전해야 합니다. [윈도우와](./provision-vm.md) [우분투에](./dsvm-ubuntu-intro.md)대한 빠른 시작을 참조하십시오 .

## <a name="available-samples"></a>사용 가능한 샘플
| 샘플 범주 | 설명 | 위치 |
| ------------- | ------------- | ------------- |
| R 언어  | 샘플에서는 Azure 기반 클라우드 데이터 저장소와 연결하는 방법 및 오픈 소스 R 및 Microsoft 기계 학습 서버를 비교하는 방법과 같은 시나리오를 보여 줍니다. 또한 Microsoft 기계 학습 서버 및 SQL Server에서 모델을 작동하는 방법에 대해서도 설명합니다. <br/> [R 언어](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 언어  | 샘플에서는 Azure 기반 클라우드 데이터 저장소와 연결하는 방법 및 Azure 기계 학습을 사용하여 작업하는 방법과 같은 시나리오를 설명합니다.  <br/> [파이썬 언어](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 언어  | 줄리아의 플롯 및 딥 러닝에 대한 자세한 설명을 제공합니다. 또한 줄리아에서 C와 파이썬을 호출하는 방법에 대해 설명합니다. <br/> [줄리아어](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | 기계 학습을 사용하여 기계 학습 및 딥 러닝 모델을 구축하는 방법을 보여 줍니다. 어디서든지 모델을 배포합니다. 자동화된 기계 학습 및 지능형 하이퍼 매개 변수 조정을 사용합니다. 또한 모델 관리 및 분산 학습을 사용합니다. <br/> [기계 학습](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch 노트북  | PyTorch 기반 신경망을 사용하는 딥 러닝 샘플입니다. 노트북은 초급부터 고급 시나리오까지 포함합니다.  <br/> [파이토치 노트북](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  TensorFlow 프레임워크를 사용하여 구현된 다양한 신경망 샘플 및 기술입니다. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive 도구 키트 <br/>   | Microsoft의 코그너티브 툴킷 팀에서 발표한 딥 러닝 샘플입니다.  <br/> [Cognitive 도구 키트](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Caffe2 기반 신경망을 사용하는 딥 러닝 샘플입니다. 여러 노트북은 사용자에게 Caffe2와 이를 효과적으로 사용하는 방법을 숙지합니다. 예를 들어 이미지 전처리 및 데이터 집합 생성이 있습니다. 또한 회귀 및 미리 학습된 모델을 사용하는 방법도 포함됩니다. <br/> [카페2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 실제 문제 시나리오에 H2O를 사용하는 파이썬 기반 샘플입니다. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 언어  | pySpark 및 MMLSpark를 통해 아파치 스파크 MLLib 툴킷의 기능을 사용하는 샘플 : 아파치 스파크에 아파치 스파크에 대한 마이크로 소프트 기계 학습 2.x.  <br/> [스파크ML 언어](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | 분류 및 회귀와 같은 시나리오에 대한 XGBoost의 표준 기계 학습 샘플입니다. <br/> [XG부스트](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>액세스 Jupyter 

Jupyter에 액세스하려면 바탕 화면 또는 응용 프로그램 메뉴에서 **Jupyter** 아이콘을 선택합니다. 또한 DSVM의 리눅스 버전에서 Jupyter에 액세스할 수 있습니다. 웹 브라우저에서 원격으로 액세스하려면 우분투로 이동하십시오. `https://<Full Domain Name or IP Address of the DSVM>:8000`

예외를 추가하고 브라우저를 통해 Jupyter 액세스를 사용할 수 있도록 하려면 다음 지침을 사용하십시오.


![Jupyter 예외 활성화](./media/ubuntu-jupyter-exception.png)


데이터 과학 가상 컴퓨터에 로그인하는 데 사용하는 것과 동일한 암호로 로그인합니다.
<br/>

**주피터 홈**
<br/>![Jupyter 홈](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 언어 
<br/>![R 샘플](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 언어
<br/>![Python 샘플](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 언어 
<br/>![Julia 샘플](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure 기계 학습 샘플](./media/azureml-samples.png)<br/>

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

