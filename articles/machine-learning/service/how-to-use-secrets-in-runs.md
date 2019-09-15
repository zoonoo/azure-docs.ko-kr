---
title: 학습 실행에서 비밀 사용
titleSuffix: Azure Machine Learning
description: 작업 영역을 사용 하 여 안전한 방식으로 학습 실행에 비밀 전달 Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 4872ba8a707192cd61ec371fa982a076d410e918
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996564"
---
# <a name="use-secrets-in-training-runs"></a>학습 실행에서 비밀 사용

이 문서에서는 학습 실행 시 안전 하 게 암호를 사용 하는 방법에 대해 알아봅니다. 예를 들어 외부 데이터베이스에 연결 하 여 학습 데이터를 쿼리하려면 원격 실행 컨텍스트에 사용자 이름 및 암호를 전달 해야 합니다. 일반 텍스트의 학습 스크립트에 이러한 값을 코딩 하는 것은 암호를 노출 하므로 안전 하지 않습니다. 

대신 Azure Machine Learning 작업 영역는 연결 된 리소스로 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 됩니다. 이 Key Vault는 Azure Machine Learning Python SDK의 Api 집합을 통해 안전 하 게 원격 실행에 암호를 전달 하는 데 사용할 수 있습니다.

비밀 사용에 대 한 기본 흐름은 다음과 같습니다.
 1. 로컬 컴퓨터에서 Azure에 로그인 하 고 작업 영역에 연결 합니다.
 2. 로컬 컴퓨터의 작업 영역에서 비밀을 설정 Key Vault
 3. 원격 실행을 제출 합니다.
 4. 원격 실행 내에서 키 값에서 비밀을 가져와 사용 합니다.

## <a name="set-secrets"></a>비밀 설정

Azure Machine Learning Python SDK에서 [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) 클래스에는 비밀을 설정 하는 메서드가 포함 되어 있습니다. 로컬 Python 세션에서 먼저 Key Vault 작업 영역에 대 한 참조를 가져온 다음 [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) 메서드를 사용 하 여 이름 및 값으로 암호를 설정 합니다.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

비밀 값을 파일에 일반 텍스트로 저장 하는 것은 안전 하지 않으므로 Python 코드에 저장 하지 마십시오. 대신 환경 변수에서 비밀 값 (예: Azure DevOps build secret 또는 대화형 사용자 입력)을 가져옵니다.

[List_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) 메서드를 사용 하 여 비밀 이름을 나열할 수 있습니다. __Set_secret__ 메서드는 이름이 이미 있는 경우 비밀 값을 업데이트 합니다.

## <a name="get-secrets"></a>암호 가져오기

로컬 코드에서 [Keyvault. get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) 메서드를 사용 하 여 이름으로 비밀 값을 가져올 수 있습니다.

[실험](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)을 사용 하 여 제출 된 실행에서 [Run. get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) 메서드를 사용 합니다. 제출 된 실행은 작업 영역을 인식 하므로이 메서드는 작업 영역 인스턴스화를 바로 실행 하 고 비밀 값을 직접 반환 합니다.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

비밀 값을 쓰거나 인쇄 하 여 노출 하지 않도록 주의 해야 합니다.

Set 및 get 메서드는 한 번에 여러 비밀에 액세스 하기 위한 batch 버전 [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) 및 [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) 도 포함 합니다.

## <a name="next-steps"></a>다음 단계

 * [예제 노트북 보기](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure Machine Learning를 사용 하 여 엔터프라이즈 보안에 대해 알아보기](concept-enterprise-security.md)
