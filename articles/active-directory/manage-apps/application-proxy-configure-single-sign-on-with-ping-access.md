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
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab08c93662988655154cf300ac4ee3758fbc7872
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472811"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>애플리케이션 프록시 및 PingAccess를 사용하여 Single Sign-On에 대한 헤더 기반 인증

Azure AD 고객에 게 응용 프로그램에 액세스할 수 있도록 azure Active Directory (Azure AD) 응용 프로그램 프록시는 PingAccess를 사용 하 여 협력 했습니다. PingAccess는 [기존 애플리케이션 프록시 제품](application-proxy.md)을 확장하여 인증에 헤더를 사용하는 애플리케이션에 대한 Single Sign-On 액세스를 포함하고 있습니다.

## <a name="whats-pingaccess-for-azure-ad"></a>Azure AD 용 PingAccess 란?

Azure AD 용 PingAccess를 사용 하 여 인증에 대 한 헤더를 사용 하는 응용 프로그램에서 single sign-on (SSO) 및 사용자 액세스를 제공할 수 있습니다. 애플리케이션 프록시는 이러한 애플리케이션을 Azure AD를 사용하여 액세스를 인증한 다음, 커넥터 서비스를 통해 트래픽을 전달하는 방식으로 다른 앱과 유사하게 처리합니다. PingAccess는 응용 프로그램 앞에 위치 하 고 Azure AD에서 액세스 토큰을 헤더로 변환 합니다. 그런 다음 응용 프로그램 형식으로 읽을 수 있습니다 인증을 받습니다.

사용자는 회사 애플리케이션을 사용하기 위해 로그인할 때 다른 점을 알아차리지 못합니다. 여전히 어디에서든지 모든 디바이스에서 작업할 수 있습니다. 응용 프로그램 프록시 커넥터는에서는 여전히 부하를 분산 자동으로 하므로 해당 인증 유형에 관계 없이 모든 앱에 원격 트래픽을 직접.

## <a name="how-do-i-get-access"></a>액세스 권한은 어떻게 얻을 수 있나요?

이 시나리오는 Azure Active Directory와 PingAccess 간의 파트너 관계에서 상태가 되 면 되므로 두 서비스에 대 한 라이선스가 있어야 합니다. 그러나 Azure Active Directory Premium 구독에는 최대 20개의 애플리케이션을 보장하는 기본 PingAccess 라이선스가 포함되어 있습니다. 헤더 기반 애플리케이션을 20개 이상 게시해야 하는 경우 PingAccess에서 라이선스를 추가로 구입할 수 있습니다.

