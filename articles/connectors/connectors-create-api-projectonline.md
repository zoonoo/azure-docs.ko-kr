---
title: ProjectOnline | Microsoft Docs
description: "Azure 앱 서비스로 논리 앱을 만듭니다. Project Online은 PPM(프로젝트 포트폴리오 관리) 및 Microsoft의 일상 업무를 위한 유연한 온라인 솔루션입니다. 조직에서는 Office 365를 통해 지원되는 Project Online을 통해 강력한 프로젝트 관리 기능을 신속하게 시작하여 어디서든지 모든 장치의 프로젝트와 프로젝트 포트폴리오 투자를 계획하고 우선 순위를 정하며 관리할 수 있습니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 77e106d9170fc49d66d322bd9c92bf0e14869259


---
# <a name="get-started-with-the-projectonline-connector"></a>ProjectOnline 커넥터 시작
Project Online은 PPM(프로젝트 포트폴리오 관리) 및 Microsoft의 일상 업무를 위한 유연한 온라인 솔루션입니다. 조직에서는 Office 365를 통해 지원되는 Project Online을 통해 강력한 프로젝트 관리 기능을 신속하게 시작하여 어디서든지 모든 장치의 프로젝트와 프로젝트 포트폴리오 투자를 계획하고 우선 순위를 정하며 관리할 수 있습니다.

> [!NOTE]
> 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 
> 
> 

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)를 참조하세요.

## <a name="triggers-and-actions"></a>트리거 및 작업
ProjectOnline 커넥터를 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다. 

 ProjectOnline 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### <a name="projectonline-actions"></a>ProjectOnline 작업
다음 작업을 수행할 수 있습니다.

