---
title: 데이터 과학 프로세스의 Agile 개발 - Team Data Science Process
description: TDSP(Team Data Science Process)를 사용하여 프로젝트 팀 내에서 체계적이고 버전을 제어하며 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행합니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "89440289"
---
# <a name="agile-development-of-data-science-projects"></a>데이터 과학 프로젝트의 Agile 개발

이 문서에서는 [TDSP(Team Data Science Process)](overview.md)를 사용하여 개발자가 프로젝트 팀 내에서 체계적이고 버전을 제어하고 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행하는 방법에 대해 설명합니다. TDSP는 Microsoft에서 개발하여 클라우드 기반 예측 분석 솔루션을 효율적으로 실행하며 일련의 구조화된 작업을 제공하는 프레임워크입니다. TDSP를 표준화하는 데이터 과학 팀에서 처리하는 역할 및 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요. 

이 문서에는 다음을 수행하는 방법에 대한 지침이 포함되어 있습니다. 

- 프로젝트와 관련된 작업 항목에 대해 *스프린트 계획* 을 수행합니다.
- 스프린트에 *작업 항목* 을 추가합니다.
- 특별히 TDSP 수명 주기 단계에 맞춰진 *agile 파생 작업 항목 템플릿을* 만들고 사용합니다.

다음 지침은 Azure DevOps에서 Azure Boards 및 Azure Repos를 사용하여 TDSP 팀 환경을 설정하는 데 필요한 단계를 간략하게 설명합니다. 이 지침은 Microsoft에서 TDSP를 구현하는 방법이기 때문에 Azure DevOps를 사용합니다. 그룹이 다른 코드 호스팅 플랫폼을 사용하는 경우 팀 리더 작업은 일반적으로 변경되지 않지만 작업을 완료하는 방법은 달라집니다. 예를 들어, 작업 항목을 Git 분기와 연결하는 것은 Azure Repos와 연결하는 것과 동일하므로 GitHub와는 다를 수 있습니다.

다음 그림에서는 데이터 과학을 위한 일반적인 스프린트 계획, 코딩 및 소스 제어 워크플로를 보여줍니다.

