---
title: Azure Machine Learning에 대 한 Git 통합
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 로컬 Git 리포지토리와 통합 하는 방법에 대해 알아봅니다. Git 리포지토리의 로컬 디렉터리에서 학습 실행을 제출할 때 리포지토리, 분기 및 현재 커밋에 대 한 정보는 실행의 일부로 추적 됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 71ac7793fe5226215c5d4eab98f84dba356b114c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91275968"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 Git 통합

[Git](https://git-scm.com/) 은 프로젝트를 공유 하 고 공동 작업할 수 있는 인기 있는 버전 제어 시스템입니다. 

작업 추적을 위한 Git 리포지토리를 완벽 하 게 지원 Azure Machine Learning-공유 작업 영역 파일 시스템에 직접 리포지토리를 복제 하거나 로컬 워크스테이션에서 Git를 사용 하거나 CI/CD 파이프라인에서 Git를 사용할 수 있습니다.

Azure Machine Learning에 작업을 제출할 때 원본 파일이 로컬 git 리포지토리에 저장 된 경우 리포지토리에 대 한 정보는 학습 프로세스의 일부로 추적 됩니다.

Azure Machine Learning는 로컬 git 리포지토리의 정보를 추적 하므로 특정 중앙 리포지토리에 연결 되지 않습니다. GitHub, GitLab, Bitbucket, Azure DevOps 또는 다른 모든 git 호환 서비스에서 리포지토리를 복제할 수 있습니다.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>작업 영역 파일 시스템에 Git 리포지토리 복제
Azure Machine Learning는 작업 영역에 있는 모든 사용자에 게 공유 파일 시스템을 제공 합니다.
이 파일 공유에 Git 리포지토리를 복제 하려면 계산 인스턴스 & 만들어 터미널을 여는 것이 좋습니다.
터미널이 열리면 전체 Git 클라이언트에 액세스할 수 있고 Git CLI 환경을 통해 Git를 복제 하 고 사용할 수 있습니다.

사용자가 작업 분기에서 직접 충돌 하지 않도록 사용자 디렉터리에 리포지토리를 복제 하는 것이 좋습니다.

인증할 수 있는 Git 리포지토리 (GitHub, Azure Repos, BitBucket 등)를 복제할 수 있습니다.

복제에 대 한 자세한 내용은 [GIT CLI를 사용 하는 방법](https://guides.github.com/introduction/git-handbook/)에 대 한 가이드를 참조 하세요.

## <a name="authenticate-your-git-account-with-ssh"></a>SSH를 사용 하 여 Git 계정 인증
### <a name="generate-a-new-ssh-key"></a>새 SSH 키 생성
1) Azure Machine Learning 노트북 탭에서 [터미널 창을 엽니다](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#terminal) .

2) 전자 메일 주소를 대체 하 여 아래 텍스트를 붙여넣습니다.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

그러면 제공 된 전자 메일을 레이블로 사용 하 여 새 ssh 키가 생성 됩니다.

```
> Generating public/private rsa key pair.
```

3) "키를 저장할 파일을 입력 하십시오." 라는 메시지가 표시 되 면 Enter 키를 누릅니다. 기본 파일 위치를 적용 합니다.

4) 기본 위치가 '/home/azureuser/.ssh ' 인지 확인 하 고 enter 키를 누릅니다. 그렇지 않은 경우 '/home/azureuser/.ssh ' 위치를 지정 합니다.

> [!TIP]
> SSH 키가 '/home/azureuser/.ssh '에 저장 되어 있는지 확인 하세요. 이 파일은 계산 인스턴스 소유자만 액세스할 수 있습니다.

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) 프롬프트에 보안 암호를 입력 합니다. 보안을 강화 하기 위해 SSH 키에 암호를 추가 하는 것이 좋습니다.

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Git 계정에 공개 키 추가
1) 터미널 창에서 공개 키 파일의 내용을 복사 합니다. 키의 이름을 바꾼 경우 id_rsa를 공개 키 파일 이름으로 바꿉니다.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **터미널에서 복사 및 붙여넣기**
> * Windows: `Ctrl-Insert` 복사 하 여 사용 `Ctrl-Shift-v` 하거나 `Shift-Insert` 붙여 넣을 수 있습니다.
> * Mac OS: `Cmd-c`: 복사, `Cmd-v`: 붙여넣기
> * FireFox/IE에서 클립보드 사용 권한을 제대로 지원하지 않을 수 있습니다.

2) 키 출력을 선택 하 여 클립보드에 복사 합니다.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops#step-2--add-the-public-key-to-azure-devops-servicestfs)  **2 단계**에서 시작 합니다.

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). **4 단계**에서 시작 합니다.

### <a name="clone-the-git-repository-with-ssh"></a>SSH를 사용 하 여 Git 리포지토리 복제

