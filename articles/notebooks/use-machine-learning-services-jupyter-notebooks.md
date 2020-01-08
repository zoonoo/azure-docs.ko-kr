---
title: Azure Notebooks 미리 보기에서 Azure Machine Learning 사용
description: Azure Notebooks Preview에서 사용할 수 있는 Azure Machine Learning에 대 한 샘플 노트북의 개요입니다.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c0e0e9ccea079d7e8f4e35e9af2a0e1c1ec5051
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646955"
---
# <a name="use-azure-machine-learning-in-azure-notebooks-preview"></a>Azure Notebooks 미리 보기에서 Azure Machine Learning 사용

Azure Notebooks은 [Azure Machine Learning](/azure/machine-learning/service/)작업 하는 데 필요한 환경으로 미리 구성 되어 있습니다. 샘플 프로젝트를 Notebooks 계정에 쉽게 복제하여 다양한 Machine Learning 시나리오를 살펴볼 수 있습니다.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-the-sample-into-your-account"></a>사용자 계정에 샘플 복제

1. [Azure Notebooks](https://notebooks.azure.com/)에 로그인합니다.
1. **내 프로젝트** 를 선택 하 여 프로젝트 대시보드로 이동 합니다.
1. Github 리포지토리 **업로드 (위쪽** 화살표) 단추를 선택 하 여 **github 리포지토리 업로드** 팝업을 엽니다.
1. 팝업에서 **GitHub 리포지토리에서**`Azure/MachineLearningNotebooks`를 입력 하 고, "Azure Machine Learning"와 같은 **프로젝트 이름** 에 프로젝트 이름을 입력 하 고, **프로젝트 ID**에 식별자를 제공 하 고, 원하는 경우 **공용** 을 선택 취소 한 후 **가져오기**를 선택 합니다.

    ![Notebooks 계정으로 Azure Machine Learning Notebook 샘플 가져오기](media/azureml-import-project.png)

1. 1~2분 후에 Azure Notebooks가 새 프로젝트의 대시보드로 자동 전환됩니다.

## <a name="run-a-sample-notebook"></a>샘플 노트북 실행

1. **00 - configuration.ipynb**를 선택하여 노트북의 구성 섹션을 시작하고 지침에 따라 Azure Machine Learning 작업 영역을 만듭니다.

    - Azure Notebooks에는 필요한 Python 패키지가 이미 포함되어 있으므로 필수 조건의 2단계에서 코드 조각만 실행하면 Azure ML SDK 버전을 확인할 수 있습니다.

1. 구성이 완료 되 면 **01** 을 선택 하 여 13 개의 다른 샘플 노트북을 포함 하는 폴더를 엽니다. 이러한 폴더는 각각 별도의 설명이 필요 합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 설명서에는 노트북 내에서 Machine Learning 작업 하는 과정을 안내 하는 다양 한 리소스가 포함 되어 있습니다.

- [빠른 시작: Python을 사용 하 여 Azure Machine Learning 시작](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [자습서 #1: Azure Machine Learning를 사용 하 여 이미지 분류 모델 학습](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [자습서 #2: ACI (Azure Container Instance)에서 이미지 분류 모델 배포](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [자습서: Azure Machine Learning에서 자동화 된 machine learning을 사용 하 여 분류 모델 학습](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

[Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 문서도 참조하세요.