![Team Data Science Process](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>작업 항목 형식

TDSP 스프린트 계획 프레임워크에는 자주 사용되는 네 가지 유형의 *작업 항목*, 즉 *기능*, *사용자 스토리*, *작업* 및 *버그* 가 있습니다. 모든 작업 항목의 백로그는 Git 리포지토리 수준이 아닌 프로젝트 수준에 있습니다. 

작업 항목 형식에 대한 정의는 다음과 같습니다.

- **기능**: 프로젝트 참여에 해당하는 기능입니다. 클라이언트를 사용하는 다양한 참여는 기능이 다양하며, 프로젝트의 여러 단계를 서로 다른 기능으로 간주하는 것이 가장 좋습니다. *\<ClientName>-\<EngagementName>* 와(과) 같은 스키마를 선택하여 기능 이름을 지정하면 이름 자체에서 프로젝트 및 참여의 컨텍스트를 쉽게 인식할 수 있습니다.
  
- **사용자 스토리**: 사용자 스토리는 엔드투엔드 기능을 완료하는 데 필요한 작업 항목입니다. 사용자 스토리의 예제는 다음과 같습니다.
  - 데이터 가져오기 
  - 데이터 살펴보기 
  - 기능 생성
  - 모델 빌드
  - 모델 운영 
  - 모델 다시 학습
  
- **작업**: 특정 사용자 스토리를 완료하기 위해 수행해야 하는 할당 가능한 작업 항목입니다. 예를 들어 사용자 스토리 *데이터 가져오기* 의 작업은 다음과 같을 수 있습니다.
  - SQL Server 자격 증명 가져오기
  - Azure Synapse Analytics에 데이터 업로드
  
- **버그**: 버그는 작업을 완료하기 위해 수정해야 하는 기존 코드 또는 문서의 문제입니다. 작업 항목이 누락되어 버그가 발생하는 경우 사용자 스토리 또는 작업으로 에스컬레이션할 수 있습니다. 

데이터 과학자는 기능, 사용자 스토리 및 작업을 TDSP 수명 주기 단계와 하위 단계로 대체하는 agile 템플릿을 사용하면 더 편안하게 느낄 수 있습니다. TDSP 수명 주기 단계에 맞춰 특별히 조정되는 agile 파생 템플릿을 만들려면 [agile TDSP 작업 템플릿 사용](#set-up-agile-dsp-6)을 참조하세요.

> [!NOTE]
> TDSP는 SCM(소프트웨어 코드 관리)에서 제공하는 기능, 사용자 스토리, 작업 및 버그의 개념을 빌려옵니다. TDSP 개념은 기존의 SCM 정의와 약간 다를 수 있습니다.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>스프린트 계획

많은 데이터 과학자들이 여러 프로젝트에 참여하고 있으며 프로젝트는 완료하는 데 몇 달이 걸릴 수도 있고 서로 다른 속도로 진행됩니다. 스프린트 계획은 프로젝트 우선 순위 지정 및 리소스 계획 및 할당에 유용합니다. Azure Boards에서 프로젝트의 작업 항목을 쉽게 생성, 관리 및 추적하고 스프린트 계획을 수행하여 프로젝트가 예상대로 진행되도록 할 수 있습니다.

스프린트 계획에 대한 자세한 내용은 [ 스프린트 스크럼](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint)을 참조하세요. 

Azure Boards 스프린트 계획에 대한 자세한 내용은 [스프린트에 백로그 항목 할당](/azure/devops/boards/sprints/assign-work-sprint)을 참조하세요. 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>백로그에 기능 추가 

프로젝트 및 프로젝트 코드 리포지토리를 만든 후 백로그에 기능을 추가하여 프로젝트에 대한 작업을 나타낼 수 있습니다.

1. 프로젝트 페이지의 왼쪽 탐색 영역에서 **Boards** > **백로그** 를 선택합니다. 
   
1. **백로그** 탭에서 위쪽 막대의 작업 항목 형식이 **스토리** 인 경우 드롭다운하여 **기능** 을 선택합니다. 그런 다음 **새 작업 항목** 을 선택합니다.
   
   ![새 작업 항목 선택](./media/agile-development/2-sprint-team-overview.png)
   
1. 기능의 제목(일반적으로 프로젝트 이름)을 입력한 다음 **맨 위에 추가** 를 선택합니다. 
   
   ![제목을 입력하고 맨 위에 추가를 선택합니다.](./media/agile-development/3-sprint-team-add-work.png)
   
1. **백로그** 목록에서 새로운 기능을 선택하여 엽니다. 설명을 입력하고, 팀 멤버를 할당하며, 계획 매개 변수를 설정합니다. 
   
   **개발** 섹션에서 **링크 추가** 를 선택하여 프로젝트의 Azure Repos 코드 리포지토리에 기능을 연결할 수도 있습니다. 
   
   기능을 편집한 다음 **저장 후 닫기** 를 선택합니다.
   
   ![기능을 편집하고 저장 후 닫기를 선택합니다.](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>기능에 사용자 스토리 추가 

이 기능을 사용하여 프로젝트를 완료하는 데 필요한 주요 단계를 설명하는 사용자 스토리를 추가할 수 있습니다. 

기능에 새 사용자 스토리를 추가하려면 다음을 수행합니다.

1. **백로그** 탭에서 기능의 왼쪽에 있는 **+** 를 선택합니다. 
   
   ![기능에 새 사용자 스토리 추가](./media/agile-development/4-sprint-add-story.png)
   
1. 사용자 스토리에 제목을 지정하고 할당, 상태, 설명, 주석, 계획 및 우선 순위와 같은 세부 정보를 편집합니다. 
   
   **개발** 섹션에서 **링크 추가** 를 선택하여 프로젝트의 Azure Repos 코드 리포지토리 분기에 사용자 스토리를 연결할 수도 있습니다. 작업 항목을 연결하려는 리포지토리 및 분기를 선택하고 **확인** 을 선택합니다.
   
   ![링크 추가](./media/agile-development/5-sprint-edit-story.png)
   
1. 사용자 스토리 편집을 마쳤으면 **저장 후 닫기** 를 선택합니다. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>사용자 스토리에 작업 추가 

작업은 각 사용자 스토리를 완료하는 데 필요한 구체적인 세부 단계입니다. 사용자 스토리의 모든 작업이 완료되면 사용자 스토리도 완료됩니다. 

사용자 스토리에 작업을 추가하려면 사용자 스토리 항목 옆에 있는 **+** 을(를) 선택하고 **작업** 을 선택합니다. 작업의 제목 및 기타 정보를 입력합니다.

![사용자 스토리에 작업 추가](./media/agile-development/7-sprint-add-task.png)

기능, 사용자 스토리 및 작업을 만든 후에는 **백로그** 또는 **Boards** 보기에서 확인하고 상태를 추적할 수 있습니다.

![백로그 보기](./media/agile-development/8-sprint-backlog-view.png)

![Boards 보기](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>agile TDSP 작업 템플릿 사용

데이터 과학자는 기능, 사용자 스토리 및 작업을 TDSP 수명 주기 단계와 하위 단계로 대체하는 agile 템플릿을 사용하면 더 편안하게 느낄 수 있습니다. Azure Boards에서 TDSP 수명 주기 단계를 사용하여 작업 항목을 만들고 추적하는 agile 파생 템플릿을 만들 수 있습니다. 다음 단계는 데이터 과학 특유의 agile 프로세스 템플릿을 설정하고 템플릿을 기반으로 데이터 과학 작업 항목을 생성하는 과정을 안내합니다.

### <a name="set-up-an-agile-data-science-process-template"></a>Agile 데이터 과학 프로세스 템플릿 설정

1. Azure DevOps 조직 기본 페이지의 왼쪽 탐색 영역에서 **조직 설정** 을 선택합니다. 
   
1. **조직 설정** 왼쪽 탐색 영역의 **Boards** 에서 **프로세스** 를 선택합니다. 
   
1. **모든 프로세스** 창에서 **Agile** 옆의 **...** 를 선택한 후 **상속된 프로세스 만들기** 를 선택합니다.
   
   ![Agile에서 상속된 프로세스 만들기](./media/agile-development/10-settings.png) 
   
1. **Agile에서 상속된 프로세스 만들기** 대화 상자에서 *AgileDataScienceProcess* 이름을 입력하고 **프로세스 만들기** 를 선택합니다.
   
   ![AgileDataScienceProcess 프로세스 만들기](./media/agile-development/11-agileds.png)
   
1. **모든 프로세스** 에서 새 **AgileDataScienceProcess** 를 선택합니다. 
   
1. **작업 항목 형식** 탭에서 각 항목 옆에 있는 **...** 을 선택한 다음 **사용 안 함** 을 선택하여 **대규모 사용자 스토리**, **기능**, **사용자 스토리** 및 **작업** 을 사용하지 않도록 설정합니다. 
   
   ![작업 항목 형식 사용 안 함](./media/agile-development/12-disable.png)
   
1. **모든 프로세스** 에서 **백로그 수준** 탭을 선택합니다. **포트폴리오 백로그** 에서 **대규모 사용자 스토리(사용 안 함)** 옆의 **...** 을 선택한 다음 **편집/이름 바꾸기** 를 선택합니다. 
   
1. **백로그 수준 편집** 대화 상자에서 다음을 수행합니다.
   1. **이름** 에서 **대규모 사용자 스토리** 를 *TDSP 프로젝트* 로 바꿉니다. 
   1. **이 백로그 수준의 작업 항목 형식** 에서 **새 작업 항목 형식** 을 선택하고 *TDSP 프로젝트* 를 입력한 다음 **추가** 를 선택합니다. 
   1. **기본 작업 항목 형식** 에서 드롭다운하여 **TDSP 프로젝트** 를 선택합니다. 
   1. **저장** 을 선택합니다.
   
   ![포트폴리오 백로그 수준 설정](./media/agile-development/13-rename.png)  
   
1. 동일한 단계에 따라 **기능** 이름을 *TDSP 단계* 로 바꾸고 다음과 같은 새 작업 항목 형식을 추가합니다.
   
   - *비즈니스 이해*
   - *데이터 취득*
   - *모델링*
   - *배포*
   
1. **요구 사항 백로그** 에서 **스토리** 이름을 *TDSP 하위 단계* 로 바꾸고, 새 작업 항목 형식 *TDSP 하위 단계* 를 추가한 다음 기본 작업 항목 형식을 **TDSP 하위 단계** 로 설정합니다.
   
1. **반복 백로그** 에서 새 작업 항목 형식 *TDSP 작업* 을 추가한 다음 기본 작업 항목 형식으로 설정합니다. 
   
단계를 완료하고 나면 백로그 수준이 다음과 같이 표시됩니다.
   
 ![TDSP 템플릿 백로그 수준](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Agile 데이터 과학 프로세스 작업 항목 만들기

데이터 과학 프로세스 템플릿을 사용하여 TDSP 프로젝트를 만들고 TDSP 수명 주기 단계에 해당하는 작업 항목을 추적할 수 있습니다.

1. Azure DevOps 조직 기본 페이지에서 **새 프로젝트** 를 선택합니다. 
   
1. **새 프로젝트 만들기** 대화 상자에서 프로젝트 이름을 지정하고 **고급** 을 선택합니다. 
   
1. **작업 항목 프로세스** 에서 **AgileDataScienceProcess** 를 선택하고 **만들기** 를 선택합니다.
   
   ![TDSP 프로젝트 만들기](./media/agile-development/15-newproject.png)
   
1. 새로 만든 프로젝트의 왼쪽 탐색 영역에서 **Boards** > **백로그** 를 선택합니다.
   
1. TDSP 프로젝트를 표시하려면 **팀 설정 구성** 아이콘을 선택합니다. **설정** 화면에서 **TDSP 프로젝트** 확인란을 선택한 다음 **저장 후 닫기** 를 선택합니다.
   
   ![TDSP 프로젝트 확인란 선택](./media/agile-development/16-enabledsprojects1.png)
   
1. 데이터 과학 관련 TDSP 프로젝트를 만들려면 위쪽 표시줄에서 **TDSP 프로젝트** 를 선택한 다음 **새 작업 항목** 을 선택합니다. 
   
1. 팝업에서 TDSP 프로젝트 작업 항목에 이름을 지정하고 **맨 위에 추가** 를 선택합니다.
   
   ![데이터 과학 프로젝트 작업 항목 만들기](./media/agile-development/17-dsworkitems0.png)
   
1. TDSP 프로젝트에서 작업 항목을 추가하려면 프로젝트 옆에 있는 **+** 을(를) 선택하고 만들려는 작업 항목의 형식을 선택합니다. 
   
   ![데이터 과학 작업 항목 형식 선택](./media/agile-development/17-dsworkitems1.png)
   
1. 새 작업 항목의 세부 정보를 입력하고 **저장 후 닫기** 를 선택합니다.
   
1. 계속해서 작업 항목 옆에 있는 **+** 기호를 선택하여 새 TDSP 단계, 하위 단계 및 작업을 추가합니다. 
   
다음은 데이터 과학 프로젝트 작업 항목을 **백로그** 보기에 표시하는 방법의 예제입니다.

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>다음 단계

[Git을 사용한 공동 코딩](collaborative-coding-with-git.md)은 Git을 공유 코드 개발 프레임워크로 사용하여 데이터 과학 프로젝트에 대한 공동 코드 개발을 수행하는 방법 및 Agile 프로세스를 사용하여 이러한 코딩 작업을 계획된 작업에 연결하는 방법을 설명합니다.

[예제 연습](walkthroughs.md)에는 링크 및 썸네일 설명과 함께 특정 시나리오의 연습이 나열됩니다. 연결된 시나리오에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다.
  
Agile 프로세스에 대한 추가 리소스:

- [Agile 프로세스](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agile 프로세스 작업 항목 형식 및 워크플로](/azure/devops/boards/work-items/guidance/agile-process-workflow)

