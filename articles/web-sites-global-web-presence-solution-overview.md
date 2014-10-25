<properties linkid="websites-global-web-presence" urlDisplayName="Create a Global Web Presence on Azure Websites" pageTitle="Create a Global Web Presence on Azure Websites" metaKeywords="" description="This guide provides a technical overview of how to host your organization's (.COM) site on Azure Websites. This includes deployment, custom domains, SSL, and monitoring." metaCanonical="http://www.windowsazure.com/ko-KR/documentation/articles/web-sites-global-web-presence-solution-overview/" services="" documentationCenter="" title="Create a Global Web Presence on Azure Websites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Azure 웹 사이트에 글로벌 웹 서비스 만들기

이 가이드는 Azure에 조직의 사이트(.COM)를 호스트하는 방법에 대한 기술적 개요를 제공합니다. 이 시나리오를 글로벌 웹 서비스라고도 합니다. 웹 사이트는 Azure에서 웹 응용 프로그램을 생성, 마이그레이션, 확장 및 관리하는 가장 빠르고 간단한 방법이기 때문에 이 가이드는 [Azure 웹 사이트][] 사용을 중점적으로 다룹니다. 그러나 일부 응용 프로그램 요구 사항은 IIS를 실행하는 [Azure 클라우드 서비스][](영문) 또는 [Azure 가상 컴퓨터][](영문)에 더 적합합니다. 이 또한 웹 응용 프로그램 호스팅에 대해 탁월한 선택입니다. 처음 계획 단계인 경우 [Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우][](영문)를 참조하세요. 클라우드 서비스 또는 가상 컴퓨터를 사용하기 위한 요구 사항이 없는 경우, 글로벌 웹 서비스 호스팅에 웹 사이트를 사용하는 것이 좋습니다. 이 문서의 나머지는 이 시나리오로 웹 사이트를 사용하는 방법을 안내합니다.

이 가이드에서는 다음 영역에 대해 설명합니다.

-   [Azure 웹 사이트 만들기][]
-   [웹 사이트 배포][]
-   [사용자 지정 도메인 추가][]
-   [SSL을 사용하여 웹 사이트 보안 유지][]
-   [사이트 모니터링][]

<div class="dev-callout">
<strong>참고</strong>
<p>이 가이드에서는 공용 .COM 사이트 개발에 맞는 가장 일반적인 몇 가지 영역과 작업에 대해 설명합니다. 그러나 특정 구현에서 사용할 수 있는 Azure 웹 사이트의 다른 기능이 있습니다. 이러한 기능을 검토하려면 <a href="http://www.windowsazure.com/ko-KR/manage/services/web-sites/digital-marketing-campaign-solution-overview">디지털 마케팅 캠페인</a>(영문) 및 <a href="http://www.windowsazure.com/ko-KR/manage/services/web-sites/business-application-solution-overview">비즈니스 응용 프로그램</a>(영문)의 다른 가이드도 참조하십시오.</p>
</div>

## <a name="createwebsite"></a>Azure 웹 사이트 만들기

Azure 관리 포털을 사용하여 여러 가지 방법으로 새 Azure 웹 사이트를 만들 수 있습니다. 포털 하단에 있는 **새로 만들기** 단추를 클릭하면 다음 대화 상자가 표시됩니다.

![GlobalWebCreate][]

새 웹 사이트를 만드는 세 가지 옵션, **빠른 생성**, **사용자 지정 만들기** 및 **갤러리에서**가 있습니다. 각 옵션을 사용할 때 대다수 사용자층과 부합하는 Azure 지역을 선택해야 합니다.

기존 사이트를 마이그레이션하는 경우 **사용자 지정 만들기** 옵션을 사용하여 SQL 데이터베이스 또는 MySQL 데이터베이스를 만들거나 연결할 수 있습니다. 이 옵션은 GitHub 또는 TFS(Team Foundation Server) 등 배포에 대해 여러 가지 소스 제어 옵션을 지정하는 기능도 제공합니다. 이미 소스 제어 메커니즘을 사용하여 웹 사이트를 관리하고 있다면 배포에 대해 Azure 웹 사이트를 설정하는 빠른 방법을 제공합니다.

