---
title: 교육 실행에 비밀 사용
titleSuffix: Azure Machine Learning
description: 작업 영역 키 볼트를 사용하여 안전한 방식으로 교육 실행에 비밀 전달
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942270"
---
# <a name="use-secrets-in-training-runs"></a>교육 실행에 비밀 사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 교육 실행에서 비밀을 안전하게 사용하는 방법을 배웁니다. 사용자 이름 및 암호와 같은 인증 정보는 비밀입니다. 예를 들어 학습 데이터를 쿼리하기 위해 외부 데이터베이스에 연결하는 경우 사용자 이름과 암호를 원격 실행 컨텍스트에 전달해야 합니다. 이러한 값을 cleartext로 학습 스크립트에 코딩하는 것은 비밀을 노출하는 것처럼 안전하지 않습니다. 

대신 Azure 기계 학습 작업 영역에 Azure [키 자격 증명 모음이라는](https://docs.microsoft.com/azure/key-vault/key-vault-overview)연결된 리소스가 있습니다. 이 키 볼트를 사용하여 Azure 기계 학습 파이썬 SDK의 API 집합을 통해 원격 실행에 대한 비밀을 안전하게 전달합니다.

비밀을 사용하기 위한 기본 흐름은 다음과 같은 것입니다.
 1. 로컬 컴퓨터에서 Azure에 로그인하고 작업 영역에 연결합니다.
 2. 로컬 컴퓨터에서 작업 영역 키 자격 증명 모음에서 비밀을 설정합니다.
 3. 원격 실행을 제출합니다.
 4. 원격 실행 내에서 키 볼트에서 비밀을 얻고 사용합니다.

## <a name="set-secrets"></a>비밀 설정

Azure 기계 학습에서 [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) 클래스에는 비밀을 설정하는 메서드가 포함되어 있습니다. 로컬 Python 세션에서 먼저 작업 영역 키 볼트에 대한 참조를 [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) 얻은 다음 메서드를 사용하여 이름과 값으로 비밀을 설정합니다. __set_secret__ 메서드는 이름이 이미 있는 경우 비밀 값을 업데이트합니다.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

비밀 값을 파이썬 코드에 넣지 마십시오. 대신 환경 변수(예: Azure DevOps 빌드 보안 검색기 또는 대화형 사용자 입력)에서 비밀 값을 가져옵니다.

메서드를 [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) 사용하여 비밀 이름을 나열할 수 있으며 한 번에 여러 비밀을 설정할 수 있는 일괄 처리[버전인 set_secrets()도](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) 있습니다.

## <a name="get-secrets"></a>암호 가져오기

로컬 코드에서 메서드를[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) 사용하여 이름으로 비밀 값을 얻을 수 있습니다.

제출된 실행의 [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) 경우 [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) 클래스와 [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) 함께 메서드를 사용합니다. 제출된 실행은 작업 영역을 인식하므로 이 메서드는 작업 영역 인스턴스화를 바로 가하고 비밀 값을 직접 반환합니다.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

비밀 값을 작성하거나 인쇄하여 노출하지 않도록 주의하십시오.

한 번에 여러 암호에 액세스하기 위한 일괄 처리 [버전인 get_secrets()도](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) 있습니다.

## <a name="next-steps"></a>다음 단계

 * [예제 노트북 보기](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure 기계 학습을 통해 엔터프라이즈 보안에 대해 알아보기](concept-enterprise-security.md)
