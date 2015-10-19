<properties
	pageTitle="Azure Active Directory 버전 | Microsoft Azure"
	description="무료 및 유료 버전의 Azure Active Directory를 설명하는 항목입니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="markvi"/>

# Azure Active Directory 버전

Azure Active Directory는 클라우드에서 포괄적인 ID 및 액세스 관리 기능을 제공하는 서비스입니다. 이 서비스는 개발자를 위한 디렉터리 서비스, 고급 ID 거버넌스, 응용 프로그램 액세스 관리 및 풍부한 표준 기반 플랫폼을 결합합니다. 자세한 내용은 [이 비디오를 참조](http://azure.microsoft.com/documentation/videos/teched-europe-2014-cloud-identity-microsoft-azure-active-directory-explained/)하세요.

Microsoft Azure Active Directory의 많은 무료 기능 위에 구축된 Azure Active Directory Premium 및 Basic 버전은 까다로운 ID 및 액세스 관리 요구를 갖는 엔터프라이즈를 지원할 수 있는 더 많은 고급 기능 모음을 제공합니다. 이러한 버전에 대한 가격 옵션은 [Azure Active Directory 가격](http://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요. Azure를 구독하면 무료 및 유료 버전의 Azure Active Directory 중에서 선택할 수 있습니다.

- **무료** -Azure Active Directory의 무료 버전은 모든 Azure 구독의 일부입니다. 라이선스 및 설치할 것이 없습니다. 사용하면 사용자 계정을 관리하고, 온-프레미스 디렉토리와 동기화하고, Azure, Office 365 및 인기 많은 수천 가지의 SaaS 응용 프로그램(예: Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox 등)에서 SSO(Single Sign-On)를 수행할 수 있습니다.
- **Basic** - Azure Active Directory Basic 버전은 클라우드를 주로 필요로 하는 작업자의 응용 프로그램 액세스 및 셀프 서비스 ID 관리 요구를 충족시켜줍니다. Azure Active Directory Basic 버전을 사용하면 Azure Active Directory Free 버전이 제공하는 모든 기능 외에, 그룹 기반 액세스 관리, 클라우드 응용 프로그램에 대한 셀프 서비스 암호 재설정, Azure Active Directory 응용 프로그램 프록시(Azure Active Directory를 사용하여 온-프레미스 웹 응용 프로그램을 게시하는 데 필요), 엔터프라이즈 및 소비자 클라우드 응용 프로그램을 실행하기 위한 사용자 기능 가능 환경, 엔터프라이즈급의 99.9% 가동 시간 SLA를 활용할 수 있습니다. Azure Active Directory Basic 버전에 대한 라이센스가 있는 관리자는 Azure Active Directory Premium 평가판의 정품 인증을 받을 수도 있습니다.
- **Premium** - Azure Active Directory Premium Edition을 사용하면 Azure Active Directory Free 및 Basic 버전이 제공하는 모든 기능 외에, 아래 설명된 추가적인 풍부한 엔터프라이즈급 ID 관리 기능을 활용할 수 있습니다.

지금 당장 Active Directory Premium을 등록하고 사용하려면 [Azure Active Directory Premium 시작](active-directory-get-started-premium.md)을 참조하세요.

> [AZURE.NOTE]Azure Active Directory Premium 및 Azure Active Directory Basic은 중국에서 현재 지원되지 않습니다. 자세한 내용은 [Azure Active Directory 포럼](http://feedback.azure.com/forums/169401-azure-active-directory)을 통해 문의하세요.

## Azure Active Directory Basic의 기능

Active Directory Basic 버전은 Azure Active Directory의 유료 버전이며 다음과 같은 기능을 포함합니다.

- **회사 브랜딩** - 최종 사용자에게 보다 나은 환경을 제공하기 위해 조직의 로그인 및 액세스 패널 페이지에 사용자 회사의 로고와 색 구성표를 추가할 수 있습니다. 로고를 추가하면 다른 언어 및 로캘에 대한 지역화된 로고 버전을 추가할 수 있는 옵션도 제공됩니다. 자세한 내용은 [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가하기](active-directory-add-company-branding.md)를 참조하세요.
- **그룹 기반 응용 프로그램 액세스** - 그룹을 사용하여 사용자를 프로비전하고 수천 개의 SaaS 응용 프로그램에 대량으로 사용자 액세스를 할당합니다. 이러한 그룹을 클라우드에 따로 만들거나 온-프레미스 Active directory의 동기화된 기존 그룹을 활용할 수 있습니다. 자세한 내용은 [Azure AD에서 그룹에 대해 SaaS 응용 프로그램 액세스 권한 할당](active-directory-accessmanagement-group-saasapps.md)을 참조하세요.
- **셀프 서비스 암호 재설정** - Azure에서는 항상 디렉터리 관리자가 암호를 재설정할 수 있습니다. Azure Active Directory Basic을 사용하면 디렉터리의 모든 사용자가 Office 365의 경우와 동일한 로그인 환경을 사용하여 암호를 재설정할 수 있도록 함으로써 암호를 잊어버린 문제로 인해 헬프 데스크로 걸려오는 전화 수를 줄일 수 있습니다. 자세한 내용은 [어디에서나 암호 관리](active-directory-passwords.md)를 참조하세요.
- **99.9%의 엔터프라이즈 SLA** - Azure Active Directory Basic 서비스에 대해 99.9% 이상의 가용성을 보장합니다.
- [**온-프레미스 응용 프로그램에 대한 안전한 원격 액세스를 제공하는 방법**](active-directory-application-proxy-get-started.md) - 직원들이 Azure Active Directory를 사용하여 클라우드에서 SharePoint 및 Exchange/OWA와 같은 온-프레미스 응용 프로그램에 안전하게 액세스할 수 있도록 합니다.

## Azure Active Directory Premium의 기능

Active Directory Premium 버전은 Azure Active Directory의 유료 버전이며 Free 및 Basic 버전의 모든 기능 외에 다음과 같은 기능을 포함합니다.

- **셀프 서비스 그룹 관리** - Azure Active Directory Premium은 사용자가 그룹을 만들고, 다른 그룹에 대한 액세스를 요청하고, 그룹 소유권을 위임하여 다른 사람이 요청을 승인하고 그룹의 멤버 자격을 유지 관리할 수 있도록 함으로써 일상적인 그룹 관리 작업을 간소화합니다.

    자세한 내용은 [Azure AD의 사용자를 위한 셀프 서비스 그룹 관리](https://msdn.microsoft.com/library/azure/dn641267.aspx)(영문)를 참조하세요.

- **고급 보안 보고서 및 경고** – 비정상적이거나 일관되지 않은 액세스 패턴에 대한 고급 보고서를 표시하는 자세한 로그를 확인하여 클라우드 응용 프로그램에 대한 액세스를 모니터링하고 보호합니다. 고급 보고서는 기계 학습을 기반으로 하며, 액세스 보안을 향상시키고 잠재적인 위협에 대응하기 위한 새로운 통찰력을 얻는 데 도움이 될 수 있습니다.

    자세한 내용은 [액세스 및 사용 보고서 보기](active-directory-view-access-usage-reports.md)를 참조하세요.

- **Multi-Factor Authentication** - Multi-Factor Authentication은 Premium 버전에 포함된 기능으로, 온-프레미스 응용 프로그램(VPN, RADIUS 등), Azure, Microsoft Online Services(예: Office 365 및 Dynamics CRM Online), Azure Active Directory와 미리 통합된 수천 개의 타사 클라우드 서비스에 대한 보안 액세스를 지원할 수 있습니다. Azure Active Directory ID에 대해 다단계 인증을 사용하도록 설정하면 사용자가 다음에 로그인할 때 추가 확인을 설정하라는 메시지가 표시됩니다.

    자세한 내용은 [Azure Multi-Factor Authentication 정의](multi-factor-authentication.md)를 참조하세요.

- **Microsoft ID 관리자** - Premium 버전에는 온-프레미스 네트워크에서 MIM 서버(및 CAL)를 사용하여 어떤 하이브리드 ID 솔루션 조합도 지원할 수 있는 권한을 부여하는 옵션이 포함되어 있습니다. 이 옵션은 Azure Active Directory에 직접 동기화하려는 다른 유형의 온-프레미스 디렉터리 및 데이터베이스가 있을 때 아주 유용합니다. 사용할 수 있는 MIM 서버 수에는 제한이 없지만 MIM CAL은 Azure Active Directory Premium 사용자 라이선스 할당에 따라 부여됩니다.

    자세한 내용은 [Microsoft ID 관리자](http://www.microsoft.com/ko-KR/server-cloud/products/microsoft-identity-manager/default.aspx)를 참조하세요.

- **99.9%의 엔터프라이즈 SLA** - Azure Active Directory Premium 서비스에 대해 99.9% 이상의 가용성을 보장합니다.

    자세한 내용은 [Active Directory Premium SLA](http://azure.microsoft.com/support/legal/sla/)를 참조하세요.

- **나중 쓰기를 사용한 암호 재설정** - 셀프 서비스 암호 재설정을 온-프레미스 디렉터리에 다시 쓸 수 있습니다.

- [Azure Active Directory Connect Health](active-directory-aadconnect-health.md): 온-프레미스 Active Directory 인프라의 상태를 모니터링하고 사용 현황 분석을 가져옵니다.



## Free, Basic 및 Premium 버전 비교

<br> 사용 가능한 버전: ![검사 목록](./media/active-directory-editions/ic195031.png)


<table>
	<tr>
		<th>&#160;</th>
		<th>기능 </th>
		<th>Free Edition </th>
		<th>Basic Edition </th>
		<th>Premium Edition </th>
	</tr>
	<tr>
		<td rowspan="8">
		<p>일반 기능</p>
		</td>
		<td>
		<p>서비스로서의 디렉터리</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /><br />
		최대 50만 개의 개체 [1]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /><br />
		개체 제한 없음</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /><br />
		개체 제한 없음</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UI 또는 Windows PowerShell cmdlet을 사용하는 사용자 및 그룹 관리</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>장치 등록</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SaaS 및 사용자 지정 응용 프로그램에 대한 SSO 기반 사용자 액세스를 위한 액세스 패널 포털</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /><br />
		사용자당 최대 10개 앱 [2]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /><br />
		사용자당 최대 10개 앱 [2]</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /><br />
		앱 제한 없음</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>사용자 기반 응용 프로그램 액세스 관리 및 프로비전</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>클라우드 사용자를 위한 셀프 서비스 암호 변경</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Azure AD Connect - 온-프레미스 디렉터리와 Azure Active Directory 간 동기화용</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>표준 보안 보고서</p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td rowspan="5">
		<p>Premium 및 Basic 기능</p>
		</td>
		<td>
		<p>높은 가용성의 SLA 작동 시간(99.9%)</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>그룹 기반 응용 프로그램 액세스 관리 및 프로비전</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>로그인 및 액세스 패널 페이지에 사용자 지정한 회사 로고 및 색상 적용</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>클라우드 사용자를 위한 셀프 서비스 암호 재설정</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>응용 프로그램 프록시: 온-프레미스 웹 응용 프로그램에 대한 원격 액세스 및 SSO 보안 유지</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td rowspan="9">
		<p>프리미엄 전용 기능</p>
		</td>
		<td>
		<p>고급 응용 프로그램 사용 현황 보고</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>클라우드 사용자를 위한 셀프 서비스 그룹 관리</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>온-프레미스 나중 쓰기를 사용한 셀프 서비스 암호 재설정 </p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>MIM(Microsoft Identity Manager) 사용자 라이선스 - 온-프레미스 ID 및 액세스 관리용</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /> [3]</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>고급 비정상 보안 보고서(기계 학습 기반)</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>
		<a href="http://channel9.msdn.com/Series/EMS/Azure-Cloud-App-Discovery">Cloud App Discovery</a> </p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>클라우드 사용자를 위한 다단계 인증 서비스</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>온-프레미스 사용자를 위한 다단계 인증 서버</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>
		<a href="https://msdn.microsoft.com/ko-KR/library/azure/dn906722.aspx">Azure Active Directory Connect Health</a>: 온-프레미스 Active Directory 인프라의 상태를 모니터링하고 사용 현황 분석을 가져옵니다.</p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p></p>
		</td>
		<td>
		<p>&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;<img id="2b05dce3-938f-4168-9b8f-1f4398cbdb9b" alt="검사 목록" src="./media/active-directory-editions/ic195031.png" title="검사 목록" xmlns="" /></p>
		</td>
	</tr>
</table>


[1] Office 365, Microsoft Intune 또는 디렉터리 서비스로 Azure Active Directory를 사용하는 다른 모든 Microsoft 온라인 서비스에 대해 50만 개 개체 제한이 적용되지 않습니다.

[2] 각 SaaS 앱에 대한 액세스 권한이 할당된 최종 사용자는 Azure Active Directory Free 및 Azure Active Directory Basic을 사용하여 액세스 패널에서 최대 10개의 앱을 볼 수 있고 SSO 방식으로 액세스할 수 있습니다(관리자가 먼저 SSO로 해당 사용자를 구성한 경우). 관리자는 Free 버전을 사용하여 SSO를 구성하고 원하는 수만큼의 SaaS 앱에 사용자 액세스를 할당할 수 있지만 최종 사용자는 액세스 패널에서 한 번에 10개의 앱만 볼 수 있습니다.

[3] Microsoft Identity Manager 서버 소프트웨어 권한은 Windows Server 라이선스(모든 버전)와 함께 부여됩니다. Microsoft Identity Manager는 Windows Server 운영 체제에서 실행되기 때문에 서버가 Windows Server의 유효하고 라이선스가 부여된 복사본을 실행하는 한 Microsoft Identity Manager를 해당 서버에 설치하고 사용할 수 있습니다. Microsoft Identity Manager 서버에는 별도의 다른 라이선스가 필요하지 않습니다.



<br> <br>









## 현재 공개 미리 보기에 제공되는 기능

다음 기능은 현재 공개 미리 보기에 제공되며 곧 추가될 예정입니다.

- [관리 단위](https://msdn.microsoft.com/library/azure/dn832057.aspx): 사용자의 하위 집합에 대해 관리 권한을 위임하고 사용자의 하위 집합에 정책을 적용하는 데 사용할 수 있는 리소스의 새 Azure Active Directory 컨테이너입니다.
- Azure Active Directory에 [자체 SaaS 응용 프로그램을 추가](https://msdn.microsoft.com/library/azure/dn893637.aspx)합니다.
- Facebook, Twitter 및 LinkedIn에 대한 암호 롤오버. 자세한 내용은 [이 문서](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)(영문)를 읽어보세요.
- 동적 그룹 멤버 자격입니다. 자세한 내용은 [이 문서](https://msdn.microsoft.com/library/azure/dn913807.aspx)(영문)를 읽어보세요.
- [조건부 액세스](https://msdn.microsoft.com/library/azure/dn906877.aspx): 응용 프로그램별 Multi-Factor Authentication
- HR 응용 프로그램 통합: Workday
- 권한이 부여된 ID 관리: 권한이 부여된 ID 관리는 서비스 수준 계약 및 규정 준수 요구 사항을 충족할 수 있도록 향상된 감독을 제공합니다.
- 셀프서비스 응용 프로그램 요청: 관리자는 SaaS 앱의 목록을 제공하여 사용자가 사용할 앱을 선택하고 즉시 또는 승인 후 앱을 사용할 수 있습니다.
- Azure 보고 API: Azure Active Directory의 모든 보안 보고서에 대한 데이터를 다른 모니터링 또는 SIEM 도구에 사용할 수 있습니다.


## 다음 단계

- [Azure Active Directory Premium 시작](active-directory-get-started-premium.md)
- [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가하기](active-directory-add-company-branding.md)
- [액세스 및 사용 보고서 보기](active-directory-view-access-usage-reports.md)

<!---HONumber=Oct15_HO2-->