**갤러리에서** 옵션을 사용하면 Drupal 또는 WordPress 같은 여러 프레임워크 중 하나를 사용하여 새 사이트를 설정할 수 있습니다. 이 옵션은 새 사이트를 빨리 설정한 다음 선택한 프레임워크 내에서 사용자 지정할 수 있습니다.

대부분의 Azure 서비스처럼 새 웹 사이트에 대해 Azure 지역을 선택해야 합니다. Azure에는 전 세계 여러 지역이 있습니다. 한 지역에 웹 사이트를 배포하면 인터넷을 통해 전 세계에서 이 웹 사이트에 액세스할 수 있습니다. 그러나 여러 지역에 배포할수록 유연성이 커집니다. 사용자와 가장 가까운 지역의 사이트에 배포하는 것이 좋습니다.

새 웹 사이트를 만드는 단계에 대한 자세한 내용은 [Azure 웹 사이트 및 ASP.NET 시작][]을 참조하세요.

## <a name="deploywebsite"></a>웹 사이트 배포

웹 사이트를 Azure에 배포하는 방법은 여러 가지입니다. 갤러리에서 프레임워크를 선택한 경우 이미 시작 사이트를 배포했습니다. 그러나 발전시키기 위해 몇몇 편집 유형 및 배포 절차를 설정해야 합니다. 몇 가지 배포 옵션은 다음과 같습니다.

-   FTP 클라이언트 사용
-   소스 제어에서 배포
-   Visual Studio에서 게시
-   [WebMatrix][](영문)에서 게시

이러한 옵션은 모두 다양한 장점이 있습니다. FTP 클라이언트에서 게시하는 기능은 새 파일을 사이트에 게시하는 간단하고 쉬운 방법입니다. 또한 FTP를 사용하는 기존 게시 도구 또는 프로세스를 Azure 웹 사이트와 계속 사용할 수 있습니다. 소스 제어는 필요한 경우 변경 내용을 추적하고 게시하며 이전 버전으로 롤백할 수 있기 때문에 사이트 콘텐츠 릴리스를 가장 잘 제어할 수 있습니다. Visual Studio 또는 Web Matrix에서 바로 게시하는 옵션은 두 도구를 사용하는 개발자에게 편리합니다. 이 기능은 프로젝트의 초기 단계나 프로토타입 생성 과정에서 유용합니다. 두 경우 모두 개발 환경에서 빈번한 게시 및 테스트가 더욱 편리합니다.

여기에 있는 많은 배포 작업은 Azure 관리 포털의 정보 사용과 관련이 있습니다. 웹 사이트로 이동하여 **대시보드** 탭을 선택한 다음 **간략 상태** 섹션을 찾으세요. 다음 스크린샷은 여러 가지 옵션을 보여 줍니다.

![GlobalWebQuickGlance][]

일부 소스 제어 도구 및 FTP 클라이언트는 사용자 이름/암호 액세스 권한이 필요합니다. 새 웹 사이트에 대해 자격 증명이 자동으로 만들어지지 않습니다. 그러나 **Reset your deployment credentials**을 클릭하여 쉽게 만들 수 있습니다. 작업을 완료하면, 같은 **대시보드** 페이지의 **FTP 호스트 이름**과 함께 이 자격 증명을 사용하여 FTP 클라이언트를 통해 웹 사이트를 배포할 수 있습니다.

![GlobalWebFTPSettings][]

배포/FTP 사용자 이름은 웹 사이트 이름과 제공한 사용자 이름의 조합입니다. 그러므로 사이트가 "<http://contoso.azurewebsite.net>"이고 사용자 이름이 "myuser"인 경우 배포 및 FTP에 대한 사용자 이름은 "contoso\\myuser"가 됩니다.

