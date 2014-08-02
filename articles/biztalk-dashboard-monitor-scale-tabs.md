<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor and Scale tabs" pageTitle="Dashboard, Monitor, and Scale in Biztalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭
================================================

Azure 관리 포털을 처음으로 열면 **모든 항목** 탭이 자동으로 표시됩니다. **모든 항목** 탭의 열은 정렬할 수 있습니다. BizTalk 서비스를 보려면 **모든 항목** 탭에서 BizTalk 서비스를 선택하거나 **BIZTALK 서비스** 탭을 클릭한 후 BizTalk 서비스 이름을 클릭합니다.

그러면 다음 옵션을 제공하는 새 창이 표시됩니다.

-   [빠른 시작](#QuickStart)

-   [대시보드](#Dashboard)

-   [모니터](#Monitor)

-   [크기 조정](#Scale)

이 항목에서는 이러한 탭에 대해 설명합니다.

빠른 시작
---------

빠른 시작 탭에서 다음을 수행할 수 있습니다.

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">옵션</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">설명</strong></td>
</tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">도구 얻기</td>
 
        <td data-morhtml="true">BizTalk 서비스 SDK를 다운로드하여 온-프레미스 개발 컴퓨터에 Visual Studio 프로젝트 템플릿을 설치합니다. 이러한 템플릿은 BizTalk 서비스에 배포되는 <strong data-morhtml="true">BizTalk 서비스</strong>(브리지) 및 <strong data-morhtml="true">BizTalk 서비스 아티팩트</strong>(변형) Visual Studio 프로젝트를 만듭니다.
 
        <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302335">Azure BizTalk 서비스 SDK로 시작하는 방법</a> 및 <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=241589">Azure BizTalk 서비스 SDK 설치</a>(영문)에서 시작 단계를 나열합니다.
        </td>
    </tr>
 
    <tr data-morhtml="true">
        <td data-morhtml="true">파트너 계약 만들기</td>
 
        <td data-morhtml="true">Azure에서 호스트되는 Azure BizTalk 서비스 포털을 엽니다. 이 포털에서 파트너를 추가하고 X12 및 AS2 EDI 계약을 생성할 수 있습니다.
 
        <br data-morhtml="true" /><br data-morhtml="true" />
 
        <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk 서비스 포털에서 EDI 메시징 구성 요소 구성</a>(영문)에서 시작 단계를 나열합니다.
        </td>
    </tr>
 
<tr data-morhtml="true">
        <td data-morhtml="true">BizTalk 서비스 알아보기</td>
 
        <td data-morhtml="true">Azure BizTalk 서비스에 대해 자세히 알아볼 수 있는 학습 센터로 이동합니다.</td>
</tr>
 
</table>

맨 아래의 작업 표시줄에서 BizTalk 서비스를 **관리**하거나 액세스 제어 네임스페이스 **연결 정보**를 보거나 저장소 계정의 **키를 동기화**하거나 BizTalk 서비스를 **삭제**할 수 있습니다.

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">옵션</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">설명</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">관리</td>
<td data-morhtml="true">관리를 클릭하면 Azure BizTalk 서비스 포털이 열립니다. BizTalk 서비스 포털에서 파트너 추가, AS2 및 X12 계약 생성 등 EDI 구성을 시작할 수 있습니다. <br data-morhtml="true" /><br data-morhtml="true" /> 이 메뉴는 <strong data-morhtml="true">빠른 시작</strong> 탭의 <strong data-morhtml="true">파트너 계약 생성</strong>과 동일합니다. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk 서비스 포털에서 EDI 메시징 구성 요소 구성</a>(영문)에서 BizTalk 서비스 포털에 대한 자세한 정보를 제공합니다.</td>
</tr>
 
<tr data-morhtml="true">
<td data-morhtml="true">연결 정보</td>
<td data-morhtml="true">연결 정보를 클릭하면 액세스 제어 네임스페이스, 기본 발급자 및 기본 키가 표시됩니다. 이러한 값은 복사할 수 있습니다. <br data-morhtml="true" /><br data-morhtml="true" /> 또한 액세스 제어 관리 포털을 열 수도 있습니다. 액세스 제어 관리 포털은 왼쪽 탐색 창에서 Active Directory 옵션을 사용하는 경우와 동일한 기능을 제공합니다. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=285670">ACS 네임스페이스 관리</a>에서 액세스 제어 관리 포털에 대한 자세한 정보를 제공합니다.</td>
</tr>
 
<tr data-morhtml="true">
<td data-morhtml="true">동기화 키</td>
<td data-morhtml="true">저장소 계정을 만들면 기본 키와 보조 키가 자동으로 만들어집니다. 이러한 키는 저장소 계정에 대한 액세스를 제어합니다. BizTalk 서비스는 자동으로 기본 키를 사용합니다. <strong data-morhtml="true">동기화 키</strong>를 통해 사용자는 BizTalk 서비스를 중단하지 않고 기본 키와 보조 키 간에 전환할 수 있습니다. <br data-morhtml="true" /><br data-morhtml="true" /> 예를 들어 BizTalk 서비스에서 저장소 계정의 새로운 기본 키를 사용할 수 있습니다. 다음을 수행합니다. <br data-morhtml="true" /><br data-morhtml="true" />
<ol data-morhtml="true">
<li data-morhtml="true">BizTalk 서비스를 클릭하고 <strong data-morhtml="true">동기화 키</strong>를 클릭합니다. 보조 키를 선택합니다. 이 작업을 수행하면 BizTalk 서비스에서 보조 키를 사용합니다.</li>
<li data-morhtml="true">Azure 관리 포털에서 저장소 계정을 클릭하고 기본 키를 다시 생성합니다. BizTalk 서비스에서 보조 키를 사용하고 있다는 점에 유의하십시오.</li>
<li data-morhtml="true">BizTalk 서비스를 클릭하고 <strong data-morhtml="true">동기화 키</strong>를 클릭합니다. 이제 기본 키를 선택합니다. 이 키는 앞서 다시 생성한 새로운 기본 키입니다.</li>
<li data-morhtml="true">Azure 관리 포털에서 저장소 계정을 클릭하고 보조 키를 다시 생성합니다.</li>
</ol>
<br data-morhtml="true" /> 이 프로세스를 &quot;키 롤오버&quot;라고 합니다. 이 프로세스의 목적은 사용자가 BizTalk 서비스를 중단하지 않고 기본 키와 보조 키 간에 전환할 수 있도록 하는 것입니다.</td>
</tr>
 
<tr data-morhtml="true">
<td data-morhtml="true">삭제</td>
<td data-morhtml="true">삭제를 클릭하면 BizTalk 서비스와 해당 서비스에 배포된 모든 항목이 제거됩니다.</td>
</tr>
</table>


대시보드
--------

BizTalk 서비스 이름을 클릭하면 대시보드 탭이 표시됩니다. 대시보드에서는 다음 정보를 표시합니다.

#### 메트릭 그래프

성능 메트릭의 고정 목록을 표시하는 그래프입니다. 이러한 메트릭은 BizTalk 서비스 상태와 관련된 실시간 값을 제공합니다. 메트릭은 다음과 같습니다.

-   CPU 사용량
-   원본에서 실패
-   처리 중 실패
-   처리된 메시지
-   수신된 메시지
-   처리 지연

이러한 성능 메트릭에 대한 설명은 이 항목의 [사용 가능한 메트릭](#Metrics)을 참조하십시오.

##### 상대 또는 절대

이 그래프는 추세를 나타내며, 각 메트릭의 현재 값인 **상대값** 옵션만 표시합니다. 절대값을 볼 수 있도록 Y축을 표시하려면 **절대값**을 선택합니다.

##### 간격

메트릭이 그래프에 표시되는 시간 범위를 수정합니다. 옵션은 다음과 같습니다.

-   1시간
-   1일
-   7일

#### 간략 상태

다음을 포함한 BizTalk 서비스 속성을 나열합니다.

<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">옵션</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">설명</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">추적 데이터베이스 자격 증명 업데이트</td>
<td data-morhtml="true">추적 데이터베이스에 로그인하는 데 사용하는 사용자 이름 및 암호를 변경합니다.<br data-morhtml="true" /><br data-morhtml="true" /> BizTalk 서비스를 프로비전할 때 추적 데이터베이스에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다. 이 옵션을 사용하면 다른 사용자 이름 및 암호를 사용하여 추적 데이터베이스에 로그인하도록 BizTalk 서비스를 수정할 수 있습니다.<br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전</a>에서 BizTalk 서비스를 프로비전하는 단계를 나열합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">SSL 인증서 업데이트</td>
<td data-morhtml="true">다른 SSL 인증서를 입력할 수 있습니다.<br data-morhtml="true" /><br data-morhtml="true" /> BizTalk 서비스를 프로비전하면 자체 서명된 SSL 인증서가 자동으로 만들어집니다. 이 옵션을 사용하여 다른 SSL 인증서를 사용하도록 BizTalk 서비스를 수정할 수 있습니다.<br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전</a>에서 BizTalk 서비스를 프로비전하는 단계를 나열합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">인증서 다운로드</td>
<td data-morhtml="true">이 옵션을 사용하여 BizTalk 서비스에 사용되는 SSL 인증서를 다운로드할 수 있습니다.<br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전</a>에서 BizTalk 서비스를 프로비전하는 단계를 나열합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">상태</td>
<td data-morhtml="true">BizTalk 서비스의 현재 상태를 보여 줍니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">서비스 URL</td>
<td data-morhtml="true">이 URL은 BizTalk 서비스로 라우팅합니다. 이는 BizTalk 서비스를 프로비전할 때 입력한 <strong data-morhtml="true">도메인 URL</strong>과 동일합니다. </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">공용 VIP(가상 IP) 주소</td>
<td data-morhtml="true">이 IP 주소는 BizTalk 서비스에 할당됩니다. 이는 모든 입력 끝점에 사용되며 아웃바운드 트래픽의 원본 주소입니다. 이 IP 주소가 프로비전된 상태에서는 BizTalk 서비스에 속합니다. BizTalk 서비스를 삭제하면 IP 주소가 다른 서비스 배포에 할당됩니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ACS 네임스페이스</td>
<td data-morhtml="true">BizTalk 서비스로 인증합니다. 이는 BizTalk 서비스를 프로비전할 때 입력한 <strong data-morhtml="true">ACS 네임스페이스</strong>와 동일합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">버전</td>
<td data-morhtml="true">버전을 나열합니다. 옵션에는 Developer, Basic, Standard 및 Premium이 포함됩니다. 이는 BizTalk 서비스를 프로비전할 때 입력한 버전과 동일합니다. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=302281">BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트</a>는 비용을 비롯하여 버전 간의 차이를 나열합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">위치</td>
<td data-morhtml="true">BizTalk 서비스를 호스트하는 지리적 지역을 표시합니다. 이는 BizTalk 서비스를 프로비전할 때 입력한 <strong data-morhtml="true">지역</strong>과 동일합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">만든 날짜</td>
<td data-morhtml="true">BizTalk 서비스를 프로비전한 날짜 및 시간을 표시합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">추적 데이터베이스</td>
<td data-morhtml="true">BizTalk 서비스에 사용되는 추적 테이블을 저장하는 Azure SQL 데이터베이스 이름입니다. 이는 BizTalk 서비스를 프로비전할 때 입력한 <strong data-morhtml="true">추적 데이터베이스</strong>와 동일합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">모니터링/보관 저장소</td>
<td data-morhtml="true">BizTalk 서비스에 대한 모니터링 출력을 저장하는 Azure 저장소 계정 이름입니다. 이는 BizTalk 서비스를 프로비전할 때 입력한 <strong data-morhtml="true">모니터링/보관 저장소 계정</strong>과 동일합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">구독 이름</td>
<td data-morhtml="true">구독은 Azure 관리 포털에 대한 액세스를 제어합니다. 이는 BizTalk 서비스를 프로비전할 때 선택한 <strong data-morhtml="true">구독</strong> 이름과 동일합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">구독 ID</td>
<td data-morhtml="true">구독은 Azure 관리 포털에 대한 액세스를 제어합니다. 구독을 만들 때 구독 ID가 자동으로 생성됩니다. REST API를 사용할 경우 구독 ID를 입력해야 할 수도 있습니다.</td>
</tr>
</table>
 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전</a>에서 BizTalk 서비스를 프로비전하는 단계를 나열합니다.</p>
 
<h4>관리, 연결 정보, 동기화 키 및 삭제</h4>
<p>맨 아래의 작업 표시줄에서 BizTalk 서비스를 <strong>관리</strong>하거나 액세스 제어 네임스페이스 <strong>연결 정보</strong>를 보거나 저장소 계정의 <strong>키를 동기화</strong>하거나 BizTalk 서비스를 <strong>삭제</strong>할 수 있습니다.</p>
 
<table data-morhtml="true" border="1">
<tr data-morhtml="true" bgcolor="FAF9F9">
        <td data-morhtml="true"><strong data-morhtml="true">옵션</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">설명</strong></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">관리</td>
<td data-morhtml="true">관리를 클릭하면 Azure BizTalk 서비스 포털이 열립니다. BizTalk 서비스 포털에서 파트너 추가, AS2 및 X12 계약 생성 등 EDI 구성을 시작할 수 있습니다. <br data-morhtml="true" /><br data-morhtml="true" /> 이 메뉴는 <strong data-morhtml="true">빠른 시작</strong> 탭의 <strong data-morhtml="true">파트너 계약 생성</strong>과 동일합니다. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=303653">BizTalk 서비스 포털에서 EDI 메시징 구성 요소 구성</a>(영문)에서 BizTalk 서비스 포털에 대한 자세한 정보를 제공합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">연결 정보</td>
<td data-morhtml="true">연결 정보를 클릭하면 액세스 제어 네임스페이스, 기본 발급자 및 기본 키가 표시됩니다. 이러한 값은 복사할 수 있습니다. <br data-morhtml="true" /><br data-morhtml="true" /> 또한 액세스 제어 관리 포털을 열 수도 있습니다. 액세스 제어 관리 포털은 왼쪽 탐색 창에서 Active Directory 옵션을 사용하는 경우와 동일한 기능을 제공합니다. <br data-morhtml="true" /><br data-morhtml="true" /> <a data-morhtml="true" href="http://go.microsoft.com/fwlink/p/?LinkID=285670">ACS 네임스페이스 관리</a>에서 액세스 제어 관리 포털에 대한 자세한 정보를 제공합니다.</td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">동기화 키</td>
<td data-morhtml="true">저장소 계정을 만들면 기본 키와 보조 키가 자동으로 만들어집니다. 이러한 키는 저장소 계정에 대한 액세스를 제어합니다. BizTalk 서비스는 자동으로 기본 키를 사용합니다. <strong data-morhtml="true">동기화 키</strong>를 통해 사용자는 BizTalk 서비스를 중단하지 않고 기본 키와 보조 키 간에 전환할 수 있습니다. <br data-morhtml="true" /><br data-morhtml="true" /> 예를 들어 BizTalk 서비스에서 저장소 계정의 새로운 기본 키를 사용할 수 있습니다. 다음을 수행합니다. <br data-morhtml="true" /><br data-morhtml="true" />
<ol data-morhtml="true">
<li data-morhtml="true">BizTalk 서비스를 클릭하고 <strong data-morhtml="true">동기화 키</strong>를 클릭합니다. 보조 키를 선택합니다. 이 작업을 수행하면 BizTalk 서비스에서 보조 키를 사용합니다.</li>
<li data-morhtml="true">Azure 관리 포털에서 저장소 계정을 클릭하고 기본 키를 다시 생성합니다. BizTalk 서비스에서 보조 키를 사용하고 있다는 점에 유의하십시오.</li>
<li data-morhtml="true">BizTalk 서비스를 클릭하고 <strong data-morhtml="true">동기화 키</strong>를 클릭합니다. 이제 기본 키를 선택합니다. 이 키는 앞서 다시 생성한 새로운 기본 키입니다.</li>
<li data-morhtml="true">Azure 관리 포털에서 저장소 계정을 클릭하고 보조 키를 다시 생성합니다.</li>
</ol>
<br data-morhtml="true" /> 이 프로세스를 &quot;키 롤오버&quot;라고 합니다. 이 프로세스의 목적은 사용자가 BizTalk 서비스를 중단하지 않고 기본 키와 보조 키 간에 전환할 수 있도록 하는 것입니다.</td>
</tr>
 
<tr data-morhtml="true">
<td data-morhtml="true">삭제</td>
<td data-morhtml="true">삭제를 클릭하면 BizTalk 서비스와 해당 서비스에 배포된 모든 항목이 제거됩니다.</td>
</tr>
</table>


모니터
------

모니터 탭에서는 다음 정보를 표시합니다.

#### 메트릭 그래프

선택한 성능 메트릭을 표시하는 그래프입니다. 이러한 메트릭은 BizTalk 서비스 상태와 관련된 실시간 값을 제공합니다. 표시할 성능 메트릭을 선택합니다. 최대 6개의 성능 메트릭을 동시에 표시할 수 있습니다.

##### 상대 또는 절대

이 그래프는 추세를 나타내며, 각 메트릭의 현재 값인 **상대값** 옵션만 표시합니다. 절대값을 볼 수 있도록 Y축을 표시하려면 **절대값**을 선택합니다.

##### 간격

메트릭이 그래프에 표시되는 시간 범위를 수정합니다. 옵션은 다음과 같습니다.

-   1시간
-   1일
-   7일

#### 그래프에서 메트릭을 제거하거나 표시하려면

>1. **모니터** 탭을 클릭합니다.
>2. 작업 표시줄에서 **메트릭 추가**를 클릭합니다.  
![메트릭 추가 클릭]
>3. **모니터** 탭에 표시할 성능 메트릭을 선택합니다.
>4. 확인 표시를 클릭하여 **모니터** 탭으로 돌아갑니다.
>5. 메트릭 옆에 있는 원을 클릭하여 그래프에 메트릭 값을 표시합니다.<br/>
예를 들어 **CPU 사용량** 메트릭이 회색으로 표시되면 이 메트릭의 출력이 그래프에 표시되지 않습니다.<br/>
![CPU 사용량 메트릭이 흐릿함]
<br/>
흐릿한 원을 클릭하면 그래프에 **CPU 사용량** 메트릭의 출력을 표시할 수 있습니다.<br/>
![CPU 사용량 메트릭 사용]

> 1.  디스플레이 그래프 및 목록에서 메트릭을 제거하려면 작업 표시줄에서 **메트릭 삭제**를 클릭합니다. **메트릭 삭제**를 클릭하면 모니터 탭에서 메트릭이 제거됩니다. 목록에 메트릭을 다시 추가하려면 작업 표시줄에서 **메트릭 추가**를 클릭하고 메트릭을 선택하고 확인 표시를 클릭하여 **모니터** 탭으로 돌아갑니다. 흐릿한 원을 클릭하면 그래프에 메트릭을 사용할 수 있습니다.

사용 가능한 메트릭
------------------

다음 성능 카운터/메트릭을 사용할 수 있습니다.

<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>메트릭</strong></td>
<td><strong>설명</strong></td>
</tr>
<tr>
<td>왕복 지연</td>
<td>이 성능 메트릭은 메시지를 받은 시간부터 모든 브리지에서 BizTalk 서비스를 통해 메시지가 완전하게 처리될 때까지 메시지 처리의 평균 시간을 밀리초(ms) 단위로 표시합니다. 성공적으로 처리된 메시지만 계산됩니다.<br/><br/>
다음 이벤트가 발생할 경우 타임스탬프가 만들어집니다.
<ul>
<li>메시지가 게이트웨이에 들어오는 경우</li>
<li>메시지가 대상으로 라우팅되는 경우</li>
<li>대상 응답을 받은 경우</li>
<li>대상 확인 응답이 게이트웨이에 전송된 경우</li>
</ul>
<br/>
이 메트릭은 다음 계산의 결과를 표시합니다.
<br/><br/>
[대상 확인 응답이 게이트웨이에 전송된 시간] - [메시지가 게이트웨이에 들어온 시간]</td>
</tr>
<tr>
<td>원본에서 실패</td>
<td>원본 끝점에서 메시지를 풀할 때 BizTalk 서비스에서 실패한 총 메시지 수를 표시하는 성능 메트릭입니다.</td>
</tr>
<tr>
<td>CPU 사용량</td>
<td>모든 역할 인스턴스의 평균 프로세서 시간 백분율을 나열합니다.</td>
</tr>
<tr>
<td>처리 지연</td>
<td>이 성능 메트릭은 BizTalk 서비스가 모든 브리지에 걸쳐 메시지를 처리하는 데 걸린 평균 시간을 밀리초(ms)로 표시합니다. 단, 대상에서 소요된 시간은 제외됩니다. 성공적으로 처리된 메시지만 계산됩니다.<br/><br/>
다음 이벤트가 발생할 경우 타임스탬프가 만들어집니다. 

<ul>
<li>메시지가 게이트웨이에 들어오는 경우</li>
<li>메시지가 대상으로 라우팅되는 경우</li>
<li>대상 응답을 받은 경우</li>
<li>대상 확인 응답이 게이트웨이에 전송된 경우</li>
</ul>
<br/>이 메트릭은 다음 계산의 결과를 표시합니다.<br/><br/>
[대상 확인 응답이 게이트웨이에 전송된 시간] - [메시지가 게이트웨이에 들어온 시간] - [대상 응답을 받은 시간] + [메시지가 대상으로 라우팅된 시간]</td>
</tr>
<tr>
<td>처리 중 실패</td>
<td>BizTalk 서비스가 일정 시간 간격 내에 모든 브리지에 걸쳐 메시지를 처리하는 동안 실패한 총 메시지 수를 표시하는 성능 메트릭입니다.</td>
</tr>
<tr>
<td>전송된 메시지</td>
<td>BizTalk 서비스가 일정 시간 간격 내에 모든 브리지에 걸쳐 전송한 총 메시지 수를 표시하는 성능 메트릭입니다. 이 메트릭은 파이프라인에서 전송된 메시지가 경로 대상에 도달할 때 증가합니다. 이 메트릭이 메시지가 성공적으로 처리되었음을 나타내지는 않습니다.<br/><br/>
요청-회신 시나리오에서, 경로 대상이 수신 확인을 파이프라인에 다시 보낼 때 이 메트릭이 증가합니다.</td>
</tr>
<tr>
<td>수신된 메시지</td>
<td>BizTalk 서비스가 일정 시간 간격 내에 모든 브리지에 걸쳐 수신한 총 메시지 수를 표시하는 성능 메트릭입니다. 이 메트릭은 파이프라인에서 새 메시지를 수신할 때 증가합니다.</td>
</tr>
<tr>
<td>진행 중 메시지</td>
<td>BizTalk 서비스가 일정 시간 간격 내에 처리 중인 총 메시지 수를 표시하는 성능 메트릭입니다.</td>
</tr>
<tr>
<td>처리된 메시지</td>
<td>BizTalk 서비스가 일정 시간 간격 내에 모든 브리지에 걸쳐 성공적으로 처리한 총 메시지 수를 표시하는 성능 메트릭입니다. 이 메트릭은 파이프라인에서 메시지를 성공적으로 수신하여 대상으로 라우팅할 때 증가합니다.</td>
</tr>
</table>

크기 조정
---------

크기 조정 탭에서 BizTalk 서비스에 사용되는 단위 수를 추가하거나 줄일 수 있습니다. 기본적으로 한 개의 단위가 구성되어 있습니다. 단위를 더 추가하여 BizTalk 서비스를 확장할 수 있습니다. 크기를 늘리면 처리량을 늘리는 것입니다. 배포된 브리지, 계약, LOB 연결, 처리 능력 등 리소스의 양도 증가합니다. 예를 들어 1단위에서 2단위로 크기를 늘립니다. 이 경우 브리지 수, 계약, LOB 연결, 처리 기능을 두 배로 배포할 수 있습니다.

일부 BizTalk 버전에서는 크기 조정 옵션을 제공하지 않습니다. 이 경우 1 단위가 허용됩니다. 사용 중인 버전에서 크기 조정할 수 있는 단위 수를 확인하려면 [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)를 참조하십시오.

단위 수를 늘리면 가격이 달라질 수 있습니다. 단위를 늘리고 **저장**을 클릭하면 청구 금액이 달라질 수 있다는 메시지가 표시됩니다. 그런 다음 계속하도록 선택합니다. 단위 수를 늘리면 BizTalk 서비스 상태가 활성에서 업데이트 중으로 변합니다. 업데이트 중 상태에서도 BizTalk 서비스는 계속 실행됩니다.

다음
----

여러 탭을 살펴봤으므로 다음과 같은 Azure BizTalk 서비스 기능에 대해 자세히 알아볼 수 있습니다.

-   [BizTalk 서비스: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [BizTalk 서비스: 발급자 이름 및 발급자 키](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [BizTalk 서비스: 백업 및 복원](http://go.microsoft.com/fwlink/p/?LinkID=329873)

참고 항목
---------

-   [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk 서비스: BizTalk 서비스 상태 차트](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[메트릭 추가 클릭]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[CPU 사용량 메트릭이 흐릿함]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[CPU 사용량 메트릭 사용]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
