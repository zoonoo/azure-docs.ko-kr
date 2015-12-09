<properties
 pageTitle="Azure 포털에서 Azure 스케줄러 사용 시작 | Microsoft Azure"
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
 ms.date="08/18/2015"
 ms.author="krisragh"/>

# Azure 포털에서 Azure 스케줄러 사용 시작

## Azure 포털을 사용하여 작업을 쉽고 빠르게 만들 수 있게 스케줄러 구성

이 자습서를 완료하려면 Azure 스케줄러 기능이 사용되는 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## 시작

Azure 포털을 사용하면 Azure 스케줄러에서 쉽게 작업 및 작업 컬렉션을 만들 수 있습니다. 이 자습서에서는 작업 저장하는 데 사용할 작업 컬렉션 만들기, 작업 컬렉션에 작업 만들기, 포털에서 사용 가능한 작업 모니터링 및 관리 작업 개요를 단계별로 안내합니다. 이전에 Azure를 사용한 경험이 있어야 이 자습서를 사용할 수 있습니다.

Azure 포털을 처음으로 열면 **모든 항목** 탭이 자동으로 표시됩니다. **모든 항목** 탭의 열은 정렬할 수 있습니다. 스케줄러 작업 및 작업 컬렉션을 보려면**스케줄러** 탭을 클릭합니다.

![][1]

## 작업 컬렉션 및 작업 만들기