GitHub 또는 TFS Online 같은 소스 제어 관리 서비스를 통해 배포하도록 선택할 수도 있습니다. **소스 제어에서 배포 설정** 옵션을 클릭합니다. 그런 다음 소스 제어 시스템 또는 선택한 서비스의 지침을 따릅니다. 로컬 Git 리포지토리에서 게시에 대한 단계별 지침은 [소스 제어에서 Azure 웹 사이트로 게시][](영문)를 참조하십시오.

Visual Studio를 사용하여 사이트를 만들고 관리하려는 경우 Visual Studio에서 바로 게시하도록 선택할 수 있습니다. **Download the publish profile** 옵션을 클릭합니다. 그러면 웹 게시에 대해 Visual Studio로 가져올 수 있는 publishsetting 파일을 저장할 수 있습니다.

<div class="dev-callout">
<strong>참고</strong>
<p><i>publishsettings</i> 파일에는 두 배포에 대한 사용자 이름 및 암호와 연결된 데이터베이스 연결 문자열도 들어 있기 때문에 이 파일을 안전하게 보관하고 소스 제어 외부에 두는 것이 중요합니다.</p>
</div>

Visual Studio로 바로 구독 정보를 가져올 수도 있습니다. 예를 들어 Visual Studio에 있는 로컬 ASP.NET 프로젝트에 대해 생각해봅시다. 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. **웹 게시** 대화 상자의 **게시** 단추를 누르면 Azure 구독 설정이 들어 있는 파일이나 웹 사이트 대시보드에서 다운로드한 publishsettings 파일을 가져올 수 있습니다. 다음 스크린샷은 이러한 옵션을 보여 줍니다.

![GlobalWebVSPublish][]

Visual Studio에서 Azure에 게시하는 방법에 대한 자세한 내용은 Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포를 참조하세요.

개발 및 배포에 대한 또 한가지 옵션은 Azure 관리 포털의 WebMatrix입니다.

![GlobalWebWebMatrix][]

이 옵션에 대한 자세한 내용은 [Microsoft WebMatrix를 사용하여 웹 사이트 개발 및 배포][](영문)를 참조하십시오.

이러한 단계에 .COM 사이트를 배포하는 데 필요한 사항이 제공되지만 진행 중인 콘텐츠 게시 주기 관리에 대한 계획도 세워야 합니다. 이러한 계획의 범위는 사용자 지정 솔루션 롤링에서 드물게 변경하는 사이트에 대한 주기적인 재배포, 모든 기능을 갖춘 CMS(콘텐츠 관리 시스템)까지 이를 수 있습니다. 새 웹 사이트를 만드는 경우 [Drupal][] 또는 [Umbraco][]처럼 기존 CMS 프레임워크를 사용하는 갤러리 옵션이 있습니다.

## <a name="customdomain"></a>사용자 지정 도메인 추가

글로벌 웹 서비스의 경우 등록된 도메인 이름을 웹 사이트와 연결할 수 있습니다. 도메인 등록 서비스를 제공하는 타사 제공업체가 많이 있습니다. 각 제공업체는 도메인을 관리하기 위해 여러 가지 유형의 DNS 레코드 생성을 지원합니다. DNS 레코드는 "www.contoso.com"처럼 사용자에게 친숙한 URL을 이 사이트를 호스트하는 실제 URL 또는 IP 주소에 매핑하는 데 유용합니다.

<div class="dev-callout">
<strong>참고</strong>
<p>아래 설명에는 두 가지 DNS 레코드 유형이 있습니다. 첫 번째, CNAME 레코드는 &quot;www.contoso.com&quot; 같은 한 URL에서 &quot;contoso.azurewebsites.net&quot; 같은 다른 URL로 리디렉션할 수 있습니다. 두 번째, A 레코드는 &quot;www.contoso.com&quot; 같은 URL을 172.16.48.1 같은 IP 주소로 매핑할 수 있습니다.</p>
</div>

