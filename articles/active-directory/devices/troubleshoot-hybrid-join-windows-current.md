---
title: 하이브리드 Azure Active Directory 조인 디바이스 문제 해결
description: Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 87de8f27114c8b79c297f65805226a33c70b11a9
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108286963"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>하이브리드 Azure Active Directory 조인 디바이스 문제 해결

이 문서의 내용은 Windows 10 또는 Windows Server 2016를 실행하는 디바이스에 적용됩니다.

다른 Windows 클라이언트의 경우 [하위 수준 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-legacy.md) 문서를 참조하세요.

이 문서에서는 다음 시나리오를 지원하도록 [디바이스에 조인된 하이브리드 Azure Active Directory를 구성](hybrid-azuread-join-plan.md)했다고 가정합니다.

- 디바이스 기반 조건부 액세스
- [엔터프라이즈 설정 로밍](./enterprise-state-roaming-overview.md)
- [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

이 문서에서는 잠재적인 문제를 해결하기 위한 문제 해결 지침을 제공합니다.

Windows 10 및 Windows Server 2016의 경우 하이브리드 Azure Active Directory 조인은 Windows 10 2015년 11월 업데이트 이상을 지원합니다.

## <a name="troubleshoot-join-failures"></a>조인 오류 문제 해결

### <a name="step-1-retrieve-the-join-status"></a>1단계: 조인 상태 검색

**조인 상태를 검색하려면:**

1. 관리자로 명령 프롬프트를 엽니다.
2. `dsregcmd /status` 입력

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>2단계: 조인 상태 평가

다음 필드를 검토하고 예상 값을 갖는지 확인합니다.

#### <a name="domainjoined--yes"></a>DomainJoined : YES

이 필드는 디바이스가 온-프레미스 Active Directory에 조인되는지 여부를 나타냅니다. 값이 **아니요** 인 경우 디바이스는 하이브리드 Azure AD 조인을 수행할 수 없습니다.

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO

이 필드는 디바이스가 Azure AD에 개인 디바이스로 등록되어 있는지 여부를 나타냅니다(*작업 영역 조인* 으로 표시). 이 값은 하이브리드 Azure AD 조인된 도메인에 가입된 컴퓨터에 대해 **아니요** 이어야 합니다. 값이 **예** 인 경우 하이브리드 Azure AD 조인을 완료하기 전에 회사 또는 학교 계정이 추가되었습니다. 이 경우 Windows 10 버전 1607 이상을 사용하는 경우 계정이 무시됩니다.

#### <a name="azureadjoined--yes"></a>AzureAdJoined : YES

이 필드는 디바이스가 조인되어 있는지 여부를 나타냅니다. 디바이스가 Azure AD 조인 디바이스 또는 하이브리드 Azure AD 조인 디바이스인 경우에는 값이 **예** 입니다.
값이 **아니요** 인 경우 Azure AD에 대한 조인은 아직 완료되지 않았습니다.

추가적인 문제 해결을 위해 다음 단계를 진행합니다.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>3단계: 조인이 실패한 단계 및 오류 코드 찾기

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 이상

조인 상태 출력의 'Diagnostic Data' 섹션에서 'Previous Registration' 하위 섹션을 찾습니다. 이 섹션은 디바이스가 도메인에 조인되어 있고 하이브리드 Azure AD 조인을 할 수 없는 경우에만 표시됩니다.
'Error Phase' 필드는 조인 실패 단계를 나타내고 'Client ErrorCode'는 조인 작업의 오류 코드를 나타냅니다.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>이전 Windows 10 버전

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 디바이스 등록** 이벤트 로그를 엽니다. **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 아래에 있습니다.
2. 아래와 같이 eventID가 304, 305, 307인 이벤트를 찾습니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="이벤트 뷰어의 스크린샷. ID가 304인 이벤트가 선택되고 해당 정보가 표시되며 오류 코드와 단계가 강조 표시되어 있습니다." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="이벤트 뷰어의 스크린샷. ID가 305인 이벤트가 표시되고 해당 정보가 표시되며 오류 코드가 강조 표시되어 있습니다." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>4단계: 아래 목록에서 가능한 원인 및 해결 방법 확인

#### <a name="pre-check-phase"></a>사전 검사 단계

가능한 실패 원인:

- 디바이스에서 도메인 컨트롤러에 대한 가시선을 찾을 수 없습니다.
   - 디바이스는 조직의 내부 네트워크 또는 온-프레미스 AD(Active Directory) 도메인 컨트롤러의 네트워크 가시선 내에 있는 VPN에 있어야 합니다.

#### <a name="discover-phase"></a>검색 단계

가능한 실패 원인:

- SCP(서비스 연결 지점) 개체가 잘못 구성되었거나 DC에서 SCP 개체를 읽을 수 없습니다.
   - Azure AD에서 확인된 도메인 이름을 가리키고 디바이스가 속하는 AD 포리스트에 유효한 SCP 개체가 있어야 합니다.
   - 자세한 내용은 [서비스 연결 지점 구성](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join) 섹션에서 찾을 수 있습니다.
- 검색 엔드포인트에 연결하여 검색 메타데이터를 가져오지 못했습니다.
   - 등록 및 권한 부여 엔드포인트를 검색하려면 디바이스가 시스템 컨텍스트에서 `https://enterpriseregistration.windows.net`에 액세스할 수 있어야 합니다.
   - 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 디바이스의 컴퓨터 계정이 아웃바운드 프록시를 검색하여 자동으로 인증할 수 있는지 확인해야 합니다.
- 사용자 영역 엔드포인트에 연결하여 영역 검색을 수행하지 못했습니다. (Windows 10 버전 1809 이상​만 해당)
   - 디바이스는 확인된 도메인에 대해 영역 검색을 수행하고 도메인 유형(관리/페더레이션)을 결정할 수 있도록 시스템 컨텍스트에서 `https://login.microsoftonline.com`에 액세스할 수 있어야 합니다.
   - 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 디바이스의 시스템 컨텍스트가 아웃바운드 프록시를 검색하여 자동으로 인증할 수 있는지 확인해야 합니다.

**일반적인 오류 코드:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED**(0x801c001d/-2145648611)
   - 원인: SCP 개체를 읽고 Azure AD 테넌트 정보를 가져올 수 없습니다.
   - 해결 방법: [서비스 연결 지점 구성](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join) 섹션을 참조하세요.
- **DSREG_AUTOJOIN_DISC_FAILED**(0x801c0021/-2145648607)
   - 원인: 일반 검색에 실패했습니다. DRS에서 검색 메타데이터를 가져오지 못했습니다.
   - 해결 방법: 아래에서 하위 오류를 찾아 자세히 조사합니다.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**(0x801c001f/-2145648609)
   - 원인: 검색을 수행하는 동안 작업 시간이 초과되었습니다.
   - 해결 방법: 시스템 컨텍스트에서 `https://enterpriseregistration.windows.net`에 액세스할 수 있는지 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites) 섹션을 참조하세요.
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED**(0x801c0021/-2145648611)
   - 원인: 일반 영역 검색에 실패했습니다. STS에서 도메인 유형(관리/페더레이션)을 확인하지 못했습니다.
   - 해결 방법: 아래에서 하위 오류를 찾아 자세히 조사합니다.

