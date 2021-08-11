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
ms.openlocfilehash: ca24a781f4f3ad5c210813dabbb896de35056ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97588712"
---
# <a name="collaborative-coding-with-git"></a>Git를 사용하여 공동 코딩

이 문서에서는 Git을 데이터 과학 프로젝트용 공동 작업 코드 개발 프레임워크로 사용하는 방법을 설명합니다. 이 문서에서는 Azure Repos의 코드를 Azure Boards의 [Agile 개발](agile-development.md) 작업 항목에 연결하는 방법, 코드 검토를 수행하는 방법 및 변경에 대한 끌어오기 요청을 만들고 병합하는 방법을 설명합니다.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Azure Repos 분기에 작업 항목 연결 

Azure DevOps는 Azure Boards 사용자 스토리 또는 태스크 작업 항목을 Azure Repos Git 리포지토리 분기에 연결하는 편리한 방법을 제공합니다. 사용자 스토리 또는 태스크를 연결된 코드에 직접 연결할 수 있습니다. 

작업 항목을 새 분기에 연결하려면 작업 항목 옆에 있는 **작업** 줄임표( **...** )를 선택하고 상황에 맞는 메뉴에서 **새 분기** 로 스크롤한 후 선택합니다.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

**분기 만들기** 대화 상자에서 새 분기 이름과 기본 Azure Repos Git 리포지토리 및 분기를 제공합니다. 기본 리포지토리는 작업 항목과 동일한 Azure DevOps 프로젝트에 있어야 합니다. 기본 분기는 기존 분기일 수 있습니다. **분기 만들기** 를 선택합니다. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Windows 또는 Linux에서 다음 Git bash 명령을 사용하여 새 분기를 만들 수도 있습니다.

```bash
git checkout -b <new branch name> <base branch name>

```
\<base branch name>을 지정하지 않으면 새 분기는 `main`을 기준으로 합니다. 

작업 분기로 전환하려면 다음 명령을 실행합니다. 

```bash
git checkout <working branch name>
```

작업 중인 분기로 전환한 후에는 작업 항목을 완료하기 위한 코드 또는 문서 아티팩트 개발을 시작할 수 있습니다. `git checkout main`을 실행하면 `main` 분기로 다시 전환됩니다.

각 사용자 스토리 작업 항목에 대해 Git 분기를 만드는 것이 좋습니다. 그런 다음, 각 태스크 작업 항목에 대해 사용자 스토리 분기에 따라 분기를 만듭니다. 동일한 프로젝트의 여러 다른 사용자 스토리 또는 동일한 사용자 사례에 대해 여러 다른 태스크를 사용하는 사람이 여러 명 있는 경우 사용자 스토리 태스크 관계에 해당하는 계층 구조로 분기를 구성합니다. 분기를 공유하는 경우 각 팀 구성원이 다른 분기 또는 다른 코드나 다른 아티팩트에 대해 작업을 수행하도록 하여 충돌을 최소화할 수 있습니다. 

다음 그림에서는 TDSP에 권장되는 분기 전략을 보여 줍니다. 여기에 표시된 것처럼 많은 분기가 필요하지 않을 수 있습니다. 특히 한두 명의 사람만 하나의 프로젝트에서 작업하거나 한 사람만 사용자 스토리의 모든 태스크에서 작업을 수행하는 경우가 여기에 해당합니다. 하지만 개발 분기를 기본 분기에서 분리하는 것은 항상 좋은 방법이며, 개발 작업으로 인해 릴리스 분기가 중단되지 않도록 방지하는 데 도움이 될 수 있습니다. Git 분기 모델에 대한 전체 설명은 [성공적인 Git 분기 모델](https://nvie.com/posts/a-successful-git-branching-model/)을 참조하세요.

![3](./media/collaborative-coding-with-git/3-git-branches.png)

또한 작업 항목을 기존 분기에 연결할 수도 있습니다. 작업 항목의 **세부 정보** 페이지에서 **링크 추가** 를 선택합니다. 그런 다음, 작업 항목을 연결할 기존 분기를 선택하고 **확인** 을 선택합니다. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>분기에서 작업 및 변경 내용 커밋 

로컬 머신의 `script` 분기에 R 스크립트 파일을 추가하는 등 작업 항목을 변경한 후에는 다음 Git bash 명령을 사용하여 로컬 분기에서 업스트림 작업 분기로 변경 내용을 커밋할 수 있습니다.

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>끌어오기 요청 만들기

하나 이상의 커밋 및 푸시 후에 현재 작업 분기를 기본 분기에 병합할 준비가 되면 Azure Repos에서 *끌어오기 요청* 을 만들고 제출할 수 있습니다. 

Azure devops 프로젝트의 기본 페이지에서 왼쪽 탐색 영역에 있는 **리포지토리** > **끌어오기 요청** 을 가리킵니다. 그런 다음, **새 끌어오기 요청** 단추 또는 **끌어오기 요청 만들기** 링크를 선택합니다.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

**새 끌어오기 요청** 화면에서 필요한 경우 변경 내용을 병합할 Git 리포지토리 및 분기로 이동합니다. 원하는 다른 정보를 추가하거나 변경합니다. **검토자** 에서 검토자의 이름을 추가하고 **만들기** 를 선택합니다. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>검토 및 병합

끌어오기 요청을 만든 후에는 검토자가 끌어오기 요청을 검토하기 위한 메일 알림을 받습니다. 검토자는 변경 내용이 작동하는지 여부를 테스트하고 가능한 경우 요청자에게 변경 내용을 확인합니다. 검토자는 평가에 따라 주석을 만들고, 변경을 요청하고, 끌어오기 요청을 승인 또는 거부할 수 있습니다. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

검토자가 변경 내용을 승인한 후에는 사용자나 병합 권한을 가진 누구든지 작업 분기를 기본 분기에 병합할 수 있습니다. **완료** 를 선택하고 **끌어오기 요청 완료** 대화 상자에서 **병합 완료** 를 선택합니다. 병합한 후에 작업 분기를 삭제하도록 선택할 수 있습니다. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

요청이 **완료됨** 으로 표시되는지 확인합니다. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

왼쪽 탐색 영역에서 **리포지토리** 로 돌아가면 `script` 분기가 삭제된 이후에 주 분기로 전환되었음을 알 수 있습니다.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

또한 다음 Git bash 명령을 사용하여 작업 중인 `script` 분기를 기본 분기에 병합하고, 병합한 후에 작업 중인 분기를 삭제할 수 있습니다.

```bash
git checkout main
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>다음 단계

[데이터 과학 작업 실행](execute-data-science-tasks.md)은 유틸리티를 사용하여 반복 데이터 탐색, 데이터 분석, 보고 및 모델 생성 등과 같은 여러 가지 일반적인 데이터 과학 작업을 완료하는 방법을 보여 줍니다.

[예제 연습](walkthroughs.md)에는 링크 및 썸네일 설명과 함께 특정 시나리오의 연습이 나열됩니다. 연결된 시나리오에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다. 