Azure 웹 사이트의 경우, Azure 웹 사이트에 대해 먼저 CNAME 레코드를 만들어야 합니다. 이 설정은 타사 등록 사이트를 통해 완료됩니다. 다음은 CNAME 레코드의 예입니다.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">유형</th>
   <th align="left" valign="top">호스트</th>
   <th align="left" valign="top">응답</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>CNAME</strong></td>
   <td valign="top">www.contoso.com</td>
   <td valign="top">contoso.azurewebsites.net</td>
   <td valign="top">8000</td>
</tr>
</table>

도메인이 새로 등록된 경우, 캐시된 DNS 항목을 작동하는 모든 DNS 서버에서 확인하는 데 하루 이상이 걸릴 수 있습니다. 그러나 도메인이 이미 있는 경우 CNAME 변경이 1분 이내에 발생되어야 합니다. CNAME 레코드는 Azure 웹 사이트 URL에 대한 도메인("www" 같은 하위 도메인 별칭으로 한정되어야 함) 간 매핑을 제공합니다. CNAME 레코드의 어느 쪽에도 "http://" 접두사가 포함되지 않습니다.

Azure 관리 포털에서 **공유** 또는 **크기 조정** 탭의 표준 모드에서 실행하고 있는지 확인합니다(사용자 지정 도메인은 **무료** 웹 사이트에 대해 지원되지 않음). 그런 다음 **구성** 탭으로 이동하여 **도메인 관리** 단추를 클릭합니다. 그러면 웹 사이트를 사용자 지정 도메인 이름과 연결할 수 있습니다.

![GlobalWebWebMatrix][]

사용자 지정 도메인을 목록에 넣기 전에 먼저 DNS 공급자로 이동하여 Azure 웹 사이트(contoso.azurewebsites.net)에 대한 URL을 가리키는 사용자 지정 도메인(www.contoso.com) 에 대해 CNAME 레코드를 만들어야 합니다. 이 전파 후 이전 스크린샷에 표시된 대화 상자에 사용자 지정 도메인을 입력할 수 있습니다. 이 웹 사이트를 가리키는 www.contoso.com 에 대한 CNAME 레코드는 이 웹 사이트에서 해당 도메인 이름을 사용할 권한이 있음을 보장합니다. 이때 대화 상자 하단에 있는 IP 주소를 사용하여 A 레코드를 만들 수 있습니다.

| 유형  | 호스트      | 응답        | TTL  |
|-------|-------------|-------------|------|
| **A** | contoso.com | 172.16.48.1 | 8000 |

자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성][](영문)을 참조하십시오.

## <a name="ssl"></a>SSL을 사용하여 웹 사이트 보안 유지

사이트에 읽기 전용 정보가 포함되는 경우 사이트에 대한 보안 액세스 권한을 제공할 필요가 없습니다. 그러나 사용자 정보를 수집하거나, 전자 상거래를 수행하거나, 기타 중요한 데이터를 관리하는 경우 사이트를 보호해야 합니다. 보안은 중요한 문제이며, 여기에서 모든 모범 사례 및 기술을 다룰 수는 없습니다. 그러나 웹 사이트에 대해 SSL(Secure Sockets Layer)을 활성화하는 프로세스를 강조하는 것은 중요합니다. SSL을 통해 사용자는 HTTP 대신 HTTPS 주소를 사용하여 암호화된 방법으로 사이트에 연결할 수 있습니다. Azure 웹 사이트에서 SSL을 사용해야 하는 특정 단계가 있습니다.

Azure 웹 사이트는 실제 사이트 URL에 대해 자동으로 보안 연결을 제공합니다. 예를 들어 사이트가 <http://contoso.azurewebsites.net>인 경우, **https**://contoso.azurewebsites.net처럼 "http"를 "https"로 변경함으로써 SSL을 통해 연결할 수 있습니다.

