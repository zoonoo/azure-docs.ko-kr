---
title: 예제 Jupyter Notebook
titleSuffix: Azure Machine Learning service
description: Jupyter Notebook 예제를 찾아 사용하여 Python SDK에서 Azure Machine Learning Service를 검색합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: e5088cfa417c76933be6785188f2b173b7b461d0
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772569"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>Jupyter Notebooks를 사용하여 Azure Machine Learning Service 탐색

[예제 Azure Machine Learning Notebook 리포지토리](https://github.com/azure/machinelearningnotebooks)에는 최신 Azure Machine Learning Python SDK 샘플이 포함되어 있습니다. 이러한 Jupyter Notebook은 사용자가 SDK를 살펴보고 사용자 고유의 Machine Learning 프로젝트를 위한 모델로 사용할 수 있도록 설계되었습니다.

이 문서는 다음 환경에서 리포지토리에 액세스하는 방법을 보여 줍니다.

- [Azure Machine Learning Notebook VM](#notebookvm)
- [사용자 고유의 Notebook 서버 구현](#byo)
- [데이터 과학 Virtual Machine](#dsvm)
- [Azure 노트](#aznb)

> [!NOTE]
> 리포지토리를 복제하면 **tutorials** 폴더에서 자습서 Notebook, **how-to-use-azureml** 폴더에서 기능별 Notebook을 확인할 수 있습니다.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Azure Machine Learning Notebook VM에 대한 샘플 가져오기

샘플을 시작하는 가장 쉬운 방법은 [자습서: 환경 및 작업 영역 설정](tutorial-1st-experiment-sdk-setup.md)을 완료하는 것입니다. 완료되면 전용 Notebook 서버에 SDK 및 샘플 리포지토리가 사전 로드됩니다. 다운로드 또는 설치가 필요 없습니다.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Notebook 서버에서 샘플 가져오기

로컬 개발용으로 사용자 고유의 Notebook 서버를 구현하려는 경우 아래 단계를 따르세요.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

이러한 지침은 빠른 시작 및 자습서 Notebook에 필요한 기본 SDK 패키지를 설치합니다. 다른 샘플 Notebook에서는 추가 구성 요소를 설치해야 할 수 있습니다. 자세한 내용은 [Python용 Azure Machine Learning SDK 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install)를 참조하세요.

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>DSVM에서 샘플 가져오기

DSVM(Data Science Virtual Machine)은 데이터 과학 수행을 위해 특별히 구축된 사용자 지정 VM 이미지입니다. [DSVM을 만드는](how-to-configure-environment.md#dsvm) 경우 SDK 및 Notebook 서버가 사용자를 위해 설치 및 구성됩니다. 그러나 작업 영역을 만들고 샘플 리포지토리는 복제해야 합니다.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

<a name="aznb"></a>
## <a name="get-samples-on-azure-notebooks"></a>Azure Notebooks에서 샘플 가져오기

[Azure Notebooks](https://notebooks.azure.com/)에서 SDK 및 Notebook 서버가 사용자를 위해 설치 및 구성됩니다. Azure Notebooks는 사용자가 살펴볼 수 있도록 완전 관리형 경량 Notebook 환경을 제공합니다.

Azure Notebooks에서 샘플 리포지토리에 액세스하려면 [Azure Portal](https://portal.azure.com)을 통해 Azure Machine Learning 작업 영역으로 이동합니다. **개요** 섹션에서 **Azure Notebooks에서 시작**을 선택합니다.

## <a name="next-steps"></a>다음 단계

[샘플 Notebook](https://aka.ms/aml-notebooks)을 살펴보고 Azure Machine Learning Service에서 제공하는 기능을 확인하거나 아래 자습서를 사용해 봅니다.

- [MNIST를 사용하여 이미지 분류 모델 학습 및 배포](tutorial-train-models-with-aml.md)

- [데이터를 준비하고 자동화된 기계 학습을 사용하여 NYC 택시 데이터 세트로 회귀 모델 학습](tutorial-data-prep.md)
