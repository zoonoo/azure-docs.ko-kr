<properties urlDisplayName="Create a Line-of-Business Application on Azure Websites" pageTitle="Azure 웹 사이트에서 LOB(기간 업무) 응용 프로그램 만들기" metaKeywords="Web Sites" description="This guide provides a technical overview of how to use Azure Websites to create intranet, line-of-business applications. This includes authentication strategies, service bus relay, and monitoring." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="wpickett" title="Create a Line-of-Business Application on Azure Websites" authors="jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/01/2014" ms.author="jroth" />



# Azure 웹 사이트에서 LOB(기간 업무) 응용 프로그램 만들기

이 가이드는 Azure 웹 사이트에서 LOB(기간 업무) 응용 프로그램을 만드는 방법에 대한 기술적 개요를 제공합니다. 이 문서의 목적에 따라, 이러한 응용 프로그램은 내부 비즈니스 사용을 위해 보안 기능을 갖춘 인트라넷 응용 프로그램인 것으로 가정합니다. 비즈니스 응용 프로그램에는 두 가지 고유한 특성이 있습니다. 이러한 응용 프로그램에는 대개 회사 디렉터리에 대한 인증이 필요합니다. 또한 일반적으로 온-프레미스 데이터 및 서비스에 대한 액세스 또는 통합이 필요합니다. 이 가이드는 [Azure 웹 사이트][websitesoverview]에서 비즈니스 응용 프로그램을 빌드하는 데 초점을 맞춥니다. 그러나 [Azure 클라우드 서비스][csoverview] 또는 [Azure 가상 컴퓨터][vmoverview]가 요구 사항에 더 적합한 상황도 있습니다. 이러한 옵션 간의 차이점을 검토하는 것이 중요합니다. 자세한 내용은[Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우][chooseservice]를 참조하세요. 

이 가이드에서는 다음 영역에 대해 설명합니다.

