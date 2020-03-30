---
title: 테넌트 제한을 사용하여 SaaS 앱 - Azure AD에 대한 액세스를 관리합니다.
description: 테넌트 제한을 사용하여 Azure AD 테넌트를 기반으로 앱에 액세스할 수 있는 사용자를 관리하는 방법
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
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481180"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>테넌트 제한을 사용하여 SaaS 클라우드 애플리케이션에 대한 액세스 관리

보안을 강조하는 대규모 조직에서는 Office 365와 같은 클라우드 서비스로 이동하려고 하지만, 사용자가 승인된 리소스에만 액세스할 수 있다는 사실을 인식하고 있어야 합니다. 일반적으로 회사에서는 액세스를 관리하려는 경우에 도메인 이름 또는 IP 주소를 제한합니다. 이 방법은 서비스(또는 SaaS) 앱으로 소프트웨어가 공용 클라우드에서 호스팅되어 [outlook.office.com](https://outlook.office.com/) 및 [login.microsoftonline.com](https://login.microsoftonline.com/)같은 공유 도메인 이름에서 실행되는 환경에서 는 실패합니다. 이러한 주소를 차단하면 사용자가 단순히 승인된 ID 및 리소스로만 제한되는 것이 아니라 웹상의 Outlook에 완전히 액세스할 수 없게 됩니다.

이 문제에 대한 Azure Active Directory(Azure AD) 솔루션은 테넌트 제한이라는 기능입니다. 테넌트 제한을 통해 조직은 응용 프로그램이 단일 사인온에 사용하는 Azure AD 테넌트를 기반으로 SaaS 클라우드 응용 프로그램에 대한 액세스를 제어할 수 있습니다. 예를 들어 이러한 동일한 애플리케이션의 다른 조직 인스턴스에 대한 액세스를 차단하면서 조직의 Office 365 애플리케이션에 대한 액세스를 허용하려고 할 수 있습니다.  

테넌트 제한을 통해 조직은 사용자가 액세스할 수 있는 테넌트 목록을 지정할 수 있습니다. 그러면 Azure AD는 이러한 허용된 테넌트에 대해서만 액세스를 허가합니다.

이 문서에서는 Office 365에 대한 테넌트 제한에 중점을 두지만 이 기능은 단일 사인온을 위해 Azure AD를 사용하여 최신 인증 프로토콜을 사용하는 모든 SaaS 클라우드 앱에서 작동해야 합니다. Office 365에서 사용되는 테넌트와는 다른 Azure AD 테넌트에서 SaaS 앱을 사용하는 경우 필요한 모든 테넌트가 허용되는지 확인합니다. 클라우드 SaaS 앱에 대한 자세한 내용은 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)를 참조하세요.

## <a name="how-it-works"></a>작동 방법

전체 솔루션은 다음 구성 요소로 구성됩니다.

1. **Azure AD**: `Restrict-Access-To-Tenants: <permitted tenant list>` Azure AD가 있는 경우 Azure AD는 허용된 테넌에 대해서만 보안 토큰을 발급합니다.

2. **온-프레미스 프록시 서버 인프라**: 이 인프라는 TLS(전송 계층 보안) 검사가 가능한 프록시 장치입니다. 허용된 테넌자 목록이 포함된 헤더를 Azure AD로 향하는 트래픽에 삽입하도록 프록시를 구성해야 합니다.

3. **클라이언트 소프트웨어**: 테넌트 제한을 지원하려면 클라이언트 소프트웨어가 Azure AD에서 직접 토큰을 요청해야 프록시 인프라가 트래픽을 가로챌 수 있습니다. 브라우저 기반 Office 365 응용 프로그램은 현재 테넌트 제한을 지원하며, OAuth 2.0과 같이 최신 인증을 사용하는 Office 클라이언트도 지원합니다.

4. **최신 인증**: 클라우드 서비스는 테넌트 제한을 사용하고 허용되지 않는 모든 테넌트에 대한 액세스를 차단하기 위해 최신 인증을 사용해야 합니다. 기본적으로 최신 인증 프로토콜을 사용하도록 Office 365 클라우드 서비스를 구성해야 합니다. 최신 인증에 대한 Office 365 지원과 관련된 최신 정보를 보려면 [업데이트된 Office 365 최신 인증](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)을 참조하세요.