**일반적인 하위 오류 코드:**

검색 오류 코드에 대한 하위 오류 코드를 찾으려면 다음 방법 중 하나를 사용합니다.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 이상

조인 상태 출력의 'Diagnostic Data' 섹션에서 'DRS Discovery Test'를 찾습니다. 이 섹션은 디바이스가 도메인에 조인되어 있고 하이브리드 Azure AD 조인을 할 수 없는 경우에만 표시됩니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>이전 Windows 10 버전

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 디바이스 등록** 이벤트 로그를 엽니다. **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 아래에 있습니다.
2. 아래와 같이 eventID가 201인 이벤트를 찾습니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="이벤트 뷰어의 스크린샷. ID가 201인 이벤트가 선택되고 해당 정보가 표시되며 오류 코드가 강조 표시되어 있습니다." border="false":::

###### <a name="network-errors"></a>네트워크 오류

- **WININET_E_CANNOT_CONNECT**(0x80072efd/-2147012867)
   - 원인: 서버와의 연결을 설정할 수 없습니다.
   - 해결 방법: 필요한 Microsoft 리소스에 대한 네트워크 연결을 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조하세요.
- **WININET_E_TIMEOUT**(0x80072ee2/-2147012894)
   - 원인: 일반 네트워크 시간 제한입니다.
   - 해결 방법: 필요한 Microsoft 리소스에 대한 네트워크 연결을 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조하세요.
