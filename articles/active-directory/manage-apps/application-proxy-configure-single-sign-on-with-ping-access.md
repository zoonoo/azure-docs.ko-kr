---
title: Azure AD 애플리케이션 프록시용 PingAccess를 통한 헤더 기반 인증 | Microsoft Docs
description: PingAccess 및 앱 프록시를 사용하여 애플리케이션을 게시하여 헤더 기반 인증을 지원합니다.
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
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367987"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>애플리케이션 프록시 및 PingAccess를 사용하여 Single Sign-On에 대한 헤더 기반 인증

Azure Active Directory(Azure AD) 응용 프로그램 프록시는 PingAccess와 제휴하여 Azure AD 고객이 더 많은 응용 프로그램에 액세스할 수 있도록 합니다. PingAccess는 [기존 애플리케이션 프록시 제품](application-proxy.md)을 확장하여 인증에 헤더를 사용하는 애플리케이션에 대한 Single Sign-On 액세스를 포함하고 있습니다.

## <a name="whats-pingaccess-for-azure-ad"></a>Azure AD에 대한 PingAccess란 무엇입니까?

Azure AD용 PingAccess를 사용하면 인증에 헤더를 사용하는 응용 프로그램에 액세스 및 SSO(단일 사인온)를 사용자에게 제공할 수 있습니다. 애플리케이션 프록시는 이러한 애플리케이션을 Azure AD를 사용하여 액세스를 인증한 다음, 커넥터 서비스를 통해 트래픽을 전달하는 방식으로 다른 앱과 유사하게 처리합니다. PingAccess는 응용 프로그램 앞에 앉아 Azure AD의 액세스 토큰을 헤더로 변환합니다. 그런 다음 응용 프로그램은 읽을 수 있는 형식으로 인증을 받습니다.

사용자는 회사 애플리케이션을 사용하기 위해 로그인할 때 다른 점을 알아차리지 못합니다. 여전히 어디에서든지 모든 디바이스에서 작업할 수 있습니다. Application 프록시 커넥터는 인증 유형에 관계없이 모든 앱으로 원격 트래픽을 직접 연결하므로 로드의 균형을 자동으로 조정합니다.

## <a name="how-do-i-get-access"></a>액세스 권한은 어떻게 얻을 수 있나요?

이 시나리오는 Azure Active Directory와 PingAccess 간의 파트너십에서 비롯되므로 두 서비스에 대한 라이선스가 필요합니다. 그러나 Azure Active Directory Premium 구독에는 최대 20개의 애플리케이션을 보장하는 기본 PingAccess 라이선스가 포함되어 있습니다. 헤더 기반 애플리케이션을 20개 이상 게시해야 하는 경우 PingAccess에서 라이선스를 추가로 구입할 수 있습니다.

