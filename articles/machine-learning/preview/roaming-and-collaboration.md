---
title: "Azure Machine Learning Workbench에서 로밍 및 공동 작업 | Microsoft Docs"
description: "알려진 문제 목록 및 문제 해결에 도움이 되는 가이드"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 50f48fb096cb907e050769a8a4159689eb25418c
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench에서 로밍 및 공동 작업
이 문서는 Azure Machine Learning Workbench를 활용하여 프로젝트를 컴퓨터 간에 로밍하는 것은 물론 팀 동료와 공동 작업을 할 수 있는 방법을 안내합니다. 

원격 Git Repository(repo) 링크와 함께 Azure Machine Learning 프로젝트를 만들 때에 프로젝트 메타 데이터와 스냅숏은 클라우드에 저장됩니다. 클라우드 링크를 사용하면 다른 컴퓨터 (로밍)에서 프로젝트에 액세스할 수 있습니다. 또한 공동 작업을 사용할 수 있게 하여 동료들에게 액세스 권한을 제공할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
먼저, 실험 계정 액세스 권한을 사용해 Azure Machine Learning Workbench를 설치합니다. 자세한 내용은 [설치 가이드](quickstart-installation.md)를 따릅니다.

둘째, [Visual Studio Team System](https://www.visualstudio.com)에 액세스하여 프로젝트를 연결할 리포지토리를 만듭니다. Git에 대한 자세한 내용은 [Using Git Repo with an Azure Machine Learning Workbench Project(Azure Machine Learning Workbench 프로젝트로 Git Repo 사용하기)](using-git-ml-project.md) 문서를 참조합니다.

## <a name="create-a-new-azure-machine-learning-project"></a>새 Azure Machine Learning 프로젝트 만들기
Azure Machine Learning Workbench를 시작하고 새 프로젝트를 만듭니다 (예를 들어 _아이리스_) 유효한 VSTS Git 리포지토리 URL로 **Visualstudio.com GIT 리포지토리 URL** 텍스트 상자에 채웁니다. 

> [!IMPORTANT]
> 빈 프로젝트 템플릿을 선택한 경우 선택한 Git 리포지토리에 _master_ 분기가 이미 있어도 괜찮습니다. Azure ML은 _master_ 분기를 로컬에서 복제만 하고 `aml_config` 폴더와 다른 프로젝트 메타데이터 파일을 로컬 프로젝트 폴더에 추가합니다. 하지만 다른 프로젝트 템플릿을 선택하면 Git 리포지토리에 _master_ 분기가 반드시 없어야 하며 그렇지 않으면 오류가 표시됩니다. 대안은 프로젝트를 만들도록 `az ml project create` 명령줄 도구를 사용하고 `--force` 스위치를 제공하는 것입니다. 이렇게 하면 원래 master 분기의 파일이 삭제되고 선택한 템플릿의 새 파일로 대체됩니다.

프로젝트를 만들었다면 프로젝트 내에서 모든 스크립트를 몇 차례 실행해 봅니다. 이 작업은 원격 Git 리포지토리의 실행 기록 분기로 프로젝트 상태를 커밋합니다. 

> [!NOTE] 
> 스크립트만 실행 기록 분기에 대한 트리거 커밋을 실행합니다. 데이터 준비 실행 또는 Notebook 실행은 실행 기록 분기에서 프로젝트 스냅숏을 트리거하지 않습니다.

Git 인증을 설정한 경우, 명시적으로 마스터 분기에서 작동하거나 새 분기를 만들 수 있습니다. 

예를 들어 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>로밍
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>두 번째 컴퓨터에서 Azure Machine Learning Workbench 열기
VSTS Git 리포지토리가 프로젝트와 연결되면 Azure Machine Learning Workbench를 설치한 모든 컴퓨터에서 _아이리스_ 프로젝트에 액세스할 수 있습니다. 

또 다른 컴퓨터에서 아이리스 프로젝트에 액세스하려면 프로젝트를 만들 때 사용한 것과 동일한 자격 증명으로 앱에 로그인해야 합니다. 또한 동일한 Experimentation Account and Workspace(실험 계정 및 작업 영역)으로 이동해야 합니다. _아이리스_ 프로젝트는 작업 영역 내에서 다른 프로젝트와 함께 사전순으로 나열됩니다. 

### <a name="download-project-on-second-machine"></a>두 번째 컴퓨터에 프로젝트를 다운로드합니다.
작업 영역이 두 번째 컴퓨터에서 열릴 때 _아이리스_ 프로젝트 옆의 아이콘은 일반적인 폴더 아이콘과는 다릅니다. 다운로드 아이콘은 프로젝트의 콘텐츠가 클라우드에 있으며 현재 컴퓨터로 다운로드해야 함을 나타냅니다. 

![프로젝트 만들기](./media/roaming-and-collaboration/downloadable-project.png)

_아이리스_ 프로젝트를 클릭하면 다운로드 작업을 시작합니다. 잠시 후, 다운로드가 완료되면, 두 번째 컴퓨터에서 프로젝트에 액세스할 수 있습니다. 

Windows에서는 `C:\Users\<username>\Documents\AzureML`입니다.

MacOS에서는 여기입니다.`/home/<username>/Documents/AzureML`

향후 릴리스에서는 대상 폴더를 선택할 수 있도록 하는 기능을 강화할 계획입니다. 

> [!NOTE]
> Azure ML 디렉터리에 프로젝트와 정확히 같은 이름을 가진 폴더가 있을 경우 다운로드가 실패합니다. 당분간은 이 문제를 해결하기 위해 기존 폴더의 이름을 바꿀 필요가 있습니다.


### <a name="work-on-the-downloaded-project"></a>다운로드한 프로젝트에서 작업 
새로 다운로드한 프로젝트는 프로젝트에서 마지막 실행했을 때의 프로젝트 상태를 반영합니다. 실행을 제출할 때마다 프로젝트 상태의 스냅숏이 VSTS Git 리포지토리에 있는 실행 기록 분기에 자동으로 커밋됩니다. 두 번째 컴퓨터에서 프로젝트를 인스턴스화할 때 연결된 가장 최근 실행과 관련된 스냅숏을 사용합니다. 
 

## <a name="collaboration"></a>공동 작업
VSTS Git 리포지토리에 연결된 프로젝트에서 팀 동료와 공동 작업할 수 있습니다. 실험 계정, 작업 영역 및 프로젝트에서 사용자에게 사용 권한을 할당할 수 있습니다. 이때 Azure 명령줄 인터페이스를 사용하여 Azure Resource Manager 명령을 수행할 수 있습니다. 또는 [Azure Portal](https://portal.azure.com)을 사용할 수도 있습니다. [다음 섹션](#portal)을 참조하세요.    

### <a name="using-command-line-to-add-users"></a>명령줄을 사용하여 사용자를 추가하려면
예를 들어 보겠습니다. 예를 들어, Alice는 e_Iris_ 프로젝트의 소유자이고 Bob과 액세스를 공유하고자 합니다. 

Alice는 **파일** 메뉴를 클릭하여 **명령 프롬프트**를 선택한 후 _아이리스_ 프로젝트에 맞게 구성된 명령 프롬프트를 시작합니다. 그런 후 Alice는 다음 명령을 실행하여 Bob에게 어떤 수준의 액새스 권한을 부여할지 결정할 수 있습니다.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

직접 또는 상속으로 역할 할당을 하고나면 Bob이 워크벤치 프로젝트 목록에서 그 프로젝트를 볼 수 있습니다. 프로젝트를 보려면 응용 프로그램을 다시 시작해야 할 수도 있습니다. 그런 후 Bob은 [로밍 섹션](#roaming)에 설명된 대로 프로젝트를 다운로드하고 Alice와 공동 작업을 할 수 있습니다. 

프로젝트에 공동 작업하는 모든 사용자에 대한 실행 기록이 동일한 원격 Git 리포지토리로 커밋됩니다. 따라서 Alice가 실행을 할 때, Bob은 워크벤치 앱의 프로젝트 실행 기록 섹션에서 그 실행을 볼 수 있습니다. Bob은 또한 Alice가 시작한 실행을 포함하여 어떤 실행의 상태로도 프로젝트를 복원할 수 있습니다. 

프로젝트에 대해 원격 Git 리포지토리를 공유함으로써 Alice와 Bob은 또한 마스터 분기에서 공동 작업을 할 수 있습니다. 필요한 경우 공동 작업을 하기 위해 개인 분기를 만들어 Git 끌어오기 요청을 사용하여 병합할 수 있습니다. 

### <a name="using-azure-portal-to-add-users"></a>Azure 포털을 사용하여 사용자를 추가하려면
<a name="portal"></a>

Azure Machine Learning 실험 계정, 작업 영역 및 프로젝트는 Azure Resource Manager 리소스입니다. 역할을 할당하려면 [Azure Portal](https://portal.azure.com)에서 액세스 제어 링크를 사용할 수 있습니다. 

모든 리소스 뷰에서 사용자를 추가할 리소스를 찾습니다. 페이지 내의 액세스 제어(IAM) 링크를 클릭합니다. 사용자 추가 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>샘플 공동 작업 워크플로
공동 작업 흐름을 설명하기 위해 예제를 살펴보겠습니다. Contoso의 직원인 Alice와 Bob은 Azure ML Workbench를 사용하여 데이터 과학 프로젝트에서 공동 작업을 하려고 합니다. 이들의 ID는 동일한 Contoso Azure AD 테넌트에 속합니다.

1. Alice는 먼저 VSTS 프로젝트에 빈 Git 리포지토리를 만듭니다. 이 VSTS 프로젝트는 Contoso AAD 테넌트 아래에 만들어진 Azure 구독에 있습니다. 

2. 그런 다음 Alice가 자신의 컴퓨터에 Azure ML 실험 계정, 작업 영역 및 Azure ML Workbench 프로젝트를 만듭니다. 프로젝트를 만들 때 Git 리포지토리 URL을 제공합니다.

3. Alice가 프로젝트 작업을 시작합니다. 몇 가지 스크립트를 작성하고 몇 가지 실행을 수행합니다. 각각의 실행에 대해 전체 프로젝트 폴더의 스냅숏이 Workbench에서 만든 VSTS Git 리포지토리의 실행 기록 분기에 커밋으로 자동 푸시됩니다.

4. Alice는 현재 진행 중인 작업에 만족합니다. 변경 내용을 로컬 _master_ 분기에 커밋하고 VSTS Git 리포지토리 _master_ 분기에 푸시하려고 합니다. 이를 위해 프로젝트를 열어 놓고 Azure ML Workbench에서 명령 프롬프트 창을 시작하여 다음 명령을 실행합니다.
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. 그런 다음 Alice가 Bob을 작업 영역에 Contributor로 추가합니다. 이 작업은 Azure Portal에서 수행하거나 위에서 설명한 `az role assignment` 명령을 사용하여 수행할 수 있습니다. 또한 Bob에게 VSTS Git 리포지토리에 대한 읽기/쓰기 권한을 부여합니다.

6. 이제 Bob이 자신의 컴퓨터에서 Azure ML Workbench에 로그인합니다. Alice가 공유해준 작업 영역과 그 작업 영역에 나열된 프로젝트를 볼 수 있습니다. 

7. Bob이 프로젝트 이름을 클릭하면 프로젝트가 컴퓨터로 다운로드됩니다.
    
    a. 다운로드한 프로젝트 파일은 실행 기록에 기록된 최신 실행에 대한 스냅숏의 복제본입니다. master 분기의 마지막 커밋은 아닙니다.
    
    b. 로컬 프로젝트 폴더가 스테이징이 취소된 변경 내용으로 _master_ 분기에 설정되어 있습니다.

8. 이제 Bob은 Alice가 수행한 실행을 찾아보고 이전 실행의 스냅숏을 복원할 수 있습니다.

9. Bob은 Alice가 푸시한 최신 변경 사항을 가져와서 다른 분기에서 작업을 시작하려고 합니다. 그래서 Azure ML Workbench에서 명령 프롬프트 창을 열고 다음 명령을 실행합니다.

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. 이제 Bob이 프로젝트를 수정하고 새 실행을 제출합니다. 변경이 _bob_ 분기에서 수행됩니다. 그리고 Bob의 실행이 Alice에게도 보입니다.

11. 이제 Bob이 변경 사항을 원격 Git 리포지토리에 푸시할 준비가 되었습니다. Alice가 작업 중인 _master_ 분기와의 충돌을 피하기 위해 _bob_이라는 새로운 원격 분기에 자신의 작업을 푸시하기로 결정합니다.

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. 이제 Bob은 Alice에게 자신의 코드에 있는 멋진 새 요령을 알릴 수 있고, 원격 Git 리포지토리에 _bob_ 분기에서 _master_ 분기로 끌어오기 요청을 만들 수 있습니다. 그런 다음 Alice는 끌어오기 요청을 _master_ 분기에 병합할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure ML Workbench에 Git를 사용하는 방법은 [Azure Machine Learning Workbench 프로젝트에서 Git 리포지토리 사용](using-git-ml-project.md)을 참조하세요.