그러나 사용자 지정 도메인 이름을 사용하는 경우, 웹 사이트에 대해 Azure 관리 포털을 통해 인증서를 업로드하고 SSL을 활성화하는 단계를 수행해야 합니다. 다음 단계는 이 프로세스에 대한 요약이지만 [Azure 웹 사이트에 대해 SSL 인증서 구성][](영문)에서 자세한 지침을 볼 수 있습니다.

먼저 인증 기관에서 SSL 인증서를 받습니다. 여러 하위 도메인과 함께 도메인을 보호하려는 경우(예: www.contoso.com 및 staging.contoso.com) 와일드카드 인증서(\*.contoso.com)를 받아야 합니다. 와일드카드 인증서는 비용이 더 많이 들 수 있기 때문에 이 인증서 유형의 유연성이 비용에 합당한지 결정해야 합니다.

인증 기관에서 인증서를 받은 후 같은 형식으로 Azure에 단순히 업로드할 수 없습니다. openssl 명령을 사용하여 .pfx 파일을 생성해야 합니다. openssl 명령은 OpenSSL Project의 일부입니다. 소스는 [OpenSSL 웹 사이트][](영문)에 배포되지만, 일반적으로 인터넷에서 미리 컴파일된 버전의 도구를 찾을 수 있습니다. 다음 예에서는 인증서(myserver.crt) 및 개인 키 파일(myserver.key)이 .pfx 파일을 생성하는 데 사용됩니다.

    openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

인증서를 Azure에 업로드하려면 먼저 **크기 조정** 탭으로 이동한 다음 **표준** 모드에서 실행 중인지 확인합니다. 사용자 도메인에 대한 SSL은 **무료** 또는 **공유** 모드에 대해 지원되지 않습니다. **구성** 탭에서 **인증서 업로드** 단추를 클릭합니다.

![GlobalWebUplodateCert][]

그런 다음 **ssl 바인딩** 섹션에서 보호하는 도메인 이름으로 인증서를 매핑합니다. 이 매핑에는 SNI SSL과 IP 기반 SSL의 두 가지 옵션이 있습니다.

![GlobalWebSSLBindings][]

**IP 기반 SSL** 옵션은 공용 전용 IP 주소를 도메인 이름에 매핑하는 일반적인 방법입니다. 이 방법은 모든 브라우저에서 작동합니다. **SNI SSL** 옵션을 사용하면 여러 도메인이 같은 IP 주소를 공유하지만 각 도메인에 대해 연결된 SSL 인증서는 다릅니다. SNI SSL은 일부 이전 브라우저에서는 작동하지 않습니다(호환성에 대한 자세한 내용은 [SNI SSL에 대한 Wikipedia 항목][] 참조). 각 SSL 인증서와 연결된 월별 요금(시간당 계산됨)이 있으며, 가격은 IP 기반 또는 SNI SSL 선택에 따라 다릅니다. 가격 정보는 [웹 사이트 가격 정보][](영문)를 참조하십시오. 이 프로세스에 대한 자세한 내용은 [Azure 웹 사이트에 대한 SSL 인증서 구성][Azure 웹 사이트에 대해 SSL 인증서 구성](영문)을 참조하십시오.

## <a name="monitor"></a>사이트 모니터링

사이트가 사용자 요청을 활발히 처리한 후 모니터링을 사용하는 것이 중요합니다. 예를 들어 사용자 부하로 인해 사이트 확장 필요성을 나타내는 CPU 시간이 증가하는지 여부를 알고 싶을 수 있습니다. 또는 응용 프로그램 비효율성으로 인해 응답 시간이 증가하거나 오류가 발생할 수 있습니다. 이 섹션에서는 Azure 관리 포털에서 기본 제공되는 모니터링 기능에 대해 다룹니다.

**모니터** 탭에는 웹 사이트에 대한 몇 가지 주요 메트릭이 그래프 형식으로 들어 있습니다.