다음 다이어그램은 고급 트래픽 흐름을 보여 줍니다. 테넌트 제한은 Office 365 클라우드 서비스가 아닌 Azure AD로의 트래픽에 대해서만 TLS 검사를 요구합니다. Azure AD에 대한 인증용 트래픽 볼륨이 일반적으로 Exchange Online 및 SharePoint Online과 같은 SaaS 응용 프로그램에 대한 트래픽 볼륨보다 훨씬 낮기 때문에 이러한 구분이 중요합니다.

![테넌트 제한 트래픽 흐름 - 다이어그램](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>테넌트 제한 설정

테넌트 제한을 시작하는 두 단계가 있습니다. 먼저 클라이언트가 올바른 주소에 연결할 수 있는지 확인합니다. 둘째, 프록시 인프라를 구성합니다.

### <a name="urls-and-ip-addresses"></a>URL 및 IP 주소

테넌트 제한을 사용하려면 클라이언트가 다음 Azure AD URL에 연결하여 login.microsoftonline.com, [login.microsoft.com](https://login.microsoft.com/) [login.microsoft.com](https://login.microsoftonline.com/)및 [login.windows.net.](https://login.windows.net/) 또한 Office 365에 액세스하려면 클라이언트가 [Office 365 URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)및 IP 주소 범위에 정의된 정규화된 도메인 이름(FQDN), URL 및 IP 주소에 연결할 수도 있어야 합니다. 

### <a name="proxy-configuration-and-requirements"></a>프록시 구성 및 요구 사항

프록시 인프라를 통해 테넌트 제한을 활성화하려면 다음 구성이 필요합니다. 이 지침은 일반적인 것이므로 보다 구체적인 구현 단계는 프록시 공급업체의 설명서를 참조차세요.

#### <a name="prerequisites"></a>사전 요구 사항

- 프록시는 FQDNs/URL을 사용하여 TLS 가로채기, HTTP 헤더 삽입 및 필터링 대상을 수행할 수 있어야 합니다.

- 클라이언트는 TLS 통신에 대한 프록시가 제공하는 인증서 체인을 신뢰해야 합니다. 예를 들어 [PKI(내부 공개 키 인프라)의](/windows/desktop/seccertenroll/public-key-infrastructure) 인증서를 사용하는 경우 내부 발급 루트 인증서 기관 인증서를 신뢰할 수 있어야 합니다.

- 이 기능은 Office 365 구독에 포함되어 있지만 테넌트 제한을 사용하여 다른 SaaS 앱에 대한 액세스를 제어하려면 Azure AD Premium 1 라이선스가 필요합니다.

#### <a name="configuration"></a>Configuration

Login.microsoftonline.com, login.microsoft.com 및 login.windows.net으로 들어오는 각 요청에 대해 두 개의 HTTP 헤더인 *Restrict-Access-To-Tenants* 및 *Restrict-Access-Context*를 삽입합니다.

헤더에는 다음 요소가 포함되어야 합니다.

- *테넌트에 대한 제한의*경우 사용자가 \<액세스할 수\>있도록 허용하려는 테넌트의 쉼표로 구분된 테넌트 목록인 허용된 테넌트 목록의 값을 사용합니다. 테넌트에 등록된 모든 도메인은 이 목록의 테넌트를 식별하는 데 사용할 수 있습니다. 예를 들어 Contoso 및 Fabrikam 테넌트 모두에 대한 액세스를 허용하기 위해 이름/값 쌍은 다음과 같이 지정됩니다.  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- *제한-액세스 컨텍스트의*경우 테넌트 제한을 설정하는 테넌트를 선언하는 단일 디렉터리 ID 값을 사용합니다. 예를 들어 Contoso를 테넌트 제한 정책을 설정한 테넌트로 선언하려면 이름/값 쌍은 다음과 같습니다. `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> [Azure Active Directory 포털에서](https://aad.portal.azure.com/)디렉터리 ID를 찾을 수 있습니다. 관리자로 로그인한 후 **Azure Active Directory**를 선택하고 **속성**을 선택합니다.

사용자가 승인되지 않은 테넌트가 있는 자신의 HTTP 헤더를 삽입하지 못하도록 하려면 프록시가 들어오는 요청에 이미 있는 경우 *제한 액세스-테넌트* 헤더를 교체해야 합니다.

클라이언트는 Login.microsoftonline.com, login.microsoft.com 및 login.windows.net에 대한 모든 요청에 강제로 해당 프록시를 사용하도록 지정되어야 합니다. 예를 들어 PAC 파일이 클라이언트가 프록시를 사용하도록 지시하는 데 사용되는 경우 최종 사용자는 PAC 파일을 편집하거나 사용하지 않도록 설정할 수 없습니다.

## <a name="the-user-experience"></a>사용자 환경

이 섹션에서는 최종 사용자와 관리자 모두의 환경을 설명합니다.

### <a name="end-user-experience"></a>최종 사용자 환경

예제 사용자는 Contoso 네트워크에 있지만 Outlook 온라인과 같은 공유 SaaS 애플리케이션의 Fabrikam 인스턴스에 액세스하려고 합니다. Fabrikam이 Contoso 인스턴스에 대해 허용되지 않는 테넌트인 경우 사용자는 IT 부서에서 승인하지 않은 조직에 속한 리소스에 액세스하려고 하는 액세스 거부 메시지를 볼 수 있습니다.

### <a name="admin-experience"></a>관리자 환경

테넌트 제한 구성은 회사 프록시 인프라에서 수행되지만 관리자는 Azure Portal에서 테넌트 제한 보고서에 직접 액세스할 수 있습니다. 보고서를 보려면 다음을 수행하십시오.

1. Azure Active [디렉터리 포털에](https://aad.portal.azure.com/)로그인합니다. **Azure Active Directory 관리 센터** 대시보드가 나타납니다.

2. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. Azure Active 디렉터리 개요 페이지가 나타납니다.

3. 기타 **기능** 제목에서 **테넌트 제한을**선택합니다.

제한된 액세스 컨텍스트 테넌트로 지정된 테넌트의 관리자는 이 보고서를 사용하여 사용된 ID 및 대상 디렉터리 ID를 포함하여 테넌트 제한 정책으로 인해 차단된 로그인을 볼 수 있습니다. 이러한 제한을 설정하는 테넌트가 로그인에 대해 사용자 테넌트이거나 리소스 테넌트인 경우 로그인이 포함됩니다.

> [!NOTE]
> 보고서에는 제한된 액세스 컨텍스트 테넌트가 아닌 테넌트에 있는 사용자가 서명할 때 대상 디렉터리 ID와 같은 제한된 정보가 포함될 수 있습니다. 이 경우 이름 및 사용자 주체 이름과 같은 사용자 식별 정보는 다른 테넌트에서 사용자 데이터를 보호하기 위해 마스로 지정됩니다.

Azure Portal의 다른 보고서와 마찬가지로, 필터를 사용하여 보고서의 범위를 지정할 수 있습니다. 특정 시간 간격, 사용자, 응용 프로그램, 클라이언트 또는 상태를 필터링할 수 있습니다. **열 단추를** 선택하면 다음 필드의 조합으로 데이터를 표시하도록 선택할 수 있습니다.

- **사용자**
- **애플리케이션**
- **상태**
- **날짜**
- **날짜(UTC) (UTC가** 유니버설 타임으로 조정되는 위치)
- **MFA 인증** 방법(다단계 인증 방법)
- **MFA 인증 세부** 정보(다단계 인증 세부 정보)
- **MFA 결과**
- **IP 주소**
- **클라이언트**
- **사용자**
- **위치**
- **대상 테넌트 ID**

## <a name="office-365-support"></a>Office 365 지원

Office 365 응용 프로그램은 테넌트 제한을 완전히 지원하기 위해 두 가지 기준을 충족해야 합니다.

1. 사용된 클라이언트는 최신 인증을 지원합니다.
2. 최신 인증이 클라우드 서비스에 대한 기본 인증 프로토콜로 사용되어야 합니다.

현재 최신 인증을 지원하는 Office 클라이언트에 대한 최신 정보를 보려면 [업데이트된 Office 365 최신 인증](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)을 참조하세요. 해당 페이지에는 특정 Exchange Online 및 비즈니스용 Skype Online 테넌트에 대해 최신 인증을 사용하도록 설정하기 위한 지침으로 연결되는 링크도 포함되어 있습니다. SharePoint Online은 이미 기본적으로 최신 인증을 사용할 수 있습니다.

Office 365 브라우저 기반 응용 프로그램(Office 포털, Yammer, SharePoint 사이트, 웹의 Outlook 등)은 현재 테넌트 제한을 지원합니다. 두꺼운 클라이언트(Outlook, 비즈니스용 Skype, 워드, 엑셀, 파워포인트 등)는 최신 인증을 사용하는 경우에만 테넌트 제한을 적용할 수 있습니다.  

최신 인증을 지원하는 Outlook 및 Skype For Business 클라이언트는 최신 인증을 사용할 수 없는 테넌트에 대해 레거시 프로토콜을 계속 사용할 수 있으며 테넌트 제한을 효과적으로 우회할 수 있습니다. 테넌트 제한은 인증 중에 login.microsoftonline.com, login.microsoft.com 또는 login.windows.net 연락하는 경우 레거시 프로토콜을 사용하는 응용 프로그램을 차단할 수 있습니다.

Windows의 Outlook에서 고객은 최종 사용자가 프로필에 승인되지 않은 메일 계정을 추가하는 것을 방지하는 제한을 구현하도록 선택할 수 있습니다. 예제를 보려면 [비기본 Exchange 계정 추가 금지](https://gpsearch.azurewebsites.net/default.aspx?ref=1) 그룹 정책 설정을 참조하세요.

## <a name="testing"></a>테스트

전체 조직에 대해 테넌트 제한을 구현하기 전에 테넌트 제한을 사용해 보려면 Fiddler와 같은 도구를 사용하는 호스트 기반 접근 방식 또는 프록시 설정의 단계적 롤아웃이라는 두 가지 옵션이 있습니다.

### <a name="fiddler-for-a-host-based-approach"></a>호스트 기반 접근 방식을 위한 Fiddler

Fiddler는 HTTP 헤더 삽입을 비롯하여 HTTP/HTTPS 트래픽을 캡처하고 수정하는 데 사용할 수 있는 무료 웹 디버깅 프록시입니다. 테넌트 제한을 테스트하도록 Fiddler를 구성하려면 다음 단계를 수행합니다.

1. [다운로드 및 Fiddler를 설치합니다.](https://www.telerik.com/fiddler)

2. [Fiddler 도움말 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)에 따라 HTTPS 트래픽 암호를 해독하도록 Fiddler를 구성합니다.

3. 사용자 지정 규칙을 사용하여 *Restrict-Access-To-Tenants* 및 *Restrict-Access-Context* 머리글을 삽입하도록 Fiddler를 구성합니다.

   1. Fiddler Web Debugger 도구에서 **Rules** 메뉴를 선택하고 **Customize Rules...** 를 선택하여 CustomRules 파일을 엽니다.

   2. 함수의 시작 부분에 다음 `OnBeforeRequest` 줄을 추가합니다. 테넌트\> 도메인을 테넌트에 등록된 `contoso.onmicrosoft.com`도메인(예: )으로 바꿉니다. \< \<directory ID\>를 테넌트의 Azure AD GUID 식별자로 바꿉니다.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      여러 테넌트를 허용해야 하는 경우 쉼표로 테넌트 이름을 구분합니다. 예를 들어:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. CustomRules 파일을 저장한 후 닫습니다.

Fiddler를 구성한 후 **파일** 메뉴로 이동하고 **트래픽 캡처**를 선택하여 트래픽을 캡처할 수 있습니다.

### <a name="staged-rollout-of-proxy-settings"></a>프록시 설정의 단계별 롤아웃

프록시 인프라의 기능에 따라 사용자에게 설정을 단계별로 롤아웃할 수 있습니다. 다음은 고려해야 할 몇 가지 고급 옵션입니다.

1. 일반 사용자는 프로덕션 프록시 인프라를 계속 사용할 수 있지만 PAC 파일을 사용하여 테스트 사용자는 테스트 프록시 인프라를 가리키도록 합니다.
2. 일부 프록시 서버는 그룹을 사용하여 다양한 구성을 지원할 수 있습니다.

자세한 내용은 프록시 서버 설명서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

- [업데이트된 Office 365 최신 인증](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) 참조
- [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 검토