1) Git 리포지토리에서 SSH Git 복제 URL을 복사 합니다.

2) `git clone`SSH Git 리포지토리 url을 사용 하려면 아래 명령에 url을 붙여넣습니다. 이는 다음과 같습니다.

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

다음과 같은 응답이 표시 됩니다.

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH는 서버의 SSH 지문을 표시 하 고 확인 하도록 요청할 수 있습니다. 표시 된 지문이 SSH 공개 키 페이지의 지문 중 하 나와 일치 하는지 확인 해야 합니다.

SSH는 메시지 가로채기 ( [man-in-the-middle) 공격](https://technet.microsoft.com/library/cc959354.aspx)으로부터 보호 하기 위해 알 수 없는 호스트에 연결할 때이 지문을 표시 합니다. 호스트의 지문을 수락한 후에는 지문이 변경 되지 않는 한 SSH에서 다시 묻지 않습니다.

3) 연결을 계속할지 묻는 메시지가 표시 되 면을 입력 `yes` 합니다. Git은 리포지토리를 복제 하 고 나중에 Git 명령에 대해 SSH를 사용 하 여 연결 하도록 원본 원격을 설정 합니다.

## <a name="track-code-that-comes-from-git-repositories"></a>Git 리포지토리에서 제공 되는 코드 추적

Python SDK 또는 Machine Learning CLI에서 학습 실행을 제출 하면 모델을 학습 하는 데 필요한 파일이 작업 영역에 업로드 됩니다. `git`개발 환경에서이 명령을 사용할 수 있는 경우 업로드 프로세스에서이 명령을 사용 하 여 파일이 git 리포지토리에 저장 되었는지 확인 합니다. 그렇다면 git 리포지토리의 정보는 학습 실행의 일부로도 업로드 됩니다. 이 정보는 학습 실행을 위해 다음 속성에 저장 됩니다.

| 속성 | 값을 가져오는 데 사용 되는 Git 명령입니다. | 설명 |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | 리포지토리가 복제 된 URI입니다. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | 리포지토리가 복제 된 URI입니다. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 실행을 제출한 활성 분기입니다. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 실행을 제출한 활성 분기입니다. |
| `azureml.git.commit` | `git rev-parse HEAD` | 실행을 위해 제출 된 코드의 커밋 해시입니다. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 실행을 위해 제출 된 코드의 커밋 해시입니다. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`분기/커밋이 변경 되었으면이 고, 그렇지 않으면입니다. 그렇지 않으면 `false` 입니다. |

이 정보는 평가기, machine learning 파이프라인 또는 스크립트 실행을 사용 하는 실행을 위해 전송 됩니다.

개발 환경의 git 리포지토리에 학습 파일이 없거나 `git` 명령을 사용할 수 없는 경우 git 관련 정보는 추적 되지 않습니다.

> [!TIP]
> 개발 환경에서 git 명령을 사용할 수 있는지 확인 하려면 셸 세션, 명령 프롬프트, PowerShell 또는 기타 명령줄 인터페이스를 열고 다음 명령을 입력 합니다.
>
> ```
> git --version
> ```
>
> 이 설치 되어 있는 경우 경로에와 유사한 응답이 표시 `git version 2.4.1` 됩니다. 개발 환경에 git을 설치 하는 방법에 대 한 자세한 내용은 [git 웹 사이트](https://git-scm.com/)를 참조 하세요.

## <a name="view-the-logged-information"></a>기록 된 정보 보기

Git 정보는 학습 실행을 위한 속성에 저장 됩니다. Azure Portal, Python SDK 및 CLI를 사용 하 여이 정보를 볼 수 있습니다. 

### <a name="azure-portal"></a>Azure portal

1. [Studio 포털](https://ml.azure.com)에서 작업 영역을 선택 합니다.
1. __실험__을 선택 하 고 실험 중 하나를 선택 합니다.
1. __실행 번호__ 열에서 실행 중 하나를 선택 합니다.
1. __출력 + 로그__를 선택한 다음 __로그__ 및 __azureml__ 항목을 확장 합니다. __ ### \_ Azure__로 시작 하는 링크를 선택 합니다.

기록 된 정보에는 다음 JSON과 유사한 텍스트가 포함 됩니다.

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

학습 실행을 제출한 후 [실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) 개체가 반환 됩니다. `properties`이 개체의 특성에는 로깅된 git 정보가 포함 됩니다. 예를 들어 다음 코드는 커밋 해시를 검색 합니다.

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run`CLI 명령은 실행에서 속성을 검색 하는 데 사용할 수 있습니다. 예를 들어 다음 명령은 실험에서 마지막으로 실행 된 속성을 반환 합니다 `train-on-amlcompute` .

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

자세한 내용은 [az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true) reference 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [모델 학습에 컴퓨팅 대상 사용](how-to-set-up-training-targets.md)
