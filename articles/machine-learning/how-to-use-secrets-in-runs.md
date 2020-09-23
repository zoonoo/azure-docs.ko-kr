---
title: 교육의 인증 비밀
titleSuffix: Azure Machine Learning
description: 작업 영역을 사용 하 여 안전한 방식으로 학습 실행에 비밀 전달 Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e984c0c43dcc47c3e11a36f3d5c32bf2ddb9973a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902326"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Azure Machine Learning 교육 실행에서 인증 자격 증명 비밀 사용


이 문서에서는 학습 실행 시 안전 하 게 암호를 사용 하는 방법에 대해 알아봅니다. 사용자 이름 및 암호와 같은 인증 정보는 비밀입니다. 예를 들어 학습 데이터를 쿼리 하기 위해 외부 데이터베이스에 연결 하는 경우 사용자 이름 및 암호를 원격 실행 컨텍스트에 전달 해야 합니다. 이러한 값을 일반 텍스트의 학습 스크립트에 코딩 하는 것은 암호를 노출 하므로 안전 하지 않습니다. 

대신 Azure Machine Learning 작업 영역에 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)라는 연결 된 리소스가 있습니다. 이 Key Vault를 사용 하 여 Azure Machine Learning Python SDK의 Api 집합을 통해 안전 하 게 원격으로 실행할 수 있습니다.

비밀을 사용 하는 표준 흐름은 다음과 같습니다.
 1. 로컬 컴퓨터에서 Azure에 로그인 하 고 작업 영역에 연결 합니다.
 2. 로컬 컴퓨터에서 작업 영역 Key Vault에 암호를 설정 합니다.
 3. 원격 실행을 제출 합니다.
 4. 원격 실행 내에서 Key Vault에서 비밀을 가져와 사용 합니다.

## <a name="set-secrets"></a>비밀 설정

Azure Machine Learning에서 [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) 클래스에는 비밀을 설정 하는 메서드가 포함 되어 있습니다. 로컬 Python 세션에서 먼저 Key Vault 작업 영역에 대 한 참조를 가져온 다음 메서드를 사용 하 여 이름과 값을 사용 하 [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secret-name--value-) 여 암호를 설정 합니다. __Set_secret__ 메서드는 이름이 이미 있는 경우 비밀 값을 업데이트 합니다.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

비밀 값을 파일에 일반 텍스트로 저장 하는 것은 안전 하지 않으므로 Python 코드에 저장 하지 마세요. 대신 환경 변수에서 비밀 값 (예: Azure DevOps build secret 또는 대화형 사용자 입력)을 가져옵니다.

메서드를 사용 하 여 비밀 이름을 나열할 수 있으며 [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=truelist-secrets--) , 한 번에 여러 암호를 설정할 수 있는 일괄 처리 버전[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secrets-secrets-batch-) 도 있습니다.

## <a name="get-secrets"></a>암호 가져오기

로컬 코드에서 메서드를 사용 하 여 [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueget-secret-name-) 이름으로 비밀 값을 가져올 수 있습니다.

가 제출 된 실행의 경우 [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-) [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secret-name-) 클래스와 함께 메서드를 사용 [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) 합니다. 제출 된 실행은 작업 영역을 인식 하므로이 메서드는 작업 영역 인스턴스화를 바로 실행 하 고 비밀 값을 직접 반환 합니다.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

비밀 값을 쓰거나 인쇄 하 여 노출 하지 않도록 주의 해야 합니다.

한 번에 여러 암호에 액세스 하는 데 사용할 수 있는 일괄 처리 버전인 [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secrets-secrets-) 도 있습니다.

## <a name="next-steps"></a>다음 단계

 * [예제 노트북 보기](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure Machine Learning를 사용 하 여 엔터프라이즈 보안에 대해 알아보기](concept-enterprise-security.md)
