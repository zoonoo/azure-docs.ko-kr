---
title: 하이브리드 Azure Active Directory 조인 된 장치 문제 해결
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
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331777"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>하이브리드 Azure Active Directory 조인 된 장치 문제 해결 

이 문서의 내용은 Windows 10 또는 Windows Server 2016을 실행하는 장치에 적용할 수 있습니다.

다른 Windows 클라이언트의 경우 [하이브리드 Azure Active Directory가 하위 수준 장치에 가입한 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)문서를 참조하십시오.

이 문서에서는 다음 시나리오를 지원하도록 [디바이스에 조인된 하이브리드 Azure Active Directory를 구성](hybrid-azuread-join-plan.md)했다고 가정합니다.

- 디바이스 기반 조건부 액세스
- [엔터프라이즈 설정 로밍](../active-directory-windows-enterprise-state-roaming-overview.md)
- [비즈니스용 Windows Hello](../active-directory-azureadjoin-passport-deployment.md)

이 문서에서는 잠재적인 문제를 해결하기 위한 문제 해결 지침을 제공합니다. 

Windows 10 및 Windows Server 2016의 경우 하이브리드 Azure Active Directory 조인은 Windows 10 2015년 11월 업데이트 이상을 지원합니다.

## <a name="troubleshoot-join-failures"></a>조인 실패 문제 해결

### <a name="step-1-retrieve-the-join-status"></a>1단계: 조인 상태 검색 

**조인 상태를 검색하려면:**

1. 관리자로 명령 프롬프트 열기
2. `dsregcmd /status`를 입력합니다.

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

이 필드는 디바이스가 온-프레미스 Active Directory에 조인되는지 여부를 나타냅니다. 값이 **아니요**인 경우 디바이스는 하이브리드 Azure AD 조인을 수행할 수 없습니다.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

이 필드는 디바이스가 Azure AD에 개인 디바이스로 등록되어 있는지 여부를 나타냅니다(*작업 영역 조인*으로 표시). 이 값은 하이브리드 Azure AD 조인된 도메인에 가입된 컴퓨터에 대해 **아니요**이어야 합니다. 값이 **예**인 경우 하이브리드 Azure AD 조인을 완료하기 전에 회사 또는 학교 계정이 추가되었습니다. 이 경우 Windows 10 버전의 1주년 업데이트(1607)를 사용하는 경우 계정은 무시됩니다.

#### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

이 필드는 장치가 조인되었는지 여부를 나타냅니다. 장치가 Azure AD 조인 장치 또는 하이브리드 Azure AD 조인 장치인 경우 값이 **YES가** 됩니다.
값이 **아니요**인 경우 Azure AD에 대한 조인은 아직 완료되지 않았습니다. 

추가 문제 해결을 위해 다음 단계로 진행합니다.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>3단계: 조인이 실패한 단계와 오류 코드 찾기

#### <a name="windows-10-1803-and-above"></a>윈도우 10 1803 이상

조인 상태 출력의 '진단 데이터' 섹션에서 '이전 등록' 하위 섹션을 찾습니다. 이 섹션은 장치가 도메인에 가입되어 있고 Azure AD 조인을 하이브리드할 수 없는 경우에만 표시됩니다.
'오류 단계' 필드는 조인 실패의 단계를 나타내고 '클라이언트 ErrorCode'는 조인 작업의 오류 코드를 나타냅니다.

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

#### <a name="older-windows-10-versions"></a>이전 윈도우 10 버전

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 사용자 장치 등록 이벤트 로그를 **엽니다.** 응용 **프로그램 및 서비스 로그** > **마이크로 소프트** > **윈도우** > **사용자 장치 등록** 아래에 위치
2. 다음 이벤트IDs 304, 305, 307이 있는 이벤트를 찾습니다.

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/1.png)

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>4 단계 : 아래 목록에서 가능한 원인과 해결 을 확인하십시오.

#### <a name="pre-check-phase"></a>사전 점검 단계

실패의 가능한 이유:

- 장치에 도메인 컨트롤러에 대한 가시선이 없습니다.
   - 이 장치는 조직의 내부 네트워크 또는 온-프레미스 Active Directory(AD) 도메인 컨트롤러에 대한 네트워크 라인이 있는 VPN에 있어야 합니다.

#### <a name="discover-phase"></a>단계 검색

실패의 가능한 이유:

