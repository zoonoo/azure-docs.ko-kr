#Azure 보안 지침

##요약

Azure용 응용 프로그램을 개발할 때 ID 및 액세스 권한과 관련한 기본적인 보안 문제에 유의해야 합니다. 이 항목에서는 클라우드의 ID 및 액세스 권한에 관련된 주요 보안 문제 및 클라우드 응용 프로그램을 가장 잘 보호할 수 있는 방법을 설명합니다.

##개요

응용 프로그램의 보안은 해당 표면에 대한 기능입니다. 응용 프로그램이 노출되는 표면이 많을수록 더 큰 보안 문제가 발생합니다. 예를 들어 무인 배치 프로세스로 실행되는 응용 프로그램은 공개적으로 사용 가능한 웹 사이트보다 보안 측면에서 더 적게 노출됩니다.

클라우드로 이동하면 인프라 및 네트워킹이 세계 최고의 보안 규정, 도구, 기술을 사용하는 데이터 센터에서 관리되므로 이를 편리하게 사용할 수 있습니다. 반면, 클라우드에서는 공격자가 잠재적으로 악용할 수 있는 응용 프로그램의 노출 영역이 본질적으로 더 많습니다. 이는 많은 클라우드 기술 및 서비스가 끝점 또는 메모리 내 구성 요소로 노출되기 때문입니다. Azure 저장소, 서비스 버스, SQL
데이터베이스(이전의 SQL Azure) 및 다른 많은 서비스를 네트워크상의 끝점을 통해 액세스할 수 있습니다.

클라우드 응용 프로그램에서는 공격자의 접근을 막기 위해 높은 보안 수준으로 클라우드 응용 프로그램을 설계, 개발 및 유지 관리하도록 응용 프로그램 개발자에게 더 많은 책임이 주어집니다. 다음 다이어그램을 보면(출처: J.D. Meier의 [Azure 보안 정보 PDF](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx)(영문)), 클라우드 공급자(이 경우에는 Azure)가 더 많은 보안 작업을 응용 프로그램 개발자에게 맡겨 인프라 부분을 처리하는 방식을 확인할 수 있습니다.

![Securing the application][01]

반가운 소식은 클라우드 응용 프로그램을 개발할 때 사용자가 이미 알고 있는 모든 보안 개발 사례, 원칙 및 기술이 여전히 적용된다는 점입니다. 다음은 처리해야 할 주요 영역을 설명합니다.

-   삽입 공격을 막으려면 모든 입력에서 유형, 길이, 범위 및 문자열 패턴이 올바른지 검증하고 응용 프로그램에서 반환되는 모든 데이터를 올바르게 정리합니다.
-   정보 공개 및 데이터 변조 위협을 줄이려면 네트워크상에서 중요한 데이터를 안전하게 보호해야 합니다.
-   부인 방지 위협을 줄이려면 감사 및 로깅을 올바르게 구현해야 합니다.
-   ID 스푸핑 및 권한 상승 위협을 방지하려면 플랫폼에서 제공되는 입증된 메커니즘을 사용하여 인증 및 권한 부여를 구현해야 합니다.

위협, 공격, 취약성 및 대응 방법에 대한 전체 목록은 [참고 자료: 웹 응용 프로그램 보안 프레임](http://msdn.microsoft.com/ko-kr/library/ff649461.aspx)(영문) 및 [응용 프로그램 인덱스의 보안 가이드](http://msdn.microsoft.com/ko-kr/library/ff650760.aspx)(영문)를 참조하세요.

클라우드에서의 인증 및 액세스 제어 메커니즘은 온-프레미스 응용 프로그램에서 사용할 수 있는 메커니즘과 매우 다릅니다. 클라우드 응용 프로그램에서는 혼란을 초래하고 결과적으로 구현 오류를 발생시킬 수 있는 더 많은 인증 및 액세스 옵션이 제공됩니다. 클라우드 응용 프로그램이 무엇인지를 정의할 때 혼란이 가중됩니다. 예를 들어 응용 프로그램을 클라우드에 배포할 수도 있지만 Active Directory에서 관련 인증 메커니즘이 제공될 수 있습니다. 다른 한편으로는, 클라우드에서 인증 메커니즘(예: 이전에 액세스 제어 서비스 또는 ACS로 알려진 Azure Active Directory 액세스 제어)을 사용하여 응용 프로그램을 온-프레미스에 배포할 수 있습니다.

##위협, 취약성 및 공격

위협은 중요한 정보가 공개되거나 서비스를 사용할 수 없게 되는 등 사용자가 방지하려는 잠재적인 잘못된 결과입니다. 일반적으로 "STRIDE"라는 머리글자어를 사용하여 위협을 분류합니다.

-   스푸핑 또는 ID 도용
-   데이터 훼손
-   작업 거부
-   정보 공개
-   서비스 거부
-   권한 상승

취약성은 개발자인 우리가 코드에 발생시키는 버그로, 이 버그를 통해 공격자는 응용 프로그램을 이용할 수 있습니다. 예를 들어 중요한 데이터를 암호화되지 않은 텍스트로 보내면 트래픽 검사 공격을 통해 정보 공개 위협이 가능해집니다.

공격은 이러한 취약성을 이용하여 응용 프로그램을 손상시킵니다. 예를 들어 Cross Site Scripting 또는 XSS는 정리되지 않은 출력을 이용하는 공격입니다. 다른 예로는 네트워크에서 암호화되지 않은 상태로 보낸 자격 증명을 수집하는 도청이 있습니다. 이러한 공격으로 인해 ID 스푸핑 위협이 발생할 수 있습니다. 간단하게 위협, 취약성 및 공격을 부적절한 상황으로 간주합니다.
다음은 Azure에 배포된 웹 응용 프로그램과 관련된 부적절한 상황을 전체적으로 설명하는 다이어그램(출처: J.D.
 Meier의 [Azure 보안 정보 PDF)](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx))입니다.

![Threats Vulnerabilities and Attacks][02]

개발자는 취약성을 제어할 수 있습니다. 취약성이 적어질수록 공격자가 이용할 수 있는 옵션도 줄어듭니다.

