---
title: "데이터 과학 프로젝트 실행 - Azure | Microsoft Docs"
description: "데이터 과학자가 추적 가능하고, 버전을 제어하고, 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행하는 방법을 설명합니다."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>데이터 과학 프로젝트 실행

이 문서에서는 [TDSP(Team Data Science Process)](overview.md)를 사용하여 데이터 과학자가 프로젝트 팀 내에서 체계적이고 버전을 제어하고 동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행하는 방법에 대해 설명합니다. TDSP는 Microsoft에서 개발하여 클라우드 기반 예측 분석 솔루션을 효율적으로 실행하는 일련의 구조화된 작업을 제공하는 프레임워크입니다. 이 프로세스를 표준화하는 데이터 과학 팀에서 처리하는 인력 역할 및 관련 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요. 

이 항목에는 다음을 수행하는 방법에 대한 지침이 포함되어 있습니다. 

1. 프로젝트와 관련된 작업 항목에 대해 **스프린트 계획**을 수행합니다.<br> 스프린트 계획에 익숙하지 않은 경우 [여기](https://en.wikipedia.org/wiki/Sprint_(software_development) "여기")서 세부 정보와 일반 정보를 찾을 수 있습니다. 
2. 스프린트에 **작업 항목을 추가**합니다.
3. git에서 추적되는 **코딩 작업과 작업 항목을 연결**합니다.
4. **코드 검토**를 수행합니다. 


>[AZURE.NOTE] 다음 지침 집합에서는 VSTS(Visual Studio Team Services)를 사용하여 TDSP 팀 환경을 설정하는 데 필요한 단계에 대해 설명합니다. Microsoft에서 TDSP를 구현하는 방법이기 때문에 VSTS를 사용하여 이러한 작업을 수행하는 방법을 지정합니다. 위 목록의 (3)과 (4) 항목은 VSTS을 사용하도록 선택하는 경우 자연스럽게 얻는 이점입니다. 그룹에 다른 코드 호스팅 플랫폼이 사용되는 경우 팀 리더가 수행해야 하는 작업은 일반적으로 변경되지 않습니다. 그러나 이러한 작업을 완료하는 방법은 다를 수 있습니다. 예를 들어 섹션 6 **git 분기와 작업 항목 연결** 항목은 VSTS에서처럼 쉽지 않을 수 있습니다.

다음 그림에서는 데이터 과학 프로젝트 구현과 관련된 일반적인 스프린트 계획, 코딩 및 소스 제어 워크플로를 보여 줍니다.

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1. <a name='Terminology-1'></a>용어 

TDSP 스프린트 계획 프레임워크에는 자주 사용되는 네 가지 유형의**작업 항목**, 즉 **기능**, **사용자 스토리**, **작업** 및 **버그**가 있습니다. 각 팀 프로젝트에서 모든 작업 항목에 대한 단일 백로그를 유지 관리합니다. 팀 프로젝트의 git 리포지토리 수준에는 백로그가 없습니다. 정의는 다음과 같습니다.

- **기능**: 프로젝트 참여에 해당하는 기능입니다. 클라이언트와의 다른 참여는 다른 기능으로 간주됩니다. 마찬가지로 클라이언트가 있는 프로젝트의 다른 단계를 다른 기능으로 간주하는 것이 가장 좋습니다. 기능 이름을 지정하기 위해 ***ClientName-EngagementName***과 같은 스키마를 선택하면 이름 자체에서 프로젝트/참여의 컨텍스트를 쉽게 인식할 수 있습니다.
- **스토리**: 종단 간에 기능(프로젝트)을 완료하는 데 필요한 별도의 작업 항목입니다. 스토리의 예는 다음과 같습니다.
    - 데이터 가져오기 
    - 데이터 탐색 
    - 기능 생성
    - 모델 작성
    - 모델 조작 
    - 모델 재학습
- **작업**: 할당 가능한 코드 또는 문서 작업 항목 또는 특정 스토리를 완료하기 위해 수행해야 하는 다른 작업입니다. 예를 들어 *데이터 가져오기* 스토리의 작업은 다음과 같습니다.
    -  SQL Server 자격 증명 가져오기 
    -  SQL Data Warehouse에 데이터 업로드. 
- **버그**: 일반적으로 작업을 완료할 때 수행되는 기존 코드 또는 문서에 필요한 수정을 참조합니다. 각 스테이지 또는 작업이 누락되어 버그가 발생하는 경우 스토리 또는 작업이 되도록 에스컬레이션할 수 있습니다. 

>[AZURE.NOTE] 기능, 스토리, 작업 및 버그 개념은 데이터 과학에 사용되는 SCM(소프트웨어 코드 관리)에서 빌려온 것이며, 기존의 SCM 정의와 약간 다를 수 있습니다.

##  2. <a name='SprintPlanning-2'></a>스프린트 계획 

스프린트 계획은 프로젝트 우선 순위 지정 및 리소스 계획 및 할당에 유용합니다. 많은 데이터 과학자들이 여러 프로젝트에 참여하고 있으며 각 프로젝트는 완료하는 데 몇 달이 걸릴 수 있습니다. 프로젝트는 종종 다른 속도로 진행됩니다. VSTS 서버에서 팀 프로젝트의 작업 항목을 쉽게 생성, 관리 및 추적하고 스프린트 계획을 수행하여 프로젝트가 예상대로 진행되도록 할 수 있습니다. 

VSTS의 스프린트 계획에 대한 단계별 지침은 [이 링크](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning)를 참조하세요. 


##  3. <a name='AddFeature-3'></a>기능 추가  

팀 프로젝트 아래에 프로젝트 리포지토리가 만들어지면 팀 **개요** 페이지로 이동하여 **작업 관리**를 클릭합니다.

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

백로그에 기능을 포함하려면 **백로그** --> **기능** --> **새로 만들기**를 차례로 클릭하고, 기능 **제목**(일반적으로 프로젝트 이름)을 입력한 다음, **추가**를 클릭합니다.

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

방금 만든 기능을 두 번 클릭합니다. 설명을 입력하고, 팀 구성원에게 이 기능을 할당하고, 이 기능의 계획 매개 변수를 설정합니다. 

또한 이 기능을 프로젝트 리포지토리에 연결할 수도 있습니다. **개발** 섹션 아래에서 **링크 추가**를 클릭합니다. 기능 편집을 완료하면 **저장 후 닫기**를 클릭하여 종료합니다.


##  4. <a name='AddStoryunderfeature-4'></a>기능 아래에서 스토리 추가 

이 기능을 사용하면 프로젝트(기능)를 완료하는 데 필요한 주요 단계를 설명하는 스토리를 추가할 수 있습니다. 새 스토리를 추가하려면 백로그 보기에서 기능의 왼쪽에 있는 **+** 기호를 클릭합니다.  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

팝업 창에서 상태, 설명, 주석, 계획 및 우선 순위와 같은 스토리 세부 정보를 편집할 수 있습니다.

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

**개발** 아래에서 **+ 링크 추가**를 클릭하여 이 스토리를 기존 리포지토리에 연결할 수 있습니다. 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5. <a name='AddTaskunderstory-5'></a>스토리에 작업 추가 

작업은 각 스토리를 완료하는 데 필요한 구체적인 세부 단계입니다. 스토리의 모든 작업이 완료되면 스토리도 완료됩니다. 

스토리에 작업을 추가하려면 스토리 항목 옆에 있는 **+** 기호를 클릭하고, **작업**을 선택한 다음, 팝업 창에서 이 작업에 대한 자세한 정보를 채웁니다.

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

기능, 스토리 및 작업을 만들면 **백로그** 또는 **보드** 보기에서 볼 수 있습니다.

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6. <a name='Linkaworkitemwithagitbranch-6'></a>git 분기와 작업 항목 연결 

VSTS는 작업 항목(스토리 또는 작업)을 git 분기와 연결하는 편리한 방법을 제공합니다. 이렇게 하면 스토리 또는 작업을 연결되는 코드에 직접 연결할 수 있습니다. 

작업 항목을 새 분기에 연결하려면 작업 항목을 두 번 클릭하고, 팝업 창의 **+ 링크 추가** 아래에서 **새 분기 만들기**를 클릭합니다.  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

이 새 분기에 대한 정보(예: 분기 이름, 기본 git 리포지토리 및 분기)를 제공합니다. 선택한 git 리포지토리는 작업 항목이 속한 동일한 팀 프로젝트 아래에 있는 리포지토리여야 합니다. 기본 분기는 마스터 분기 또는 다른 기존 분기일 수 있습니다.

![11](./media/project-execution/project-execution-11-create-a-branch.png)

각 스토리 작업 항목에 대해 git 분기를 만드는 것이 좋습니다. 그런 다음 각 작업 작업 항목에 대해 스토리 분기에 따라 분기를 만듭니다. 스토리-작업 관계에 해당하는 분기를 이러한 계층적 방식으로 구성하는 것은 여러 사람이 동일한 프로젝트의 다른 스토리에 대해 작업하거나 동일한 스토리의 다른 작업을 수행하는 경우에 유용합니다. 각 팀 구성원이 다른 분기에서 작업하는 경우 및 분기를 공유할 때 각 구성원이 다른 코드 또는 다른 아티팩트에서 작업하는 경우에 발생하는 충돌을 최소화할 수 있습니다. 

다음 그림에서는 TDSP에 권장되는 분기 전략을 보여 줍니다. 이 그림과 같이 많은 분기가 필요하지 않을 수 있습니다. 특히 한두 명의 사람이 동일한 프로젝트에서 작업하거나 한 사람이 스토리의 모든 작업을 수행할 때 그렇습니다. 그러나 개발 분기와 마스터 분기는 분리하는 것이 항상 좋습니다. 이렇게 하면 릴리스 분기가 개발 작업으로 인해 중단되는 것을 방지할 수 있습니다. git 분기 모델에 대한 더 자세한 설명은 [성공적인 Git 분기 모델](http://nvie.com/posts/a-successful-git-branching-model/)에서 찾을 수 있습니다.

![12](./media/project-execution/project-execution-12-git-branches.png)

작업하려는 분기로 전환하려면 셸 명령(Windows 또는 Linux)에서 다음 명령을 실행합니다. 

    git checkout <branch name>

*<branch name\>*을 **master**로 변경하면 **마스터** 분기로 다시 전환됩니다. 작업 중인 분기로 전환하면 해당 작업 항목에 대한 작업을 시작하여 항목을 완성하는 데 필요한 코드 또는 문서 아티팩트를 개발할 수 있습니다. 

또한 작업 항목을 기존 분기에 연결할 수도 있습니다. 작업 항목의 **세부 정보** 페이지에서 **새 분기 만들기**를 클릭하는 대신 **+ 링크 추가**를 클릭합니다. 그런 다음 작업 항목을 연결할 분기를 선택합니다. 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

git bash 명령으로 새 분기를 만들 수도 있습니다. <base branch name\>이 누락된 경우 <new branch name\>은 _마스터_ 분기를 기반으로 합니다. 
    
    git checkout -b <new branch name> <base branch name>


##  7. <a name='WorkonaBranchandCommittheChanges-7'></a>분기에서 작업 및 변경 내용 커밋 

이제 작업 항목(예: 로컬 컴퓨터의 분기에 R 파일 추가)에 대한 *data\_ingestion* 분기를 변경합니다. Git 셸의 해당 분기에 있으면 다음 Git 명령을 사용하여 이 작업 항목에 대한 분기에 추가된 R 파일을 커밋할 수 있습니다.

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8. <a name='CreateapullrequestonVSTS-8'></a>VSTS에서 끌어오기 요청 만들기 

몇 가지 커밋 및 푸시 후에 준비가 되면 현재 분기를 기본 분기에 병합하기 위해 VSTS 서버에서 **끌어오기 요청**을 제출할 수 있습니다. 

팀 프로젝트의 기본 페이지로 이동하고 **코드**를 클릭합니다. 병합할 분기와 분기를 병합하려는 git 리포지토리 이름을 선택합니다. 그런 다음 분기 작업을 기본 분기에 병합하기 전에 **끌어오기 요청**을 클릭하고 **새 끌어오기 요청**을 클릭하여 끌어오기 요청 검토를 만듭니다.

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

이 끌어오기 요청에 대한 설명을 채우고 검토자를 추가하여 외부로 보냅니다.

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9. <a name='ReviewandMerge-9'></a>검토 및 병합 

끌어오기 요청이 만들어지면 검토자가 끌어오기 요청을 검토하기 위한 전자 메일 알림을 받습니다. 검토자가 변경 내용의 작동 여부를 확인하고, 가능한 경우 요청자의 변경 내용을 테스트해야 합니다. 검토자는 평가에 따라 끌어오기 요청을 승인하거나 거부할 수 있습니다. 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

검토가 완료되면 **완료** 단추를 클릭하여 작업 중인 분기를 기본 분기에 병합합니다. 병합한 후에 작업 중인 분기를 삭제하도록 선택할 수 있습니다. 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

왼쪽 위 모서리에서 요청이 **완료됨**으로 표시되는지 확인합니다. 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

**코드** 아래의 리포지토리로 돌아가면 마스터 분기로 전환되었음을 알 수 있습니다.

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

또한 다음 Git 명령을 사용하여 작업 중인 분기를 기본 분기에 병합하고, 병합한 후에 작업 중인 분기를 삭제할 수 있습니다.

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10. <a name='DataQualityReportUtility-10'></a>IDEAR(대화형 데이터 탐색, 분석 및 보고) 유틸리티

이 R Markdown 기반 유틸리티는 데이터 집합을 평가하고 탐색할 수 있는 유연한 대화형 도구를 제공합니다. 사용자는 최소한의 코딩으로 데이터 집합에서 보고서를 빠르게 생성할 수 있습니다. 사용자가 단추를 클릭하여 대화형 도구에서 표시된 탐색 결과를 최종 보고서로 내보낼 수 있습니다. 이 보고서는 클라이언트에 전달되거나 후속 모델링 단계에 포함될 변수를 결정하는 데 사용됩니다.

현재 이 도구는 메모리 내 데이터 프레임에서만 작동합니다. .yaml 파일이 탐색할 데이터 집합의 매개 변수를 지정하는 데 필요합니다. 자세한 내용은 [TDSP 데이터 과학 유틸리티의 IDEAR(영문)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils)을 참조하세요.


##  11. <a name='ModelingUtility-11'></a>기본 모델링 및 보고 유틸리티

이 유틸리티는 사용자 지정 가능한 반자동화 도구를 제공하여 하이퍼 매개 변수 스윕이 있는 모델을 만들고 이러한 모델의 정확도를 비교합니다. 

모델 생성 유틸리티는 다른 섹션을 쉽게 탐색할 수 있는 목차가 포함된 자체 포함 HTML 출력을 생성하기 위해 실행할 수 있는 R Markdown 파일입니다. Markdown 파일이 실행(knit)될 때 세 가지 알고리즘, 즉 glmnet 패키지를 사용하는 정규화된 회귀, randomForest 패키지를 사용하는 임의의 포리스트, xgboost 패키지를 사용하는 트리 승격이 실행됩니다. 이러한 알고리즘 각각은 학습된 모델을 생성합니다. 이러한 모델의 정확도를 비교한 다음 상대적 기능 중요도 플롯을 보고합니다. 현재 두 가지 유틸리티가 있습니다. 각각 이진 분류 작업과 회귀 작업을 위한 유틸리티입니다. 이들 간의 주요 차이점은 제어 매개 변수와 정확도 메트릭이 이러한 학습 작업에 지정되는 방식에 있습니다. 

Yaml 파일은 다음을 지정하는 데 사용됩니다.

- 데이터 입력(SQL 원본 또는 R 데이터 파일) 
- 학습 및 테스트에 사용되는 부분
- 실행할 알고리즘 
- 모델 최적화에 대한 제어 매개 변수 선택:
    - 교차 유효성 검사 
    - 부트스트래핑
    - 교차 유효성 검사 접기
- 각 알고리즘에 대해 설정된 하이퍼 매개 변수 

또한 모델을 빠르게 실행하기 위해 Yaml 파일에서 알고리즘 수, 최적화 접기 수, 하이퍼 매개 변수 및 스윕하도록 설정된 하이퍼 매개 변수 집합 수를 수정할 수 있습니다. 예를 들어 더 적은 수의 CV 접기와 매개 변수 집합으로 실행할 수 있습니다. 신뢰할 수 있는 경우 CV 접기 수를 높이거나 매개 변수 집합을 더 많이 사용하면 더 포괄적으로 실행할 수도 있습니다.

자세한 내용은 [TDSP 데이터 과학 유틸리티의 자동화된 모델링 및 보고 유틸리티](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)를 참조하세요.


##  12. <a name='PowerBI-12'></a>Power BI 대시보드로 프로젝트 진행 상황 추적

데이터 과학 그룹 관리자, 팀 리더 및 프로젝트 리더는 프로젝트의 진행 상황, 수행된 작업, 작업 수행자 및 할 일 목록에 남아 있는 작업을 추적해야 합니다. VSTS를 사용하는 경우 Power BI 대시보드를 작성하여 Git 리포지토리와 연결된 작업 및 작업 항목을 추적할 수 있습니다. Visual Studio Team Services에 Power BI를 연결하는 방법에 대한 자세한 내용은 [Team Services에 Power BI 연결](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs)을 참조하세요. 

VSTS 데이터가 Power BI에 연결된 후 Git 리포지토리 작업 및 작업 항목을 추적하기 위해 Power BI 대시보드 및 보고서를 만드는 방법을 알아보려면 [Power BI 대시보드 및 보고서 만들기](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs)를 참조하세요. 

다음은 Git 작업 및 작업 항목을 추적하기 위해 빌드한 간단한 두 가지 대시보드의 예입니다. 첫 번째 예에서는 사용자, 날짜 및 리포지토리가 다른 git 커밋 작업이 나열됩니다. 쉽게 분할하고 분석하여 관심 있는 항목을 필터링할 수 있습니다.

![23](./media/project-execution/project-execution-23-powerbi-git.png)

두 번째 예에서는 별도로 반복되는 작업 항목(스토리 및 작업)이 표시됩니다. 할당자와 우선 순위별로 그룹화되고 상태별로 색이 지정되어 있습니다.

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>다음 단계

**특정 시나리오** 에 대한 프로세스의 모든 단계를 보여 주는 종합적인 전체 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 항목에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

Azure Machine Learning Studio를 사용하는 Team Data Science Process의 단계를 실행하는 예제는 [Azure ML 사용](http://aka.ms/datascienceprocess) 학습 경로를 참조하세요.