- 서비스 연결 지점(SCP) 개체가 잘못 구성되었어도 DC에서 SCP 개체를 읽을 수 없습니다.
   - 장치가 속한 AD 포리스트에 유효한 SCP 개체가 필요하며 Azure AD에서 확인된 도메인 이름을 가리킵니다.
   - 자세한 내용은 [서비스 연결 지점 구성](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)섹션에서 찾을 수 있습니다.
- 검색 끝점에서 검색 메타데이터를 연결하고 가져오지 않습니다.
   - 장치는 SYSTEM 컨텍스트에서 `https://enterpriseregistration.windows.net`등록 및 권한 부여 끝점을 검색할 수 있어야 합니다. 
   - 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 장치의 컴퓨터 계정이 아웃바운드 프록시를 검색하고 자동으로 인증할 수 있는지 확인해야 합니다.
- 사용자 영역 끝점에 연결하고 영역 검색을 수행하지 못합니다. (윈도우 10 버전 1809 이상만)
   - 장치는 SYSTEM 컨텍스트에서 `https://login.microsoftonline.com`확인된 도메인에 대한 영역 검색을 수행하고 도메인 유형(관리/페더레이션)을 결정하기 위해 에 액세스할 수 있어야 합니다.
   - 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 장치의 SYSTEM 컨텍스트가 아웃바운드 프록시를 검색하고 자동으로 인증할 수 있는지 확인해야 합니다.

**일반적인 오류 코드:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - 이유: SCP 개체를 읽고 Azure AD 테넌트 정보를 얻을 수 없습니다.
   - 해결 방법: [서비스 연결 지점 구성](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)섹션을 참조하십시오.
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - 이유: 일반 검색 실패. DRS에서 검색 메타데이터를 가져오는 데 실패했습니다.
   - 해결 방법: 아래 하위 오류를 찾아 자세히 조사합니다.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - 이유: 검색을 수행하는 동안 작업이 시간 시간이 비되었습니다.
   - 해결 방법: `https://enterpriseregistration.windows.net` SYSTEM 컨텍스트에서 액세스할 수 있는지 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)섹션을 참조하십시오.
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - 이유: 일반 영역 검색 실패. STS에서 도메인 유형(관리/페더레이션)을 결정하지 못했습니다. 
   - 해결 방법: 아래 하위 오류를 찾아 자세히 조사합니다.

**일반적인 하위 오류 코드:**

검색 오류 코드에 대한 하위 오류 코드를 찾으려면 다음 방법 중 하나를 사용합니다.

##### <a name="windows-10-1803-and-above"></a>윈도우 10 1803 이상

조인 상태 출력의 '진단 데이터' 섹션에서 'DRS 검색 테스트'를 찾습니다. 이 섹션은 장치가 도메인에 가입되어 있고 Azure AD 조인을 하이브리드할 수 없는 경우에만 표시됩니다.

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

