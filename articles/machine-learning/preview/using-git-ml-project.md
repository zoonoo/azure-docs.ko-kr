---
title: "Azure Machine Learning Workbench 프로젝트와 함께 Git 리포지토리 사용 | Microsoft Docs"
description: "이 문서는 Azure Machine Learning Workbench 프로젝트와 함께 Git 리포지토리를 사용하는 방법을 설명합니다."
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 0cd447a52964578dd2348a786dd57a45ea87516e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Azure Machine Learning Workbench 프로젝트와 함께 Git 리포지토리 사용
이 문서에서는 Azure Machine Learning Workbench가 Git을 사용하여 데이터 과학 실험에서 버전 제어 기능을 제공하고 재현 가능성을 보장하는 방법을 설명합니다. 프로젝트를 클라우드 Git 리포지토리와 연결하는 방법에 대한 지침도 함께 제공됩니다.

## <a name="introduction"></a>소개
Azure Machine Learning Workbench는 처음부터 Git 통합으로 설계되었습니다. 새 프로젝트를 만들 때 프로젝트 폴더는 자동으로 로컬 Git 리포지토리(repo)로 "Git 초기화"됩니다. 한편, 각 실행의 프로젝트 폴더 변경 내용을 추적할 수 있도록 분기 이름이 _AzureMLHistory/<project_GUID>_인 두 번째 숨겨진 로컬 Git 리포지토리가 생성됩니다. 

Azure ML 프로젝트를 VSTS(Visual Studio Team Service) 프로젝트 내에서 호스팅되는 Git 리포지토리에 연결하면 로컬 및 원격 모두에서 자동 버전 제어를 사용할 수 있습니다. 이 연결을 통해 누구나 원격 리포지토리에 액세스하여 다른 컴퓨터(로밍)로 최신 소스 코드를 다운로드할 수 있습니다.  

> [!NOTE]
> VSTS에는 Azure Machine Learning 실험 서비스와 독립적인 자체 액세스 제어 목록이 있습니다. 사용자 액세스는 Git 리포지토리와 Azure ML 작업 영역 또는 프로젝트 사이에 여러 개가 있으며 관리가 필요할 수 있습니다. 작업 영역 이외에도 코드 수준 액세스를 포함한 Azure ML 프로젝트를 팀 멤버와 공유하려면 명시적으로 VSTS Git 리포지토리에 대한 적절한 액세스 권한을 해당 멤버에게 부여해야 합니다. 

Git을 사용하면 _마스터_ 분기를 사용하거나 다른 분기를 리포지토리에 만들어서 버전 제어를 명시적으로 관리할 수 있습니다. 로컬 Git 리포지토리만 사용해도 되고, 프로비전된 경우 원격 Git 리포지토리로 푸시할 수도 있습니다.

이 다이어그램에서는 VSTS Git 리포지토리와 Azure ML 프로젝트 간 관계를 보여 줍니다.

![AML Git](media/using-git-ml-project/aml_git.png)

원격 Git 리포지토리를 사용하여 시작하려면 다음 기본 지침을 따릅니다.