- **WININET_E_DECODING_FAILED**(0x80072f8f/-2147012721)
   - 원인: 네트워크 스택이 서버의 응답을 디코딩할 수 없습니다.
   - 해결 방법: 네트워크 프록시가 서버 응답을 방해하고 수정하지 않는지 확인합니다.

###### <a name="http-errors"></a>HTTP 오류

- **DSREG_DISCOVERY_TENANT_NOT_FOUND**(0x801c003a/-2145648582)
   - 원인: SCP 개체가 잘못된 테넌트 ID로 구성되었습니다. 또는 테넌트에서 활성 구독을 찾을 수 없습니다.
   - 해결 방법: SCP 개체가 올바른 Azure AD 테넌트 ID 및 활성 구독으로 구성되어 있는지 또는 테넌트에 있는지 확인합니다.
- **DSREG_SERVER_BUSY**(0x801c0025/-2145648603)
   - 원인: DRS 서버의 HTTP 503.
   - 해결 방법: 현재 서버를 사용할 수 없습니다. 이후 조인 시도는 서버가 다시 온라인 상태가 되어야 성공할 수 있습니다.

###### <a name="other-errors"></a>다른 오류

- **E_INVALIDDATA**(0x8007000d/-2147024883)
   - 원인: 서버 응답 JSON을 구문 분석할 수 없습니다. HTTP 200을 HTML 인증 페이지와 함께 반환하는 프록시로 인해 이 문제가 발생했을 수 있습니다.
   - 해결 방법: 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 디바이스의 시스템 컨텍스트가 아웃바운드 프록시를 검색하여 자동으로 인증할 수 있는지 확인해야 합니다.

#### <a name="authentication-phase"></a>인증 단계

페더레이션된 도메인 계정에만 적용됩니다.

실패 원인:

- DRS 리소스에 대한 액세스 토큰을 자동으로 가져올 수 없습니다.
   - Windows 10 디바이스는 활성 WS-Trust 엔드포인트에 통합된 Windows 인증을 사용하여 페더레이션 서비스에서 인증 토큰을 가져옵니다. 세부 정보: [페더레이션 서비스 구성](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**일반적인 오류 코드:**

이벤트 뷰어 로그를 사용하여 오류 코드, 하위 오류 코드, 서버 오류 코드 및 서버 오류 메시지를 찾을 수 있습니다.

1. 이벤트 뷰어에서 **사용자 디바이스 등록** 이벤트 로그를 엽니다. **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 아래에 있습니다.
2. 아래와 같이 eventID가 305인 이벤트를 찾습니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="이벤트 뷰어의 스크린샷. ID가 305인 이벤트가 표시됩니다. 또한 해당 정보가 표시되며 ADAL 오류 코드와 상태는 강조 표시되어 있습니다." border="false":::

##### <a name="configuration-errors"></a>구성 오류

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED**(0xcaa90017/-894894057)
   - 원인: 인증 프로토콜이 WS-Trust가 아닙니다.
   - 해결 방법: 온-프레미스 ID 공급자가 WS-Trust를 지원해야 합니다.
- **ERROR_ADAL_FAILED_TO_PARSE_XML**(0xcaa9002c/-894894036)
   - 원인: 온-프레미스 페더레이션 서비스에서 XML 응답을 반환하지 않았습니다.
   - 해결 방법: MEX 엔드포인트가 유효한 XML을 반환하는지 확인합니다. 프록시가 방해하지 않고 비 xml 응답을 반환하는지 확인합니다.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT**(0xcaa90023/-894894045)
   - 원인: 사용자 이름/암호 인증을 위한 엔드포인트를 검색할 수 없습니다.
   - 해결 방법: 온-프레미스 ID 공급자 설정을 확인합니다. WS-Trust 엔드포인트를 사용하도록 설정하고 MEX 응답에 이러한 올바른 엔드포인트가 포함되어 있는지 확인합니다.

##### <a name="network-errors"></a>네트워크 오류

- **ERROR_ADAL_INTERNET_TIMEOUT**(0xcaa82ee2/-894947614)
   - 원인: 일반 네트워크 시간 제한입니다.
   - 해결 방법: 시스템 컨텍스트에서 `https://login.microsoftonline.com`에 액세스할 수 있는지 확인합니다. 시스템 컨텍스트에서 온-프레미스 ID 공급자에 액세스할 수 있는지 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조하세요.
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED**(0xcaa82efe/-894947586)
   - 원인: 인증 엔드포인트에 대한 연결이 중단되었습니다.
   - 해결 방법: 잠시 후 다시 시도하거나 안정적인 다른 네트워크 위치에서 조인해 보세요.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE**(0xcaa82f8f/-894947441)
   - 원인: 이전에 SSL(Secure Sockets Layer)로 알려진 TLS(전송 계층 보안)에 대해 서버에서 보낸 인증서의 유효성을 검사할 수 없습니다.
   - 해결 방법: 클라이언트 시간차를 확인합니다. 잠시 후 다시 시도하거나 안정적인 다른 네트워크 위치에서 조인해 보세요.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT**(0xcaa82efd/-894947587)
   - 원인: `https://login.microsoftonline.com`에 연결하려는 시도에 실패했습니다.
   - 해결 방법: `https://login.microsoftonline.com`에 대한 네트워크 연결을 확인합니다.

##### <a name="other-errors"></a>다른 오류

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT**(0xcaa20003/-895352829)
   - 원인: 온-프레미스 ID 공급자의 SAML 토큰을 Azure AD에서 수락하지 않았습니다.
   - 해결 방법: 페더레이션 서버 설정을 확인합니다. 인증 로그에서 서버 오류 코드를 찾습니다.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED**(0xcaa90014/-894894060)
   - 원인: 서버 WS-Trust 응답이 오류 예외를 보고했으며 어설션을 가져오지 못했습니다.
   - 해결 방법: 페더레이션 서버 설정을 확인합니다. 인증 로그에서 서버 오류 코드를 찾습니다.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL**(0xcaa90006/-894894074)
   - 원인: 토큰 엔드포인트에서 액세스 토큰을 가져오는 동안 오류가 발생했습니다.
   - 해결 방법: ADAL 로그에서 기본 오류를 찾습니다.
- **ERROR_ADAL_OPERATION_PENDING**(0xcaa1002d/-895418323)
   - 원인: 일반 ADAL 오류
   - 해결 방법: 인증 로그에서 하위 오류 코드 또는 서버 오류 코드를 찾습니다.

#### <a name="join-phase"></a>조인 단계

실패 원인:

등록 유형을 찾은 후 아래 목록에서 오류 코드를 찾습니다.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 이상

조인 상태 출력의 'Diagnostic Data' 섹션에서 'Previous Registration' 하위 섹션을 찾습니다. 이 섹션은 디바이스가 도메인에 조인되어 있고 하이브리드 Azure AD 조인을 할 수 없는 경우에만 표시됩니다.
'Registration Type' 필드는 수행된 조인 유형을 나타냅니다.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>이전 Windows 10 버전

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 디바이스 등록** 이벤트 로그를 엽니다. **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **사용자 디바이스 등록** 아래에 있습니다.
2. 아래와 같이 eventID가 204인 이벤트를 찾습니다.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="이벤트 뷰어의 스크린샷. ID가 204인 이벤트에 대한 정보가 표시되고 오류 코드, HTTP 상태 및 메시지가 강조 표시됩니다." border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>DRS 서버에서 반환된 HTTP 오류

- **DSREG_E_DIRECTORY_FAILURE**(0x801c03f2/-2145647630)
   - 원인: DRS에서 오류 응답을 받았습니다. ErrorCode: "DirectoryError"
   - 해결 방법: 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조하세요.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR**(0x801c0002/-2145648638)
   - 원인: DRS에서 오류 응답을 받았습니다. ErrorCode는 "AuthenticationError"이고 ErrorSubCode는 "DeviceNotFound"가 아닙니다.
   - 해결 방법: 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조하세요.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR**(0x801c0006/-2145648634)
   - 원인: DRS에서 오류 응답을 받았습니다. ErrorCode: "DirectoryError"
   - 해결 방법: 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조하세요.

##### <a name="tpm-errors"></a>TPM 오류

- **NTE_BAD_KEYSET**(0x80090016/-2146893802)
   - 원인: TPM 작업에 실패했거나 잘못되었습니다.
   - 해결 방법: 잘못된 sysprep 이미지 때문일 수 있습니다. sysprep 이미지가 생성된 컴퓨터가 Azure AD 조인, 하이브리드 Azure AD 조인 또는 Azure AD 등록이 되어 있지 않은지 확인합니다.
- **TPM_E_PCP_INTERNAL_ERROR**(0x80290407/-2144795641)
   - 원인: 일반 TPM 오류입니다.
   - 해결 방법: 이 오류가 발생한 디바이스에서 TPM을 사용하지 않도록 설정합니다. Windows 10 버전 1809 이상에서는 TPM 오류를 자동으로 감지하며 TPM을 사용하지 않고 하이브리드 Azure AD 조인을 완료합니다.
- **TPM_E_NOTFIPS**(0x80280036/-2144862154)
   - 원인: FIPS 모드에서는 현재 TPM이 지원되지 않습니다.
   - 해결 방법: 이 오류가 발생한 디바이스에서 TPM을 사용하지 않도록 설정합니다. Windows 1809에서는 TPM 오류를 자동으로 감지하며 TPM을 사용하지 않고 하이브리드 Azure AD 조인을 완료합니다.
- **NTE_AUTHENTICATION_IGNORED**(0x80090031/-2146893775)
   - 원인: TPM이 잠겼습니다.
   - 해결 방법: 일시적인 오류입니다. 휴지 시간 동안 기다리세요. 잠시 후 조인 시도에 성공하게 됩니다. 자세한 내용은 [TPM 기본 사항](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering) 문서에서 확인할 수 있습니다.

##### <a name="network-errors"></a>네트워크 오류

- **WININET_E_TIMEOUT**(0x80072ee2/-2147012894)
   - 원인: DRS에서 디바이스를 등록하는 동안 일반 네트워크 시간 초과
   - 해결 방법: `https://enterpriseregistration.windows.net`에 대한 네트워크 연결을 확인합니다.
- **WININET_E_NAME_NOT_RESOLVED**(0x80072ee7/-2147012889)
   - 원인: 서버 이름 또는 주소를 확인할 수 없습니다.
   - 해결 방법: `https://enterpriseregistration.windows.net`에 대한 네트워크 연결을 확인합니다. 호스트 이름에 대한 DNS 확인이 네트워크와 디바이스에서 정확한지 확인합니다.
- **WININET_E_CONNECTION_ABORTED**(0x80072efe/-2147012866)
   - 원인: 서버와의 연결이 비정상적으로 종료되었습니다.
   - 해결 방법: 잠시 후 다시 시도하거나 안정적인 다른 네트워크 위치에서 조인해 보세요.

##### <a name="other-errors"></a>다른 오류

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED**(0x801c001d/-2145648611)
   - 원인: EventID 220이 사용자 디바이스 등록 이벤트 로그에 표시됩니다. Windows가 Active Directory의 컴퓨터 개체에 액세스할 수 없습니다. Windows 오류 코드가 이벤트에 포함될 수 있습니다. 오류 코드 ERROR_NO_SUCH_LOGON_SESSION(1312) 및 ERROR_NO_SUCH_USER(1317)의 경우 온-프레미스 AD의 복제 문제와 관련이 있습니다.
   - 해결 방법: AD의 복제 문제를 해결합니다. 복제 문제는 일시적인 것일 수 있으며 일정 시간이 지난 후 사라질 수 있습니다.

##### <a name="federated-join-server-errors"></a>페더레이션된 조인 서버 오류

| 서버 오류 코드 | 서버 오류 메시지 | 가능한 이유 | 해상도 |
| --- | --- | --- | --- |
| DirectoryError | 요청이 일시적으로 제한되었습니다. 300초 후에 시도하십시오. | 예상된 오류입니다. 빠르게 연속으로 여러 번의 등록 요청이 있었기 때문일 수 있습니다. | 휴지 기간 후 조인 다시 시도 |

##### <a name="sync-join-server-errors"></a>동기화 조인 서버 오류

| 서버 오류 코드 | 서버 오류 메시지 | 가능한 이유 | 해상도 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: 테넌트 <UUID>를 찾을 수 없습니다. 이 오류는 테넌트에 대한 활성 구독이 없는 경우 발생할 수 있습니다. 구독 관리자와 함께 확인하세요. | SCP 개체의 테넌트 ID가 잘못되었습니다. | SCP 개체가 올바른 Azure AD 테넌트 ID 및 활성 구독으로 구성되어 있고 테넌트에 있는지 확인합니다. |
| DirectoryError | 지정된 ID의 디바이스 개체를 찾을 수 없습니다. | 조인 동기화에서 예상되는 오류입니다. 디바이스 개체가 AD에서 Azure AD로 동기화되지 않았습니다. | Azure AD Connect 동기화가 완료될 때까지 기다리면 동기화 완료 후 다음 조인 시도에서 문제가 해결됩니다. |
| AuthenticationError | 대상 컴퓨터의 SID 확인 | Azure AD 디바이스의 인증서가 동기화 조인 중 Blob에 서명하는 데 사용된 인증서와 일치하지 않습니다. 이 오류는 일반적으로 동기화가 아직 완료되지 않았음을 의미합니다. |  Azure AD Connect 동기화가 완료될 때까지 기다리면 동기화 완료 후 다음 조인 시도에서 문제가 해결됩니다. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>5단계: 로그 수집 및 Microsoft 지원에 연락

[https://github.com/CSS-Identity/DRS/tree/main/Auth](https://github.com/CSS-Identity/DRS/tree/main/Auth)에서 Auth.zip 파일을 다운로드합니다.

1. 파일의 압축을 풀고 포함된 **start-auth.txt** 및 **stop-auth.txt** 파일을 **start-auth.cmd** 및 **stop-auth.cmd** 로 이름 변경합니다.
1. 관리자 권한 명령 프롬프트에서 **start-auth.cmd** 를 실행합니다.
1. 계정 전환을 사용하여 문제가 있는 사용자의 다른 세션으로 전환합니다.
1. 문제를 재현합니다.
1. 계정 전환을 사용하여 추적을 실행하는 관리자 세션으로 다시 전환합니다.
1. 관리자 권한 명령 프롬프트에서 **stop-auth.cmd** 를 실행합니다.
1. 스크립트가 실행된 폴더에서 **Authlogs** 폴더를 압축하여 보냅니다.

## <a name="troubleshoot-post-join-issues"></a>조인 후 문제 해결

### <a name="retrieve-the-join-status"></a>조인 상태 검색

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: YES 및 AzureADPrt: YES

이러한 필드는 사용자가 디바이스에 로그인 시 Azure AD에서 성공적으로 인증되었는지 여부를 나타냅니다.
값이 **아니요** 인 경우 다음의 원인 때문일 수 있습니다.

- 등록 시 디바이스에 연결된 TPM에 잘못된 스토리지 키가 있습니다(상승된 권한으로 실행하면서 KeySignTest 확인).
- 대체 로그인 ID
- HTTP 프록시를 찾을 수 없음

## <a name="known-issues"></a>알려진 문제
- 하이브리드 Azure AD 조인 디바이스가 모바일 핫스팟 또는 외부 WiFi 네트워크에 연결된 경우, 설정 -> 계정 -> 회사 또는 학교 액세스 아래에서 Azure AD용 하나와 온-프레미스 AD용 하나씩 두 개의 계정을 표시할 수 있습니다. 이것은 UI 문제일 뿐이며 기능에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

- [dsregcmd 명령을 사용하여 디바이스 문제 해결](troubleshoot-device-dsregcmd.md) 계속

- [Microsoft 오류 조회 도구](/windows/win32/debug/system-error-code-lookup-tool)