##### <a name="older-windows-10-versions"></a>이전 윈도우 10 버전

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 사용자 장치 등록 이벤트 로그를 **엽니다.** 응용 **프로그램 및 서비스 로그** > **마이크로 소프트** > **윈도우** > **사용자 장치 등록** 아래에 위치
2. 다음 이벤트IDs 201과 함께 이벤트를 찾습니다.

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>네트워크 오류

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - 이유: 서버와의 연결을 설정하지 못했습니다.
   - 해결 방법: 필요한 Microsoft 리소스에 대한 네트워크 연결을 보장합니다. 자세한 내용은 [네트워크 연결 요구 사항을](hybrid-azuread-join-managed-domains.md#prerequisites)참조하십시오.
- **WININET_E_TIMEOUT** (0x80072e2/-2147012894)
   - 이유: 일반 네트워크 시간 시간.
   - 해결 방법: 필요한 Microsoft 리소스에 대한 네트워크 연결을 보장합니다. 자세한 내용은 [네트워크 연결 요구 사항을](hybrid-azuread-join-managed-domains.md#prerequisites)참조하십시오.
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - 이유: 네트워크 스택이 서버의 응답을 디코딩할 수 없습니다.
   - 해결 방법: 네트워크 프록시가 서버 응답을 방해하고 수정하지 않는지 확인합니다.

###### <a name="http-errors"></a>HTTP 오류

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - 이유: 잘못된 테넌트 ID로 구성된 SCP 개체입니다. 또는 테넌트에서 활성 구독을 찾을 수 없습니다.
   - 해결 방법: SCP 개체가 올바른 Azure AD 테넌트 ID 및 활성 구독으로 구성되었거나 테넌트에 존재하는지 확인합니다.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - 이유: DRS 서버에서 HTTP 503.
   - 해결 방법: 현재 서버를 사용할 수 없습니다. 향후 조인 시도는 서버가 다시 온라인 상태가 되면 성공할 수 있습니다.

###### <a name="other-errors"></a>기타 오류

- **E_INVALIDDATA** (0x800700d/-2147024883)
   - 이유: 서버 응답 JSON을 구문 분석할 수 없습니다. HTML 인증 페이지와 함께 HTTP 200을 반환하는 프록시 때문일 수 있습니다.
   - 해결 방법: 온-프레미스 환경에 아웃바운드 프록시가 필요한 경우 IT 관리자는 장치의 SYSTEM 컨텍스트가 아웃바운드 프록시를 검색하고 자동으로 인증할 수 있는지 확인해야 합니다.

#### <a name="authentication-phase"></a>인증 단계

페더레이션도메인 계정에만 적용됩니다.

실패 이유:

- DRS 리소스에 대해 액세스 토큰을 자동으로 얻을 수 없습니다.
   - Windows 10 장치는 통합 Windows 인증을 사용하여 페더레이션 서비스에서 활성 WS-Trust 엔드포인트로 인증 토큰을 획득합니다. 세부 정보: [페더레이션 서비스 구성](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**일반적인 오류 코드:**

이벤트 뷰어 로그를 사용하여 오류 코드, 하위 오류 코드, 서버 오류 코드 및 서버 오류 메시지를 찾습니다.

1. 이벤트 뷰어에서 사용자 장치 등록 이벤트 로그를 **엽니다.** 응용 **프로그램 및 서비스 로그** > **마이크로 소프트** > **윈도우** > **사용자 장치 등록** 아래에 위치
2. 다음 eventID 305가 있는 이벤트 찾기

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>구성 오류

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - 이유: 인증 프로토콜은 WS-Trust가 아닙니다.
   - 해결 방법: 온-프레미스 ID 공급자는 WS-Trust를 지원해야 합니다. 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - 이유: 온-프레미스 페더레이션 서비스가 XML 응답을 반환하지 않았습니다.
   - 해결 방법: MEX 끝점이 유효한 XML을 반환하고 있는지 확인합니다. 프록시가 xml이 아닌 응답을 방해하고 반환하지 않는지 확인합니다.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - 이유: 사용자 이름/암호 인증을 위한 끝점을 검색할 수 없습니다.
   - 해결 방법: 온-프레미스 ID 공급자 설정을 확인합니다. WS-Trust 끝점이 활성화되어 있는지 확인하고 MEX 응답에 이러한 올바른 끝점이 포함되어 있는지 확인합니다.

##### <a name="network-errors"></a>네트워크 오류

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82e2/-894947614)
   - 이유: 일반 네트워크 시간 시간.
   - 해결 방법: `https://login.microsoftonline.com` SYSTEM 컨텍스트에서 액세스할 수 있는지 확인합니다. SYSTEM 컨텍스트에서 온-프레미스 ID 공급자에 액세스할 수 있는지 확인합니다. 자세한 내용은 [네트워크 연결 요구 사항을](hybrid-azuread-join-managed-domains.md#prerequisites)참조하십시오.
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - 이유: 인증 끝점과의 연결이 중단되었습니다.
   - 해결 방법: 언젠가 다시 시도하거나 대체 안정적인 네트워크 위치에서 조인을 시도합니다.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - 이유: 이전에 SSL(보안 소켓 계층)이라고 불리던 전송 계층 보안(TLS)은 서버에서 보낸 인증서의 유효성을 검사할 수 없습니다.
   - 해결 방법: 클라이언트 시간 기울이기 확인합니다. 언젠가 다시 시도하거나 대체 안정적인 네트워크 위치에서 조인을 시도합니다. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - 이유: 연결 시도가 `https://login.microsoftonline.com` 실패했습니다.
   - 해결 방법: 에 `https://login.microsoftonline.com`대한 네트워크 연결을 확인합니다.

##### <a name="other-errors"></a>기타 오류

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - 이유: 온-프레미스 ID 공급자의 SAML 토큰은 Azure AD에서 허용되지 않았습니다.
   - 해결 방법: 페더레이션 서버 설정을 확인합니다. 인증 로그에서 서버 오류 코드를 찾습니다.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - 이유: 서버 WS-Trust 응답에서 오류 예외를 보고했지만 어설션을 얻지 못했습니다.
   - 해결 방법: 페더레이션 서버 설정을 확인합니다. 인증 로그에서 서버 오류 코드를 찾습니다.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - 이유: 토큰 끝점에서 액세스 토큰을 얻으려고 할 때 오류가 발생했습니다.
   - 해결 방법: ADAL 로그의 기본 오류를 찾습니다. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - 이유: 일반 ADAL 실패
   - 해결 방법: 인증 로그에서 하위 오류 코드 또는 서버 오류 코드를 찾습니다.
    
#### <a name="join-phase"></a>조인 단계

실패 이유:

등록 유형을 찾아 아래 목록에서 오류 코드를 찾습니다.

#### <a name="windows-10-1803-and-above"></a>윈도우 10 1803 이상

조인 상태 출력의 '진단 데이터' 섹션에서 '이전 등록' 하위 섹션을 찾습니다. 이 섹션은 장치가 도메인에 가입되어 있고 Azure AD 조인을 하이브리드할 수 없는 경우에만 표시됩니다.
'등록 유형' 필드는 수행된 조인 유형을 나타냅니다.

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

#### <a name="older-windows-10-versions"></a>이전 윈도우 10 버전

이벤트 뷰어 로그를 사용하여 조인 실패에 대한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 사용자 장치 등록 이벤트 로그를 **엽니다.** 응용 **프로그램 및 서비스 로그** > **마이크로 소프트** > **윈도우** > **사용자 장치 등록** 아래에 위치
2. 다음 이벤트IDs 204와 함께 이벤트 찾기

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>DRS 서버에서 반환된 HTTP 오류

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - 이유: 오류 코드가 있는 DRS에서 오류 응답을 받았습니다.
   - 해결 방법: 가능한 이유와 해결 방법을 보려면 서버 오류 코드를 참조하십시오.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c002/-2145648638)
   - 이유: 오류 코드가 있는 DRS에서 오류 응답을 받았습니다: "인증 오류" 및 오류 하위 코드는 "DeviceNot"이 아닙니다. 
   - 해결 방법: 가능한 이유와 해결 방법을 보려면 서버 오류 코드를 참조하십시오.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c006/-2145648634)
   - 이유: 오류 코드가 있는 DRS에서 오류 응답을 받았습니다.
   - 해결 방법: 가능한 이유와 해결 방법을 보려면 서버 오류 코드를 참조하십시오.

##### <a name="tpm-errors"></a>TPM 오류

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - 이유: TPM 작업이 실패했거나 유효하지 않음
   - 해상도: 잘못된 sysprep 이미지로 인해 발생할 수 있습니다. sysprep 이미지가 생성된 컴퓨터가 Azure AD 조인, 하이브리드 Azure AD 조인 또는 Azure AD가 등록되어 있지 않은지 확인합니다.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - 이유: 일반 TPM 오류입니다. 
   - 해결 방법: 이 오류가 있는 장치에서 TPM을 사용하지 않도록 설정합니다. Windows 10 버전 1809 이상은 TPM 오류를 자동으로 감지하고 TPM을 사용하지 않고 하이브리드 Azure AD 조인을 완료합니다.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - 이유: FIPS 모드에서 TPM이 현재 지원되지 않습니다.
   - 해결 방법: 이 오류가 있는 장치에서 TPM을 사용하지 않도록 설정합니다. Windows 1809는 TPM 오류를 자동으로 감지하고 TPM을 사용하지 않고 하이브리드 Azure AD 조인을 완료합니다.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - 이유: TPM이 잠겼습니다.
   - 해결 방법: 일시적인 오류입니다. 대기시간 동안 기다립니다. 시간이 지나면 조인 시도가 성공합니다. 자세한 내용은 [TPM 기본](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering) 사항 에서 찾을 수 있습니다.

##### <a name="network-errors"></a>네트워크 오류

- **WININET_E_TIMEOUT** (0x80072e2/-2147012894)
   - 이유: DRS에서 장치를 등록하려고 하는 일반 네트워크 시간
   - 해결 방법: 에 `https://enterpriseregistration.windows.net`대한 네트워크 연결을 확인합니다.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072e7/-2147012889)
   - 이유: 서버 이름 또는 주소를 확인할 수 없습니다.
   - 해결 방법: 에 `https://enterpriseregistration.windows.net`대한 네트워크 연결을 확인합니다. 호스트 이름에 대한 DNS 해상도가 n/w 및 장치에서 정확한지 확인합니다.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - 이유: 서버와의 연결이 비정상적으로 종료되었습니다.
   - 해결 방법: 언젠가 다시 시도하거나 대체 안정적인 네트워크 위치에서 조인을 시도합니다.