STRIDE 모델에서 ID 및 액세스 관련 취약성은 모든 위협에 노출될 수 있습니다. 예를 들어 부적절하게 구현된 인증 메커니즘으로 인해 ID 스푸핑이 발생할 수 있으며, 결과적으로 정보 공개, 데이터 훼손, 권한 상승 작업 또는 전체 서비스 종료가 발생할 수 있습니다. 다음은 클라우드 응용 프로그램의 ID 및 액세스를 구현할 때 잠재적인 취약성을 나타낼 수 있는 질문입니다.

-   네트워크에서 Azure 서비스로 암호화되지 않은 자격 증명을 보내나요?
-   Azure 서비스 자격 증명을 암호화되지 않은 상태로 저장하나요?
-   Azure 서비스 자격 증명이 강력한 암호 정책을 준수하나요?
-   Azure를 기반으로 자격 증명을 확인하나요? 아니면 사용자 지정 확인 메커니즘을 사용하나요?
-   Azure 서비스 인증 세션 또는 토큰 수명을 적절한 기간으로 제한하나요?
-   분산된 클라우드 응용 프로그램의 각 Azure 진입점에 대한 사용 권한을 확인하나요?
-   중요한 정보를 노출하거나 무제한 액세스를 허용하지 않고 권한 부여 메커니즘이 안전하게 실패하나요?

##대응 방법

공격에 대한 최상의 대응 방법은 메커니즘을 자체적으로 구현하는 대신 플랫폼에서 제공되는 ID 및 액세스 메커니즘을 사용하는 것입니다. 유명한 ID 및 액세스 기술은 다음과 같습니다.

**WIF(Windows Identity Foundation).** WIF는 .NET Framework 4.5에 포함된 .NET 런타임 라이브러리입니다(.NET 3.5/4.0에 대한 별도의 다운로드로도 사용 가능). WIF는 WS-Federation, WS-Trust 같은 프로토콜 처리 및 SAML(Security Assertion Markup Language) 같은 토큰 처리를 지원하므로 응용 프로그램에서 매우 복잡한 보안 관련 코드를 작성하지 않아도 됩니다. WIF에 대한 자세한 설명은 다음 리소스를 참조하세요.

