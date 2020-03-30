---
title: Azure 기계 학습을 위한 Git 통합
titleSuffix: Azure Machine Learning
description: Azure 기계 학습이 로컬 Git 리포지토리와 통합되는 방법을 알아봅니다. Git 리포지토리인 로컬 디렉터리에서 실행된 교육을 제출할 때 리포지토리, 분기 및 현재 커밋에 대한 정보가 실행의 일부로 추적됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402832"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure 기계 학습을 위한 Git 통합

[Git은](https://git-scm.com/) 프로젝트에서 공유하고 공동 작업할 수 있는 인기 있는 버전 제어 시스템입니다. 

Azure Machine Learning은 작업을 추적하기 위한 Git 리포지토리를 완벽하게 지원하므로 리포지토리를 공유 작업 영역 파일 시스템에 직접 복제하거나 로컬 워크스테이션에서 Git을 사용하거나 CI/CD 파이프라인에서 Git을 사용할 수 있습니다.

Azure Machine Learning에 작업을 제출할 때 원본 파일이 로컬 git 리포지토리에 저장되면 리포지토리에 대한 정보가 학습 프로세스의 일부로 추적됩니다.

Azure Machine Learning은 로컬 git 리포지토리의 정보를 추적하므로 특정 중앙 리포지토리에 연결되지 않습니다. 리포지토리는 GitHub, GitLab, 비트버킷, Azure DevOps 또는 기타 git 호환 서비스에서 복제할 수 있습니다.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>작업 영역 파일 시스템에 Git 리포지토리 복제
Azure 기계 학습은 작업 영역의 모든 사용자에게 공유 파일 시스템을 제공합니다.
Git 리포지토리를 이 파일 공유에 복제하려면 터미널을 열기 & 계산 인스턴스를 만드는 것이 좋습니다.
터미널이 열리면 전체 Git 클라이언트에 액세스할 수 있으며 Git CLI 환경을 통해 Git을 복제하고 작업할 수 있습니다.

다른 사용자가 작업 브랜치에서 직접 충돌하지 않도록 리포지토리를 사용자 디렉토리에 복제하는 것이 좋습니다.

인증할 수 있는 Git 리포지토리(GitHub, Azure 리포지토리, 비트버킷 등)를 복제할 수 있습니다.

Git CLI 를 사용하는 방법에 대한 가이드는 [여기를](https://guides.github.com/introduction/git-handbook/)참조하십시오.

## <a name="track-code-that-comes-from-git-repositories"></a>Git 리포지토리에서 제공하는 코드 추적

Python SDK 또는 기계 학습 CLI에서 실행된 교육을 제출하면 모델을 학습하는 데 필요한 파일이 작업 영역에 업로드됩니다. 개발 `git` 환경에서 명령을 사용할 수 있는 경우 업로드 프로세스에서 파일을 git 리포지토리에 저장되어 있는지 확인하는 데 사용합니다. 그렇다면 git 리포지토리의 정보도 교육 실행의 일부로 업로드됩니다. 이 정보는 교육 실행을 위해 다음 속성에 저장됩니다.

| 속성 | 값을 얻는 데 사용되는 Git 명령 | 설명 |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | 리포지토리가 복제된 URI입니다. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | 리포지토리가 복제된 URI입니다. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 실행이 제출되었을 때의 활성 분기입니다. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 실행이 제출되었을 때의 활성 분기입니다. |
| `azureml.git.commit` | `git rev-parse HEAD` | 실행을 위해 제출된 코드의 커밋 해시입니다. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 실행을 위해 제출된 코드의 커밋 해시입니다. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`브랜치/커밋이 더러워지면 그렇지 `false`않으면 . |

이 정보는 추정기, 기계 학습 파이프라인 또는 스크립트 실행을 사용하는 실행에 대해 전송됩니다.

학습 파일이 개발 환경의 git 리포지토리에 없거나 `git` 명령을 사용할 수 없는 경우 git 관련 정보가 추적되지 않습니다.

> [!TIP]
> 개발 환경에서 git 명령을 사용할 수 있는지 확인하려면 셸 세션, 명령 프롬프트, PowerShell 또는 기타 명령줄 인터페이스를 열고 다음 명령을 입력합니다.
>
> ```
> git --version
> ```
>
> 설치한 경우 경로에서 와 `git version 2.4.1`유사한 응답을 받게 됩니다. 개발 환경에 git 설치에 대한 자세한 내용은 [Git 웹 사이트를](https://git-scm.com/)참조하십시오.

## <a name="view-the-logged-information"></a>기록된 정보 보기

git 정보는 학습 실행을 위해 속성에 저장됩니다. Azure 포털, Python SDK 및 CLI를 사용하여 이 정보를 볼 수 있습니다. 

### <a name="azure-portal"></a>Azure portal

1. Azure [포털에서](https://portal.azure.com)작업 영역을 선택합니다.
1. __실험을__선택한 다음 실험 중 하나를 선택합니다.
1. __RUN 번호__ 열에서 실행 중 하나를 선택합니다.
1. __로그를__선택한 다음 로그 및 __azureml__ 항목을 __확장합니다.__ __ ### \_Azure로__시작하는 링크를 선택합니다.

    ![포털의 ###_azure 항목](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

기록된 정보에는 다음 JSON과 유사한 텍스트가 포함되어 있습니다.

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

교육 실행을 제출한 후 [실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 개체가 반환됩니다. 이 `properties` 개체의 특성에는 기록된 git 정보가 포함되어 있습니다. 예를 들어 다음 코드는 커밋 해시를 검색합니다.

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run` CLI 명령을 사용하여 실행에서 속성을 검색할 수 있습니다. 예를 들어 다음 명령은 다음 명령이 명명된 `train-on-amlcompute`실험에서 마지막 실행에 대한 속성을 반환합니다.

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

자세한 내용은 az [ml 실행](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) 참조 설명서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [모델 학습에 계산 대상 설정 및 사용](how-to-set-up-training-targets.md)