| 작업 | 설명 |
| --- | --- |
| [ListProjects](connectors-create-api-projectonline.md#listprojects) |Project Online 사이트에 프로젝트 나열 |
| [CreateProject](connectors-create-api-projectonline.md#createproject) |Project Online 사이트에 새 프로젝트 만들기 |
| [CreateTask](connectors-create-api-projectonline.md#createtask) |프로젝트에 새 작업 만들기 |
| [CreateResource](connectors-create-api-projectonline.md#createresource) |Project Online 사이트에 엔터프라이즈 리소스 만들기 |
| [ListTasks](connectors-create-api-projectonline.md#listtasks) |프로젝트에 게시된 작업 나열 |
| [CheckoutProject](connectors-create-api-projectonline.md#checkoutproject) |사이트에서 프로젝트 확인 |
| [PublishProject](connectors-create-api-projectonline.md#publishproject) |사이트에서 기존 프로젝트 확인 및 게시 |

### <a name="projectonline-triggers"></a>ProjectOnline 트리거
다음 이벤트를 수신할 수 있습니다.

| 트리거 | 설명 |
| --- | --- |
| 새 프로젝트를 만든 경우 |새 프로젝트를 만들 때마다 흐름 트리거 |
| 새 리소스를 만든 경우 |새 리소스를 만든 경우 새 흐름 트리거 |
| 새 작업을 만든 경우 |새 작업을 만든 경우 흐름 트리거 |

## <a name="create-a-connection-to-projectonline"></a>ProjectOnline에 대한 연결 만들기
ProjectOnline으로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다. 

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |ProjectOnline 자격 증명 제공 |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> 다른 논리 앱에서 이 연결을 사용할 수 있습니다.
> 
> 

## <a name="reference-for-projectonline"></a>ProjectOnline에 대한 참조
적용 버전: 1.0

## <a name="onnewproject"></a>OnNewProject
새 프로젝트를 만든 경우: 새 프로젝트를 만들 때마다 흐름 트리거 

```GET: /trigger/_api/ProjectData/Projects``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="onnewresource"></a>OnNewResource
새 리소스를 만든 경우: 새 리소스를 만든 경우 새 흐름 트리거 

```GET: /trigger/_api/ProjectData/Resources``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="onnewtask"></a>OnNewTask
새 작업을 만든 경우: 새 작업을 만든 경우 흐름 트리거 

```GET: /trigger/_api/ProjectData/Tasks``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="listprojects"></a>ListProjects
프로젝트 나열: Project Online 사이트에 프로젝트 나열 

```GET: /_api/ProjectServer/Projects``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createproject"></a>CreateProject
새 프로젝트 만들기: Project Online 사이트에 새 프로젝트 만들기 

```POST: /_api/ProjectServer/Projects``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |
| proj | |yes |body |없음 |만들 새 프로젝트 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |ForbIDden |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createtask"></a>CreateTask
새 작업 만들기: 프로젝트에 새 작업 만들기 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |예 |path |없음 |작업을 추가할 프로젝트의 고유 ID |
| task | |yes |body |없음 |프로젝트에 추가할 새 작업 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="createresource"></a>CreateResource
새 리소스 만들기: Project Online 사이트에 엔터프라이즈 리소스 만들기 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |
| resource | |yes |body |없음 |프로젝트에 추가할 새 엔터프라이즈 리소스 |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="listtasks"></a>ListTasks
작업 나열: 프로젝트에 게시된 작업 나열 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |예 |path |없음 |작업을 가져올 프로젝트의 고유 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="checkoutproject"></a>CheckoutProject
프로젝트 확인: 사이트에서 프로젝트 확인 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |예 |path |없음 |작업을 추가할 프로젝트의 고유 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="publishproject"></a>PublishProject
프로젝트 확인 및 게시: 사이트에서 기존 프로젝트 확인 및 게시 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| 이름 | 데이터 형식 | 필수 | 위치 | 기본값 | 설명 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |yes |쿼리 |없음 |프로젝트 사이트의 루트 사이트 URL(예: https://sampletenant.sharepoint.com/teams/sampleteam) |
| project_id |string |예 |path |없음 |확인할 프로젝트의 고유 ID |

#### <a name="response"></a>응답
| 이름 | 설명 |
| --- | --- |
| 200 |확인 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생 |
| 기본값 |작업이 실패했습니다. |

## <a name="object-definitions"></a>개체 정의
### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="triggerproject"></a>TriggerProject
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| ProjectStartDate |string |아니요 |
| ProjectFinishDate |string |아니요 |
| ProjectCreatedDate |string |아니요 |
| ProjectId |string |아니요 |
| ProjectModifiedDate |string |아니요 |
| ProjectType |정수 |아니요 |
| ProjectName |string |아니요 |

### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="triggerresource"></a>TriggerResource
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| ResourceId |string |아니요 |
| ResourceBaseCalendar |string |아니요 |
| ResourceBookingType |정수 |아니요 |
| ResourceCanLevel |부울 |아니요 |
| ResourceCostPerUse |number |아니요 |
| ResourceCreatedDate |string |아니요 |
| ResourceEarliestAvailableFrom |string |아니요 |
| ResourceEmail |string |아니요 |
| ResourceInitials |string |아니요 |
| ResourceIsActive |부울 |아니요 |
| ResourceIsGeneric |부울 |아니요 |
| ResourceLatestAvailableTo |string |아니요 |
| ResourceModifiedDate |string |아니요 |
| ResourceName |string |아니요 |
| ResourceStatsuName |string |아니요 |
| ResourceType |정수 |아니요 |
| TypeDescription |string |아니요 |
| TypeName |string |아니요 |

### <a name="triggertaskswrapper"></a>TriggerTasksWrapper
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="triggertask"></a>TriggerTask
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| ProjectId |string |아니요 |
| TaskId |string |아니요 |
| ProjectName |string |아니요 |
| TaskName |string |아니요 |
| TaskCreatedDate |string |아니요 |
| TaskModifieddate |string |아니요 |
| TaskStartDate |string |아니요 |
| TaskFinishDate |string |아니요 |
| TaskPriority |정수 |아니요 |
| TaskIsActive |부울 |아니요 |

### <a name="newproject"></a>NewProject
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |예 |
| 설명 |string |아니요 |
| 시작 |string |아니요 |

### <a name="newreource"></a>NewReource
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |예 |
| IsBudget |부울 |아니요 |
| IsGeneric |부울 |아니요 |
| IsInactive |부울 |아니요 |

### <a name="project"></a>Project
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| ApprovedStart |string |아니요 |
| ApprovedEnd |string |아니요 |
| CheckedOutDate |string |아니요 |
| CheckOutDescription |string |아니요 |
| CheckOutId |string |아니요 |
| CreatedDate |string |아니요 |
| Id |string |아니요 |
| IsCheckedOut |부울 |아니요 |
| LastPublishedDate |string |아니요 |
| LastSavedDate |string |아니요 |
| OptimizerDecision |정수 |아니요 |
| PlannerDecision |정수 |아니요 |
| ProjectType |정수 |아니요 |
| Name |string |아니요 |
| WinprojVersion |string |아니요 |

### <a name="projectswrapper"></a>ProjectsWrapper
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="newtask"></a>NewTask
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 매개 변수 |정의되지 않음 |예 |

### <a name="taskparameters"></a>TaskParameters
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 이름 |string |예 |
| 참고 사항 |string |아니요 |
| 시작 |string |아니요 |
| 기간 |string |아니요 |

### <a name="enterpriseresource"></a>EnterpriseResource
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| CanLevel |부울 |아니요 |
| 코드 |string |아니요 |
| CostAccrual |정수 |아니요 |
| CostCenter |string |아니요 |
| 작성자 |string |아니요 |
| DefaultBookingType |정수 |아니요 |
| Email |string |아니요 |
| ExternalId |string |아니요 |
| 그룹 |string |아니요 |
| HireDate |string |아니요 |
| Id |string |아니요 |
| Initials |string |아니요 |
| IsActive |부울 |아니요 |
| IsBudget |부울 |아니요 |
| IsCheckedOut |부울 |아니요 |
| IsGeneric |부울 |아니요 |
| IsTeam |부울 |아니요 |
| MaterialLabel |string |아니요 |
| 수정자 |string |아니요 |
| Name |string |아니요 |
| Phonetics |string |아니요 |
| ResourceType |정수 |아니요 |
| TerminationDate |string |아니요 |

### <a name="taskswrapper"></a>TasksWrapper
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| value |array |아니요 |

### <a name="task"></a>작업
| 속성 이름 | 데이터 형식 | 필수 |
| --- | --- | --- |
| 작성자 |string |아니요 |
| 수정자 |string |아니요 |
| 시작 |string |아니요 |
| 마침 |string |아니요 |
| Name |string |아니요 |
| Id |string |아니요 |
| 우선 순위 |정수 |아니요 |
| PercentComplete |정수 |아니요 |
| 참고 사항 |string |아니요 |
| 연락처 |string |아니요 |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