-   MSDN 코드 갤러리의 [Windows Identity Foundation 4.5 샘플](http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication)
-   MSDN 코드 갤러리의 [Visual Studio 11 Beta용 Windows Identity Foundation 4.5 도구](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e)
-   MSDN의 [Windows Identity Foundation 런타임(.Net 3.5/4.0)](http://www.microsoft.com/ko-kr/download/details.aspx?id=17331)
-   MSDN의 [Windows Identity Foundation 3.5/4.0 샘플 및 Visual Studio 2008/2010 템플릿](http://www.microsoft.com/ko-kr/download/details.aspx?displaylang=en&id=4451)

**Azure AD 액세스 제어(이전의 ACS)**.
Azure AD 액세스 제어는 STS(보안 토큰 서비스)를 제공하고 회사 Active Directory 같은 다양한 ID 공급자(IdP) 또는 Windows Live ID / Microsoft 계정, Facebook, Google, Yahoo! 및 Open ID 2.0 ID 공급자 같은 인터넷 IdP와의 페더레이션을 허용하는 클라우드 서비스입니다. Azure AD 액세스 제어에 대한 자세한 설명은 다음 리소스를 참조하세요.

-   [액세스 제어 서비스 2.0](http://msdn.microsoft.com/library/gg429786.aspx)
-   [ACS를 사용하는 시나리오 및 솔루션](http://msdn.microsoft.com/ko-kr/library/gg185920.aspx)
-   [ACS 사용 방법](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg185939.aspx)
-   [클레임 기반 ID 및 액세스 제어 가이드](http://msdn.microsoft.com/ko-kr/library/ff423674.aspx)
-   [ID 개발자 교육 키트](http://www.microsoft.com/ko-kr/download/details.aspx?id=14347)
-   [MSDN에서 주최하는 ID 개발자 교육 코스](http://msdn.microsoft.com/ko-kr/IdentityTrainingCourse)

**AD FS(Active Directory Federation Services).**AD FS(Active Directory Federation Services) 2.0은 Windows Server 및 Active Directory 기술이 포함된 클레임 인식 ID 솔루션에 대한 지원을 제공합니다. AD FS 2.0은 WS-Trust, WS-Federation 및 SAML 프로토콜을 지원합니다. AD FS에 대한 자세한 설명은 다음 리소스를 참조하세요.

-   [AD FS 2.0 콘텐츠 맵](http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx)
-   [웹 SSO 디자인][웹 SSO 디자인](영문)
-   [페더레이션된 웹 SSO 디자인][페더레이션된 웹 SSO 디자인](영문)

**Azure 공유 액세스 서명.** 공유 액세스 서명을 사용하면 Blob 또는 컨테이너 리소스에 대한 액세스를 미세하게 조정할 수 있습니다. 공유 액세스 서명에 대한 자세한 설명은 다음 리소스를
참조하세요.

-   [Blob 및 컨테이너에 대한 액세스 관리](http://msdn.microsoft.com/ko-kr/library/ee393343.aspx)
-   [새 저장소 기능: 공유 액세스 서명](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [오늘날 공유 액세스 서명이 간단함](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

##시나리오 맵

이 섹션에서는 이 항목에서 설명하는 주요 시나리오를 간략하게 설명합니다. 이를 맵으로 활용하여 사용 중인 응용 프로그램에 맞는 올바른 ID 솔루션을 정합니다.

-   **페더레이션 인증을 사용하는 ASP.NET 웹 양식 응용 프로그램.** 이 시나리오에서는 Live ID / Microsoft 계정 같은 인터넷 ID 또는 Windows Server Active Directory에서 관리되는 회사 ID를 사용하여 ASP.NET 웹 양식 응용 프로그램에 대한 액세스를 제어합니다.
-   **페더레이션 인증을 사용하는 WCF(SOAP) 서비스.**이 시나리오에서는 Azure AD 액세스 제어로 관리되는 서비스 ID를 사용하여 WCF(SOAP) 서비스에 대한 액세스를 제어합니다.
-   **페더레이션 인증(Active Directory의 ID)을 사용하는 WCF(SOAP) 서비스.** 이 시나리오에서는 회사 Windows Server Active Directory로 관리되는 ID를 사용하여 WCF(SOAP) 웹 서비스에 대한 액세스를 제어합니다.
-   **페더레이션 인증을 사용하는 WCF(REST) 서비스.** 이 시나리오에서는 Azure AD 액세스 제어로 관리되는 서비스 ID를 사용하여 WCF(REST) 서비스에 대한 액세스를 제어합니다.
-   **Live ID / Microsoft 계정, Facebook, Google, Yahoo!, Open ID를 사용하는 WCF(REST) 서비스.** 이 시나리오에서는 Live ID / Microsoft 계정 같은 인터넷 ID를 사용하여 WCF(REST) 서비스에 대한 액세스를 제어합니다.
-   **공유 SWT 토큰을 사용하는 ASP.NET 웹 응용 프로그램 및 REST WCF 서비스.** 이 시나리오에서는 프런트 엔드 ASP.NET 웹 응용 프로그램 및 다운스트림 REST 서비스를 사용하여 응용 프로그램을 분산한 후 물리적 계층을 통해 최종 사용자의 컨텍스트를 이동합니다.
-   **클레임 인식 응용 프로그램 및 서비스의 RBAC(역할 기반 액세스 제어) 권한 부여.** 이 시나리오에서는 응용 프로그램에서 역할을 기반으로 권한 부여 논리를 구현합니다.
-   **클레임 인식 응용 프로그램 및 서비스의 클레임 기반 권한 부여.** 이 시나리오에서는 응용 프로그램에서 복잡한 권한 부여 규칙을 기반으로 권한 부여 논리를 구현합니다.
-   **Azure 저장소 서비스 ID 및 액세스 시나리오.** 이 시나리오에서는 Azure 저장소 Blob 및 컨테이너에 대한 액세스를 안전하게 공유해야 합니다.
-   **Azure SQL 데이터베이스 ID 및 액세스 시나리오.** SQL 데이터베이스는 SQL Server 인증만 지원하며 Windows 인증(통합 보안)은 지원하지 않습니다. 사용자는 SQL 데이터베이스에 연결할 때마다 자격 증명(로그인 및 암호)을 제공해야 합니다.
-   **Azure 서비스 버스 ID 및 액세스 시나리오.** 이 시나리오에서는 Azure 서비스 버스 큐에 안전하게 액세스할 수 있어야 합니다.
-   **메모리 내 캐시 ID 및 액세스 시나리오.** 이 시나리오에서는 메모리 내 캐시에서 관리되는 데이터에 안전하게 액세스할 수 있어야 합니다.
-   **Azure 마켓플레이스 ID 및 액세스 시나리오.** 이 시나리오에서는 Azure 마켓플레이스 데이터 집합에 안전하게 액세스할 수 있어야 합니다.

##Azure ID 및 액세스 시나리오

이 섹션에서는 다양한 응용 프로그램 아키텍처에 대한 일반적인 ID 및 액세스 시나리오를 간략하게 설명합니다. 빠른 이해를 위해 시나리오 맵을 사용합니다.

###페더레이션 인증을 사용하는 ASP.NET 웹 양식 응용 프로그램

이 응용 프로그램 아키텍처 시나리오에서는 웹 응용 프로그램을 Azure 또는 온-프레미스에 배포할 수 있습니다. 응용 프로그램을 사용하려면 해당 사용자가 회사 Active Directory 또는 인터넷 ID 공급자로부터
인증을 받아야 합니다.

이 시나리오를 구현하려면 Azure AD 액세스 제어 및 Windows Identity Foundation을 사용합니다.

![Azure Active Directory Access control][03]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: ACS를 사용하여 첫 번째 클레임 인식 ASP.NET 응용 프로그램 만들기](http://msdn.microsoft.com/ko-kr/library/gg429779.aspx)
-   [방법: ASP.NET 웹 응용 프로그램에서 로그인 페이지 호스트](http://msdn.microsoft.com/ko-kr/library/gg185926.aspx)
-   [방법: 클레임 인식 ASP.NET 응용 프로그램에서 WIF 및 ACS를 사용하여 클레임 권한 부여 구현](http://msdn.microsoft.com/ko-kr/library/gg185907.aspx)    
-   [방법: 클레임 인식 ASP.NET 응용 프로그램에서 WIF 및 ACS를 사용하여 RBAC(역할 기반 액세스 제어) 구현](http://msdn.microsoft.com/ko-kr/library/gg185914.aspx)
-   [방법: X.509 인증서를 사용하여 ACS 및 ASP.NET 웹 응용 프로그램 간에 트러스트 구성](http://msdn.microsoft.com/ko-kr/library/gg185947.aspx)
-   [코드 샘플: ASP.NET 단순 양식](http://msdn.microsoft.com/ko-kr/library/gg185938.aspx)

###서비스 ID를 사용하는 WCF(SOAP) 서비스

이 응용 프로그램 아키텍처 시나리오에서는 WCF(SOAP) 서비스를 Azure 또는 온-프레미스에 배포할 수 있습니다. 이 서비스는 웹 응용 프로그램이나 다른 웹 서비스에서도 다운스트림 서비스로 액세스됩니다. 응용 프로그램별 ID를 사용하여 이 서비스에 대한 액세스를 제어해야 합니다. IIS에서 사용한 응용 프로그램 풀 계정 유형의 관점에서 생각해보면, 비록 기술은 다르지만 응용 프로그램 범위 계정이나 최종 사용자 계정을 사용하여 서비스를 액세스하는 방법은 비슷합니다.

Azure AD 액세스 제어에서 서비스 ID 기능을 사용합니다. 이는 Windows Server 및 IIS에 응용 프로그램을 배포할 때 사용하던 IIS 응용 프로그램 풀 응용 프로그램과 비슷합니다. WCF(SOAP) 서비스의
WIF에서 처리되는 SAML 토큰을 발급하도록 Azure AD 액세스 제어를 구성합니다.

![WCF (SOAP) Service][04]


이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: X.509 인증서, 암호 또는 대칭 키를 사용하여 서비스 ID 추가](http://msdn.microsoft.com/ko-kr/library/gg185924.aspx)
-   [방법: 클라이언트 인증서를 사용하여 ACS로 보호된 WCF 서비스에 인증](http://msdn.microsoft.com/ko-kr/library/hh289316.aspx)
-   [방법: 사용자 이름과 암호를 사용하여 ACS로 보호된 WCF 서비스에 인증](http://msdn.microsoft.com/ko-kr/library/gg185954.aspx)
-   [코드 샘플: WCF 인증서 인증](http://msdn.microsoft.com/ko-kr/library/gg185952.aspx)
-   [코드 샘플: WCF 사용자 이름 인증](http://msdn.microsoft.com/ko-kr/library/gg185927.aspx)

###페더레이션 인증(Active Directory의 ID)을 사용하는 WCF(SOAP) 서비스

이 응용 프로그램 아키텍처 시나리오에서는 WCF(SOAP) 서비스를 Azure 또는 온-프레미스에 배포할 수 있습니다. 회사 Windows Server AD(Active Directory)에서 관리하는 ID를 사용하여 이 서비스에 대한 액세스를 제어해야
합니다.

Windows Server AD FS와의 페더레이션을 위해 구성된
Azure AD 액세스 제어를 사용합니다. 이 경우 Azure AD 액세스 제어를 사용하여
서비스 ID를 구성하지 않아도 됩니다. WCF(SOAP) 서비스에 액세스해야 하는 에이전트는 AD FS에 자격 증명을 제공하고 성공적으로 인증되면 AD FS에서 토큰이 발급됩니다. 그런 다음 토큰이 Azure AD 액세스 제어로 제출된 후 에이전트에 다시 발급됩니다. 에이전트는 이 토큰을 사용하여 WCF(SOAP) 서비스에 요청을 제출합니다.

![WCF (SOAP) Service With AD][05]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: X.509 인증서, 암호 또는 대칭 키를 사용하여 서비스 ID 추가](http://msdn.microsoft.com/ko-kr/library/gg185924.aspx)
-   [방법: AD FS 2.0을 ID 공급자로 구성](http://msdn.microsoft.com/ko-kr/library/gg185961.aspx)
-   [방법: 관리 서비스를 사용하여 AD FS 2.0을 엔터프라이즈 ID 공급자로 구성](http://msdn.microsoft.com/ko-kr/library/gg185905.aspx)
-   [코드 샘플: AD FS 2.0을 사용하여 WCF 페더레이션 인증
](http://msdn.microsoft.com/ko-kr/library/hh127796.aspx)

###서비스 ID를 사용하는 WCF(REST) 서비스

이 시나리오에서는 WCF(REST) 서비스를 Azure 또는 온-프레미스에 배포할 수 있습니다. 이 서비스는 웹 응용 프로그램이나 다른 웹 서비스에서 다운스트림 서비스로 액세스됩니다. 응용 프로그램별 ID를 사용하여 이 서비스에 대한 액세스를 제어해야 합니다. IIS에서 사용한 응용 프로그램 풀 계정 유형의 관점에서 생각해보면, 비록 기술은 다르지만 응용 프로그램 범위 계정이나 최종 사용자 계정을 사용하여 서비스를 액세스하는 방법은 비슷합니다.

Azure AD 액세스 제어에서 서비스 ID 기능을 사용합니다. SWT(단순 웹 토큰) 토큰을 발급하도록 Azure AD 액세스 제어를 구성합니다. REST 서비스 쪽에서 SWT 토큰을 처리하려면 사용자 지정 토큰 처리기를 구현하여 토큰을 WIF 파이프라인에 연결하거나 WIF 인프라를 사용하지 않고 이를 "수동으로" 구문 분석할 수 있습니다.

다음 다이어그램을 참조하세요.(WIF는 선택 사항임).

![REST Service][06]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: 대칭 키를 사용하여 ACS 및 WCF 서비스 간에 트러스트 구성](http://msdn.microsoft.com/ko-kr/library/gg185958.aspx)
-   [방법: ACS를 사용하여 Azure에 배포된 REST WCF 서비스에 인증](http://msdn.microsoft.com/ko-kr/library/hh289317.aspx)
-   [코드 샘플: ASP.NET 웹 서비스](http://msdn.microsoft.com/ko-kr/library/gg983271.aspx)
-   [코드 샘플: Windows Phone 7 응용 프로그램](http://msdn.microsoft.com/ko-kr/library/gg983271.aspx)
-   [Azure ACS(액세스 제어 서비스)에서 발급된 SWT 토큰을 사용하는REST WCF](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

###Live ID / Microsoft 계정, Facebook, Google, Yahoo!, Open ID를 사용하는 WCF(REST) 서비스

이 시나리오에서는 WCF(REST) 서비스를 Azure 또는 온-프레미스에 배포할 수 있습니다. Live ID / Microsoft 계정 또는 Facebook 같은 공용 인터넷 ID를 사용하여 이 서비스에 대한 액세스를 제어해야 합니다.

Azure AD 액세스 제어를 사용하여 SWT 토큰을 발급합니다. REST 서비스 쪽에서
SWT 토큰을 처리하려면 사용자 지정 토큰 처리기를 구현하여 토큰을 WIF 파이프라인에 연결하거나 WIF 인프라를 사용하지 않고 이를 "수동으로" 구문 분석할 수 있습니다.

이 시나리오를 구현하려면 응용 프로그램에서 웹 브라우저 컨트롤을 사용하여 최종 사용자 자격 증명을 수집해야 합니다. 따라서 ASP.NET 웹 응용 프로그램에서 REST 서비스에 액세스하는 시나리오에서는 적합하지 않으며, Windows Phone 7 응용 프로그램 또는 다양한 기능의 데스크톱 클라이언트 같은 사용자 클라이언트 응용 프로그램에서 REST 서비스에 액세스하는 시나리오에만 적합합니다. 웹 브라우저 컨트롤을 언급하는 주된 이유는 인터넷 ID는 기본적으로 활성 프로필 시나리오(웹 서비스 시나리오)를 지원하지 않기 때문입니다. 인터넷 ID는 주로 브라우저 리디렉션이 필요한 수동 프로필 시나리오(웹 응용 프로그램)를 지원합니다. 이 경우 웹 브라우저 컨트롤이 도움이 됩니다.

다음 다이어그램을 참조하세요.(WIF는 선택 사항이므로 표시되지 않음).

![WIF is optional][07]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: ACS를 사용하여 Azure에 배포된 REST WCF 서비스에 인증](http://msdn.microsoft.com/ko-kr/library/hh289317.aspx)
-   [방법: Google을 ID 공급자로 구성](http://msdn.microsoft.com/ko-kr/library/gg185976.aspx)
-   [방법: Facebook을 ID 공급자로 구성](http://msdn.microsoft.com/ko-kr/library/gg185919.aspx)
-   [방법: Yahoo!를 ID 공급자로 구성](http://msdn.microsoft.com/ko-kr/library/gg185977.aspx)
-  [ 코드 샘플: Windows Phone 7 응용 프로그램](http://msdn.microsoft.com/ko-kr/library/gg983271.aspx)
-   [Azure ACS(액세스 제어 서비스)에서 발급된 SWT 토큰을 사용하는REST WCF](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)


###공유 SWT 토큰을 사용하는 ASP.NET 웹 응용 프로그램 및 REST WCF 서비스

이 시나리오에서는 프런트 엔드 ASP.NET 웹 응용 프로그램
및 다운스트림 REST 서비스를 사용하여 응용 프로그램을 분산한 후 물리적 계층을 통해 최종 사용자의 컨텍스트를 유지 관리합니다. 경우에 따라 다운스트림 REST 서비스에서 최종 사용자 ID를 기반으로 권한 부여 논리나 로깅을 구현할 때 이 시나리오가 필요합니다.

SWT 토큰을 발급하도록 Azure AD 액세스 제어를 구성합니다. 프런트 엔드 ASP.NET 웹 응용 프로그램에 SWT 토큰이 발급된 후 다운스트림 REST 서비스와 이를 공유합니다. 이 경우 Azure AD 액세스 제어에 신뢰 당사자가 하나만 구성됩니다. 하지만 주의할 점이 여러 개 있습니다.

-   WIF는 SWT 토큰 처리기를 즉시 제공하지 않으므로 ASP.NET 웹 응용 프로그램에서 사용할 사용자 지정 토큰 처리기를 구현해야 합니다. WIF가 브라우저 리디렉션이 필요한 WS-Federation 프로토콜을 지원하도록 하거나 아니면 자체적으로 이를 구현해야 합니다.
-   SWT 사용자 지정 토큰 처리기를 구현할 때 부트스트랩 토큰이 유지되도록 이를 처리하고 있는지 확인합니다. 그렇지 않으면 이 토큰을 공유하여 다운스트림 REST 서비스에 보낼 수 없습니다.
-   REST 서비스에서 WIF를 사용하지 않아도 됩니다. 대신 이 경우에는 리디렉션을 처리할 필요가 없으므로 토큰을 "수동으로" 구문 분석할 수 있습니다.

![ASP.NET Web Application][08]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: Google을 ID 공급자로 구성](http://msdn.microsoft.com/ko-kr/library/gg185976.aspx)
-   [방법: Facebook을 ID 공급자로 구성](http://msdn.microsoft.com/ko-kr/library/gg185919.aspx)
-   [방법: Yahoo!를 ID 공급자로 구성](http://msdn.microsoft.com/ko-kr/library/gg185977.aspx)
-   [공유 SWT 토큰을 사용하여 REST WCF 서비스로 ASP.NET 웹 응용 프로그램위임](http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82)

###클레임 인식 응용 프로그램 및 서비스의 RBAC(역할 기반 액세스 제어)

이 시나리오에서는 사용자 역할을 기반으로 웹 응용 프로그램 또는 서비스에서 권한 부여를 구현해야 합니다. 즉, 필요한 역할이 있는 사용자는 액세스할 수 있고 필요한 역할이 없는 사용자는 거부됩니다. 간단히 말해 응용 프로그램이 간단한 문제에 답해야 합니다(X 역할이 있는
사용자인가요?).

이 시나리오를 구현할 수 있는 몇 가지 방법이 있습니다. Azure AD 액세스 제어, WIF 클레임 인증 관리자, samlSecurityTokenRequirement 매핑 또는 고객 역할 관리자를 사용할 수 있습니다.

WIF는 모든 경우에 사용됩니다. WIF는 IPrincipal.IsInRole("MyRole") 메서드를 지원합니다. 대부분의 경우 IsInRole 메서드가 호출될 때 WIF가 역할 구성원을 확인할 수 있도록 토큰에 URI가 http://schemas.microsoft.com/ws/2008/06/identity/claims/role인 역할 유형 클레임이 있는지 확인해야 합니다.

**Azure AD 액세스 제어**. 이 구현에서는 Azure AD 액세스 제어 클레임 변환 규칙 엔진이 사용됩니다. 클레임 변환 규칙 엔진의 규칙을 사용하면 들어오는 클레임을 역할 유형 클레임으로 변환할 수 있습니다. 이를 통해 토큰이 응용 프로그램이나 서비스에 도달할 때 WIF가 이 역할 클레임을 구문 분석하여 IsInRole 메서드를 성공적으로 호출할 수 있습니다.

![][09]

**WIF ClaimsAuthenticationManager**. 이 구현에서는 WIF의 확장 포인트로 ClaimsAuthenticationManager를 사용합니다. 이 방법을 사용하여 응용 프로그램에서 들어오는 임의 클레임을 역할 클레임 유형으로 변환합니다. 변환의 복잡한 정도는 사용자가 작성하는 코드로만 제한됩니다.

![][10]

**samlSecurityTokenRequriement 매핑**. 이 구현에서는 web.config의 samlSecurityTokenRequirement 구성을 사용하여 WIF에 역할 클레임 유형처럼 동작할 클레임 유형을 알려줍니다. 예를 들어 토큰이 그룹 유형의 클레임을 전달하는 경우 역할 클레임 유형에 이를 매핑할 수 있습니다. 이 방법을 사용하면 간단한 매핑만 수행할 수 있습니다.

![][11]

**사용자 지정 RoleManager.** 이 구현에서는 사용자 지정 RoleManger를 구현합니다. GetAllRoles() 같은 사용자 지정 RoleManager 인터페이스 메서드를 구현할 경우 WIF를 사용하여 들어오는 클레임을 검사합니다.

![][12]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: 클레임 인식 ASP.NET 응용 프로그램에서 WIF 및 ACS를 사용하여 RBAC(역할 기반 액세스 제어) 구현](http://msdn.microsoft.com/ko-kr/library/gg185914.aspx)
-   [방법: 규칙을 사용하여 토큰 변환 논리 구현](http://msdn.microsoft.com/ko-kr/library/gg185955.aspx)
-   [RoleManager를 사용하여 클레임 인식(WIF) ASP.NET 웹 응용 프로그램에 대한권한 부여](http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx)
-   코드 샘플: [Windows Identity Foundation SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)에서 IsInRole의 클레임 사용

###클레임 인식 응용 프로그램 및 서비스의 클레임 기반 권한 부여

이 시나리오에서는 웹 응용 프로그램이나 서비스에서 복잡한 권한 부여 논리를 구현해야 하며 IsInRole() 메서드는 권한 부여에 적합하지 않습니다. 권한 부여 방법이 역할에 따라 다른 경우 이전 섹션에서 설명한 역할 기반의 액세스 제어를 구현하는 것이 좋습니다.

WIF의 확장 포인트로 ClaimsAuthorizationManager를 사용합니다.
ClaimsAuthorizationManager를 사용하면 외부 액세스 검사를 호출할 수 있으므로 응용 프로그램 코드에 액세스 검사가 구현되어 있을 때보다 응용 프로그램 코드가 더 간결하고 유지 관리하기에 더 편리합니다.

![][13]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: 규칙을 사용하여 토큰 변환 논리 구현](http://msdn.microsoft.com/ko-kr/library/gg185955.aspx)
-   [방법: 클레임 인식 ASP.NET 응용 프로그램에서 WIF 및 ACS를 사용하여 클레임 권한 부여 구현](http://msdn.microsoft.com/ko-kr/library/gg185907.aspx)
-   코드 샘플: [Windows Identity Foundation SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)의 클레임 기반 권한 부여


##Azure 저장소 서비스 ID 및 액세스 시나리오

이 시나리오에서는 Azure 저장소 Blob 및 컨테이너에 대한 액세스를 안전하게 공유해야 합니다.

공유 액세스 서명을 사용합니다. 고유한 응용 프로그램에서 저장소 서비스 계정에 액세스하려면 저장소 서비스 계정을 구성하고
관리할 때 Azure 포털에서 사용할 수 있는 공유 해시를 사용합니다. 저장소 서비스 계정의 Blob 및 컨테이너에 다른 사용자가 액세스할 수 있도록 하려면 공유 액세스 서명 URL을
사용합니다.

정보를 안전하게 관리하여 노출하지 않도록 각별히 주의해야 하며 공유 액세스 서명의 수명도 유의해야
합니다.

![][14]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [Blob 및 컨테이너에 대한 액세스 관리](http://msdn.microsoft.com/ko-kr/library/ee393343.aspx)
-   [새 저장소 기능: 공유 액세스 서명](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [오늘날 공유 액세스 서명이 간단함](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)


##Azure SQL 데이터베이스 ID 및 액세스 시나리오

SQL 데이터베이스는 SQL Server 인증만 지원하며 Windows
인증(통합 보안)은 지원하지 않습니다. 사용자는 SQL 데이터베이스에 연결할 때마다 자격 증명(로그인 및 암호)을
제공해야 합니다. 정보가 공개되지 않도록 사용자 이름 및 암호를 관리할 때 각별히 주의해야 합니다.

![][15]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [보안 지침 및 제한 사항(SQL 데이터베이스)](http://msdn.microsoft.com/ko-kr/library/windowsazure/ff394108.aspx#authentication)
-   [방법: sqlcmd를 사용하여 SQL 데이터베이스에 연결](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336280.aspx)
-   [방법: ADO.NET를 사용하여 SQL 데이터베이스에 연결](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee336243.aspx)
-   [방법: ASP.NET를 통해 SQL 데이터베이스에 연결](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee621781.aspx)
-   [방법: WCF Data Services를 통해 SQL 데이터베이스에 연결](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee621789.aspx)
-  [ 방법: PHP를 사용하여 SQL 데이터베이스에 연결](http://msdn.microsoft.com/ko-kr/library/windowsazure/ff394110.aspx)
-   [방법: JDBC를 사용하여 SQL 데이터베이스에 연결](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg715284.aspx)
-   [방법: ADO.NET Entity Framework를 사용하여 SQL 데이터베이스에 연결](http://msdn.microsoft.com/ko-kr/library/windowsazure/ff951633.aspx)

##Azure 서비스 버스 ID 및 액세스 시나리오

서비스 버스 및 Azure AD 액세스 제어에는 특수한 관계가 있습니다. 즉, 각 서비스 버스 서비스 네임스페이스("-sb" 접미사가 붙음)는 같은 이름의 일치하는 액세스 제어 서비스 네임스페이스와 쌍을 이룹니다. 서비스 버스 및 액세스 제어에서 상호 트러스트 관계와 연결된 암호화 암호를 관리하는 방식으로 인해 이러한 특수 관계가 사용됩니다. 자세한 내용은 아래 나열된 리소스를 참조하세요.

![][16]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [ACS로 서비스 버스 보안](http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS)(비디오)
-   [ACS로 서비스 버스 보안](https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849)(슬라이드)
-   [액세스 제어 서비스를 사용한 서비스 버스 인증 및 권한 부여](http://msdn.microsoft.com/ko-kr/library/hh403962.aspx)

##메모리 내 캐시 ID 및 액세스 시나리오

메모리 내 캐시(이전의 Azure 캐시)는 Azure AD 액세스 제어를 기반으로 인증을 수행합니다. 또한 관리 포털에서 사용할 수 있는 공유 키를 사용합니다. 캐시에 액세스할 때 코드 또는 구성 파일에서 키를 사용합니다. 정보가 공개되지 않도록 키를 안전하게 저장해야 합니다.

![][17]


이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [방법: Azure 캐싱용으로 프로그래밍 방식으로 캐시 클라이언트 구성](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg618003.aspx)
-   [방법: Azure 캐싱에 대해 응용 프로그램 구성 파일을 사용하여 캐시 클라이언트 구성](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg278346.aspx)
-   [Azure 서비스 버스 및 캐싱 샘플](http://msdn.microsoft.com/ko-kr/library/ee706741.aspx)(캐싱 샘플 섹션)

##Azure 마켓플레이스 ID 및 액세스 시나리오

무료 또는 유료로 Azure 마켓플레이스 데이터 집합에 액세스할 때마다 사용자를 인증해야만 액세스가 허용됩니다. 응용 프로그램을 만들 때 코드에 인증 프로세스를 포함해야 합니다. 다음과 같은 일반적인 시나리오를 고려해 보세요.

###내가 내 데이터 집합에 액세스

이 시나리오에서는 마켓플레이스 구독에서 데이터 집합을 사용하는 응용 프로그램을 빌드합니다. 자신이 응용 프로그램의 사용자입니다. 응용 프로그램은 Azure, 온-프레미스 또는 마켓플레이스에 배포할 수 있습니다.

마켓플레이스 구독에서 사용할 수 있는 공유 키를 사용합니다. 마켓플레이스 포털을 사용하여 공유 키를 얻습니다.

![][18]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [마켓플레이스 응용 프로그램에서 HTTP 기본 인증 사용](http://msdn.microsoft.com/ko-kr/library/gg193417.aspx)

###사용자가 내 데이터 집합에 액세스

이 시나리오에서는 사용자가 데이터 집합에 액세스할 수 있는 응용 프로그램을 빌드합니다. 응용 프로그램은 Azure, 온-프레미스 또는 마켓플레이스에 배포할 수 있습니다.

이 시나리오를 구현하려면 OAuth 위임을 사용합니다. 사용자에게 Live ID / Microsoft 계정 자격 증명을 입력하라는 메시지가 표시된 후 동의 절차로 이동합니다.

![][19]

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [OAuth 웹 클라이언트 예](http://go.microsoft.com/fwlink/?LinkId=219162)
-   [OAuth 리치 클라이언트 예](http://go.microsoft.com/fwlink/?LinkId=219163)

###응용 프로그램이 마켓플레이스 API에 액세스

이 시나리오에서는 마켓플레이스 API에 액세스하는 응용 프로그램을 빌드합니다. 마켓플레이스 API에서 호출을 수행하려면 인증이 필요합니다. 응용 프로그램은 Azure 마켓플레이스에 배포됩니다.

![][20]

인증 구현에 대한 자세한 내용은 마켓플레이스 게시 키트를 참조하세요.

이 시나리오를 구현하려면 다음 리소스를 참조하세요.

-   [응용 프로그램 게시 키트 다운로드](http://go.microsoft.com/fwlink/?LinkId=221323)
-   [응용 프로그램용 Azure 마켓플레이스 소개](https://datamarket.azure.com/)

##보안 노브

이 섹션에서는 Windows Identity Foundation 및 Azure AD 액세스 제어의
보안 노브를 간략하게 설명합니다. 응용 프로그램을 설계하고 배포할 때 이러한 기술에 대한 기본 보안 검사 목록으로 이를 사용합니다.

###Windows Identity Foundation

다음은 WIF의 키 보안 노브입니다. 아래 정보는 [WIF 설계 고려 사항](http://msdn.microsoft.com/ko-kr/library/ee517298.aspx) 및 [ASP.NET 웹 응용 프로그램에 대한WIF(Windows Identity Foundation) 보안 - 위협 및 대응 방법](http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx)
(영문)을 요약한 것입니다.

-   **IssuerNameRegistry**. 신뢰할 수 있는 STS(Security Token Services)를 지정합니다. 신뢰할 수 있는 STS만 표시됩니다.
-   **cookieHandler requireSsl="true"**. 세션 쿠키가 SSL 프로토콜을 통해 전송되는지를 지정합니다.
-   **wsFederation's requireHttps="true"**. ID 공급자와의 페더레이션 프로토콜 통신이 SSL 프로토콜을 통해 수행되는지를 지정합니다.
-   **tokenReplayDetection enabled="true"**. 토큰 재생 검색 기능을 사용할 수 있는지를 지정합니다. 이 기능은 사용된 토큰의 로컬 복사본을 관리하면서 서버 선호도를 만듭니다.
-   **audienceUris**. 토큰의 대상 그룹을 지정합니다. 응용 프로그램이 응용 프로그램에 사용할 수 없는 토큰을 받으면 WIF에서 이를 거부합니다.
-   **requestValidation** 및 **httpRuntime requestValidationType**. ASP.NET 유효성 검사 기능을 사용하거나 사용하지 않도록 설정합니다. [WIF(Windows Identity Foundation): 잠재적으로 위험한 Request.Form 값이 클라이언트에서 검색되었음](http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx)에서 설명하는 지침을 참조하세요.

###Azure AD 액세스 제어

Azure AD 액세스 제어 배포 시 다음 보안 노브를 고려합니다. 아래 정보는 [ACS 보안
지침](http://msdn.microsoft.com/ko-kr/library/gg185962.aspx) 및 [인증서 및 키 관리 지침](http://msdn.microsoft.com/ko-kr/library/hh204521.aspx)을 요약한 것입니다.

-   **STS 토큰 만료**. Azure AD 액세스 제어 관리 포털을 사용하여 토큰 만료를 적극적으로 설정합니다.
-   **오류 URL 기능 사용 시 데이터 유효성 검사**. Azure AD 액세스 제어 오류 URL 기능은 오류 메시지를 보낼 때 응용 프로그램 페이지에 익명으로 액세스해야 합니다. 신뢰할 수 없는 원본에서 이 페이지로 들어오는 모든 데이터를 위험한 데이터로 간주합니다.
-   **기밀을 철저하게 유지하기 위해 토큰 암호화**. 토큰에서 사용할 수 있는 정보가 공개될 위협을 줄이려면 토큰을 암호화하는 것이 좋습니다.
-   **Azure에 배포할 때 RSA를 사용하여 쿠키 암호화**. WIF는 기본적으로 DPAPI를 사용하여 쿠키를 암호화합니다. 또한 서버 선호도를 만들며 웹 팜 및 Azure 환경에 배포할 때 예외를 발생시킬 수 있습니다. 대신 웹 팜 및 Azure 시나리오에서는 RSA를 사용합니다.
-   **토큰 서명 인증서**. 서비스 거부를 방지하려면 토큰 서명 인증서를 주기적으로 갱신합니다. Azure AD 액세스 제어는 발급하는 모든 보안 토큰에 서명합니다. ACS에서 발급한 SAML 토큰을 사용하는 응용 프로그램을 작성할 때는 X.509 인증서를 사용하여 서명합니다. 서명 인증서가 만료되면 토큰을 요청할 때 오류가 표시됩니다.
-   **토큰 서명 키**. 서비스 거부를 방지하려면 토큰 서명 키를 주기적으로 갱신합니다. Azure AD 액세스 제어는 발급하는 모든 보안 토큰에 서명합니다. ACS에서 발급한 SWT 토큰을 사용하는 응용 프로그램을 작성할 때는 256비트 대칭 서명 키가 사용됩니다. 서명 키가 만료되면 토큰을 요청할 때 오류가 표시됩니다.
-   **토큰 암호화 인증서**. 서비스 거부를 방지하려면 토큰 암호화 인증서를 주기적으로 갱신합니다. 신뢰 당사자 응용 프로그램이 WS-Trust 프로토콜을 통해 소유 증명 토큰을 사용하는 웹 서비스인 경우에는 토큰 암호화가 필요합니다. 그 외 경우에, 토큰 암호화는 선택 사항입니다. 암호화 인증서가 만료되면 토큰을 요청할 때 오류가 표시됩니다.
-   **토큰 암호 해독 인증서**. 서비스 거부를 방지하려면 토큰 암호 해독 인증서를 주기적으로 갱신합니다. Azure AD 액세스 제어에서는 WS-Federation ID 공급자(예: AD FS 2.0)에서 보낸 암호화된 토큰을 수락할 수 있습니다. 암호 해독에는 Azure AD 액세스 제어에서 호스트되는 X.509 인증서가 사용됩니다. 암호 해독 인증서가 만료되면 토큰을 요청할 때 오류가 표시됩니다.
-   **서비스 ID 자격 증명**. 서비스 거부를 방지하려면 서비스 ID 자격 증명을 주기적으로 갱신합니다. 서비스 ID는 Azure AD 액세스 제어 네임스페이스에 대해 전역적으로 구성되며, 응용 프로그램 또는 클라이언트가 Azure AD 액세스 제어에 직접 인증하고 토큰을 받을 수 있도록 하는 자격 증명을 사용합니다. Azure AD 액세스 제어 서비스 ID는 대칭 키, 암호, X.509 인증서인 세 가지 유형의 자격 증명에 연결될 수 있습니다. 자격 증명이 만료되면 예외를 받기 시작합니다.
-   **Azure AD 액세스 제어 관리 서비스 계정 자격 증명**. 서비스 거부를 방지하려면 관리 서비스 자격 증명을 주기적으로 갱신합니다. Azure AD 액세스 제어 관리 서비스는 Azure AD 액세스 제어 네임스페이스에서 설정을 프로그래밍 방식으로 관리 및 구성할 수 있도록 하는 주요 구성 요소입니다. 관리 서비스 계정은 세 가지 유형의 자격 증명에 연결될 수 있습니다. 여기에는 대칭 키, 암호 및 X.509 인증서가 있습니다. 자격 증명이 만료되면 예외를 받기 시작합니다.
-   **WS-Federation ID 공급자 서명 및 암호화 인증서**. 서비스 거부를 방지하려면 WS-Federation ID 공급자의 인증서 유효성을 쿼리합니다. WS-Federation ID 공급자 인증서는 해당 메타데이터를 통해 사용 가능합니다. AD FS와 같은 WS-Federation ID 공급자를 구성할 때는 URL이나 파일로 제공되는 WS-Federation 메타데이터를 통해 WS-Federation 서명 인증서를 구성합니다. WS-Federation ID 공급자를 구성한 후에는 Azure AD 액세스 제어 관리 서비스를 사용하여 이 공급자의 인증서 유효성을 쿼리합니다. 인증서가 만료되면 예외를 받기 시작합니다.

##Azure 웹 사이트를 사용하는 공유 호스팅

이 항목에서 설명하는 모든 시나리오와 솔루션은 응용 프로그램이 Azure 웹 사이트에서 호스트되는 경우에 사용할 수 있습니다.

##Azure 가상 컴퓨터

이 항목에서 설명하는 모든 시나리오와 솔루션은 응용 프로그램이 Azure 가상 컴퓨터에서 호스트되는 경우에 사용할 수 있습니다.

##리소스

-   [ID 개발자 교육 키트](http://go.microsoft.com/fwlink/?LinkId=214555)
-   [MSDN에서 주최하는 ID 개발자 교육 코스](http://go.microsoft.com/fwlink/?LinkId=214561)
-   [클레임 기반 ID 및 액세스 제어 가이드](http://go.microsoft.com/fwlink/?LinkId=214562)
-   [액세스 제어 서비스](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg429786.aspx)
-   [ACS 사용 방법](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg185939.aspx)
-   [액세스 제어 서비스 버전 2.0을 사용하여 Azure 웹 역할 ASP.NET 웹 응용 프로그램 보호](http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx)
-   [Azure AD ACS(액세스 제어 서비스) Academy 비디오](http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx)
-   [Microsoft 보안 개발 수명 주기](http://www.microsoft.com/security/sdl/default.aspx)
-   [SDL Threat Modeling Tool 3.1.8](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955)
-   [보안 및 개인 정보 블로그](http://www.microsoft.com/about/twc/en/us/blogs.aspx)
-   [보안 응답 센터](http://www.microsoft.com/security/msrc/default.aspx)
-   [보안 상황 보고서](http://www.microsoft.com/security/sir/)
-   [보안 개발 수명 주기](http://www.microsoft.com/security/sdl/default.aspx)
-   [보안 개발자 센터(MSDN)](http://msdn.microsoft.com/security/)


[01]:./media/SecurityRX/01_SecuringTheApplication.gif
[02]:./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
[03]:./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
[04]:./media/SecurityRX/04_WCF(SOAP)Service.gif
[05]:./media/SecurityRX/05_AzureADAccessControl.gif
[06]:./media/SecurityRX/06_RESTService.gif
[07]:./media/SecurityRX/07_WIFisOptional.gif
[08]:./media/SecurityRX/08_ASPNETWebApptoREST.gif
[09]:./media/SecurityRX/09_RBAC.gif
[10]:./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
[11]:./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
[12]:./media/SecurityRX/12_CustomRoleManager.gif
[13]:./media/SecurityRX/13_ClaimsAuthorizationManager.gif
[14]:./media/SecurityRX/14_WindowsAzurestorage.gif
[15]:./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
[16]:./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
[17]:./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
[18]:./media/SecurityRX/18_IAccessMyDataset.gif
[19]:./media/SecurityRX/19_UsersAccessMyDatasets.gif
[20]:./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif

[웹 SSO 디자인]: http://technet.microsoft.com/ko-kr/library/dd807033(WS.10).aspx
[페더레이션된 웹 SSO 디자인]: http://technet.microsoft.com/ko-kr/library/dd807050(WS.10).aspx
<!--HONumber=42-->