![GlobalWebMonitor1][]

메트릭 추가 단추를 사용하여 이 그래프의 메트릭을 사용자 지정할 수 있습니다.

![GlobalWebMonitor2][]

또한 **표준** 모드에서 실행되는 사이트의 경우, 끝점 모니터링 및 알림을 사용할 수 있습니다. **구성** 탭에서 **모니터링** 섹션으로 이동한 다음 끝점을 구성합니다. 이 끝점은 지정하는 하나 이상의 위치에서 실행되며 웹 사이트에 주기적으로 액세스를 시도합니다. 타이밍 및 오류 정보가 모두 수집됩니다.

**모니터** 탭에서 이 끝점은 응답 시간을 보여 주며 표시됩니다. 끝점 메트릭을 선택하는 경우, **규칙 추가** 아이콘을 클릭하여 경고 규칙을 추가할 수 있습니다.

![GlobalWebMonitor3][]

규칙은 응답 시간이 지정된 임계값을 초과하면 관리자 또는 다른 개인에게 전자 메일을 보낼 수 있습니다.

![GlobalWebMonitor4][]

크기 조정이 필요한 사이트를 발견하는 경우 **크기 조정** 탭에서 수동으로 또는 크기 자동 조정 미리 보기를 통해 크기를 조정할 수 있습니다. 크기 조정 탭에서 수직 확장(대규모 전용 컴퓨터) 또는 수평 확장(추가 공유 인스턴스 또는 같은 크기의 전용 인스턴스)을 선택할 수 있습니다. 그러나 크기 자동 조정 미리 보기는 수평 확장만 지원합니다. 이 옵션에 대한 자세한 내용은 [디지털 마케팅 캠페인][1] 시나리오의 "사용자 요구에 따라 확장"을 참조하세요. 웹 사이트 모니터링에 대한 자세한 내용은 [웹 사이트를 모니터링하는 방법][](영문)을 참조하십시오.

## <a name="summary"></a>요약

조직의 사이트(.COM)를 만들기 위한 표준 작업에는 개발 프레임워크 선택, 사이트 생성, 배포, 사용자 지정 도메인 할당 및 모니터링이 포함됩니다. 사용자 데이터를 보호해야 하는 사이트의 경우 SSL을 사용하는 것이 좋습니다. 이 문서에서는 Azure 웹 사이트를 사용하여 이러한 작업을 수행하는 방법에 대한 개요를 제공합니다. 자세한 내용은 다음 기술 문서를 참조하십시오.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">영역</th>
   <th align="left" valign="top">리소스 </th>
</tr>
<tr>
   <td valign="middle"><strong>계획</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ko-KR/manage/services/web-sites/choose-web-app-service">Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우(영문)</a></td>
</tr>
<tr>
   <td valign="middle"><strong>생성</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/ko-KR/documentation/articles/web-sites-dotnet-get-started/">Azure 웹 사이트 및 ASP.NET 시작</a></td>
</tr>
<tr>
   <td valign="middle"><strong>배포</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ko-KR/develop/net/common-tasks/publishing-with-git/">소스 제어에서 Azure 웹 사이트로 게시</a><br/>- <a href="http://www.windowsazure.com/ko-KR/develop/net/tutorials/get-started/">Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포</a><br/>- <a href="http://www.windowsazure.com/ko-KR/develop/net/tutorials/website-with-webmatrix/">Microsoft WebMatrix를 사용하여 웹 사이트 개발 및 배포</a></td>
</tr>
<tr>
   <td valign="middle"><strong>사용자 지정 도메인</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ko-KR/develop/net/common-tasks/custom-dns-web-site/">Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성</a></td>
</tr>
<tr>
   <td valign="middle"><strong>SSL</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ko-KR/develop/net/common-tasks/enable-ssl-web-site/">Azure 웹 사이트에 대해 SSL 인증서 구성</a></td>