자세한 내용은 [Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="publish-your-application-in-azure"></a>Azure에 애플리케이션 게시

이 문서는 처음으로이 시나리오를 사용 하 여 응용 프로그램을 게시 하 게 합니다. 게시 단계를 자세히 설명 하는 것 외에도 해당 안내 응용 프로그램 프록시 및 PingAccess를 사용 하 여 시작 합니다. 두 서비스를 이미 구성한 해도 게시 단계에서 리프레셔를 원하는 경우 건너뛸 합니다 [응용 프로그램 프록시를 사용 하 여 Azure AD에 응용 프로그램을 추가](#add-your-application-to-azure-ad-with-application-proxy) 섹션입니다.

> [!NOTE]
> 이 시나리오는 Azure AD와 PingAccess 간의 파트너 관계이므로 일부 지침은 Ping ID 사이트에 존재합니다.

### <a name="install-an-application-proxy-connector"></a>애플리케이션 프록시 커넥터 설치

설정한 경우 응용 프로그램 프록시 사용 하도록 설정 하 고 커넥터를 이미 설치 되어,이 섹션을 건너뛸 하 고 이동할 수 있습니다 [응용 프로그램 프록시를 사용 하 여 Azure AD에 응용 프로그램을 추가](#add-your-application-to-azure-ad-with-application-proxy)합니다.

응용 프로그램 프록시 커넥터는 원격 직원의 트래픽을 게시 된 응용 프로그램에 지시 하는 Windows Server 서비스. 자세한 설치 지침은 참조 [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램을 추가 합니다.](application-proxy-add-on-premises-application.md)합니다.

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/) 응용 프로그램 관리자입니다. 합니다 **Azure Active Directory 관리 센터** 페이지가 나타납니다.
2. 선택 **Azure Active Directory** > **응용 프로그램 프록시** > **커넥터 서비스 다운로드**합니다. 합니다 **응용 프로그램 프록시 커넥터 다운로드** 페이지가 나타납니다.

   ![응용 프로그램 프록시 커넥터 다운로드](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. 설치 지침을 따릅니다.

커넥터를 다운로드 해야 자동으로 응용 프로그램 프록시 사용 디렉터리에 대 한 있지만 그렇지 않은 경우 선택할 수 있습니다 **응용 프로그램 프록시 사용**합니다.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 여 Azure AD에 응용 프로그램 추가

Azure Portal에서 수행해야 하는 두 가지 작업이 있습니다. 먼저 애플리케이션 프록시를 사용하여 애플리케이션을 게시해야 합니다. 그런 다음 PingAccess 단계 동안 사용할 수 있는 응용 프로그램에 대 한 정보를 수집 해야 합니다.

#### <a name="publish-your-application"></a>애플리케이션 게시

먼저 응용 프로그램을 게시 해야 합니다. 이 작업에는 다음이 포함 됩니다.

- Azure AD에 온-프레미스 응용 프로그램 추가
- 응용 프로그램 테스트 및 헤더 기반 SSO 선택에 대 한 사용자 할당
- 응용 프로그램의 리디렉션 URL 설정
- 사용자 및 온-프레미스 응용 프로그램을 사용 하 여 다른 응용 프로그램에 대 한 권한 부여

사용자 고유의 온-프레미스 응용 프로그램 게시:

1. 마지막 섹션에서 그렇지 않은 경우에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/) 응용 프로그램 관리자입니다.
2. 선택 **엔터프라이즈 응용 프로그램** > **새 응용 프로그램** > **온-프레미스 응용 프로그램**합니다. 합니다 **고유한 온-프레미스 응용 프로그램을 추가** 페이지가 나타납니다.

   ![사용자 고유의 온-프레미스 응용 프로그램 추가](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. 새 응용 프로그램에 대 한 정보를 사용 하 여 필수 필드를 채웁니다. 설정에 대해 아래 지침을 사용 합니다.

   > [!NOTE]
   > 이 단계의 자세한 연습을 참조 하세요 [Azure AD에 온-프레미스 앱을 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)합니다.

   1. **내부 URL**: 일반적으로 회사 네트워크에 있을 때 앱의 로그인 페이지로 안내 하는 URL을 제공 하 합니다. 이 시나리오에 대 한 커넥터는 PingAccess 프록시 응용 프로그램의 프런트 페이지를 처리 해야 합니다. `https://<host name of your PingAccess server>:<port>` 형식을 사용합니다. 포트는 기본적으로 3000이지만 PingAccess에서 구성할 수 있습니다.

      > [!WARNING]
      > Single sign-on의이 형식에 대해 내부 URL을 사용 해야 합니다 `https` 사용할 수 없습니다 및 `http`합니다.

   2. **사전 인증 방법**: 선택할 **Azure Active Directory**합니다.
   3. **헤더에서 URL 변환**: 선택할 **No**합니다.

   > [!NOTE]
   > 첫 번째 애플리케이션인 경우 3000 포트를 사용하여 시작하고 PingAccess 구성을 변경하면 다시 돌아와서 이 설정을 업데이트합니다. 후속 응용 프로그램의 경우 포트 PingAccess에서 구성한 수신기와 일치 해야 합니다. [PingAccess의 수신기](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)에 대해 자세히 알아봅니다.
4. **추가**를 선택합니다. 새 응용 프로그램에 대 한 개요 페이지가 표시 됩니다.

이제 응용 프로그램 테스트에 대 한 사용자를 할당 하 고 헤더 기반 single sign on 선택 합니다.

1. 응용 프로그램 사이드바에서 선택 **사용자 및 그룹** > **사용자 추가** > **사용자 및 그룹 (\<번호 > 선택한)** . 선택 하기 위한 사용자 및 그룹의 목록이 표시 됩니다.

   ![개요](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. 선택한 응용 프로그램 테스트에 대 한 사용자 선택 **선택**합니다. 이 테스트 계정에 온-프레미스 애플리케이션에 대한 액세스 권한이 있는지 확인합니다.
3. **할당**을 선택합니다.
4. 응용 프로그램 사이드바에서 선택 **Single sign on** > **헤더 기반**입니다.

   > [!TIP]
   > 헤더 기반 Single Sign-On을 처음 사용하는 경우 PingAccess를 설치해야 합니다. Azure 구독이 PingAccess 설치와 자동으로 연결되도록 하려면 이 Single Sign-On 페이지의 링크를 사용하여 PingAccess를 다운로드합니다. 지금 다운로드 사이트를 열거나 나중에 이 페이지로 다시 돌아올 수 있습니다.

   ![헤더 기반 로그온](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. **저장**을 선택합니다.

해야 리디렉션 URL이 외부 URL로 설정 합니다.

1. **Azure Active Directory 관리 센터** 보충 선택 **Azure Active Directory** > **앱 등록**합니다. 응용 프로그램의 목록이 표시 됩니다.

   ![앱 등록](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. 애플리케이션을 선택합니다.
3. 다음 링크를 선택 **리디렉션 Uri**, 리디렉션 Uri가 웹 클라이언트와 공용 클라이언트에 대 한 설정의 수를 표시 합니다. 합니다  **\<응용 프로그램 이름 >-인증** 페이지가 나타납니다.
4. 응용 프로그램을 이전에 할당 된 외부 URL이 있는지 여부를 확인 합니다 **리디렉션 Uri** 목록입니다. 그렇지 않다면 추가 외부 URL 이제의 리디렉션 URI 형식을 사용 하 여 **웹**, 선택한 **저장**합니다.

마지막으로, 사용자가 읽기 액세스할 수 있으며 다른 응용 프로그램 읽기/쓰기 액세스할 수 있도록 온-프레미스 응용 프로그램을 설정 합니다.

1. **앱 등록** 응용 프로그램에 대 한 보충 기사 선택 **API 사용 권한** > **권한 추가**  >   **Microsoft Api** > **Microsoft Graph**합니다. 합니다 **요청 API 사용 권한** 에 대 한 페이지 **Microsoft Graph** 나타나면 Api에 대 한 Windows Azure Active Directory를 포함 하는 합니다.

   ![API 사용 권한 요청](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. 선택 **위임 된 권한** > **사용자** > **User.Read**합니다.
3. 선택 **응용 프로그램 사용 권한** > **응용 프로그램** > **Application.ReadWrite.All**합니다.
4. 선택 **권한 추가**합니다.
5. 에 **API 사용 권한** 페이지에서 **에 대 한 관리자 동의 부여 \<에 디렉터리 이름 >** 합니다.

#### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess 단계에 대한 정보 수집

이러한 세 가지 정보가 PingAccess 사용 하 여 응용 프로그램을 설정 하려면 (모든 Guid)를 수집 해야 합니다.

| Azure AD 필드의 이름 | PingAccess 필드의 이름 | 데이터 형식 |
| --- | --- | --- |
| **응용 프로그램 (클라이언트) ID** | **클라이언트 ID** | GUID |
| **디렉터리 (테 넌 트) ID** | **발급자** | GUID |
| `PingAccess key` | **클라이언트 암호** | 임의의 문자열 |

이 정보를 수집 합니다 하:

1. **Azure Active Directory 관리 센터** 보충 선택 **Azure Active Directory** > **앱 등록**합니다. 응용 프로그램의 목록이 표시 됩니다.
2. 애플리케이션을 선택합니다. 합니다 **앱 등록** 페이지 응용 프로그램에 표시 됩니다.

   ![응용 프로그램에 대 한 등록 개요](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. 옆에 **(클라이언트) 응용 프로그램 ID** 값을 선택 합니다 **클립보드로 복사** 아이콘을 복사 하 고 저장 합니다. PingAccess의 클라이언트 ID로 나중에이 값을 지정 하면
4. 다음은 **디렉터리 (테 넌 트) ID** 값을 선택할 수도 **클립보드로 복사**, 복사 및 저장 합니다. PingAccess의 발급자로이 값을 나중에 지정합니다.
5. 세로 막대에서 합니다 **앱 등록** 응용 프로그램에 대 한 선택 **인증서 및 비밀** > **새 클라이언트 암호**합니다. 합니다 **클라이언트 암호 추가** 페이지가 나타납니다.

   ![클라이언트 암호 추가](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. **설명을**, 형식 `PingAccess key`합니다.
7. 아래 **Expires**, PingAccess 키를 설정 하는 방법을 선택 합니다. **1 년 안에**, **In 2 years**, 또는 **Never**합니다.
8. **추가**를 선택합니다. PingAccess 키 클라이언트 비밀의 테이블에 나타나는 임의 문자열에서 해당 autofills 합니다 **값** 필드입니다.
9. PingAccess 키 옆의 **값** 필드를 선택 합니다 **클립보드로 복사** 아이콘을 복사 하 고 저장 합니다. PingAccess의 클라이언트 비밀로이 값이를 나중에 지정합니다.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>사용자 지정 필드 (선택 사항) 보내도록 GraphAPI 업데이트

내에서 다른 토큰을 전송 하는 사용자 지정 클레임이 필요한 경우 PingAccess에서 access_token 사용으로 설정 합니다 `acceptMappedClaims` 응용 프로그램 필드 `True`합니다. Graph Explorer를 사용할 수 있습니다 또는이 변경을 수행 하려면 Azure AD 포털의 응용 프로그램 매니페스트 합니다.

**이 예제에서는 Graph Explorer를 사용합니다.**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**이 예제에서는 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/) 업데이트는 `acceptMappedClaims` 필드:**

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/) 응용 프로그램 관리자입니다.
2. **Azure Active Directory** > **앱 등록**을 선택합니다. 응용 프로그램의 목록이 표시 됩니다.
3. 애플리케이션을 선택합니다.
4. 세로 막대에서 합니다 **앱 등록** 응용 프로그램 페이지를 선택 **매니페스트**합니다. 응용 프로그램의 등록을 위한 매니페스트 JSON 코드가 표시 됩니다.
5. 검색 된 `acceptMappedClaims` 필드 및 값으로 변경 `True`합니다.
6. **저장**을 선택합니다.


### <a name="use-of-optional-claims-optional"></a>(선택 사항) 선택적 클레임의 사용
선택적 클레임을 사용 하면 모든 사용자 및 테 넌 트에는 standard-but-not-included-by-default 클레임을 추가할 수 있습니다. 응용 프로그램 매니페스트를 수정 하 여 응용 프로그램에 대 한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 참조는 [Azure AD 응용 프로그램 매니페스트 문서를 이해 합니다.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

PingAccess를 사용 하는 access_token에 전자 메일 주소를 포함 하는 예제:
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

### <a name="use-of-claims-mapping-policy-optional"></a>클레임 매핑 정책의 (선택 사항) 사용
[클레임 매핑 정책 (미리 보기)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties/) AzureAD에 존재 하지 않는 특성에 대 한 합니다. 클레임 매핑을 사용 하면 ADFS 또는 사용자 개체에 의해 백업 되는 추가 사용자 지정 클레임을 추가 하 여 클라우드로 기존 온-프레미스 앱을 마이그레이션할 수 있습니다.

응용 프로그램 사용자 지정 클레임을 사용 하 여 추가 필드가 포함 되도록 해야 하면 수도 [사용자 지정 클레임 매핑 정책을 만들고 응용 프로그램에 할당](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)합니다.

> [!NOTE]
> 사용자 지정 클레임을 사용하려면 사용자 지정 정책도 정의하고 애플리케이션에 할당해야 합니다. 이 정책은 필요한 모든 사용자 지정 특성을 포함해야 합니다.
>
> 정책 정 및 할당 PowerShell, Azure AD Graph Explorer 또는 Microsoft Graph를 통해 수행할 수 있습니다. 먼저 사용 해야 할 경우 해당 PowerShell에서, `New-AzureADPolicy` 다음으로 응용 프로그램에 할당 하 고 `Add-AzureADServicePrincipalPolicy`입니다. 자세한 내용은 [클레임 매핑 정책 할당](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)합니다.

예제:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id 
```

### <a name="enable-pingaccess-to-use-custom-claims-optional-but-required-if-you-expect-the-application-to-consume-additional-claims"></a>(선택 사항 이지만 추가 클레임을 사용 하도록 응용 프로그램 기대 하는 경우 필수)는 사용자 지정 클레임을 사용 하는 PingAccess를 사용 하도록 설정
PingAccess에서 다음 단계를 구성 하는 경우 웹 세션을 만들려는 (설정-> 액세스 웹 세션->)가 있어야 합니다 **프로필 요청** 선택 취소 하 고 **사용자 특성 새로 고침** 로 **없음**

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess 다운로드 및 응용 프로그램 구성

이제 Azure Active Directory 설정 단계를 모두 완료했으므로 PingAccess 구성으로 이동할 수 있습니다.

이 시나리오의 PingAccess 부분에 대 한 자세한 단계는 Ping Id 문서를 진행 합니다. 지침을 따릅니다 [Microsoft Azure AD 응용 프로그램 프록시를 사용 하 여 게시 된 응용 프로그램을 보호 하기 위해 Azure AD 용 PingAccess 구성](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) Ping Identity 웹 사이트입니다.

이러한 단계 PingAccess를 설치 하 고 (아직 없는 하나) 하는 경우 PingAccess 계정을 설정할 수 있습니다. 그런 다음 Azure AD OIDC (OpenID Connect) 연결을 만들려면 설정한 사용 하 여 토큰 공급자를 **디렉터리 (테 넌 트) ID** Azure AD 포털에서 복사한 값입니다. 이제 PingAccess에서 웹 세션을 만들려면 사용 합니다 **(클라이언트) 응용 프로그램 ID** 고 `PingAccess key` 값입니다. 그런 다음 ID 매핑을 설정하고 가상 호스트, 사이트 및 애플리케이션을 만들 수 있습니다.

### <a name="test-your-application"></a>애플리케이션 테스트

이러한 모든 단계를 완료 한 경우 응용 프로그램을 실행 해야 합니다. 샘플을 테스트 하려면 브라우저를 열고 Azure에 응용 프로그램을 게시할 때 만든 외부 URL로 이동 합니다. 응용 프로그램에 할당 된 테스트 계정으로 로그인 합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure AD 응용 프로그램 프록시를 사용 하 여 게시 된 응용 프로그램을 보호 하기 위해 Azure AD 용 PingAccess 구성](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)
