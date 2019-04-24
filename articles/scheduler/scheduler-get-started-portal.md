---
title: Azure Scheduler를 사용하여 예약된 작업 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal에서 Azure Scheduler를 사용하여 첫 번째 자동화 작업을 만들고, 예약하고, 실행하는 방법을 알아봅니다.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 3b2cfc932c6322df8237ec7cdf820fc4242bfa72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531929"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Azure Scheduler를 사용하여 첫 번째 작업을 만들고 예약 - Azure Portal

> [!IMPORTANT]
> Azure Scheduler는 사용이 중지되며 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)가 대신 제공됩니다. 작업을 예약하려는 경우 [Azure Logic Apps를 대신 사용해 보세요](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

이 자습서에서는 얼마나 쉽게 작업을 만들고 예약한 다음, 해당 작업을 모니터링하고 관리할 수 있는지를 보여줍니다. 

Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

## <a name="create-job"></a>작업 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.  

1. Azure 기본 메뉴에서 **리소스 만들기**를 선택합니다. 검색 상자에 "scheduler"를 입력합니다. 결과 목록에서 **Scheduler**를 선택하고 **만들기**를 선택합니다.

   ![Scheduler 리소스 만들기](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   이제 URL `https://www.microsoft.com/`로 GET 요청을 보내는 작업을 만듭니다. 

1. **Scheduler 작업** 아래에서 다음 정보를 입력합니다.

   | 자산 | 예제 값 | 설명 |
   |----------|---------------|-------------| 
   | **Name** | getMicrosoft | 작업의 이름 | 
   | **작업 컬렉션** | <*job-collection-name*> | 작업 컬렉션을 만들거나 기존 컬렉션을 선택합니다. | 
   | **구독** | <*Azure-subscription-name*> | Azure 구독의 이름 | 
   |||| 

1. **작업 설정 - 구성**을 선택하고, 다음 정보를 입력하고, 모두 마쳤으면 **확인**을 선택합니다.

   | 자산 | 예제 값 | 설명 |
   |----------|---------------|-------------| 
   | **작업** | **Http** | 실행할 작업 유형 | 
   | **메서드** | **Get** | 호출할 메서드 | 
   | **URL** | **https://www.microsoft.com** | 대상 URL | 
   |||| 
   
   ![작업 정의](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. **일정 - 구성**을 선택하고, 일정을 정의하고, 모두 마쳤으면 **확인**을 선택합니다.

   일회성 작업을 만들 수도 있지만, 이 예제에서는 되풀이 일정을 설정합니다.

   | 자산 | 예제 값 | 설명 |
   |----------|---------------|-------------| 
   | **되풀이** | **정기** | 일회성 작업 또는 되풀이 작업 | 
   | **시작 시간** | <*오늘 날짜*> | 작업의 시작 날짜 | 
   | **되풀이 간격** | **1시간** | 되풀이 간격 및 빈도 | 
   | **종료** | **종료 날짜**: 오늘 날짜로부터 이틀 후 | 작업의 종료 날짜 | 
   | **UTC 오프셋** | **UTC +08:00** | UTC(협정 세계시)와 현지에서 관측된 시간의 차이 | 
   |||| 

   ![일정 정의](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. 준비되면 **만들기**를 선택합니다.

   작업을 만든 후에는 Azure가 작업을 배포하고, 배포된 작업은 대시보드에 표시됩니다. 

1. Azure가 배포 성공 알림을 표시하면 **대시보드에 고정**을 선택합니다. 알림이 표시되지 않으면 Azure 도구 모음에서 **알림** 아이콘(종 모양)을 선택하고 **대시보드에 고정**을 선택합니다.

## <a name="monitor-and-manage-jobs"></a>작업 모니터링 및 관리

작업을 검토, 모니터링 및 관리하려면 Azure 대시보드에서 작업을 선택합니다. **설정** 아래에는 작업을 검토하고 관리할 수 있는 영역이 있습니다.

![작업 설정](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

영역을 선택하면 영역에 대한 자세한 내용을 볼 수 있습니다.

* [**속성**](#properties)
* [**작업 설정**](#action-settings)
* [**일정**](#schedule)
* [**기록**](#history)
* [**사용자**](#users)

<a name="properties"></a>

### <a name="properties"></a>properties

작업의 관리 메타데이터를 설명하는 읽기 전용 속성을 보려면 **속성**을 선택합니다.

![작업 속성 보기](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>작업 설정

작업의 고급 설정을 변경하려면 **작업 설정**을 선택합니다. 

![작업 설정 검토](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| 작업 유형 | 설명 | 
|-------------|-------------| 
| 모든 형식 | **재시도 정책** 및 **오류 동작**을 변경할 수 있습니다. | 
| HTTP 및 HTTPS | **메서드**를 허용되는 아무 메서드로 변경할 수 있습니다. 헤더와 기본 인증 정보를 추가, 삭제 또는 변경할 수도 있습니다. | 
| Storage 큐| 저장소 계정, 큐 이름, SAS 토큰 및 본문을 변경할 수 있습니다. | 
| Service Bus | 네임스페이스, 토픽 또는 큐 경로, 인증 설정, 전송 유형, 메시지 속성, 메시지 본문을 변경할 수 있습니다. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>일정

작업 마법사를 통해 일정을 설정하는 경우 시작 날짜 및 시간, 되풀이 일정, 되풀이 작업의 종료 날짜 및 시간 등의 일정을 변경할 수 있습니다.
좀 더 [복잡한 일정 및 고급 되풀이](scheduler-advanced-complexity.md)를 빌드할 수도 있습니다.

보기를 변경하거나 작업의 일정을 변경하려면 **일정**을 선택합니다.

![작업 일정 보기](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>기록

선택한 작업의 모든 실행에 대한 메트릭을 보려면 **기록**을 선택합니다. 이러한 메트릭은 상태, 재시도 횟수, 발생 횟수, 시작 시간, 종료 시간 등 작업의 상태에 대한 실시간 값을 제공합니다.

![작업 기록 및 메트릭 보기](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

각 실행의 전체 응답 등 각 실행에 대한 기록 세부 정보를 보려면 **기록** 아래에서 각 실행을 선택합니다. 

![작업 기록 세부 정보 보기](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>사용자

Azure RBAC(역할 기반 액세스 제어)를 사용하여 각 사용자의 Azure Scheduler에 대한 액세스를 세밀하게 관리할 수 있습니다. 역할 기반 액세스를 설정하는 방법은 [RBAC를 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md) 알아보기
* [복잡한 일정 및 고급 되풀이 빌드](scheduler-advanced-complexity.md)
* [Scheduler의 고가용성 및 안정성](scheduler-high-availability-reliability.md) 알아보기
* [한도, 할당량, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md) 알아보기