##### <a name="federated-join-server-errors"></a>페더레이션 된 조인 서버 오류

| 서버 오류 코드 | 서버 오류 메시지 | 가능한 원인 | 해결 방법 |
| --- | --- | --- | --- |
| 디렉터리 오류 | 요청이 일시적으로 제한되었습니다. 300초 후에 시도하십시오. | 예상되는 오류입니다. 여러 번의 등록 요청을 빠르게 하기 때문일 수 있습니다. | 재사용 대기시간 이후에 다시 시도 조인 |

##### <a name="sync-join-server-errors"></a>동기화 조인 서버 오류

| 서버 오류 코드 | 서버 오류 메시지 | 가능한 원인 | 해결 방법 |
| --- | --- | --- | --- |
| 디렉터리 오류 | AADSTS90002: <UUID> 테넌트를 찾을 수 없습니다. 테넌트에 대한 활성 구독이 없는 경우 이 오류가 발생할 수 있습니다. 구독 관리자에게 문의하십시오. | SCP 개체의 테넌트 ID가 올바르지 않습니다. | SCP 개체가 올바른 Azure AD 테넌트 ID 및 활성 구독으로 구성되고 테넌트에 있는지 확인합니다. |
| 디렉터리 오류 | 지정된 ID에 의한 장치 개체를 찾을 수 없습니다. | 동기화 조인에 대한 예상 오류입니다. 장치 개체가 AD에서 Azure AD로 동기화되지 않았습니다. | Azure AD Connect 동기화가 완료될 때까지 기다렸다가 동기화완료 후 다음 조인 시도가 문제를 해결합니다. |
| 인증오류 | 대상 컴퓨터의 SID 확인 | Azure AD 장치의 인증서가 동기화 조인 중에 Blob에 서명하는 데 사용된 인증서와 일치하지 않습니다. 이 오류는 일반적으로 동기화가 아직 완료되지 않았음을 의미합니다. |  Azure AD Connect 동기화가 완료될 때까지 기다렸다가 동기화완료 후 다음 조인 시도가 문제를 해결합니다. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>5단계: 로그 수집 및 Microsoft 지원 팀에 문의

