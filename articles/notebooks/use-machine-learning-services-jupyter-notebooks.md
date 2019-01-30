---
title: Azure Notebooks에서 Azure Machine Learning Services 사용
description: Azure Notebooks에서 사용할 수 있는 Azure Machine Learning Services용 샘플 노트북에 대한 개요입니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b6d3cbd56182b8dba3738f38798efa378c6b49d0
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254829"
---
# <a name="use-azure-machine-learning-services-in-a-notebook"></a>노트북에서 Azure Machine Learning Services 사용

Azure Notebooks는 [Azure Machine Learning Services](/azure/machine-learning/service/)에서 작동하는 데 필요한 환경으로 사전 구성되어 있습니다. 샘플 프로젝트를 Notebooks 계정에 쉽게 복제하여 다양한 Machine Learning 시나리오를 살펴볼 수 있습니다.

## <a name="clone-the-sample-into-your-account"></a>사용자 계정에 샘플 복제

1. [Azure Notebooks](https://notebooks.azure.com/)에 로그인합니다.
1. **내 프로젝트**를 선택하여 프로젝트 대시보드로 이동합니다.
1. **GitHub 리포지토리 업로드**(위쪽 화살표) 단추를 선택하여 **GitHub 리포지토리 업로드** 팝업을 엽니다.
1. 팝업에서 **GitHub 리포지토리**에 `Azure/MachineLearningNotebooks`를 입력하고 **프로젝트 이름**에 “Azure ML 서비스”와 같은 프로젝트 이름을 입력하고, **프로젝트 ID**에 식별자를 입력하고, 원하는 경우 **공용**을 선택 취소한 다음, **가져오기**를 선택합니다.

    ![Notebooks 계정으로 Azure Machine Learning Notebook 샘플 가져오기](media/azureml-import-project.png)

1. 1~2분 후에 Azure Notebooks가 새 프로젝트의 대시보드로 자동 전환됩니다.

## <a name="run-a-sample-notebook"></a>샘플 노트북 실행

1. **00 - configuration.ipynb**를 선택하여 노트북의 구성 섹션을 시작하고 지침에 따라 Azure Machine Learning 작업 영역을 만듭니다.

    - Azure Notebooks에는 필요한 Python 패키지가 이미 포함되어 있으므로 필수 조건의 2단계에서 코드 조각만 실행하면 Azure ML SDK 버전을 확인할 수 있습니다.

1. 구성이 완료되면 **01.getting-started**를 선택하여 별도의 설명 없이도 바로 이해할 수 있는 13개의 샘플 노트북이 포함된 폴더로 이동합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning Services 문서에는 노트북 내의 Machine Learning Service 작업을 안내하는 기타 다양한 리소스가 포함되어 있습니다.

- [빠른 시작: Python을 사용하여 Azure Machine Learning 시작](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [자습서 #1: Azure Machine Learning Service를 사용하여 이미지 분류 모델 학습](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [자습서 #2: ACI(Azure Container Instance)에서 이미지 분류 모델 배포](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [자습서: Azure Machine Learning Service에서 자동화된 기계 학습을 사용하여 분류 모델 학습](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

[Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 문서도 참조하세요.
