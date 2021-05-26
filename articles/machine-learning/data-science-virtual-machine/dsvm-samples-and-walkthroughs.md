---
title: 샘플 프로그램 및 ML 연습
titleSuffix: Azure Data Science Virtual Machine
description: 이러한 샘플 및 연습을 통해 Data Science Virtual Machine을 사용하여 일반 작업 및 시나리오를 처리하는 방법을 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: 5ce6b2d80341a9c6ebb8afcbbe8f7072b54ca93c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110087907"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Azure Data Science Virtual Machines 샘플

Azure DSVM(Data Science Virtual Machines)에는 포괄적인 샘플 코드 집합이 있습니다. 이러한 샘플에는 Python 및 R과 같은 언어로 작성된 Jupyter Notebook 및 스크립트가 포함됩니다.
> [!NOTE]
> Data Science Virtual Machine에서 Jupyter Notebook을 실행하는 방법에 대한 자세한 내용은 [Jupyter 액세스](#access-jupyter) 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이러한 샘플을 실행하려면 [Ubuntu Data Science Virtual Machine](./dsvm-ubuntu-intro.md)을 프로비전해야 합니다.

## <a name="available-samples"></a>사용 가능한 샘플
| 샘플 범주 | Description | 위치 |
| ------------- | ------------- | ------------- |
| R 언어  | 샘플은 Azure 기반 클라우드 데이터 저장소에 연결하는 방법, 오픈 소스 R과 Microsoft Machine Learning Server 비교와 같은 시나리오를 보여 줍니다. 또한 Microsoft Machine Learning Server 및 SQL Server에서 모델을 운영하는 방법도 설명합니다. <br/> [R 언어](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 언어  | 샘플은 Azure 기반 클라우드 데이터 저장소에 연결하는 방법, Azure Machine Learning에서 작업하는 방법과 같은 시나리오를 설명합니다.  <br/> [Python 언어](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 언어  | Julia로 그리기 및 딥 러닝에 대한 자세한 설명을 제공합니다. 또한 Julia에서 C 및 Python을 호출하는 방법도 설명합니다. <br/> [Julia 언어](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Machine Learning을 사용하여 기계 학습 및 딥 러닝 모델을 빌드하는 방법을 보여 줍니다. 어디서든지 모델을 배포합니다. 자동화된 기계 학습 및 지능형 하이퍼 매개 변수 조정을 사용합니다. 또한 모델 관리 및 분산 학습을 사용합니다. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch 노트북  | PyTorch 기반 신경망을 사용하는 딥 러닝 샘플입니다. 노트북은 초급부터 고급 시나리오까지 포함합니다.  <br/> [PyTorch 노트북](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  TensorFlow 프레임워크를 사용하여 구현된 다양한 신경망 샘플 및 기법입니다. <br/> [Tensorflow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| H2O   | 실제 문제 시나리오에 H2O를 사용하는 Python 기반 샘플입니다. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 언어  | pySpark 및 MMLSpark(Apache Spark 2.x의 Apache Spark용 Microsoft Machine Learning)를 통해 Apache Spark MLLib 도구 키트의 기능을 사용하는 샘플입니다.  <br/> [SparkML 언어](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | 분류, 회귀와 같은 시나리오에 대해 XGBoost에 들어 있는 표준 기계 학습 샘플입니다. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>액세스 Jupyter 

Jupyter에 액세스하려면 데스크톱 또는 애플리케이션 메뉴에서 **Jupyter** 아이콘을 선택합니다. DSVM의 Linux 버전에서 Jupyter에 액세스할 수도 있습니다. 웹 브라우저에서 원격으로 액세스하려면 Ubuntu에서 `https://<Full Domain Name or IP Address of the DSVM>:8000`으로 이동합니다.

예외를 추가하고 브라우저에서 Jupyter에 액세스하려면 다음 지침을 참조하세요.


![Jupyter 예외 활성화](./media/ubuntu-jupyter-exception.png)


Data Science Virtual Machine에 로그인하는 데 사용하는 것과 동일한 암호를 사용하여 로그인합니다.
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

## <a name="h2o"></a>H2O 
<br/>![H2O 샘플](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 샘플](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 샘플](./media/xgboost-samples.png)<br/>