여기에 공개 스크립트를 가져옵니다: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. 관리자 명령 프롬프트를 열고 실행합니다. `start_ngc_tracing_public.cmd`
2. 단계를 수행하여 문제를 재현합니다.
3. 을 실행하여 로깅 스크립트 `stop_ngc_tracing_public.cmd`실행을 중지합니다.
4. 압축 및 분석을 위해 `%SYSTEMDRIVE%\TraceDJPP\*` 아래 로그를 보냅니다.

## <a name="troubleshoot-post-join-issues"></a>가입 후 문제 해결

### <a name="retrieve-the-join-status"></a>조인 상태 검색 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: 예 및 AzureADPrt: 예
  
이러한 필드는 사용자가 디바이스에 로그인 시 Azure AD에서 성공적으로 인증되었는지 여부를 나타냅니다. 값이 **아니요**인 경우 다음의 원인 때문일 수 있습니다.

- 등록 시 장치와 연결된 TPM의 잘못된 저장소 키(상승 실행 중 KeySignTest 확인).
- 대체 로그인 ID
- HTTP 프록시를 찾을 수 없음

## <a name="known-issues"></a>알려진 문제
- 설정 -> 계정 -> 액세스 작업 또는 학교, 하이브리드 Azure AD 결합 된 장치는 모바일 핫스팟 또는 외부 WiFi 네트워크에 연결 될 때 Azure AD및 온-프레미스 AD에 대 한 하나 두 개의 서로 다른 계정을 표시할 수 있습니다. 이는 UI 문제일 뿐이며 기능에 영향을 미치지 않습니다. 
 
## <a name="next-steps"></a>다음 단계

[dsregcmd 명령을 사용하여 장치 문제 해결](troubleshoot-device-dsregcmd.md) 을 계속

질문은 [디바이스 관리 FAQ](faq.md)를 참조하세요.