</tr>
<tr>
   <td valign="middle"><strong>모니터</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ko-KR/manage/services/web-sites/how-to-monitor-websites/">웹 사이트를 모니터링하는 방법</a></td>
</tr>
</table>
  [Azure 웹 사이트]: /ko-KR/documentation/services/web-sites/
  [Azure 클라우드 서비스]: /ko-KR/documentation/services/cloud-services/
  [Azure 가상 컴퓨터]: /ko-KR/documentation/services/virtual-machines/
  [Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우]: /ko-KR/manage/services/web-sites/choose-web-app-service
  [Azure 웹 사이트 만들기]: #createwebsite
  [웹 사이트 배포]: #deploywebsite
  [사용자 지정 도메인 추가]: #customdomain
  [SSL을 사용하여 웹 사이트 보안 유지]: #ssl
  [사이트 모니터링]: #monitor
  [디지털 마케팅 캠페인]: http://www.windowsazure.com/ko-KR/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [비즈니스 응용 프로그램]: http://www.windowsazure.com/ko-KR/manage/services/web-sites/business-application-solution-overview
  [GlobalWebCreate]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Create.png
  [Azure 웹 사이트 및 ASP.NET 시작]: /ko-KR/documentation/articles/web-sites-dotnet-get-started
  [WebMatrix]: http://www.microsoft.com/web/webmatrix/
  [GlobalWebQuickGlance]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_QuickGlance.png
  [GlobalWebFTPSettings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_FTPSettings.png
  [소스 제어에서 Azure 웹 사이트로 게시]: /ko-KR/develop/net/common-tasks/publishing-with-git/
  [GlobalWebVSPublish]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_VS_Publish.png
  [GlobalWebWebMatrix]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_WebMatrix.png
  [Microsoft WebMatrix를 사용하여 웹 사이트 개발 및 배포]: /ko-KR/develop/net/tutorials/get-started/
  [Drupal]: https://drupal.org/
  [Umbraco]: http://umbraco.com/
  [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성]: /ko-KR/develop/net/common-tasks/custom-dns-web-site/
  [Azure 웹 사이트에 대해 SSL 인증서 구성]: /ko-KR/develop/net/common-tasks/enable-ssl-web-site/
  [OpenSSL 웹 사이트]: http://www.openssl.org/
  [GlobalWebUplodateCert]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Uplodate_Cert.png
  [GlobalWebSSLBindings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_SSL_Bindings.png
  [SNI SSL에 대한 Wikipedia 항목]: http://en.wikipedia.org/wiki/Server_Name_Indication
  [웹 사이트 가격 정보]: /ko-KR/pricing/details/web-sites/#service-ssl
  [GlobalWebMonitor1]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor1.png
  [GlobalWebMonitor2]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor2.png
  [GlobalWebMonitor3]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor3.png
  [GlobalWebMonitor4]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor4.png
  [1]: /ko-KR/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [웹 사이트를 모니터링하는 방법]: /ko-KR/manage/services/web-sites/how-to-monitor-websites/
  [2]: http://www.windowsazure.com/ko-KR/manage/services/web-sites/choose-web-app-service
  [3]: http://azure.microsoft.com/ko-KR/documentation/articles/web-sites-dotnet-get-started/
  [4]: http://www.windowsazure.com/ko-KR/develop/net/common-tasks/publishing-with-git/
  [Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포]: http://www.windowsazure.com/ko-KR/develop/net/tutorials/get-started/
  [5]: http://www.windowsazure.com/ko-KR/develop/net/tutorials/website-with-webmatrix/
  [6]: http://www.windowsazure.com/ko-KR/develop/net/common-tasks/custom-dns-web-site/
  [7]: http://www.windowsazure.com/ko-KR/develop/net/common-tasks/enable-ssl-web-site/
  [8]: http://www.windowsazure.com/ko-KR/manage/services/web-sites/how-to-monitor-websites/
