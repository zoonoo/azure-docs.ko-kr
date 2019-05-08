---
title: 예제 Jupyter Notebook
titleSuffix: Azure Machine Learning service
description: Jupyter Notebook 예제를 찾아 사용하여 Python에서 Azure Machine Learning 서비스를 검색합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035372"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Jupyter Notebook을 사용하여 Azure Machine Learning 서비스 검색

사용자 편의를 위해 Azure Machine Learning 서비스를 검색하는 데 사용할 수 있는 일련의 Jupyter Python Notebook을 개발했습니다. 

이 사이트에 대한 설명서로 서비스를 사용하고 상황에 맞게 사용자 지정하려면 이러한 Notebook을 사용하는 방법을 알아봅니다. 

아래 경로 중 하나를 사용하여 이러한 샘플 Notebook으로 Notebook 서버를 실행합니다.  서버가 실행 중이면 **tutorials** 폴더에서 자습서 Notebook을 찾거나 **how-to-use-azureml** 폴더에서 다른 기능을 찾습니다.

## <a name="a-managed-cloud-notebook-server"></a>관리형 클라우드 Notebook 서버

사용자 고유의 클라우드 기반 Notebook 서버를 쉽게 시작할 수 있습니다. 이 클라우드 리소스를 만들면 동일한 Notebook과 [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk)가 이미 설치 및 구성되어 있습니다.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* 샘플은 Notebook 웹 페이지에서 찾을 수 있습니다.

## <a name="a-data-science-virtual-machine-dsvm"></a>DSVM(Data Science Virtual Machine)

[Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 및 Notebook 서버는 이미 DSVM에 설치 및 구성되어 있습니다. 

[DSVM 생성](how-to-configure-environment.md#dsvm) 후에는 DSVM에서 다음 단계를 사용하여 Notebook을 실행합니다.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>사용자 고유의 Jupyter Notebook 서버

사용자 컴퓨터에 로컬 Jupyter Notebook 서버를 만들려면 이러한 단계를 사용합니다.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

설치 지침에서는 빠른 시작 및 자습서 Notebook을 실행하는 데 필요한 패키지를 설치합니다.  다른 샘플 Notebook에서는 추가 구성 요소를 설치해야 할 수도 있습니다.  이러한 구성 요소에 대한 자세한 내용은 [Python용 Azure Machine Learning SDK 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install)를 참조하세요.

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com/)에 동일한 Notebook과 [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk)가 이미 설치 및 구성되어 있습니다. 설치 및 향후 업데이트를 Azure 서비스를 통해 자동으로 관리합니다.

[Azure Portal](https://portal.azure.com)을 사용하여 Azure Notebooks를 시작합니다.  작업 영역을 열고, **개요** 섹션에서 **Azure Notebooks에서 시작**을 선택합니다.

## <a name="next-steps"></a>다음 단계

+ GitHub 리포지토리에서 동일한 Azure Machine Learning Service용 Notebook(https://aka.ms/aml-notebooks)을 살펴봅니다.

이러한 자습서를 시도합니다.
+ [MNIST를 사용하여 이미지 분류 모델 학습 및 배포](tutorial-train-models-with-aml.md)

+ [데이터를 준비하고 자동화된 기계 학습을 사용하여 NYC 택시 데이터 세트로 회귀 모델 학습](tutorial-data-prep.md)