---
title: 데이터 과학 프로세스의 Agile 개발 - Team Data Science Process
description: 팀 데이터 과학 프로세스를 사용 하 여 프로젝트 팀 내에서 체계적이 고 버전이 제어 되 고 공동 작업 방식으로 데이터 과학 프로젝트를 실행 합니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722104"
---
# <a name="agile-development-of-data-science-projects"></a>데이터 과학 프로젝트의 Agile 개발

이 문서에서는 [TDSP(Team Data Science Process)](overview.md)를 사용하여 개발자가 프로젝트 팀 내에서 체계적이고 버전을 제어하고 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행하는 방법에 대해 설명합니다. TDSP는 클라우드 기반, 예측 분석 솔루션을 효율적으로 실행 하기 위한 구조화 된 작업 시퀀스를 제공 하는 Microsoft에서 개발한 프레임 워크입니다. TDSP를 표준화 하는 데이터 과학 팀에서 처리 하는 역할 및 작업에 대 한 개요는 [팀 데이터 과학 프로세스 역할 및 작업](roles-tasks.md)을 참조 하세요. 

이 문서에는 다음을 수행하는 방법에 대한 지침이 포함되어 있습니다. 

- 프로젝트에 포함 된 작업 항목에 대 한 *스 프린트 계획* 을 수행 합니다.
- 스 프린트에 *작업 항목* 을 추가 합니다.
- 특별히 TDSP 수명 주기 단계에 맞는 *agile 파생 작업 항목 템플릿을* 만들고 사용 합니다.

다음 지침은 Azure DevOps에서 Azure Boards 및 Azure Repos를 사용 하 여 TDSP 팀 환경을 설정 하는 데 필요한 단계를 간략하게 설명 합니다. 이 지침은 Microsoft에서 TDSP를 구현 하는 방법 이기 때문에 Azure DevOps를 사용 합니다. 그룹에 다른 코드 호스팅 플랫폼이 사용 되는 경우 팀 리더 작업은 일반적으로 변경 되지 않지만 작업을 완료 하는 방법은 다릅니다. 예를 들어, 작업 항목을 Git 분기와 연결 하는 것은 Azure Repos와 동일 하므로 GitHub와 동일 하지 않을 수 있습니다.

다음 그림에서는 데이터 과학 프로젝트에 대 한 일반적인 스 프린트 계획, 코딩 및 소스 제어 워크플로를 보여 줍니다.

![Team Data Science Process](./media/agile-development/1-project-execute.png)

##  <a name='Terminology-1'></a>작업 항목 형식

TDSP 스 프린트 계획 프레임 워크에는 *기능*, *사용자 스토리*, *작업*및 *버그*의 네 가지 자주 사용 되는 *작업 항목* 형식이 있습니다. 모든 작업 항목의 백로그는 Git 리포지토리 수준이 아닌 프로젝트 수준에 있습니다. 

작업 항목 형식에 대 한 정의는 다음과 같습니다.

- **기능**: 프로젝트 참여에 해당 하는 기능입니다. 클라이언트를 사용 하는 다양 한 계약은 다양 한 기능으로, 프로젝트의 여러 단계를 다양 한 기능으로 간주 하는 것이 가장 좋습니다. *\<ClientName >\<EngagementName >* 와 같은 스키마를 선택 하 여 기능 이름을 지정할 경우 프로젝트의 컨텍스트를 쉽게 인식 하 고 이름 자체에서 참여를 제공 합니다.
  
- **사용자 스토리**: 사용자 스토리는 종단 간 기능을 완료 하는 데 필요한 작업 항목입니다. 사용자 스토리의 예는 다음과 같습니다.
  - 데이터 가져오기 
  - 데이터 살펴보기 
  - 기능 생성
  - 모델 빌드
  - 모델 운영 
  - 모델 다시 학습
  
- **작업**: 태스크는 특정 사용자 스토리를 완료 하기 위해 수행 해야 하는 할당 가능한 작업 항목입니다. 예를 들어 *데이터를 가져오는* 사용자 스토리의 작업은 다음과 같을 수 있습니다.
  - SQL Server 자격 증명 가져오기
  - SQL Data Warehouse에 데이터 업로드
  
