---
title: Git를 사용하여 공동 코딩 - Team Data Science Process
description: Agile 계획과 함께 Git를 사용하여 데이터 과학 프로젝트용 공동 코드 개발을 수행하는 방법입니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721900"
---
# <a name="collaborative-coding-with-git"></a>Git를 사용하여 공동 코딩

이 문서에서는 Git을 데이터 과학 프로젝트의 공동 코드 개발 프레임워크로 사용하는 방법을 설명합니다. 이 문서에서는 Azure Repos의 코드를 Azure 보드의 개발 작업 항목을 [민첩하게](agile-development.md) 만드는 방법, 코드 검토를 수행하는 방법 및 변경에 대한 끌어오기 요청을 만들고 병합하는 방법을 설명합니다.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>작업 항목을 Azure Repos 분기에 연결 

Azure DevOps는 Azure 편집기 Git 리포지토리 분기와 Azure 보드 사용자 스토리 또는 작업 작업 항목을 연결하는 편리한 방법을 제공합니다. 사용자 스토리 또는 작업을 연결된 코드에 직접 연결할 수 있습니다. 

작업 항목을 새 분기에 연결하려면 작업 항목 옆의 **작업** 타원(... ))을 선택하고 컨텍스트 메뉴에서 **새 분기로**스크롤하여 선택합니다.**...**  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

분기 **만들기** 대화 상자에서 새 분기 이름과 기본 Azure Repos Git 리포지토리 및 분기를 제공합니다. 기본 리포지토리는 작업 항목과 동일한 Azure DevOps 프로젝트에 있어야 합니다. 기본 분기는 마스터 분기 또는 다른 기존 분기일 수 있습니다. **분기 만들기를**선택합니다. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Windows 또는 Linux에서 다음과 같은 Git bash 명령을 사용하여 새 분기를 만들 수도 있습니다.

```bash
git checkout -b <new branch name> <base branch name>

```
\<> 기본 분기 이름을 지정하지 않으면 새 분기가 `master`을 기반으로 합니다. 

작업 분기로 전환하려면 다음 명령을 실행합니다. 

```bash
git checkout <working branch name>
```

작업 분기로 전환한 후 코드 또는 문서 아티팩트 개발을 시작하여 작업 항목을 완료할 수 있습니다. 실행하면 `git checkout master` 지점으로 다시 `master` 전환됩니다.

각 사용자 스토리 작업 항목에 대해 Git 분기를 만드는 것이 좋습니다. 그런 다음 각 작업 작업 항목에 대해 사용자 스토리 분기를 기반으로 분기를 만들 수 있습니다. 동일한 프로젝트에 대해 서로 다른 사용자 스토리에서 작업하거나 동일한 사용자 스토리에 대해 서로 다른 작업에서 작업하는 여러 사람이 있을 때 사용자 스토리-태스크 관계에 해당하는 계층 구조의 분기를 구성합니다. 각 팀 구성원이 다른 분기에서 작업하거나 분기를 공유할 때 다른 코드 또는 다른 아티팩트에서 작업하여 충돌을 최소화할 수 있습니다. 

다음 다이어그램에서는 TDSP에 권장되는 분기 전략을 보여 주며, 이 에 대한 권장 분기 전략을 보여 주시면 다음과 같은 것입니다. 특히 한두 사람이 프로젝트에서 작업하거나 사용자 스토리의 모든 작업에서 한 사람만 작업하는 경우 여기에 표시된 분기가 많지 않을 수 있습니다. 그러나 마스터 분기에서 개발 분기를 분리하는 것은 항상 좋은 사례이며 개발 활동에 의해 릴리스 분기가 중단되는 것을 방지할 수 있습니다. Git 분기 모델에 대한 전체 설명은 [성공적인 Git 분기 모델을](https://nvie.com/posts/a-successful-git-branching-model/)참조하십시오.

![3](./media/collaborative-coding-with-git/3-git-branches.png)

또한 작업 항목을 기존 분기에 연결할 수도 있습니다. 작업 항목의 **세부 정보** 페이지에서 **링크 추가를**선택합니다. 그런 다음 기존 분기를 선택하여 작업 항목을 연결하고 **확인을**선택합니다. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>분기에 대한 작업 및 변경 커밋 

로컬 컴퓨터의 `script` 분기에 R 스크립트 파일을 추가하는 등 작업 항목을 변경한 후 다음 Git bash 명령을 사용하여 로컬 분기에서 업스트림 작업 분기로 변경한 것을 커밋할 수 있습니다.

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>끌어오기 요청 만들기

하나 이상의 커밋 및 푸시 후 현재 작업 분기를 기본 분기에 병합할 준비가 되면 Azure Repos에서 *끌어오기 요청을* 만들고 제출할 수 있습니다. 

Azure DevOps 프로젝트의 기본 페이지에서 왼쪽 탐색에서 **리포지토리** > **끌어오기 요청을** 가리킵니다. 그런 다음 **새 끌어오기 요청** 단추 중 하나를 선택하거나 **끌어오기 요청** 만들기 링크를 선택합니다.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

새 **끌어오기 요청** 화면에서 필요한 경우 변경 내용을 병합할 Git 리포지토리 및 분기로 이동합니다. 원하는 다른 정보를 추가하거나 변경합니다. **검토자**에서 검토자의 이름을 추가한 다음 **만들기를**선택합니다. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>검토 및 병합

끌어오기 요청을 만들면 검토자에게 끌어오기 요청을 검토하라는 이메일 알림이 전송됩니다. 검토자는 변경 내용이 작동하는지 테스트하고 가능한 경우 요청자와 함께 변경 내용을 확인합니다. 검토자는 평가에 따라 의견을 말하고, 변경을 요청하고, 끌어오기 요청을 승인하거나 거부할 수 있습니다. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

검토자가 변경 내용을 승인한 후 사용자 또는 병합 권한이 있는 다른 사용자가 작업 분기를 기본 분기에 병합할 수 있습니다. **완료를**선택한 다음 **끌어오기 요청 완료** 대화 상자에서 병합 **완료를** 선택합니다. 병합된 후 작업 분기를 삭제하도록 선택할 수 있습니다. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

요청이 **완료됨으로**표시되어 있는지 확인합니다. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

왼쪽 탐색에서 **Repos로** 돌아가면 `script` 분기가 삭제된 이후 마스터 분기로 전환된 것을 볼 수 있습니다.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

다음 Git bash 명령을 사용하여 작업 분기를 `script` 기본 분기에 병합하고 병합한 후 작업 분기를 삭제할 수도 있습니다.

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>다음 단계

[데이터 과학 작업을 실행하면](execute-data-science-tasks.md) 유틸리티를 사용하여 대화형 데이터 탐색, 데이터 분석, 보고 및 모델 생성과 같은 몇 가지 일반적인 데이터 과학 작업을 완료하는 방법을 보여 주며, 이 작업을 수행합니다.

[예제 연습에는](walkthroughs.md) 링크 및 축소판 설명과 함께 특정 시나리오의 연습이 나열됩니다. 연결된 시나리오에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

