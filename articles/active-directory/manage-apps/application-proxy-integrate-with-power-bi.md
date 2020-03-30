---
title: Azure AD 응용 프로그램 프록시를 사용하여 Power BI에 대한 원격 액세스 사용
description: 온-프레미스 Power BI를 Azure AD 응용 프로그램 프록시와 통합하는 방법에 대한 기본 사항에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111580"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 사용하여 Power BI Mobile에 대한 원격 액세스 설정

이 문서에서는 Azure AD 응용 프로그램 프록시를 사용하여 Power BI 모바일 앱이 PBIRS(Power BI 보고서 서버) 및 SSRS(SQL Server 보고 서비스) 2016 이상에 연결할 수 있도록 하는 방법에 대해 설명합니다. 이 통합을 통해 회사 네트워크에서 멀리 떨어져 있는 사용자는 Power BI 모바일 앱에서 Power BI 보고서에 액세스하고 Azure AD 인증으로 보호할 수 있습니다. 이 보호에는 조건부 액세스 및 다단계 인증과 같은 [보안 이점이](application-proxy-security.md#security-benefits) 포함됩니다.  

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 보고서 서비스를 이미 배포하고 [응용 프로그램 프록시를 사용하도록 설정한](application-proxy-add-on-premises-application.md)것으로 가정합니다.

- 응용 프로그램 프록시를 사용하도록 설정하려면 커넥터가 Azure AD 서비스와 통신할 수 있도록 Windows 서버에 커넥터를 설치하고 [필수 구성 조건을](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) 완료해야 합니다.  
- Power BI를 게시할 때동일한 내부 및 외부 도메인을 사용하는 것이 좋습니다. 사용자 지정 도메인에 대한 자세한 내용은 [응용 프로그램 프록시의 사용자 지정 도메인 작업을](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)참조하십시오.
- 이 통합은 **Power BI 모바일 iOS 및 Android** 응용 프로그램에 사용할 수 있습니다.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1단계: 커베로스 구속위임(KCD) 구성

Windows 인증을 사용하는 온-프레미스 애플리케이션에 대해 Kerberos 인증 프로토콜 및 KCD(Kerberos 제한 위임)라는 기능을 사용하여 SSO(Single Sign-On)를 획득할 수 있습니다. 구성할 때 KCD를 사용하면 사용자가 Windows에 직접 로그인하지 않은 경우에도 응용 프로그램 프록시 커넥터에서 사용자에 대한 Windows 토큰을 가져올 수 있습니다. KCD에 대한 자세한 내용은 [Kerberos 제한된 위임 개요](https://technet.microsoft.com/library/jj553400.aspx) 및 [응용 프로그램 프록시를 사용하여 앱에 대한 단일 사인온에 대한 Kerberos 제한 위임을](application-proxy-configure-single-sign-on-with-kcd.md)참조하십시오.

Reporting Services 쪽에서 구성할 항목이 많지 않습니다. 적절한 Kerberos 인증이 수행되도록 하려면 유효한 SPN(서비스 주체 이름)이 있어야 합니다. 또한 보고 서비스 서버가 인증 협상에 사용하도록 설정되어 있는지 확인합니다.

보고 서비스에 대한 KCD를 설정하려면 다음 단계를 계속합니다.

### <a name="configure-the-service-principal-name-spn"></a>서비스 주체 이름 구성(SPN)

SPN은 Kerberos 인증을 사용하는 서비스에 대한 고유한 식별자입니다. 보고서 서버에 적합한 HTTP SPN이 있는지 확인해야 합니다. 보고서 서버에 대한 적절한 SPN(서비스 주체 이름)을 구성하는 방법에 대한 자세한 내용은 [보고서 서버에 SPN(서비스 주체 이름) 등록](https://msdn.microsoft.com/library/cc281382.aspx)을 참조하세요.
-L 옵션과 함께 Setspn 명령을 실행하여 SPN이 추가되었는지 확인할 수 있습니다. 이 명령에 대해 자세히 알아보려면 [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)을 참조하세요.

### <a name="enable-negotiate-authentication"></a>인증 협상 사용

보고서 서버가 Kerberos 인증을 사용하도록 설정하려면 보고서 서버의 인증 유형을 RSWindowsNegotiate로 구성합니다. rsreportserver.config 파일을 사용하여 이 설정을 구성합니다.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

자세한 내용은 [Reporting Services 구성 파일 수정](https://msdn.microsoft.com/library/bb630448.aspx) 및 [보고서 서버에서 Windows 인증 구성](https://msdn.microsoft.com/library/cc281253.aspx)을 참조하세요.

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>보고 서비스 응용 프로그램 풀 계정에 추가된 SPN에 대한 위임에 대해 커넥터를 신뢰할 수 있는지 확인
Azure AD 응용 프로그램 프록시 서비스가 보고 서비스 응용 프로그램 풀 계정에 사용자 ID를 위임할 수 있도록 KCD를 구성합니다. 애플리케이션 프록시 커넥터에서 Azure AD에서 인증된 사용자에 대한 Kerberos 티켓을 검색할 수 있도록 하여 KCD를 구성합니다. 그런 다음 해당 서버는 컨텍스트를 대상 응용 프로그램 또는 이 경우 보고 서비스로 전달합니다.

KCD를 구성하려면 각 커넥터 컴퓨터에 대해 다음 단계를 반복합니다.

1. 도메인 관리자로 도메인 컨트롤러에 로그인한 다음 **Active Directory 사용자 및 컴퓨터를**엽니다.
2. 커넥터가 실행 중인 컴퓨터를 찾습니다.  
3. 컴퓨터를 두 번 클릭한 다음 **위임** 탭을 선택합니다.
4. 위임 설정을 **지정한 서비스에만 위임하기 위해 이 컴퓨터를 신뢰로 설정합니다.** 그런 다음 **모든 인증 프로토콜 사용**을 선택합니다.
5. **추가를**선택한 다음 **사용자 또는 컴퓨터**를 선택합니다.
6. 보고 서비스에 사용 중이는 서비스 계정을 입력합니다. Reporting Services 구성 내에서 SPN을 추가할 계정입니다.
7. **확인**을 클릭합니다. 변경 내용을 저장하려면 **확인을** 다시 클릭합니다.

자세한 내용은 [응용 프로그램 프록시를 사용하여 앱에 대한 단일 사인온에 대한 Kerberos 제한 위임을](application-proxy-configure-single-sign-on-with-kcd.md)참조하십시오.

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>2단계: Azure AD 응용 프로그램 프록시를 통해 보고서 서비스 게시

이제 Azure AD 응용 프로그램 프록시를 구성할 준비가 되었습니다.

1. 다음 설정을 통해 응용 프로그램 프록시를 통해 보고서 서비스를 게시합니다. 응용 프로그램 프록시를 통해 응용 프로그램을 게시하는 방법에 대한 단계별 지침은 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시를](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)참조하십시오.
   - **내부 URL**: 커넥터가 회사 네트워크에서 연결할 수 있는 보고서 서버에 대한 URL을 입력합니다. 커넥터가 설치된 서버에서 이 URL에 연결할 수 있는지 확인합니다. 가장 좋은 방법은 응용 프로그램 프록시를 `https://servername/` 통해 게시된 하위 경로문제를 방지하는 것과 같은 최상위 도메인을 사용하는 것입니다. 예를 들어, `https://servername/` 사용 `https://servername/reports/` `https://servername/reportserver/`및 하지 또는 .
     > [!NOTE]
     > 보고서 서버에 대한 안전한 HTTPS 연결을 사용하는 것이 좋습니다. [기본 모드 보고서 서버에서 SSL 연결 구성](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) 방법을 참조하세요.
   - **외부 URL**: Power BI 모바일 앱이 연결할 공용 URL을 입력합니다. 예를 들어 사용자 지정 `https://reports.contoso.com` 도메인이 사용되는 경우처럼 보일 수 있습니다. 사용자 지정 도메인을 사용하려면 도메인에 대한 인증서를 업로드하고 DNS 레코드를 응용 프로그램의 기본 msappproxy.net 도메인으로 가리킵니다. 자세한 단계는 [Azure AD 응용 프로그램 프록시의 사용자 지정 도메인 작업을](application-proxy-configure-custom-domain.md)참조하십시오.

   - **사전 인증 방법**: Azure Active Directory

2. 앱이 게시된 후에는 다음 단계에 따라 Single Sign-On 설정을 구성합니다.

   a. 포털의 애플리케이션 페이지에서 **Single Sign-On**을 선택합니다.

   b. **단일 사인온 모드의**경우 **통합 Windows 인증을**선택합니다.

   다. **내부 응용 프로그램 SPN을** 이전에 설정한 값으로 설정합니다.  

   d. 커넥터에 대한 **위임된 로그인 ID**를 선택하여 사용자를 대신하여 사용합니다. 자세한 내용은 [다른 온-프레미스 및 클라우드 ID로 작업하는 것을](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)참조하십시오.

   e. **저장**을 클릭하여 변경 내용을 저장합니다.

응용 프로그램 설정을 완료하려면 **사용자 및 그룹 섹션으로** 이동하여 사용자가 이 응용 프로그램에 액세스할 수 있도록 할당합니다.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>3 단계: 응용 프로그램에 대 한 회신 URI를 수정

Power BI 모바일 앱이 보고서 서비스에 연결하고 액세스하려면 먼저 2단계에서 자동으로 생성된 응용 프로그램 등록을 구성해야 합니다. 

1. Azure Active Directory **개요** 페이지에서 **앱 등록을 선택합니다.**
2. 모든 **응용 프로그램** 탭에서 2단계에서 만든 응용 프로그램을 검색합니다.
3. 응용 프로그램을 선택한 다음 **인증을**선택합니다.
4. 사용 중인 플랫폼에 따라 다음 리디렉션 URI를 추가합니다.

   Power BI Mobile **iOS용**앱을 구성할 때 공용 클라이언트(모바일 & 데스크톱)의 다음 리디렉션 URI를 추가합니다.
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Power BI 모바일 **Android용**앱을 구성할 때 공용 클라이언트 유형(모바일 & 데스크톱)의 다음 리디렉션 URI를 추가합니다.
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > 응용 프로그램이 올바르게 작동하려면 리디렉션 URI를 추가해야 합니다. Power BI Mobile iOS 및 Android 모두에 대해 앱을 구성하는 경우 iOS용으로 구성된 리디렉션 URI 목록에 공용 클라이언트(모바일 & 데스크톱)의 `urn:ietf:wg:oauth:2.0:oob`다음 리디렉션 URI를 추가합니다.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>4단계: Power BI 모바일 앱에서 연결

1. Power BI 모바일 앱에서 보고 서비스 인스턴스에 연결합니다. 이렇게 하려면 응용 프로그램 프록시를 통해 게시한 응용 프로그램의 **외부 URL을** 입력합니다.

   ![외부 URL을 사용 하 고 전원 BI 모바일 앱](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. **연결**을 선택합니다. Azure Active Directory 로그인 페이지로 이동합니다.

3. 사용자에 대해 유효한 자격 증명을 입력하고 **로그인을**선택합니다. 보고 서비스 서버의 요소가 표시됩니다.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>5단계: 관리되는 장치에 대한 Intune 정책 구성(선택 사항)

Microsoft Intune을 사용하여 회사의 인력이 사용하는 클라이언트 앱을 관리할 수 있습니다. Intune을 사용하면 데이터 암호화 및 추가 액세스 요구 사항과 같은 기능을 사용할 수 있습니다. Intune을 통해 앱 관리에 대해 자세히 알아보려면 Intune 앱 관리를 참조하세요. Power BI 모바일 응용 프로그램이 Intune 정책으로 작동하도록 하려면 다음 단계를 사용합니다.

1. Azure **Active Directory로** 이동한 다음 **앱 등록으로 이동합니다.**
2. 네이티브 클라이언트 응용 프로그램을 등록할 때 3단계에서 구성된 응용 프로그램을 선택합니다.
3. 응용 프로그램 페이지에서 API **사용 권한을 선택합니다.**
4. **권한 추가를**클릭합니다. 
5. **API에서 조직에서 사용**하 여 " Microsoft 모바일 응용 프로그램 관리"를 검색 하 고 선택 합니다.
6. 응용 프로그램에 **장치 관리관리앱.ReadWrite** 권한 추가
7. 응용 프로그램에 대한 권한 액세스 권한을 부여하려면 **관리자 동의 부여를** 클릭합니다.
8. [앱 보호 정책을 만들고 할당하는 방법을 참조하여](https://docs.microsoft.com/intune/app-protection-policies)원하는 Intune 정책을 구성합니다.

## <a name="troubleshooting"></a>문제 해결

몇 분 이상 보고서를 로드한 후 응용 프로그램에서 오류 페이지를 반환하는 경우 시간 초과 설정을 변경해야 할 수 있습니다. 기본적으로 응용 프로그램 프록시는 요청에 응답하는 데 최대 85초가 걸리는 응용 프로그램을 지원합니다. 이 설정을 180초로 연장하려면 응용 프로그램의 앱 프록시 설정 페이지에서 백 엔드 시간 설정을 **Long으로** 선택합니다. 빠르고 신뢰할 수 있는 보고서를 만드는 방법에 대한 팁은 [Power BI 보고서 모범 사례를](https://docs.microsoft.com/power-bi/power-bi-reports-performance)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [네이티브 클라이언트 응용 프로그램이 프록시 응용 프로그램과 상호 작용할 수 있도록 설정](application-proxy-configure-native-client-application.md)
- [Power BI 모바일 앱에서 온-프레미스 보고서 서버 보고서 및 KPI 보기](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