> [!NOTE]
> 현재 Azure Machine Learning은 VSTS 계정에서의 Git 리포지토리만을 지원합니다. 일반 Git 리포지토리(예: GitHub 등)에 대한 지원은 향후에 예정되어 있습니다.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>1단계. Azure ML 실험 계정 만들기
아직 계정이 없는 경우 Azure ML 실험 계정을 만들고 Azure ML Workbench 앱을 설치합니다. [설치 및 만들기 빠른 시작](quickstart-installation.md)에서 자세한 내용을 참조하세요.

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>2단계. 팀 프로젝트를 만들거나 기존 팀 프로젝트 사용
[Azure Portal](https://portal.azure.com/)에서 새 **팀 프로젝트**를 만듭니다.
1. **+**를 클릭합니다.
2. **“팀 프로젝트”**를 검색합니다.
3. 필요한 정보를 입력합니다.
    - 이름: 팀 이름입니다.
    - 버전 제어: **Git**
    - 구독: 새 Azure Machine Learning 실험 계정을 사용하는 구독입니다.
    - 위치: Azure Machine Learning 실험 리소스 가까이 있는 지역에 이상적으로 유지됩니다.
4. **만들기**를 클릭합니다. 

![Azure Portal에서 팀 프로젝트 만들기](media/using-git-ml-project/create_vsts_team.png)

Azure Machine Learning Workbench에 액세스할 때 사용하는 AAD(Azure Active Directory) 계정으로 로그인합니다. 그렇지 않으면 명령줄을 사용하여 Azure ML 프로젝트를 만들고 Git 리포지토리에 대한 개인용 액세스 토큰을 제공하지 않는 이상, 시스템에서 AAD 자격 증명을 사용하여 프로젝트에 액세스할 수 없습니다. 자세한 내용은 나중에 살펴보겠습니다.

팀 프로젝트를 만들었으면 다음 단계로 이동할 준비가 된 것입니다.

방금 만든 팀 프로젝트로 바로 이동하기 위한 URL은 `https://<team_project_name>.visualstudio.com`입니다.

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>3단계. 원격 Git 리포지토리로 새 Azure ML 프로젝트 만들기
Azure ML Workbench를 시작하고 새 프로젝트를 만듭니다. Git 리포지토리 텍스트 상자를 2단계에서 얻은 VSTS Git 리포지토리 URL로 채웁니다. 일반적으로 다음과 같이 보입니다. `http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Git 리포지토리로 Azure ML 프로젝트 만들기](media/using-git-ml-project/create_project_with_git_rep.png)

명령줄 도구를 사용하여 프로젝트를 만들 수도 있습니다. 개인용 액세스 토큰을 제공하는 옵션이 있습니다. Azure ML은 사용자의 AAD 자격 증명을 사용하지 않고 이 토큰을 사용하여 사용자 대신 Git 리포지토리에 액세스할 수 있습니다.

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> 빈 프로젝트 템플릿을 선택한 경우 선택한 Git 리포지토리에 _master_ 분기가 이미 있어도 괜찮습니다. Azure ML은 _master_ 분기를 로컬에서 복제만 하고 `aml_config` 폴더와 다른 프로젝트 메타데이터 파일을 로컬 프로젝트 폴더에 추가합니다. 하지만 다른 프로젝트 템플릿을 선택하면 Git 리포지토리에 _master_ 분기가 반드시 없어야 하며 그렇지 않으면 오류가 표시됩니다. 대안은 프로젝트를 만들도록 `az ml project create` 명령줄 도구를 사용하고 `--force` 스위치를 제공하는 것입니다. 이렇게 하면 원래 master 분기의 파일이 삭제되고 선택한 템플릿의 새 파일로 대체됩니다.

이제 새 Azure ML 프로젝트가 활성화되고 준비 완료된 원격 Git 리포지토리 통합으로 만들어졌습니다. 프로젝트 폴더는 항상 로컬 Git 리포지토리로 Git 초기화됩니다. 또한 Git _원격_이 원격 VSTS Git 리포지토리를 설정되어 있으므로 원격 Git 리포지토리로 푸시될 수 있습니다.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>3a단계. VSTS Git 리포지토리에 기존 Azure ML 프로젝트 연결
필요에 따라 VSTS Git 리포지토리를 사용하지 않고 실행된 기록 스냅숏에 대한 로컬 Git 리포지토리에 따라 Azure ML 프로젝트를 만들 수도 있습니다. 또한 다음 명령을 사용하여 나중에 이 기존 Azure ML 프로젝트에 VSTS Git 리포지토리를 연결할 수 있습니다.

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> 연결된 Git 리포지토리가 없는 Azure ML 프로젝트에서 리포지토리 업데이트 작업만 수행할 수 있습니다. Git 리포지토리가 연결된 후에는 제거할 수 없습니다.

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>4단계. Git 리포지토리에 있는 프로젝트 스냅숏 캡처
이제 프로젝트에서 몇 가지 스크립트 실행을 수행할 수 있으며 실행 사이에 몇 가지가 변경됩니다. 이는 데스크톱 앱 또는 CLI에서 `az ml experiment submit` 명령을 사용하여 수행할 수 있습니다. 자세한 내용은 [아이리스 분류 자습서](tutorial-classifying-iris-part-1.md)를 수행하세요. 각 실행에 대해 프로젝트 폴더의 어느 파일에서든 변경 사항이 있는 경우 전체 프로젝트 폴더의 스냅숏이 커밋되고 `AzureMLHistory/<Project_GUID>` 분기 아래의 원격 Git 리포지토리로 푸시됩니다. VSTS Git 리포지토리 URL을 검색하여 이 분기를 찾으면 분기 및 커밋을 볼 수 있습니다. 

> [!NOTE] 
> 스냅숏은 스크립트 실행 전에 커밋됩니다. 현재는 데이터 준비 실행 또는 Notebook 셀 실행이 스냅숏을 트리거하지 않습니다.

![실행 기록 분기](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Git 명령을 사용하여 기록 분기에서 직접 작업하지 않는 것이 좋습니다. 실행 기록이 손상될 수 있습니다. 고유의 Git 작업 대신 마스터 분기를 사용하거나 다른 분기를 만드세요.

## <a name="step-5-restore-a-previous-project-snapshot"></a>5단계. 이전 프로젝트 스냅숏 복원 
전체 프로젝트 폴더를 Azure ML Workbench에서 이전 실행 기록 프로젝트 상태 스냅숏의 상태로 복원합니다.
1. 작업 표시줄에서 **실행**을 클릭합니다(시계 모양 아이콘).
2. **실행 목록** 보기에서 복원하려는 실행을 클릭합니다.
3. **실행 세부 정보** 보기에서 **복원**을 클릭합니다.

![실행 기록 분기](media/using-git-ml-project/restore_project.png)

또는 Azure ML Workbench CLI 창에서 다음 명령을 사용할 수 있습니다.

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

이 명령을 실행하면 특정 실행이 시작된 경우 수행된 스냅숏으로 전체 프로젝트 폴더를 덮어쓰게 됩니다. 하지만 프로젝트가 현재 분기에 계속 머물게 됩니다. 즉, 현재 프로젝트 폴더의 **모든 변경 내용을 잃게** 됩니다. 따라서 이 명령을 실행하는 경우 특별한 주의가 필요합니다. 위의 작업을 수행하기 전에 Git이 변경 내용을 현재 분기에 커밋합니다. 자세한 내용은 아래를 참조하세요.

## <a name="step-6-use-the-master-branch"></a>6단계. 마스터 분기 사용
현재 프로젝트 상태를 실수로 잃지 않도록 방지하는 한 가지 방법은 Git 리포지토리의 마스터 분기(또는 직접 만든 분기)로 프로젝트를 커밋하는 것입니다. 마스터 분기에 작동하도록 명령줄에서(또는 원하는 다른 즐겨찾는 Git 클라이언트 도구) 직접 Git을 사용할 수 있습니다. 예:

```sh
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

이제 5단계에 따라 이전 스냅숏으로 프로젝트를 안전하게 복원할 수 있으며, 마스터 분기에서 만든 커밋으로 항상 되돌아올 수 있습니다.

## <a name="authentication"></a>인증
프로젝트 스냅숏의 생성 및 복원에 대한 Azure ML에서 실행 기록 기능만 사용하면 Git 리포지토리 인증에 대해 걱정할 필요가 없습니다. 실험 서비스 계층에 의해 처리됩니다.

단, 사용자 고유의 Git 도구를 사용하여 버전 제어를 관리하는 경우 VSTS에 있는 원격 Git 리포지토리에 대한 인증을 올바르게 처리해야 합니다. Azure ML에서 HTTPS 프로토콜을 사용하여 원격 Git 리포지토리가 로컬 리포지토리에 Git 원격으로 추가됩니다. 즉, 원격에 Git 명령(예: 밀어넣기 또는 끌어오기)을 실행할 때 사용자 이름과 암호 또는 개인용 액세스 토큰을 입력해야 합니다. [다음 지침](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate)에 따라 VSTS Git 리포지토리에 개인용 액세스 토큰을 만듭니다.

## <a name="next-steps"></a>다음 단계
Team Data Science Process를 사용하여 프로젝트 구조를 구성하는 방법을 알아봅니다. [TDSP를 사용한 프로젝트 구성](how-to-use-tdsp-in-azure-ml.md)을 참조하세요.
