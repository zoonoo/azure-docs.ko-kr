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
ms.openlocfilehash: 739be373992fcd994f085f8571675779e450bfee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090215"
---
# <a name="collaborative-coding-with-git"></a>Git를 사용하여 공동 코딩

이 문서에서는 데이터 과학 프로젝트용 공동 작업 코드 개발 프레임 워크로 Git를 사용 하는 방법을 설명 합니다. 이 문서에서는 Azure Repos의 코드를 Azure Boards의 [agile 개발](agile-development.md) 작업 항목에 연결 하는 방법, 코드 검토를 수행 하는 방법 및 변경에 대 한 끌어오기 요청을 만들고 병합 하는 방법에 대해 설명 합니다.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Azure Repos 분기에 작업 항목 연결 

Azure DevOps는 Azure Boards 사용자 스토리 또는 작업 작업 항목을 Azure Repos Git 리포지토리 분기에 연결 하는 편리한 방법을 제공 합니다. 사용자 스토리 또는 작업을 연결 된 코드에 직접 연결할 수 있습니다. 

작업 항목을 새 분기에 연결 하려면 작업 항목 옆에 있는 **동작** 줄임표 (**...**)를 선택 하 고 상황에 맞는 메뉴에서를 스크롤하여 **새 분기**를 선택 합니다.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

**분기 만들기** 대화 상자에서 새 분기 이름과 기본 Azure Repos Git 리포지토리 및 분기를 제공 합니다. 기본 리포지토리는 작업 항목과 동일한 Azure DevOps 프로젝트에 있어야 합니다. 기본 분기는 모든 기존 분기가 될 수 있습니다. **분기 만들기**를 선택 합니다. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Windows 또는 Linux에서 다음 Git bash 명령을 사용 하 여 새 분기를 만들 수도 있습니다.

```bash
git checkout -b <new branch name> <base branch name>

```
을 지정 하지 않으면 \<base branch name> 새 분기는을 기반으로 `master` 합니다. 

작업 분기로 전환 하려면 다음 명령을 실행 합니다. 

```bash
git checkout <working branch name>
```

작업 분기로 전환한 후에는 코드 또는 문서 아티팩트 개발을 시작 하 여 작업 항목을 완료할 수 있습니다. 를 실행 `git checkout master` 하면 분기로 다시 전환 `master` 됩니다.

각 사용자 스토리 작업 항목에 대해 Git 분기를 만드는 것이 좋습니다. 그런 다음 각 작업 (Task) 작업 항목에 대해 사용자 스토리 분기를 기반으로 분기를 만들 수 있습니다. 동일한 프로젝트에 대해 여러 사용자 스토리에서 작업 하는 사용자가 여러 명 이거나 동일한 사용자 스토리에 대해 다른 작업을 수행 하는 경우 사용자 Story-Task 관계에 해당 하는 계층의 분기를 구성 합니다. 분기를 공유 하는 경우 각 팀 멤버가 다른 분기 또는 다른 코드 또는 다른 아티팩트에 대해 작업을 수행 하 여 충돌을 최소화할 수 있습니다. 

다음 다이어그램은 TDSP에 권장 되는 분기 전략을 보여 줍니다. 여기에 표시 된 것과 같이 분기가 여러 개 필요 하지 않을 수 있습니다. 특히 한 명의 사용자만 프로젝트에서 작업 하거나 한 명의 사용자만이 사용자 스토리의 모든 작업에 대해 작업 하는 경우입니다. 하지만 개발 분기를 기본 분기에서 분리 하는 것이 항상 좋은 방법 이며, 개발 작업으로 인해 릴리스 분기가 중단 되지 않도록 방지 하는 데 도움이 될 수 있습니다. Git 분기 모델에 대 한 전체 설명은 [성공적인 Git 분기 모델](https://nvie.com/posts/a-successful-git-branching-model/)을 참조 하세요.

![3](./media/collaborative-coding-with-git/3-git-branches.png)

또한 작업 항목을 기존 분기에 연결할 수도 있습니다. 작업 항목의 **세부 정보** 페이지에서 **링크 추가**를 선택 합니다. 그런 다음 작업 항목을 연결할 기존 분기를 선택 하 고 **확인**을 선택 합니다. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>분기에 대 한 작업 및 변경 내용 커밋 

작업 항목 (예: 로컬 컴퓨터의 분기에 R 스크립트 파일 추가)을 변경한 후에는 `script` 다음 Git bash 명령을 사용 하 여 로컬 분기에서 업스트림 작업 분기로 변경 내용을 커밋할 수 있습니다.

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>끌어오기 요청 만들기

하나 이상의 커밋 및 푸시 후에 현재 작업 분기를 기본 분기에 병합할 준비가 되 면 Azure Repos에서 *끌어오기 요청* 을 만들고 제출할 수 있습니다. 

Azure devops 프로젝트의 기본 페이지에서 왼쪽 탐색 영역에 있는 **리포지토리**  >  **끌어오기 요청** 을 가리킵니다. 그런 다음 **새 끌어오기 요청** 단추 또는 **끌어오기 요청 만들기** 링크 중 하나를 선택 합니다.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

**새 끌어오기 요청** 화면에서 필요한 경우 변경 내용을 병합할 Git 리포지토리 및 분기로 이동 합니다. 원하는 다른 정보를 추가 하거나 변경 합니다. **검토자**에서 검토자의 이름을 추가 하 고 **만들기**를 선택 합니다. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>검토 및 병합

끌어오기 요청을 만들면 검토자가 끌어오기 요청을 검토 하는 전자 메일 알림을 받게 됩니다. 검토자는 변경 내용이 작동 하는지 테스트 하 고 가능한 경우 요청자의 변경 내용을 확인 합니다. 검토자는 평가에 따라 주석을 만들고, 변경을 요청 하 고, 끌어오기 요청을 승인 또는 거부할 수 있습니다. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

검토자가 변경 내용을 승인한 후에는 사용자나 병합 권한을 가진 다른 사용자가 작업 분기를 기본 분기에 병합할 수 있습니다. **완료**를 선택 하 고 **끌어오기 요청 완료** 대화 상자에서 **전체 병합** 을 선택 합니다. 병합 한 후에 작업 분기를 삭제 하도록 선택할 수 있습니다. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

요청이 **완료**된 것으로 표시 되는지 확인 합니다. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

왼쪽 탐색 영역에서 **리포지토리** 로 돌아가서 분기가 삭제 된 이후 주 분기로 전환 되었음을 알 수 있습니다 `script` .

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

다음 Git bash 명령을 사용 하 여 `script` 작업 분기를 기본 분기에 병합 하 고 병합 후에 작업 분기를 삭제할 수도 있습니다.

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>다음 단계

[데이터 과학 작업 실행](execute-data-science-tasks.md) 은 유틸리티를 사용 하 여 대화형 데이터 탐색, 데이터 분석, 보고 및 모델 작성과 같은 몇 가지 일반적인 데이터 과학 작업을 완료 하는 방법을 보여 줍니다.

[예제 연습에서는](walkthroughs.md) 링크 및 미리 보기 설명과 함께 특정 시나리오의 연습이 나열 됩니다. 연결 된 시나리오에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합 하 여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

