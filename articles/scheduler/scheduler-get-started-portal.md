<properties
 pageTitle="Azure 포털에서 Azure 스케줄러 시작 | Microsoft Azure"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Azure 포털에서 Azure 스케줄러 시작

Azure 스케줄러에서 예약된 작업을 만드는 것은 쉽습니다. 이 자습서에서는 작업을 만드는 방법을 알아봅니다. 스케줄러의 모니터링 및 관리 기능도 알아봅니다.

## 작업 만들기

1.  [Azure 포털](https://portal.azure.com/)에 로그인합니다.  

2.  **+새로 만들기**를 클릭하고 > 검색 상자에 _스케줄러_를 입력하고 > 결과에서 **스케줄러**를 선택하고 > **만들기**를 클릭합니다.

   ![][marketplace-create]

3.  간단히 GET 요청으로 http://www.microsoft.com/을 히트하는 작업을 만들어 보겠습니다. **스케줄러 작업** 화면에서 다음 정보를 입력합니다.

    1.  **이름:** `getmicrosoft`  

    2.  **구독:** Azure 구독입니다.

    3.  **작업 컬렉션:** 기존 작업 컬렉션을 선택하거나 **새로 만들기**를 클릭하고 > 이름을 입력합니다.

4.  다음으로 **동작 설정**에서 다음 값을 정의합니다.

    1.  **동작 유형:** ` HTTP`  

    2.  **메서드:** `GET`

    3.  **URL:** ` http://www.microsoft.com`

   ![][action-settings]

5.  마지막으로, 일정을 정의해 보겠습니다. 작업은 일회성 작업으로 정의할 수 있지만 되풀이 일정을 선택해 보겠습니다.

    1. **되풀이**: `Recurring`

    2. **시작**: 오늘 날짜

    3. **되풀이 간격**: `12 Hours`

    4. **종료 날짜**: 오늘 날짜부터 이틀 후

   ![][recurrence-schedule]

6.  **만들기**를 클릭합니다.

## 작업 관리 및 모니터링

작업이 만들어지면 기본 Azure 대시보드에 표시됩니다. 작업을 클릭하면 다음 탭을 제공하는 새 창이 열립니다.

1.  속성  

2.  동작 설정

3.  일정

4.  기록

5.  사용자

   ![][job-overview]

### 속성

이러한 읽기 전용 속성은 스케줄러 작업에 대한 관리 메타데이터를 설명합니다.

   ![][job-properties]


### 작업 설정

**작업** 화면에서 작업을 클릭하면 해당 작업을 구성할 수 있습니다. 이렇게 하면 빠른 만들기 마법사에서 구성하지 않은 경우 고급 설정을 구성할 수 있습니다.

모든 동작 유형의 경우 다시 시도 정책 및 오류 동작을 변경할 수 있습니다.

HTTP 및 HTTPS 작업 동작 유형에서, 메서드를 허용되는 HTTP 동사로 변경할 수 있습니다. 헤더와 기본 인증 정보를 추가, 삭제 또는 변경할 수도 있습니다.

저장소 큐 동작 유형의 경우 저장소 계정, 큐 이름, SAS 토큰 및 본문을 변경할 수 있습니다.

서비스 버스 동작 유형의 네임스페이스, 토픽/큐 경로, 인증 설정, 전송 유형, 메시지 속성 및 메시지 본문을 변경할 수 있습니다.

   ![][job-action-settings]

### 일정

이렇게 하면 빠른 만들기 마법사에서 만든 일정을 변경하지 않으려는 경우 일정을 다시 구성할 수 있습니다.

[작업에서 복잡한 일정 및 고급 되풀이](scheduler-advanced-complexity.md)를 만들 수 있는 기회를 제공합니다.

시작 날짜와 시간, 되풀이 일정 및 종료 날짜와 시간(작업이 되풀이될 경우)을 변경할 수 있습니다.

   ![][job-schedule]


### 기록

**기록** 탭은 선택한 작업에 대해 시스템의 모든 작업 실행에 선택한 메트릭을 표시합니다. 이러한 메트릭은 스케줄러 상태와 관련된 실시간 값을 제공합니다.

1.  상태  

2.  세부 정보

3.  다시 시도 횟수

4.  발생 빈도: 첫 번째, 두 번째, 세 번째 등

5.  실행 시작 시간

6.  실행 종료 시간

   ![][job-history]

**기록 세부 정보** 보기 실행을 클릭하여 각 실행의 전체 응답을 살펴볼 수 있습니다. 이 대화 상자에서는 응답을 클립보드에 복사할 수도 있습니다.

   ![][job-history-details]

### 사용자

Azure RBAC(역할 기반 액세스 제어)를 통해 Azure 스케줄러에 대한 세밀한 액세스 관리가 가능합니다. 사용자 탭을 사용하는 방법을 알아보려면 [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.


## 참고 항목

 [스케줄러란?](scheduler-intro.md)

 [스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법](scheduler-advanced-complexity.md)

 [스케줄러 REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [스케줄러 PowerShell Cmdlet 참조](scheduler-powershell-reference.md)

 [스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=AcomDC_0316_2016-->