- [이점 고려](#benefits)
- [인증 전략 선택](#authentication)
- [인증을 지원하는 Azure 웹 사이트 만들기](#createintranetsite)
- [서비스 버스를 사용하여 온-프레미스 리소스와 통합](#servicebusrelay)
- [응용 프로그램 모니터링](#monitor)

<div class="dev-callout">
<strong>참고</strong>
<p>이 가이드에서는 공용 .COM 사이트 개발에 맞는 가장 일반적인 몇 가지 영역과 작업에 대해 설명합니다. 그러나 특정 구현에서 사용할 수 있는 Azure 웹 사이트의 다른 기능이 있습니다. 이러한 기능을 검토하려면 <a href="http://www.windowsazure.com/ko-kr/manage/services/web-sites/global-web-presence-solution-overview/">글로벌 웹 서비스</a> 및 <a href="http://www.windowsazure.com/ko-kr/manage/services/web-sites/digital-marketing-campaign-solution-overview">디지털 마케팅 캠페인</a>에 대한 기타 가이드를 참조하세요.</p>
</div>

##<a name="benefits"></a>이점 고려
LOB(기간 업무) 응용 프로그램은 대개 회사 사용자를 대상으로 하기 때문에 클라우드 대 온-프레미스 회사 리소스 및 인프라를 사용하는 이유를 고려해야 합니다. 먼저, 작업이 동적으로 변경됨에 따라 확장 및 축소하는 기능 등 클라우드로 이전하는 데 따른 일반적인 몇 가지 이점이 있습니다. 예를 들면 연간 성과 검토를 처리하는 응용 프로그램을 생각해볼 수 있습니다. 이 유형의 응용 프로그램은 일 년 중 대부분의 기간에 트래픽이 거의 없습니다. 그러나 대기업의 경우 검토 기간에는 트래픽이 급격히 상승합니다. Azure는 일 년 중 트래픽이 높은 검토 기간에는 로드 처리를 위해 확장하고 나머지 기간에는 다시 축소하여 비용을 절약할 수 있는 확장 옵션을 제공합니다. 클라우드의 또 다른 이점은 인프라를 취득하고 관리하는 일보다 응용 프로그램에 좀 더 초점을 맞출 수 있다는 것입니다.

이러한 표준 장점 외에도, 비즈니스 응용 프로그램을 클라우드에 두면 직원과 파트너가 어디에서나 응용 프로그램을 사용하도록 더 잘 지원할 수 있습니다. 사용자는 응용 프로그램을 사용하기 위해 회사 네트워크에 연결할 필요가 없으며, IT 그룹은 복잡한 역방향 프록시 솔루션을 마련할 필요가 없습니다. 회사 응용 프로그램에 대한 액세스를 보호하기 위한 몇 가지 인증 옵션이 있습니다. 이 가이드의 다음 섹션에서 이러한 옵션에 대해 설명합니다.

##<a name="authentication"></a>인증 전략 선택
비즈니스 응용 프로그램 시나리오에서는 인증 전략이 가장 중요한 결정 사항 중 하나입니다. 다음과 같은 몇 가지 옵션이 있습니다.

- [Azure Active Directory 서비스][adoverview]를 사용합니다. 독립형 디렉터리로 사용할 수도 있고 온-프레미스 Active Directory와 동기화할 수도 있습니다. 그러면 응용 프로그램이 Azure Active Directory와 조작하여 사용자를 인증합니다. 이 접근 방식의 개요는 [Azure Active Directory 사용][adusing]을 참조하세요.
- Azure 가상 컴퓨터 및 가상 네트워크를 사용하여 Active Directory를 설치합니다. 이 옵션을 사용하면 온-프레미스 Active Directory를 클라우드에 확장 설치할 수 있습니다. ADFS(Active Directory Federation Services)를 사용하여 ID 요청을 온-프레미스 AD로 다시 페더레이션하는 방법을 선택할 수도 있습니다. 그러면 Azure 응용 프로그램의 인증은 ADFS를 통해 온-프레미스 Active Directory로 전달됩니다. 이 접근 방식에 대한 자세한 내용은 [VM에서 Windows Server Active Directory 실행][adwithvm] 및 [Azure 가상 컴퓨터에 Windows Server Active Directory 배포에 대한 지침][addeployguidelines]을 참조하세요. 
- 사용자를 인증하는 데 여러 ID 서비스를 사용하기 위해 [Azure ACS(액세스 제어 서비스)][acs2]와 같은 중간 서비스를 이용합니다. 이 옵션은 Active Directory 또는 다른 ID 공급자를 통해 인증하기 위한 추상화를 제공합니다. 자세한 내용은 [Azure Active Directory 액세스 제어 사용][acsusing]을 참조하세요.

이 비즈니스 응용 프로그램 시나리오에서는 첫 번째 Azure Active Directory 사용 시나리오가 응용 프로그램의 인증 전략을 구현하기 위한 가장 빠른 경로를 제공합니다. 이 가이드의 나머지 부분은 Azure Active Directory에 초점을 맞춥니다. 그러나 각자의 비즈니스 응용 프로그램에 따라 나머지 두 솔루션 중 하나가 더 적합할 수도 있습니다. 예를 들어, ID 정보를 클라우드에 동기화하는 기능이 허용되지 않는 경우 ADFS 솔루션이 더 나은 옵션일 수 있습니다. 또는 다른 ID 공급자(예: Facebook)를 지원해야 하는 경우 ACS 솔루션이 더 적합할 수 있습니다.

새로운 Azure Active Directory 설치를 시작하기 전에, Office 365나 Windows Intune 등의 기존 서비스에서 Azure Active Directory를 이미 사용하는지 확인해야 합니다. 사용하는 경우 기존 구독을 Azure 구독과 연결해야 합니다. 자세한 내용은 [Azure AD 테넌트란?][adtenant]을 참조하세요.

Azure Active Directory를 이미 사용하는 서비스가 현재 없는 경우 관리 포털에서 새 디렉터리를 만들 수 있습니다. 관리 포털의 **Active Directory** 섹션을 사용하여 새 디렉터리를 만드세요.

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

디렉터리를 만들면 사용자, 통합 응용 프로그램 및 도메인을 만들고 관리하기 위한 옵션이 제공됩니다.

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

이러한 기본 단계의 전체 안내를 보려면 [Azure AD를 사용하여 웹 응용 프로그램에 Sign-On 추가][adsso](영문)를 참조하세요. 이 새로운 디렉터리를 독립형 리소스로 사용하는 경우 다음 단계는 디렉터리와 통합할 응용 프로그램을 개발하는 것입니다. 그러나 온-프레미스 Active Directory ID가 있는 경우 일반적으로 새로운 Azure Active Directory의 ID와 동기화하게 됩니다. 동기화 방법에 대한 자세한 내용은 [디렉터리 통합][dirintegration]을 참조하세요.

디렉터리가 생성되고 채워지면 인증을 요구하는 웹 응용 프로그램을 만든 다음 디렉터리와 통합해야 합니다. 다음 두 섹션에서는 이러한 단계에 대해 설명합니다.

##<a name="createintranetsite"></a>인증을 지원하는 Azure 웹 사이트 만들기
글로벌 웹 서비스 시나리오에서는 새로운 웹 사이트를 만들고 배포하기 위한 다양한 옵션에 대해 설명했습니다. Azure 웹 사이트에 익숙하지 않은 사용자는 [이러한 정보를 검토][scenarioglobalweb]해 보는 것이 좋습니다. Windows 인증을 사용하는 인트라넷 웹 응용 프로그램에는 일반적으로 Visual Studio의 ASP.NET 응용 프로그램을 선택합니다. 그 이유 중 하나는 ASP.NET 및 Visual Studio에서 이 시나리오에 제공하는 긴밀한 통합과 지원입니다.

예를 들면, Visual Studio에서 ASP.NET MVC 4 프로젝트를 만들 때 프로젝트 만들기 대화 상자에 **인트라넷 응용 프로그램**을 만드는 옵션이 제공됩니다.

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

이렇게 하면 Windows 인증을 지원하도록 프로젝트 설정이 변경됩니다. 특히 web.config 파일에 있는 **authentication** 요소의 **mode** 특성이 **Windows**로 설정됩니다. 다른 ASP.NET 프로젝트(예: Web Forms 프로젝트)를 만들거나 기존 프로젝트로 작업하는 경우 수동으로 이와 같이 변경해야 합니다.

MVC 프로젝트에서는 프로젝트 속성 창에서 다음의 값 2개도 변경해야 합니다. **Windows 인증**을 **사용**으로 설정하고 **익명 인증**을 **사용 안 함**으로 설정합니다.

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

Azure Active Directory를 통해 인증하려면 이 응용 프로그램을 디렉터리에 등록한 다음 연결을 위해 응용 프로그램 구성을 수정해야 합니다. Visual Studio에서 두 가지 방법으로 수행할 수 있습니다.

- [Visual Studio용 ID 및 액세스 도구](#identityandaccessforvs)
- [Azure Active Directory용 Microsoft ASP.NET 도구](#aspnettoolsforwaad)

###<a name="identityandaccessforvs"></a>Visual Studio용 ID 및 액세스 도구:
한 가지 방법은 [ID 및 액세스 도구][identityandaccess](다운로드 및 설치 가능)를 사용하는 것입니다. 프로젝트의 상황에 맞는 메뉴에서 이 도구를 Visual Studio와 통합할 수 있습니다. 다음 지침 및 스크린 샷은 Visual Studio 2012에서 가져온 것입니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **ID 및 액세스**를 선택합니다. 세 가지 옵션을 구성해야 합니다. **공급자** 탭에서 **STS 메타데이터 문서에 대한 경로** 및 **APP ID URI**를 제공해야 합니다(이러한 값을 얻는 방법은 [Azure Active Directory에 응용 프로그램 등록](#registerwaadapp)의 해당 섹션 참조).

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

**ID 및 액세스** 대화 상자의 **구성** 탭에서 마지막 구성 항목을 변경해야 합니다. **웹 팜 쿠키 사용** 확인란을 선택해야 합니다. 이러한 기본 단계의 자세한 안내를 보려면 [Azure AD를 사용하여 웹 응용 프로그램에 Sign-On 추가][adsso](영문)를 참조하세요.

####<a name="registerwaadapp"></a>Azure Active Directory에 응용 프로그램 등록:
**공급자** 탭의 필드를 작성하려면 Azure Active Directory에 응용 프로그램을 등록해야 합니다. Azure 관리 포털의 **Active Directory** 섹션에서 해당 디렉터리를 선택하고 **응용 프로그램** 탭으로 이동합니다. 여기에서는 URL을 기준으로 Azure 웹 사이트를 추가할 수 있습니다. 이러한 단계를 진행할 때 먼저 URL을 Visual Studio에서 로컬 디버깅을 위해 제공하는 localhost 주소로 설정하게 됩니다. 나중에 배포할 때 이 주소를 웹 사이트의 실제 URL로 변경해야 합니다.

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

웹 사이트 추가가 완료되면, 포털에서 STS 메타데이터 문서 경로(**페더레이션 메타데이터 문서 URL**)와 **APP ID URI**를 제공합니다. 이러한 값은 Visual Studio **ID 및 액세스** 대화 상자의 **공급자** 탭에서 사용됩니다. 

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

###<a name="aspnettoolsforwaad"></a>Azure Active Directory용 Microsoft ASP.NET 도구:
이전 단계를 수행하기 위한 대체 방법은 [Azure Active Directory용 Microsoft ASP.NET 도구][aspnettools]를 사용하는 것입니다. 이 도구를 사용하려면 Visual Studio의 **프로젝트** 메뉴에서 **Azure 인증 사용** 항목을 클릭합니다. 그러면 Azure Active Directory 도메인의 주소(응용 프로그램의 URL이 아님)를 묻는 간단한 대화 상자가 나타납니다.

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

해당 Active Directory 도메인의 관리자인 사용자는 **Azure AD에서 이 응용 프로그램 프로비전** 확인란을 선택합니다. 이렇게 하면 Active Directory에 응용 프로그램이 등록됩니다. 관리자가 아닌 사용자는 확인란의 선택을 취소하고 표시되는 정보를 관리자에게 제공합니다. 관리자는 ID 및 액세스 도구에 대한 이전 단계를 사용하여 관리 포털에서 통합 응용 프로그램을 만들 수 있습니다. Azure Active Directory용 ASP.NET 도구를 사용하는 방법에 대한 자세한 내용은 [Azure 인증][azureauthtutorial](영문)을 참조하세요.

LOB(기간 업무) 응용 프로그램을 관리할 때에는 배포용 소스 코드 제어 시스템 중 지원되는 것은 무엇이든 사용할 수 있습니다. 그러나 이 시나리오에서는 Visual Studio 통합의 수준이 높기 때문에 소스 제어 시스템으로 TFS(Team Foundation Service)를 선택하는 것이 좋습니다. Azure 웹 사이트는 TFS와의 통합 기능을 제공합니다. 관리 포털에서 웹 사이트의 **대시보드** 탭으로 이동합니다. 그런 후 **소스 제어에서 배포 설정**을 선택합니다. TFS 사용을 위한 지침을 따릅니다. 

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

##<a name="servicebusrelay"></a>서비스 버스를 사용하여 온-프레미스 리소스와 통합
LOB(기간 업무) 응용 프로그램의 다수는 온-프레미스 데이터 및 서비스와 통합해야 합니다. 특정 데이터 유형을 클라우드로 이동할 수 없는 데에는 여러 가지 이유가 있습니다. 실무적인 이유이거나 규제상의 이유일 수 있습니다. 어떤 데이터를 Azure에 호스트하고 어떤 데이터를 온-프레미스에 둘지를 결정하는 계획 단계라면 [Azure 보안 센터][trustcenter]의 리소스를 검토하는 것이 중요합니다. 하이브리드 웹 응용 프로그램은 Azure에서 실행되며 온-프레미스에 남아 있는 리소스에 액세스합니다.

가상 컴퓨터 또는 클라우드 서비스를 사용할 때에는 가상 네트워크를 사용하여 Azure의 응용 프로그램을 회사 네트워크와 연결할 수 있습니다. 그러나 웹 사이트는 가상 네트워크를 지원하지 않으므로, 이런 유형의 웹 사이트 통합을 수행하기 위한 가장 좋은 방법은 [Azure 서비스 버스 릴레이 서비스][sbrelay]를 사용하는 것입니다. 서비스 버스 릴레이 서비스를 사용하면 클라우드의 응용 프로그램을 회사 네트워크에서 실행 중인 WCF 서비스에 안전하게 연결할 수 있습니다. 서비스 버스를 사용하면 방화벽 포트를 열지 않고도 이러한 통신이 가능합니다. 

아래의 다이어그램에서는 클라우드 응용 프로그램과 온-프레미스 WCF 서비스 모두 앞서 만든 네임스페이스를 통해 서비스 버스와 통신합니다. 온-프레미스 WCF 서비스는 클라우드로 이동할 수 없는 내부 데이터 및 서비스에 액세스합니다. WCF 서비스는 네임스페이스에 끝점을 등록합니다. Azure에서 실행 중인 웹 사이트는 서비스 버스의 이 끝점에도 연결됩니다. 이 단계를 완료하려면 발신 공개 HTTP 요청만 수행할 수 있어야 합니다.

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

이제 서비스 버스는 클라우드 응용 프로그램을 온-프레미스 WCF 서비스에 연결합니다. 그러면 Azure와 온-프레미스에서 모두 서비스와 리소스를 사용하는 하이브리드 응용 프로그램을 만들기 위한 기본 아키텍처가 제공됩니다. 자세한 내용은 [서비스 버스 릴레이 서비스 사용 방법][sbrelayhowto] 및 [서비스 버스 릴레이된 메시징 자습서][sbrelaytutorial](영문)를 참조하세요. 이 기술을 설명하는 샘플은 [Enterprise Pizza - 서비스 버스를 사용하여 웹 사이트를 온-프레미스에 연결][enterprisepizza](영문)을 참조하세요.

##<a name="monitor"></a>응용 프로그램 모니터링
비즈니스 응용 프로그램은 확장과 모니터링 등 표준 웹 사이트 기능의 이점을 누릴 수 있습니다. 특정 요일이나 시간 중에 부하가 몰리는 비즈니스 응용 프로그램의 경우 자동 크기 조정(미리 보기) 기능을 통해 사이트를 확장하고 다시 되돌려 리소스를 효율적으로 사용할 수 있습니다. 모니터링 옵션에는 끝점 모니터링과 할당량 모니터링이 포함됩니다. 이 모든 시나리오는 [글로벌 웹 서비스][scenarioglobalweb] 및 [디지털 마케팅 캠페인][scenariodigitalmarketing] 시나리오에서 자세히 다루었습니다.

모니터링 요구 사항은 비즈니스의 중요도 수준이 다른 LOB(기간 업무) 응용 프로그램 간에 서로 다를 수 있습니다. 업무의 중요도가 높은 응용 프로그램에는 타사 모니터링 솔루션(예: [New Relic][newrelic])을 사용하는 방안을 고려해 보세요.

LOB(기간 업무) 응용 프로그램은 일반적으로 IT 직원이 관리합니다. 예기치 못한 오류나 동작이 발생한 경우 IT 담당자는 좀 더 자세한 로깅을 사용하도록 설정한 다음 결과 데이터를 분석하여 문제를 파악할 수 있습니다. 관리 포털에서 **구성** 탭으로 이동한 다음 **응용 프로그램 진단** 및 **사이트 진단** 섹션의 옵션을 검토하세요. 

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

웹 사이트의 문제를 해결하는 데 다양한 응용 프로그램 및 사이트 로그를 사용할 수 있습니다. 일부 옵션은 사이트의 파일 시스템에 로그 파일을 두는 **파일 시스템**을 지정합니다. FTP, Azure PowerShell 또는 Azure 명령줄 도구를 통해 여기에 액세스할 수 있습니다. 다른 옵션은 **저장소**를 지정합니다. 그러면 지정하는 Azure 저장소 계정으로 정보가 전송됩니다. **웹 서버 로깅**의 경우, 파일 시스템에 대한 디스크 할당량 또는 저장소 옵션에 대한 보존 정책을 지정하는 옵션도 제공됩니다. 이 옵션을 사용하면 저장된 로깅 데이터가 무한정 커지는 것을 막을 수 있습니다.

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

로깅 설정에 대한 자세한 내용은 [방법: 진단 구성 및 웹 사이트의 로그 다운로드][configurediagnostics]를 참조하세요.

FTP 또는 저장소 유틸리티(예: Azure 저장소 탐색기)를 통해 원시 로그를 보는 것 외에 Visual Studio에서도 로그 정보를 볼 수 있습니다. 문제 해결 시나리오에서 이러한 로그를 사용하는 방법에 대한 자세한 내용은 [Visual Studio에서 Azure 웹 사이트 문제 해결][troubleshootwebsites]을 참조하세요.

##<a name="summary"></a>요약
Azure를 사용하면 보안 인트라넷 응용 프로그램을 클라우드에 호스트할 수 있습니다. Azure Active Directory는 조직의 구성원만 응용 프로그램에 액세스할 수 있도록 사용자를 인증하는 기능을 제공합니다. 서비스 버스 릴레이 서비스는 웹 응용 프로그램이 온-프레미스 서비스 및 데이터와 통신할 수 있는 메커니즘을 제공합니다. 이 하이브리드 응용 프로그램 접근 방식을 사용하면 모든 종속 데이터 및 서비스를 함께 마이그레이션하지 않고도 비즈니스 응용 프로그램을 손쉽게 클라우드에 게시할 수 있습니다. 비즈니스 응용 프로그램을 배포한 후에는 Azure 웹 사이트에서 제공하는 표준 확장 및 모니터링 기능을 활용할 수 있습니다. 자세한 내용은 다음 기술 문서를 참조하세요.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">영역</th>
   <th align="left" valign="top">리소스</th>
</tr>
<tr>
   <td valign="middle"><strong>계획</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ko-kr/manage/services/web-sites/choose-web-app-service">Azure 웹 사이트, 클라우드 서비스 및 VM: 각 항목을 사용해야 하는 경우(영문)</a></td>
</tr>
<tr>
   <td valign="middle"><strong>만들기 및 배포</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ko-kr/develop/net/tutorials/get-started/">Azure 웹 사이트에 ASP.NET 웹 응용 프로그램 배포</a><br/>- <a href="http://www.windowsazure.com/ko-kr/develop/net/tutorials/web-site-with-sql-database/">Azure에 보안 ASP.NET MVC 응용 프로그램 배포</a></td>
</tr>
<tr>
   <td valign="middle"><strong>인증</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ko-kr/manage/windows/fundamentals/identity/">Azure ID 옵션 이해</a><br/>- <a href="http://www.windowsazure.com/ko-kr/documentation/services/active-directory/">Azure Active Directory 서비스</a><br/>- <a href="http://technet.microsoft.com/ko-kr/library/jj573650.aspx">Azure AD 테넌트란?</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Azure AD를 사용하여 웹 응용 프로그램에 로그온 기능 추가</a><br/>- <a href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Azure 인증 자습서(영문)</a></td>
</tr>
<tr>
   <td valign="middle"><strong>서비스 버스 릴레이</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/ko-kr/develop/net/how-to-guides/service-bus-relay/">서비스 버스 릴레이 서비스를 사용하는 방법</a><br/>- <a href="http://msdn.microsoft.com/ko-kr/library/windowsazure/ee706736.aspx">서비스 버스 릴레이된 메시징 자습서</a></td>
</tr>
<tr>
   <td valign="middle"><strong>모니터</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/ko-kr/manage/services/web-sites/how-to-monitor-websites/">웹 사이트를 모니터링하는 방법</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">방법: Azure에서 경고 알림 받기 및 경고 규칙 관리(영문)</a><br/>- <a href="http://www.windowsazure.com/ko-kr/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">방법: 진단 구성 및 웹 사이트의 로그 다운로드</a><br/>- <a href="http://www.windowsazure.com/ko-kr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">Visual Studio에서 Azure 웹 사이트 문제 해결</a></td>
</tr>
</table>

  [websitesoverview]:/ko-kr/documentation/services/web-sites/
  [csoverview]:/ko-kr/documentation/services/cloud-services/
  [vmoverview]:/ko-kr/documentation/services/virtual-machines/
  [chooseservice]:/ko-kr/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/ko-kr/manage/services/web-sites/global-web-presence-solution-overview/
  [scenariodigitalmarketing]:/ko-kr/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [adoverview]:/ko-kr/documentation/services/active-directory/
  [adusing]:/ko-kr/manage/windows/fundamentals/identity/#ad
  [adwithvm]:/ko-kr/manage/windows/fundamentals/identity/#adinvm
  [addeployguidelines]:http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156090.aspx
  [acs2]:http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [acsusing]:/ko-kr/manage/windows/fundamentals/identity/#ac
  [adtenant]:http://technet.microsoft.com/ko-kr/library/jj573650.aspx
  [adsso]:http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [dirintegration]:http://technet.microsoft.com/ko-kr/library/jj573653.aspx
  [identityandaccess]:http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [aspnettools]:http://go.microsoft.com/fwlink/?LinkID=282306
  [azureauthtutorial]:http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [trustcenter]:/ko-kr/support/trust-center/
  [sbrelay]:http://msdn.microsoft.com/ko-kr/library/windowsazure/jj860549.aspx
  [sbrelayhowto]:/ko-kr/develop/net/how-to-guides/service-bus-relay/
  [sbrelaytutorial]:http://msdn.microsoft.com/ko-kr/library/windowsazure/ee706736.aspx
  [enterprisepizza]:http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [newrelic]:http://newrelic.com/azure
  [configurediagnostics]:/ko-kr/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [troubleshootwebsites]:/ko-kr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  
  
  
  
  
  
  
  
  
  
  
  
  
  




  




<!--HONumber=35_1-->