자세한 내용은 [Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="publish-your-application-in-azure"></a>Azure에 애플리케이션 게시

이 문서는 사람들이 이 시나리오를 사용하여 처음으로 응용 프로그램을 게시할 수 있도록 하는 것입니다. 게시 단계를 자세히 설명하는 것 외에도 응용 프로그램 프록시와 PingAccess를 모두 시작하는 데 지침이 됩니다. 이미 두 서비스를 모두 구성했지만 게시 단계에서 새로 고침을 원하는 경우 [응용 프로그램 프록시를 사용하여 Azure AD에 응용 프로그램 추가](#add-your-application-to-azure-ad-with-application-proxy) 섹션으로 건너뜁니다.

> [!NOTE]
> 이 시나리오는 Azure AD와 PingAccess 간의 파트너 관계이므로 일부 지침은 Ping ID 사이트에 존재합니다.

### <a name="install-an-application-proxy-connector"></a>애플리케이션 프록시 커넥터 설치

응용 프로그램 프록시를 사용하도록 설정하고 이미 커넥터를 설치한 경우 이 섹션을 건너뛰고 [응용 프로그램 프록시를 사용하여 Azure AD에 응용 프로그램 추가로](#add-your-application-to-azure-ad-with-application-proxy)이동할 수 있습니다.

응용 프로그램 프록시 커넥터는 원격 직원의 트래픽을 게시된 응용 프로그램으로 안내하는 Windows Server 서비스입니다. 자세한 설치 지침은 [자습서: Azure Active Directory의 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가를](application-proxy-add-on-premises-application.md)참조하십시오.

1. 응용 프로그램 관리자로 [Azure Active Directory 포털에](https://aad.portal.azure.com/) 로그인합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
1. **Azure Active Directory** > **응용 프로그램 프록시** > **다운로드 커넥터 서비스**선택합니다. **응용 프로그램 프록시 커넥터 다운로드** 페이지가 나타납니다.

   ![응용 프로그램 프록시 커넥터 다운로드](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. 설치 지침을 따릅니다.

커넥터를 다운로드하면 디렉터리에 대한 응용 프로그램 프록시가 자동으로 활성화되지만 그렇지 않은 경우 **응용 프로그램 프록시 사용 (을)** 선택할 수 있습니다.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>응용 프로그램 프록시를 사용하여 Azure AD에 응용 프로그램 추가

Azure Portal에서 수행해야 하는 두 가지 작업이 있습니다. 먼저 애플리케이션 프록시를 사용하여 애플리케이션을 게시해야 합니다. 그런 다음 PingAccess 단계에서 사용할 수 있는 응용 프로그램에 대한 몇 가지 정보를 수집해야 합니다.

#### <a name="publish-your-application"></a>애플리케이션 게시

먼저 응용 프로그램을 게시해야 합니다. 이 작업에는 다음이 포함됩니다.

- Azure AD에 온-프레미스 응용 프로그램 추가
- 응용 프로그램을 테스트하고 헤더 기반 SSO를 선택하기 위해 사용자 할당
- 응용 프로그램의 리디렉션 URL 설정
- 온-프레미스 응용 프로그램을 사용할 사용자 및 기타 응용 프로그램에 대한 권한 부여

사용자 고유의 온-프레미스 응용 프로그램을 게시하려면 다음을 수행하십시오.

1. 마지막 섹션에 없는 경우 응용 프로그램 관리자로 [Azure Active Directory 포털에](https://aad.portal.azure.com/) 로그인합니다.
1. **엔터프라이즈 응용 프로그램** > 새**응용 프로그램** > **선택 온-프레미스 응용 프로그램 추가.** **사용자 고유의 온-프레미스 응용 프로그램 추가** 페이지가 나타납니다.

   ![자체 온-프레미스 응용 프로그램 추가](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 새 응용 프로그램에 대한 정보가 필요한 필드를 작성합니다. 설정에 대 한 아래 지침을 사용 합니다.

   > [!NOTE]
   > 이 단계의 자세한 내용은 Azure [AD에 온-프레미스 앱 추가를](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)참조하십시오.

   1. **내부 URL**: 일반적으로 회사 네트워크에있을 때 앱의 로그인 페이지로 이동합니다. 이 시나리오에서 커넥터는 PingAccess 프록시를 응용 프로그램의 첫 페이지로 처리해야 합니다. `https://<host name of your PingAccess server>:<port>` 형식을 사용합니다. 포트는 기본적으로 3000이지만 PingAccess에서 구성할 수 있습니다.

      > [!WARNING]
      > 이러한 유형의 단일 사인온의 경우 내부 `https` URL을 사용해야 `http`하며 사용할 수 없습니다.

   1. **사전 인증 방법**: **Azure Active Directory를**선택합니다.
   1. **헤더의 URL 번역**: **아니요를**선택합니다.

   > [!NOTE]
   > 첫 번째 애플리케이션인 경우 3000 포트를 사용하여 시작하고 PingAccess 구성을 변경하면 다시 돌아와서 이 설정을 업데이트합니다. 후속 응용 프로그램의 경우 포트는 PingAccess에서 구성한 수신기와 일치해야 합니다. [PingAccess의 수신기](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)에 대해 자세히 알아봅니다.

1. **추가**를 선택합니다. 새 응용 프로그램의 개요 페이지가 나타납니다.

이제 응용 프로그램 테스트를 위해 사용자를 할당하고 헤더 기반 단일 사인온을 선택합니다.

1. 응용 프로그램 사이드바에서 **사용자 및 그룹** > 사용자**및\<그룹 추가(숫자****Add user** > > 선택됨)를 선택합니다. 선택할 수 있는 사용자 및 그룹 목록이 나타납니다.

   ![사용자 및 그룹 목록 표시](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. 응용 프로그램 테스트를 위해 사용자를 선택하고 **을**선택합니다. 이 테스트 계정에 온-프레미스 애플리케이션에 대한 액세스 권한이 있는지 확인합니다.
1. **을 선택합니다.**
1. 응용 프로그램 사이드바에서 **단일 사인온** > **헤더 기반**을 선택합니다.

   > [!TIP]
   > 헤더 기반 Single Sign-On을 처음 사용하는 경우 PingAccess를 설치해야 합니다. Azure 구독이 PingAccess 설치와 자동으로 연결되도록 하려면 이 Single Sign-On 페이지의 링크를 사용하여 PingAccess를 다운로드합니다. 지금 다운로드 사이트를 열거나 나중에 이 페이지로 다시 돌아올 수 있습니다.

   ![헤더 기반 사인온 화면 및 PingAccess 표시](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. **저장**을 선택합니다.

그런 다음 리디렉션 URL이 외부 URL로 설정되어 있는지 확인합니다.

1. Azure **Active Directory 관리 센터** 사이드바에서 **Azure Active Directory** > **앱 등록을 선택합니다.** 응용 프로그램 목록이 나타납니다.
1. 애플리케이션을 선택합니다.
1. 웹 및 공용 클라이언트에 대해 설정된 리디렉션 URI 수를 표시하는 **리디렉션 URI**옆에 있는 링크를 선택합니다. **응용 프로그램 이름> - 인증 페이지가 나타납니다. \<**
1. 이전에 응용 프로그램에 할당한 외부 URL이 **리디렉션 URI 목록에** 있는지 확인합니다. 그렇지 않은 경우 웹의 리디렉션 URI 유형을 사용하여 지금 **Web**외부 URL을 추가하고 **저장을**선택합니다.

마지막으로 사용자가 읽기 액세스 권한을 가지도록 온-프레미스 응용 프로그램을 설정하고 다른 응용 프로그램에서 읽기/쓰기 액세스 권한을 갖도록 합니다.

1. 응용 프로그램에 대한 **앱 등록** 사이드바에서 API **사용 권한을** > **선택하려면 권한** > 추가**Microsoft API** > **Microsoft 그래프**. Windows Azure Active Directory용 **API를** 포함하는 **Microsoft 그래프에** 대한 API 요청 권한 페이지가 나타납니다.

   ![API 권한 요청 페이지 표시](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. 위임된 사용 권한**사용자** > **사용자 읽기**를 **선택합니다.** > 
1. **응용 프로그램 권한 응용** > 프로그램**응용** > **프로그램을 선택합니다.ReadWrite.All**.
1. **권한 추가**를 선택합니다.
1. API **권한** 페이지에서 **디렉터리 이름에 대한 \<관리자 동의를>선택합니다. **

#### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess 단계에 대한 정보 수집

PingAccess를 사용하여 응용 프로그램을 설정하려면 다음 세 가지 정보(모든 GUID)를 수집해야 합니다.

| Azure AD 필드의 이름 | PingAccess 필드의 이름 | 데이터 형식 |
| --- | --- | --- |
| **애플리케이션(클라이언트) ID** | **클라이언트 ID** | GUID |
| **디렉터리(테넌트) ID** | **발급자** | GUID |
| `PingAccess key` | **클라이언트 시크릿** | 임의 문자열 |

이 정보를 수집하려면 다음 을 수행하십시오.

1. Azure **Active Directory 관리 센터** 사이드바에서 **Azure Active Directory** > **앱 등록을 선택합니다.** 응용 프로그램 목록이 나타납니다.
1. 애플리케이션을 선택합니다. 응용 프로그램의 **앱 등록 페이지가** 나타납니다.

   ![응용 프로그램에 대한 등록 개요](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. **응용 프로그램(클라이언트) ID** 값 옆에 **있는 클립보드** 복사 아이콘을 선택한 다음 복사하여 저장합니다. 나중에 이 값을 PingAccess의 클라이언트 ID로 지정합니다.
1. 다음으로 **디렉터리(테넌트) ID** 값을 선택한 다음 **클립보드에 복사를**선택한 다음 복사하여 저장합니다. 나중에 이 값을 PingAccess의 발급자로 지정합니다.
1. 응용 프로그램에 대한 **앱 등록의** 사이드 바에서 **인증서 및 비밀** > **새 클라이언트 비밀을**선택합니다. **클라이언트 비밀 추가 페이지가** 나타납니다.

   ![클라이언트 추가 비밀 페이지 표시](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. **설명에서**을 `PingAccess key`입력합니다.
1. **만료에서**PingAccess 키를 설정하는 방법을 선택합니다: **1년**, **2년**동안 또는 **안 함**.
1. **추가**를 선택합니다. PingAccess 키는 **VALUE** 필드에서 자동 채우는 임의의 문자열과 함께 클라이언트 암호 테이블에 나타납니다.
1. PingAccess 키의 **VALUE** 필드 옆에 **클립보드** 복사 아이콘을 선택한 다음 복사하여 저장합니다. 나중에 이 값을 PingAccess의 클라이언트 보안 으로 지정합니다.

**필드 `acceptMappedClaims` 업데이트:**

1. 응용 프로그램 관리자로 [Azure Active Directory 포털에](https://aad.portal.azure.com/) 로그인합니다.
1. **Azure Active Directory** > **앱 등록을 선택합니다.** 응용 프로그램 목록이 나타납니다.
1. 애플리케이션을 선택합니다.
1. 응용 프로그램에 대한 **앱 등록** 페이지의 사이드바에서 **매니페스트를**선택합니다. 응용 프로그램 등록에 대한 매니페스트 JSON 코드가 나타납니다.
1. `acceptMappedClaims` 필드를 검색하고 값을 로 변경합니다. `True`
1. **저장**을 선택합니다.

### <a name="use-of-optional-claims-optional"></a>선택적 클레임 사용(선택 사항)

선택적 클레임을 사용하면 모든 사용자 및 테넌트가 가지고 있는 표준 청구를 기본값에 포함하지 않는 클레임을 추가할 수 있습니다. 응용 프로그램 매니페스트를 수정하여 응용 프로그램에 대한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 Azure [AD 응용 프로그램 매니페스트 이해 문서를](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/) 참조하십시오.

PingAccess에서 사용할 access_token 전자 메일 주소를 포함하는 예제는 다음과 같습니다.
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>클레임 매핑 정책 사용(선택 사항)

AzureAD에 없는 특성에 대한 [클레임 매핑 정책(미리 보기)입니다.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) 클레임 매핑을 사용하면 ADFS 또는 사용자 개체에 의해 백업되는 추가 사용자 지정 클레임을 추가하여 이전 온프레미 앱을 클라우드로 마이그레이션할 수 있습니다.

응용 프로그램에서 사용자 지정 클레임을 사용하고 추가 필드를 포함하도록 하려면 [사용자 지정 클레임 매핑 정책을 만들고 응용 프로그램에 할당했는지](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)확인해야 합니다.

> [!NOTE]
> 사용자 지정 클레임을 사용하려면 사용자 지정 정책도 정의하고 애플리케이션에 할당해야 합니다. 이 정책은 필요한 모든 사용자 지정 특성을 포함해야 합니다.
>
> PowerShell 또는 Microsoft 그래프를 통해 정책 정의 및 할당을 수행할 수 있습니다. PowerShell에서 작업을 수행하는 경우 먼저 를 사용하여 `New-AzureADPolicy` 응용 프로그램에 할당해야 할 `Add-AzureADServicePrincipalPolicy`수 있습니다. 자세한 내용은 [클레임 매핑 정책 할당을](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)참조하십시오.

예제:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>PingAccess에서 사용자 지정 클레임을 사용하도록 설정

PingAccess에서 사용자 지정 클레임을 사용하도록 설정하는 것은 선택 사항이지만 응용 프로그램에서 추가 클레임을 사용할 것으로 예상되는 경우 필요합니다.

다음 단계에서 PingAccess를 구성할 때 생성할 웹 세션(설정 >액세스 >웹 세션)에는 **프로필 요청이** 선택 해제되고 **사용자 속성 새로 고침이** 아니오로 설정되어 있어야 **합니다.**

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess 다운로드 및 응용 프로그램 구성

이제 Azure Active Directory 설정 단계를 모두 완료했으므로 PingAccess 구성으로 이동할 수 있습니다.

이 시나리오의 PingAccess 부분에 대한 자세한 단계는 Ping ID 설명서에서 계속됩니다. Ping ID 웹 사이트에서 [Microsoft Azure AD 응용 프로그램 프록시를 사용하여 게시된 응용 프로그램을 보호하기 위해 Azure AD용 PingAccess 구성의](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) 지침을 따릅니다.

이러한 단계는 PingAccess를 설치하고 PingAccess 계정을 설정하는 데 도움이 됩니다(아직 없는 경우). 그런 다음 AZURE AD OpenID Connect(OIDC) 연결을 만들려면 Azure AD 포털에서 복사한 **디렉터리(테넌트) ID** 값을 사용하여 토큰 공급자를 설정합니다. 그런 다음 PingAccess에서 웹 세션을 만들려면 **응용 프로그램(클라이언트) ID** 및 `PingAccess key` 값을 사용합니다. 그런 다음 ID 매핑을 설정하고 가상 호스트, 사이트 및 애플리케이션을 만들 수 있습니다.

### <a name="test-your-application"></a>애플리케이션 테스트

이러한 모든 단계를 완료하면 응용 프로그램이 실행되고 실행되어야 합니다. 이를 테스트하려면 브라우저를 열고 Azure에서 응용 프로그램을 게시할 때 만든 외부 URL로 이동합니다. 응용 프로그램에 할당한 테스트 계정으로 로그인합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure AD 응용 프로그램 프록시를 사용하여 게시된 응용 프로그램을 보호하기 위해 Azure AD용 PingAccess 구성](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)