1.  [Azure 포털](https://manage.windowsazure.com/)에 로그인합니다.  

2.  **앱 서비스**, **새로 만들기**를 차례로 클릭한 다음 **스케줄러**에서 **사용자 지정 만들기**를 클릭합니다. <br /><br /> ![][2]

3.  **작업 컬렉션**의 **작업 컬렉션** 드롭다운 아래에서 기존 작업 컬렉션의 이름을 선택합니다. 작업을 추가할 기존 작업 컬렉션이 없으면 **새로 만들기**를 클릭하고 새 작업 컬렉션을 식별하는 이름을 입력합니다.<br /><br /> ![][3]

4.  **지역**에서 작업 컬렉션의 지리적 지역을 선택합니다.

5.  화살표 키를 클릭하여 작업 컬렉션ㅇ르 만들고 다음 단계로 이동하여 작업을 만듭니다.

6.  간단히 GET 요청으로 http://www.microsoft.com/을 히트하는 작업을 만들어 보겠습니다. **작업 동작** 화면에서 요청된 양식 필드에 대해 다음 값을 정의합니다.

    1.  **이름:** ` getmicrosoft`  

    2.  **동작 유형:** ` HTTP`

    3.  **메서드:** ` GET`

    4.  **URI:** ` http://www.microsoft.com`

   	![][4]

7.  작업을 만든 후 일정을 정의합니다. 작업은 일회성 작업으로 정의할 수 있지만 되풀이 일정을 선택해 보겠습니다. 이 자습서의 일부 스크린샷은 단지 설명을 위해 1분 되풀이를 표시하지만 12시간 되풀이를 선택합니다.

    1.  **되풀이 간격:** ` 12 Hours`  

    2.  **시작 중:** ` Now`

    3.  **종료:** ` Select date 2 days after current day and any time`

   	![][5]

8.  **확인**을 클릭합니다. 작업 및 작업 컬렉션을 만드는 시간이 걸릴 수 있습니다. 상태를 확인하려면 포털 화면의 아래쪽에서 알림을 모니터링할 수 있습니다.

   	![][6]

   	작업 및 작업 컬렉션을 만든 후 작업 또는 작업 컬렉션이 만들어졌다는 메시지가 표시됩니다. 작업은 스케줄러 섹션의 작업 섹션에, 작업 컬렉션은 작업 컬렉션 섹션에 나열됩니다. 작업에서 추가 고급 설정을 구성하려면 아래의 “작업 구성" 섹션을 참조하십시오.

   	![][7]

## 작업 컬렉션 및 작업 관리와 모니터링

만든 작업 컬렉션은 주 스케줄러 관리 화면에 표시됩니다.

![][8]

작업 컬렉션을 클릭합니다. 그러면 다음 옵션을 제공하는 새 창이 열립니다.

1.  대시보드  

2.  확장

3.  기록

4.  작업

다음 항목에서는 이러한 탭을 더 상세히 설명합니다.

### 대시보드

작업 컬렉션 이름을 클릭하면 **대시보드** 탭이 표시됩니다. 대시보드에서는 다음 정보를 표시합니다.

![][9]

#### 작업 사용 개요 및 실행 사용 개요

고정 메트릭 목록을 표시하는 표와 여러 차트입니다. 이러한 메트릭은 다음을 포함하여 작업 컬렉션 상태와 관련한 실시간 값을 제공합니다.

1.  현재 작업  

2.  완료된 작업

3.  오류가 발생한 작업

4.  활성화된 작업

5.  비활성화된 작업

6.  작업 실행

#### 빠른 보기

상태 및 설정 메트릭의 고정된 목록을 보여주는 테이블입니다. 이러한 메트릭은 다음을 포함하여 작업 컬렉션과 관련한 상태 및 설정의 실시간 값을 제공합니다.

1.  상태  

2.  지역

3.  오류 개수

4.  발생 오류 수

5.  URI

### 크기 조정

**크기 조정** 탭에서 설정과, 스케줄러에서 사용하는 서비스 계층을 변경할 수 있습니다.

![][10]

#### 일반

여기에는 **무료** 요금제인지 **표준** 요금제인지 여부가 표시됩니다.

#### 할당량

Azure 스케줄러는 여러 조건에 따라 할당량을 구현 합니다. 이 섹션에는 할당량 임계값이 나열되며 사용자가 해당 항목을 변경할 수 있습니다. 기본적으로 한 개의 할당량 집합이 구성되어 있습니다. 이 할당량 설정 한계는 버전에 따라 결정되며 버전 변경은 가격 책정에 영향을 미칩니다. 스케줄러의 크기를 조정하기 위해 할당량을 변경할 수 있습니다. 옵션은 다음과 같습니다.

1.  최대 작업  

2.  최대 빈도

3.  최대 간격

### 기록

**기록** 탭에는 선택한 작업에 대한 다음 정보가 표시됩니다.

![][11]

#### 기록 테이블

선택한 작업에 대해 시스템의 모든 작업 실행에 선택한 메트릭을 표시하는 테이블입니다. 이러한 메트릭은 스케줄러 상태와 관련된 실시간 값을 제공합니다.

#### 사용 가능한 메트릭

다음 성능 카운터/메트릭을 사용할 수 있습니다.

1.  상태  

2.  세부 정보

3.  재시도 횟수

4.  실행 횟수(1회, 2회, 3회 등)

5.  실행 타임스탬프

**기록 세부 정보 보기**를 클릭하여 각 실행의 전체 응답을 살펴볼 수 있습니다. 이 대화 상자에서는 응답을 클립보드에 복사할 수도 있습니다.

![][12]

### 작업

작업 탭은 작업 실행 기록의 모니터링을 위해 다음 정보를 표시합니다.

![][13]

#### 작업 테이블

시스템의 각 작업에 대해 선택한 메트릭을 표시하는 테이블입니다. 이러한 메트릭은 스케줄러 상태와 관련된 실시간 값을 제공합니다.

#### 작업 사용 안 함, 사용 또는 삭제

작업 이름을 클릭하면 작업을 사용 또는 사용하지 않도록 설정하거나 삭제하는 옵션이 제공됩니다. 삭제한 작업은 복구하지 못할 수 있습니다.

#### 사용 가능한 메트릭

다음 카운터 및 메트릭을 사용할 수 있습니다.

1.  이름  

2.  마지막으로 실행

3.  다음 실행

4.  상태

5.  Frequency(빈도)

6.  오류

7.  오류

8.  실행

9.  동작 유형

### 작업 구성

**작업** 화면에서 작업을 클릭하면 해당 작업을 구성할 수 있습니다. 이렇게 하면 빠른 만들기 마법사에서 사용 가능한 설정 외에 추가적인 고급 설정을 구성할 수 있습니다. 작업을 구성하려면 **작업** 화면에서 작업 이름 옆에 있는 오른쪽 화살표를 클릭합니다.

작업 구성 페이지에서는 작업 설정을 업데이트할 수 있습니다. HTTP 및 HTTPS 작업에 대한 작업 구성 페이지는 다음과 같습니다. HTTP 및 HTTPS 작업 동작 유형에서, 메서드를 허용되는 HTTP 동사로 변경할 수 있습니다. 헤더와 기본 인증 정보를 추가, 삭제 또는 변경할 수도 있습니다.

![][14]

저장소 큐 작업의 작업 구성 페이지는 다음과 같습니다. 저장소 큐 동작 유형의 경우 저장소 계정, 큐 이름, SAS 토큰 및 본문을 변경할 수 있습니다. "일정" 섹션(아래에 없음)은 HTTP/HTTPS 작업 동작 유형의 "일정" 섹션과 동일합니다.

![][15]

마지막으로, 모든 작업 유형에서 일정 자체와 되풀이 작동을 변경할 수 있습니다. 시작 날짜와 시간, 되풀이 일정 및 종료 날짜와 시간(작업이 되풀이될 경우)을 변경할 수 있습니다. 변경 후에는 **저장**을 클릭하여 변경 내용을 저장하거나 **취소**를 클릭하여 변경 내용을 버릴 수 있습니다.

## 참고 항목

 [스케줄러란?](scheduler-intro.md)

 [스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법](scheduler-advanced-complexity.md)

 [스케줄러 REST API 참조](https://msdn.microsoft.com/library/dn528946)

 [스케줄러 PowerShell Cmdlet 참조](scheduler-powershell-reference.md)

 [스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)



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

<!---HONumber=AcomDC_1203_2015-->