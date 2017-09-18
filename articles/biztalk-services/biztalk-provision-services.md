---
title: "Azure Portal에서 Azure BizTalk Services 만들기 | Microsoft 문서"
description: "Azure 포털에서 Azure BizTalk 서비스(MABS, WABS)를 프로비전하거나 만드는 방법인 대해 알아봅니다."
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: eca77b4a82eb67e1755717bb4429f8d450a64dc5
ms.contentlocale: ko-kr
ms.lasthandoff: 09/18/2017

---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Azure 포털을 사용하여 BizTalk 서비스 만들기

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]


> [!TIP]
> Azure 포털에 로그인하려면 Azure 계정과 Azure 구독이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. [Azure 무료 평가판](http://go.microsoft.com/fwlink/p/?LinkID=239738)을 참조하세요.


## <a name="CreateService"></a>BizTalk 서비스 만들기
선택한 버전에 따라 일부 BizTalk 서비스 설정을 사용하지 못할 수도 있습니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 탐색 창 맨 아래쪽에서 **새로 만들기**를 선택합니다.  
   ![새로 만들기 단추 선택][NEWButton]
3. **APP SERVICES** > **BIZTALK SERVICE** > **사용자 지정 만들기**를 선택합니다.  
   ![BizTalk 서비스 선택 및 사용자 지정 만들기 선택][NewBizTalkService]
4. BizTalk 서비스 설정을 입력합니다.
   
    <table border="1">
    <tr>
    <td><strong>BizTalk 서비스 이름</strong></td>
    <td>아무 이름이나 입력할 수 있지만 구체적이어야 합니다. 일부 사례:<br/><br/>
    <em>mycompany</em>.biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>.biztalk.windows.net<br/>
    <em>myapplication</em>.biztalk.windows.net<br/><br/>입력한 이름에 ".biztalk.windows.net"이 자동으로 추가됩니다. 이 항목은 BizTalk 서비스에 액세스하는 데 사용되는 URL(예: <strong>https://<em>myapplication</em>.biztalk.windows.net</strong>)을 만듭니다.
    </td>
    </tr>
    <tr>
    <td><strong>버전</strong></td>
    <td>테스트/개발 단계에 있으면 <strong>Developer</strong>를 선택합니다. 프로덕션 단계에 있으면 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk Services: 버전 차트</a>를 사용하여 <strong>Premium</strong>, <strong>Standard</strong>, <strong>Basic</strong> 중 비즈니스 시나리오에 적합한 버전을 선택합니다.
    </td>
    </tr>
    <tr>
    <td><strong>지역</strong></td>
    <td>BizTalk 서비스를 호스트할 지리적 지역을 선택합니다.</td>
    </tr>
    <tr>
    <td><strong>도메인 URL</strong></td>
    <td><strong>선택 사항</strong>입니다. 기본적으로 도메인 URL은 <em>YourBizTalkServiceName</em>.biztalk.windows.net입니다. 사용자 지정 도메인을 입력할 수도 있습니다. 예를 들어 도메인이 <em>contoso</em>이면 다음과 같이 입력할 수 있습니다. <br/><br/>
    <em>MyCompany</em>.contoso.com<br/>
    <em>MyCompanyMyApplication</em>.contoso.com<br/>
    <em>MyApplication</em>.contoso.com<br/>
    <em>YourBizTalkServiceName</em>.contoso.com<br/>
    </td>
    </tr>
    </table>
다음 화살표를 선택합니다.
5. 저장소 및 데이터베이스 설정을 입력합니다.  <table border="1">
    <tr>
    <td><strong>저장소 계정 모니터링/보관</strong></td>
    <td>기존 저장소 계정을 선택하거나 새 저장소 계정을 만듭니다. <br/><br/>새 저장소 계정을 만들 경우 <strong>저장소 계정 이름</strong>을 입력합니다.</td>
    </tr>
    <tr>
    <td><strong>데이터베이스 추적</strong></td>
    <td>기존 Azure SQL 데이터베이스를 사용하는 경우 다른 BizTalk 서비스에서는 이 데이터베이스를 사용할 수 없습니다. Azure SQL 데이터베이스 서버를 만들 때 입력한 로그인 이름과 암호가 있어야 합니다.<br/><br/><strong>팁</strong> BizTalk 서비스와 동일한 지역에 추적 데이터베이스 및 모니터링/보관 저장소 계정을 만듭니다.</td>
    </tr>
    </table>
다음 화살표를 선택합니다.
6. 데이터베이스 설정을 입력합니다.  <table border="1">
    <tr>
    <td><strong>Name</strong></td>
    <td>이전 화면에서 <strong>새 SQL Database 인스턴스 만들기</strong>를 선택한 경우에 사용할 수 있습니다.
    <br/><br/>
BizTalk 서비스에서 사용할 SQL Database 이름을 입력합니다.</td>
    </tr>
    <tr>
    <td><strong>서버</strong></td>
    <td>이전 화면에서 <strong>새 SQL Database 인스턴스 만들기</strong>를 선택한 경우에 사용할 수 있습니다.
    <br/><br/>
기존 SQL Database 서버를 선택하거나 새 SQL Database 서버를 만듭니다.</td>
    </tr>
    <tr>
    <td><strong>서버 로그인 이름</strong></td>
    <td>로그인 사용자 이름을 입력합니다.</td>
    </tr>
    <tr>
    <td><strong>서버 로그인 암호</strong></td>
    <td>로그인 암호를 입력합니다.</td>
    </tr>
    <tr>
    <td><strong>지역</strong></td>
    <td><strong>새 SQL Database 인스턴스 만들기</strong>를 선택한 경우에 사용할 수 있습니다. SQL 데이터베이스를 호스트할 지리적 지역을 선택합니다.</td>
    </tr>
    </table>

마법사를 완료하려면 확인 표시를 선택합니다. 진행률 아이콘이 표시됩니다.  
![완료 시 진행률 아이콘 표시][ProgressComplete]

완료되면 Azure BizTalk 서비스가 만들어져 응용 프로그램에서 사용할 준비가 됩니다. 기본 설정이 충분합니다. 기본 설정을 변경하려면 왼쪽 탐색 창에서 **BizTalk 서비스** 를 선택하고 해당 BizTalk 서비스를 선택합니다. 위쪽의 [대시보드, 모니터 및 크기 조정 탭](biztalk-dashboard-monitor-scale-tabs.md) 에 추가 설정이 표시됩니다.

BizTalk 서비스 상태에 따라 완료할 수 없는 작업도 일부 있습니다. 이러한 작업 목록은 [BizTalk 서비스 상태 차트](biztalk-service-state-chart.md)를 참조하세요.

## <a name="post-provisioning-steps"></a>프로비전 후 단계
* [로컬 컴퓨터에 인증서 설치](#InstallCert)
* [프로덕션이 준비된 인증서 추가](#AddCert)
* [액세스 제어 네임스페이스 가져오기](#ACS)

#### <a name="InstallCert"></a>로컬 컴퓨터에 인증서 설치
BizTalk 서비스 프로비전의 일부로 자체 서명된 인증서가 만들어지고 BizTalk 서비스 구독에 연결됩니다. 이 인증서를 다운로드하여 BizTalk 서비스 응용 프로그램을 배포하거나 BizTalk 서비스 끝점으로 메시지를 보낼 컴퓨터에 설치해야 합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **BizTalk 서비스** 를 선택한 다음 해당 BizTalk 서비스 구독을 선택합니다.
3. **대시보드** 탭을 선택합니다.
4. **SSL 인증서 다운로드**를 선택합니다.  
   ![SSL 인증서 수정][QuickGlance]
5. 인증서를 두 번 클릭하고 마법사를 실행하여 인증서를 설치합니다. **신뢰할 수 있는 루트 인증 기관** 저장소에 인증서를 설치해야 합니다.

#### <a name="AddCert"></a>프로덕션이 준비된 인증서 추가
BizTalk 서비스를 만들 때 자동으로 만들어진 자체 서명된 인증서는 개발 환경에서만 사용하기 위한 용도로 제공됩니다. 프로덕션 시나리오의 경우 이 인증서를 프로덕션이 준비된 인증서로 대체합니다.

1. **대시보드** 탭에서 **SSL 인증서 업데이트**를 선택합니다.
2. BizTalk 서비스 이름을 포함하는 개인 SSL 인증서(*CertificateName*.pfx)를 찾아 암호를 입력하고 확인 표시를 클릭합니다.

#### <a name="ACS"></a>액세스 제어 네임스페이스 가져오기
1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **BizTalk 서비스** 를 선택한 다음 해당 BizTalk 서비스를 선택합니다.
3. 작업 표시줄에서 **연결 정보**를 선택합니다.  
   ![연결 정보 선택][ACSConnectInfo]
4. 액세스 제어 값을 복사합니다.

Visual Studio에서 BizTalk 서비스를 배포할 때 이 액세스 제어 네임스페이스를 입력합니다. BizTalk 서비스에 대해 액세스 제어 네임스페이스가 자동으로 만들어집니다.

액세스 제어 값은 모든 응용 프로그램에서 사용할 수 있습니다. Azure BizTalk 서비스가 만들어지면 이 액세스 제어 네임스페이스는 BizTalk 서비스 배포 인증을 제어합니다. 구독을 변경하거나 네임스페이스를 관리하려면 왼쪽 탐색 창에서 **ACTIVE DIRECTORY** 를 선택한 다음 해당 네임스페이스를 선택합니다. 작업 표시줄에 옵션이 나열됩니다.

**관리** 를 클릭하면 액세스 제어 관리 포털이 열립니다. Access Control 관리 포털에서 BizTalk 서비스는 **서비스 ID**를 사용합니다.  
![액세스 제어 관리 포털의 ACS 서비스 ID][ACSServiceIdentities]

액세스 제어 서비스 ID는 응용 프로그램이나 클라이언트가 액세스 제어를 직접 인증하고 토큰을 받을 수 있도록 하는 자격 증명 집합입니다.

> [!IMPORTANT]
> BizTalk 서비스는 **암호** 값 및 기본 서비스 ID의 **소유자**를 사용합니다. 암호 값 대신 대칭 키 값을 사용하면 다음 오류가 발생할 수 있습니다<br/><br/>*지정한 자격 증명으로 액세스 제어 관리 서비스 계정에 연결할 수 없습니다*
> 
> 

[ACS 네임스페이스 관리](https://msdn.microsoft.com/library/azure/hh674478.aspx) 에는 일부 지침 및 권장 사항이 나열되어 있습니다.

## <a name="requirements-explained"></a>요구 사항 설명
다음 요구 사항은 무료 버전에는 적용되지 않습니다.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>필요한 항목</strong></td>
        <td><strong>필요한 이유</strong></td>
</tr>
<tr>
<td>Azure 구독</td>
<td>구독은 Azure 포털에 로그인할 수 있는 사용자를 결정합니다. 계정 소유자는 <a HREF="https://account.windowsazure.com/Subscriptions">Azure 구독</a>에서 구독을 만듭니다.
<br/><br/>
Azure 계정에는 여러 구독이 있을 수 있으며, 허용된 모든 사용자가 관리할 수 있습니다. 예를 들어 Azure 계정 소유자는 <em>BizTalkServiceSubscription</em>이라는 구독을 만들고, 회사 내의 BizTalk 관리자(예: ContosoBTSAdmins@live.com)에게 이 구독에 대한 액세스를 제공합니다. 이 시나리오에서 BizTalk 관리자는 Azure 포털에 로그인하고 구독의 모든 호스팅 서비스(Azure BizTalk 서비스 포함)에 대해 전체 관리자 권한을 가집니다. BizTalk 관리자는 Azure 계정 소유자가 아니므로 대금 청구 정보에 대한 액세스 권한이 없습니다.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Azure Portal에서 구독 및 저장소 계정 관리</a>에 자세한 내용이 나와 있습니다.
</td>
</tr>
<tr>
<td>Azure SQL 데이터베이스</td>
<td>추적 데이터를 포함하여 BizTalk 서비스에서 사용되는 테이블, 보기 및 저장 프로시저를 저장합니다.
<br/><br/>
BizTalk 서비스를 만들 때 기존 Azure SQL Server, Azure SQL 데이터베이스를 사용하거나 새로운 서버 또는 데이터베이스를 자동으로 만들 수 있습니다.
<br/><br/>
SQL 데이터베이스 크기는 자동으로 구성됩니다.  일반적으로 기본 크기는 BizTalk 서비스에 사용하기에 충분합니다. 크기를 변경하면 가격이 영향을 받습니다. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Azure SQL Database의 계정 및 대금 청구</a>
 참조<br/><br/>
<strong>참고</strong>
<br/>
<ul>
<li> 새 Azure SQL Server 및 데이터베이스를 만들면 Azure 서비스가 자동으로 사용하도록 설정됩니다. BizTalk 서비스의 경우에는 Azure 서비스를 사용하도록 설정해야 합니다.</li>
<li>기존 Azure SQL Server에서 새 Azure SQL 데이터베이스를 만들면 서버의 방화벽 규칙이 변경되지 않습니다. 따라서 다른 Azure 서비스가 서버의 데이터베이스에 액세스하지 못할 수 있습니다.</li>
</ul>
</td>
</tr>
<tr>
<td>Azure 액세스 제어 네임스페이스</td>
<td>Azure BizTalk 서비스로 인증합니다. Visual Studio에서 BizTalk 서비스를 배포할 때 이 액세스 제어 네임스페이스를 입력합니다. BizTalk 서비스를 만들면 액세스 제어 네임스페이스가 자동으로 만들어집니다.</td>
</tr>

<tr>
<td>Azure 저장소 계정</td>
<td>BizTalk 서비스에서 다음을 저장하는 데 사용되는 테이블, Blob 및 큐에 대한 액세스를 제공합니다.

<ul>
<li>BizTalk 서비스를 모니터링하는 로그 파일. 또한 모니터링한 결과도 Azure Portal의 **모니터링** 탭에 표시됩니다.</li>
<li>파트너 사이에 X12 또는 AS2 계약을 생성할 때 보관 기능을 사용하도록 설정하여 메시지 속성을 저장할 수 있습니다. 이 데이터는 저장소 계정에 저장됩니다.</li>
</ul>
<br/>
BizTalk 서비스를 만들 때 기존 저장소 계정을 사용하거나 새 저장소 계정을 자동으로 만들 수 있습니다.
<br/><br/>
기본 저장소 설정은 BizTalk 서비스에 사용하기에 충분합니다.
<br/><br/>
저장소 계정을 만들면 기본 키와 보조 키가 자동으로 만들어집니다. 이러한 키는 저장소 계정에 대한 액세스를 제어합니다. BizTalk 서비스는 자동으로 기본 키를 사용합니다.
<br/><br/>
자세한 내용은 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">저장소</a>를 참조하세요.
</td>
</tr>

<tr>
<td>SSL 개인 인증서</td>
<td>
Azure BizTalk 서비스가 만들어지면 BizTalk 서비스 이름이 포함된 HTTPS URL도 만들어집니다. 이 URL은 자체 서명된 개발 전용 인증서를 사용하도록 자동으로 구성됩니다. 프로덕션의 경우 개인 SSL 인증서가 필요합니다.
<br/><br/>
<strong>중요 SSL 인증서 정보</strong>

<ul>
<li>인증서 만료 날짜는 5년 미만이어야 합니다.</li>
<li>모든 개인 인증서에는 암호가 필요합니다. 이 암호를 확인하고 관리자와 공유하는 것이 좋습니다.</li>
<li>자체 서명된 인증서는 테스트/개발 환경에서 사용됩니다. 자체 서명된 인증서를 사용하는 경우 인증서를 개인 인증서 저장소 및 신뢰할 수 있는 루트 인증 기관 인증서 저장소로 가져옵니다.</li>
</ul>
<br/>프로덕션 인증서 요청을 인증 기관에 보내려면 다음 인증서 속성을 제공합니다.
<br/>

<ul>
<li><strong>확장된 키 사용</strong>: Azure BizTalk Services는 최소한 서버 인증이 필요합니다.</li>
<li><strong>일반 이름</strong>: Azure BizTalk 서비스 URL의 FQDN(정규화된 도메인 이름)을 입력합니다. 이 문서의 <a HREF="#CreateService">BizTalk 서비스 만들기</a>를 참조하세요.</li>
</ul>
<br/>
BizTalk 서비스를 만든 후 새 인증서나 다른 인증서를 추가할 수 있습니다.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>하이브리드 연결
Azure BizTalk 서비스를 만들면 **하이브리드 연결** 탭을 사용할 수 있습니다.

![하이브리드 연결 탭][HybridConnectionTab]

하이브리드 연결은 Azure 웹 사이트 또는 Azure 모바일 서비스를 정적 TCP 포트를 사용하는 온-프레미스 리소스(예: SQL Server, MySQL, HTTP 웹 API, 모바일 서비스 및 대부분의 사용자 지정 웹 서비스)에 연결하는 데 사용됩니다.  하이브리드 연결은 BizTalk 어댑터 서비스와 다릅니다. BizTalk 어댑터 서비스는 Azure BizTalk 서비스를 온-프레미스 LOB(기간 업무) 시스템에 연결하는 데 사용됩니다.

 하이브리드 연결을 만들고 관리하는 방법을 비롯한 자세한 내용은 [하이브리드 연결](integration-hybrid-connection-overview.md) 을 참조하세요.

## <a name="next-steps"></a>다음 단계
BizTalk 서비스를 만들었으므로 이제 다른 [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](biztalk-dashboard-monitor-scale-tabs.md)도 익숙해질 수 있습니다. 응용 프로그램에 BizTalk 서비스를 사용할 준비가 되었습니다. 응용 프로그램을 만들려면 [Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=235197)로 이동하십시오.

## <a name="see-also"></a>참고 항목
* [BizTalk 서비스: Editions 차트](biztalk-editions-feature-chart.md)<br/>
* [BizTalk Services: 상태 차트](biztalk-service-state-chart.md)<br/>
* [BizTalk 서비스: 백업 및 복원](biztalk-backup-restore.md)<br/>
* [BizTalk 서비스: 제한](biztalk-throttling-thresholds.md)<br/>
* [BizTalk 서비스: 발급자 이름 및 발급자 키](biztalk-issuer-name-issuer-key.md)<br/>
* [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [하이브리드 연결](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png