- **버그**: 버그는 태스크를 완료 하기 위해 수정 해야 하는 기존 코드 또는 문서에서 발생 하는 문제입니다. 작업 항목이 누락 되어 버그가 발생 하는 경우 사용자 스토리 또는 작업으로 에스컬레이션 될 수 있습니다. 

데이터 과학자는 기능, 사용자 스토리 및 작업을 TDSP 수명 주기 단계와 tdsp으로 대체 하는 agile 템플릿을 사용 하 여 더 편안 하 게 느껴질 수 있습니다. TDSP 수명 주기 단계에 맞게 특별히 조정 되는 agile 파생 템플릿을 만들려면 [agile TDSP 작업 템플릿 사용](#set-up-agile-dsp-6)을 참조 하세요.

> [!NOTE]
> TDSP는 SCM (소프트웨어 코드 관리)에서 제공 하는 기능, 사용자 스토리, 작업 및 버그의 개념을 활용 합니다. TDSP 개념은 기존의 SCM 정의와 약간 다를 수 있습니다.

## <a name='SprintPlanning-2'></a>스 프린트 계획

많은 데이터 과학자 여러 프로젝트를 사용 하 고 있으며,이를 완료 하는 데 몇 개월이 걸릴 수 있으며 다른 속도로에서 진행 됩니다. 스프린트 계획은 프로젝트 우선 순위 지정 및 리소스 계획 및 할당에 유용합니다. Azure Boards에서 프로젝트에 대 한 작업 항목을 쉽게 작성, 관리 및 추적 하 고 스 프린트 계획을 수행 하 여 프로젝트가 예상 대로 진행 되도록 할 수 있습니다.

스 프린트 계획에 대 한 자세한 내용은 [스크럼 스 프린트](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint)를 참조 하세요. 

Azure Boards 스 프린트 계획에 대 한 자세한 내용은 [스 프린트에 백로그 항목 할당](/azure/devops/boards/sprints/assign-work-sprint)을 참조 하세요. 

## <a name='AddFeature-3'></a>백로그에 기능 추가 

프로젝트 및 프로젝트 코드 리포지토리를 만든 후 백로그에 기능을 추가 하 여 프로젝트에 대 한 작업을 나타낼 수 있습니다.

1. 프로젝트 페이지의 왼쪽 탐색 영역에서 **보드** > **백로그** 를 선택 합니다. 
   
1. **백로그** 탭에서 위쪽 막대의 작업 항목 형식이 **스토리**인 경우 **기능**을 드롭다운 하 고 선택 합니다. 그런 다음 **새 작업 항목을 선택 합니다.**
   
   ![새 작업 항목 선택](./media/agile-development/2-sprint-team-overview.png)
   
1. 기능의 제목 (일반적으로 프로젝트 이름)을 입력 한 다음 **맨 위에 추가를**선택 합니다. 
   
   ![제목을 입력 하 고 맨 위에 추가를 선택 합니다.](./media/agile-development/3-sprint-team-add-work.png)
   
1. **백로그** 목록에서을 선택 하 고 새 기능을 엽니다. 설명을 입력 하 고, 팀 멤버를 할당 하 고, 계획 매개 변수를 설정 합니다. 
   
   **개발** 섹션에서 **링크 추가** 를 선택 하 여 프로젝트의 Azure Repos 코드 리포지토리에 기능을 연결할 수도 있습니다. 
   
   기능을 편집한 후에 **저장 & 닫기**를 선택 합니다.
   
   ![기능을 편집 하 고 저장 & 닫기를 선택 합니다.](./media/agile-development/3a-add-link-repo.png)

## <a name='AddStoryunderfeature-4'></a>기능에 사용자 스토리 추가 

이 기능을 사용 하 여 프로젝트를 완료 하는 데 필요한 주요 단계를 설명 하는 사용자 스토리를 추가할 수 있습니다. 

기능에 새 사용자 스토리를 추가 하려면 다음을 수행 합니다.

1. **백로그** 탭에서 기능 왼쪽의 **+** 을 선택 합니다. 
   
   ![기능 아래에 새 사용자 스토리를 추가 합니다.](./media/agile-development/4-sprint-add-story.png)
   
1. 사용자 스토리에 제목을 지정 하 고 할당, 상태, 설명, 설명, 계획 및 우선 순위와 같은 세부 정보를 편집 합니다. 
   
   **개발** 섹션에서 **링크 추가** 를 선택 하 여 프로젝트의 Azure Repos 코드 리포지토리의 분기에 사용자 스토리를 연결할 수도 있습니다. 작업 항목을 연결 하려는 리포지토리 및 분기를 선택 하 고 **확인**을 선택 합니다.
   
   ![링크 추가](./media/agile-development/5-sprint-edit-story.png)
   
1. 사용자 스토리 편집을 마쳤으면 **저장 & 닫기**를 선택 합니다. 

## <a name='AddTaskunderstory-5'></a>사용자 스토리에 작업 추가 

작업은 각 사용자 스토리를 완료 하는 데 필요한 구체적인 세부 단계입니다. 사용자 스토리의 모든 작업이 완료 되 면 사용자 스토리도 완료 해야 합니다. 

사용자 스토리에 작업을 추가 하려면 사용자 스토리 항목 옆에 있는 **+** 를 선택 하 고 **작업**을 선택 합니다. 작업의 제목 및 기타 정보를 입력 합니다.

![사용자 스토리에 작업 추가](./media/agile-development/7-sprint-add-task.png)

기능, 사용자 스토리 및 작업을 만든 후에는 **백로그** 또는 **보드** 보기에서 해당 기능을 보고 상태를 추적할 수 있습니다.

![백로그 보기](./media/agile-development/8-sprint-backlog-view.png)

![보드 보기](./media/agile-development/8a-sprint-board-view.png)

## <a name='set-up-agile-dsp-6'></a>Agile TDSP 작업 템플릿 사용

데이터 과학자는 기능, 사용자 스토리 및 작업을 TDSP 수명 주기 단계와 tdsp으로 대체 하는 agile 템플릿을 사용 하 여 더 편안 하 게 느껴질 수 있습니다. Azure Boards에서 TDSP 수명 주기 단계를 사용 하 여 작업 항목을 만들고 추적 하는 agile 파생 템플릿을 만들 수 있습니다. 다음 단계에서는 데이터 과학 관련 agile 프로세스 템플릿을 설정 하 고 템플릿을 기반으로 데이터 과학 작업 항목을 만드는 과정을 안내 합니다.

### <a name="set-up-an-agile-data-science-process-template"></a>Agile 데이터 과학 프로세스 템플릿 설정

1. Azure DevOps 조직 기본 페이지의 왼쪽 탐색 영역에서 **조직 설정** 을 선택 합니다. 
   
1. **조직 설정** 왼쪽 탐색의 **보드**에서 **프로세스**를 선택 합니다. 
   
1. **모든 프로세스** 창에서 **Agile**옆의 **...** 를 선택한 후 **상속 된 프로세스 만들기**를 선택 합니다.
   
   ![Agile에서 상속 된 프로세스 만들기](./media/agile-development/10-settings.png) 
   
1. **Agile에서 상속 된 프로세스 만들기** 대화 상자에서 *AgileDataScienceProcess*이름을 입력 하 고 **프로세스 만들기**를 선택 합니다.
   
   ![AgileDataScienceProcess 프로세스 만들기](./media/agile-development/11-agileds.png)
   
1. **모든 프로세스**에서 새 **AgileDataScienceProcess**를 선택 합니다. 
   
1. **작업 항목 형식** 탭에서 각 항목 옆 **에 있는 ...를 선택한** 다음 **사용 안 함**을 선택 하 여 **대규모**사용자 스토리, **기능**, **사용자 스토리**및 **작업** 을 사용 하지 않도록 설정 합니다. 
   
   ![작업 항목 형식 사용 안 함](./media/agile-development/12-disable.png)
   
1. **모든 프로세스**에서 **백로그 수준** 탭을 선택 합니다. **포트폴리오 백로그**에서 **대규모 사용자 (사용 안 함)** **옆의 ...** 를 선택한 다음 **편집/이름 바꾸기**를 선택 합니다. 
   
1. **백로그 수준 편집** 대화 상자에서 다음을 수행 합니다.
   1. **이름**에서 **대규모** 사용자를 *tdsp 프로젝트로*바꿉니다. 
   1. **이 백로그 수준의 작업 항목 형식**에서 **새 작업 항목 형식**을 선택 하 고 *tdsp 프로젝트*를 입력 한 다음 **추가**를 선택 합니다. 
   1. **기본 작업 항목 형식**아래에서 **tdsp 프로젝트**를 드롭다운 하 고 선택 합니다. 
   1. **저장**을 선택합니다.
   
   ![포트폴리오 백로그 수준 설정](./media/agile-development/13-rename.png)  
   
1. 동일한 단계에 따라 *Tdsp 단계로* **기능** 이름을 바꾸고 다음과 같은 새 작업 항목 형식을 추가 합니다.
   
   - *비즈니스 이해*
   - *데이터 취득*
   - *모델링*
   - *배포*
   
1. **요구 사항 백로그**에서 **스토리** 의 이름을 *tdsp tdsp*로 바꾸고, 새 작업 항목 형식 *tdsp tdsp*을 추가 하 고, 기본 작업 항목 형식을 **tdsp tdsp**로 설정 합니다.
   
1. **반복 백로그**에서 새 작업 항목 형식 *tdsp 작업*을 추가 하 고 기본 작업 항목 형식으로 설정 합니다. 
   
단계를 완료 한 후에는 백로그 수준이 다음과 같이 표시 됩니다.
   
 ![TDSP 템플릿 백로그 수준](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Agile 데이터 과학 프로세스 작업 항목 만들기

데이터 과학 프로세스 템플릿을 사용 하 여 TDSP 프로젝트를 만들고 TDSP 수명 주기 단계에 해당 하는 작업 항목을 추적할 수 있습니다.

1. Azure DevOps 조직 기본 페이지에서 **새 프로젝트**를 선택 합니다. 
   
1. **새 프로젝트 만들기** 대화 상자에서 프로젝트 이름을 지정 하 고 **고급**을 선택 합니다. 
   
1. **작업 항목 프로세스**에서 **AgileDataScienceProcess**을 선택 하 고 **만들기**를 선택 합니다.
   
   ![TDSP 프로젝트 만들기](./media/agile-development/15-newproject.png)
   
1. 새로 만든 프로젝트의 왼쪽 탐색 영역에서 **보드** > **백로그** 를 선택 합니다.
   
1. TDSP 프로젝트를 표시 하려면 **팀 설정 구성** 아이콘을 선택 합니다. **설정** 화면에서 **tdsp 프로젝트** 확인란을 선택 하 고 **저장 후 닫기**를 선택 합니다.
   
   ![TDSP 프로젝트 선택 확인란](./media/agile-development/16-enabledsprojects1.png)
   
1. 데이터 과학 관련 TDSP 프로젝트를 만들려면 위쪽 표시줄에서 **Tdsp 프로젝트** 를 선택 하 고 **새 작업 항목**을 선택 합니다. 
   
1. 팝업에서 TDSP 프로젝트 작업 항목에 이름을 지정 하 고 **맨 위에 추가를**선택 합니다.
   
   ![데이터 과학 프로젝트 작업 항목 만들기](./media/agile-development/17-dsworkitems0.png)
   
1. TDSP 프로젝트 아래에 작업 항목을 추가 하려면 프로젝트 옆에 있는 **+** 를 선택 하 고 만들 작업 항목의 형식을 선택 합니다. 
   
   ![데이터 과학 작업 항목 형식 선택](./media/agile-development/17-dsworkitems1.png)
   
1. 새 작업 항목의 세부 정보를 입력 하 고 **저장 & 닫기**를 선택 합니다.
   
1. 작업 항목 옆에 있는 **+** 기호를 계속 선택 하 여 새 Tdsp 단계, Tdsp 및 작업을 추가 합니다. 
   
다음은 데이터 과학 프로젝트 작업 항목을 **백로그** 뷰에 표시 하는 방법의 예입니다.

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>다음 단계

[Git을 사용한 공동 코딩](collaborative-coding-with-git.md) 은 공유 코드 개발 프레임 워크로 git를 사용 하 여 데이터 과학 프로젝트에 대 한 공동 코드 개발을 수행 하는 방법 및 이러한 코딩 활동을 agile 프로세스로 계획 된 작업에 연결 하는 방법을 설명 합니다.

[예제 연습에서는](walkthroughs.md) 링크 및 미리 보기 설명과 함께 특정 시나리오의 연습이 나열 됩니다. 연결 된 시나리오에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합 하 여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다.
  
Agile 프로세스에 대 한 추가 리소스:

- [Agile 프로세스](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agile 프로세스 작업 항목 형식 및 워크플로](/azure/devops/boards/work-items/guidance/agile-process-workflow)

