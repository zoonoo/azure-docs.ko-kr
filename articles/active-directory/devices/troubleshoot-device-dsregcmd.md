---
title: dsregcmd 명령을 사용한 문제 해결 - Azure Active Directory
description: dsregcmd의 출력을 사용하여 Azure AD의 장치 상태를 이해합니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128754"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>dsregcmd 명령을 사용하여 장치 문제 해결

dsregcmd /status 유틸리티는 도메인 사용자 계정으로 실행되어야 합니다.

## <a name="device-state"></a>디바이스 상태

이 섹션에서는 장치 조인 상태 매개 변수를 나열합니다. 아래 표에는 장치가 다양한 조인 상태에 있을 수 있는 기준이 나와 있습니다.

| AzureAd인조인 | 엔터프라이즈조인 | 도메인 가입 | 디바이스 상태 |
| ---   | ---   | ---   | ---   |
| YES | 아니요 | 아니요 | Azure AD 조인 |
| 아니요 | 아니요 | YES | 도메인 가입 |
| YES | 아니요 | YES | 하이브리드 광고 결합 |
| 아니요 | YES | YES | 온-프레미스 DRS 가입 |

> [!NOTE]
> Workplace 조인(Azure AD 등록) 상태가 "사용자 상태" 섹션에 표시됩니다.

- **AzureAd조인가:** 장치가 Azure AD에 조인된 경우 -"예"로 설정합니다. 그렇지 않으면 "아니오".
- **EnterpriseJoined:** - 장치가 온-프레미스 DRS에 조인된 경우 "YES"로 설정합니다. 장치는 엔터프라이즈인조인 및 AzureAd인조인(AzureAdJoined)일 수 없습니다.
- **도메인조인:** 장치가 도메인(AD)에 가입된 경우 "YES"로 설정합니다.
- **도메인 이름:** - 장치가 도메인에 가입된 경우 도메인 이름으로 설정됩니다.

### <a name="sample-device-state-output"></a>샘플 장치 상태 출력

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>디바이스 세부 정보

장치가 Azure AD 조인또는 하이브리드 Azure AD가 조인된 경우에만 표시됩니다(Azure AD가 등록되지 않음). 이 섹션에서는 클라우드에 저장된 세부 정보를 식별하는 장치를 나열합니다.

- **DeviceId:** - Azure AD 테넌트에 있는 장치의 고유 ID
- **지문 :** - 장치 인증서의 지문 
- **장치인증서 유효기간:** - 장치 인증서의 유효성
- **키컨테이너Id:** - 장치 인증서와 연결된 장치 개인 키의 ContainerId
- **키 공급자:** - 장치 개인 키를 저장하는 데 사용되는 키 공급자(하드웨어/소프트웨어)입니다.
- **TpmProtected:** - 장치 개인 키가 하드웨어 TPM에 저장되어 있는 경우 "YES"입니다.

### <a name="sample-device-details-output"></a>샘플 장치 세부 정보 출력

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>테넌트 세부 정보

장치가 Azure AD 조인또는 하이브리드 Azure AD가 조인된 경우에만 표시됩니다(Azure AD가 등록되지 않음). 이 섹션에서는 장치가 Azure AD에 조인될 때 의 일반적인 테넌트 세부 정보를 나열합니다.

> [!NOTE]
> 이 섹션의 MDM URL이 비어 있으면 MDM이 구성되지 않았거나 현재 사용자가 MDM 등록 범위에 있지 않음을 나타냅니다. Azure AD의 이동성 설정을 확인하여 MDM 구성을 검토합니다.

> [!NOTE]
> MDM URL이 표시되어 있다고 해서 장치가 MDM에서 관리된다는 의미는 아닙니다. 테넌트에 장치 자체가 관리되지 않는 경우에도 자동 등록을 위한 MDM 구성이 있는 경우 정보가 표시됩니다. 

### <a name="sample-tenant-details-output"></a>샘플 테넌트 세부 정보 출력

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>사용자 상태

이 섹션에는 현재 장치에 로그인한 사용자의 다양한 특성 상태가 나열됩니다.

> [!NOTE]
> 유효한 상태를 검색하려면 명령이 사용자 컨텍스트에서 실행되어야 합니다.

- **NgcSet:** - 현재 로그온한 사용자에 대해 Windows Hello 키가 설정된 경우 "YES"로 설정합니다.
- **NgcKeyId:** - 현재 로그온한 사용자에 대해 설정된 경우 Windows Hello 키의 ID입니다.
- **CanReset:** - 사용자가 Windows Hello 키를 재설정할 수 있는지 를 나타냅니다. 
- **가능한 값:** - 파괴만, 비파괴만, 파괴및 비파괴적, 또는 오류일 경우 알 수 없음. 
- **Workplace조됨:** 현재 NTUSER 컨텍스트에서 Azure AD 등록 계정이 장치에 추가된 경우 "YES"로 설정합니다.
- **WamDefaultSet:** - 로그인한 사용자에 대해 WAM 기본 WebAccount가 만들어지면 "YES"로 설정합니다. 이 필드에는 상승된 명령 프롬프트에서 dsreg /status가 실행되는 경우 오류가 표시될 수 있습니다. 
- **WamDefaultAuthority:** - Azure AD에 대해 "조직"으로 설정합니다.
- **WamDefaultId:** -https://login.microsoft.com항상 " " Azure AD에 대해.
- **WamDefaultGUID:** - 기본 WAM 웹 계정에 대한 WAM 공급자의 (Azure AD / Microsoft 계정) GUID. 

### <a name="sample-user-state-output"></a>샘플 사용자 상태 출력

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO 상태

Azure AD 등록 장치에 대해 이 섹션을 무시할 수 있습니다.

> [!NOTE]
> 해당 사용자에 대 한 유효한 상태를 검색 하려면 사용자 컨텍스트에서 실행 해야 합니다.

- **AzureAdPrt:** - 로그온한 사용자의 장치에 PRT가 있는 경우 "YES"로 설정합니다.
- **AzureAdPrtUpdateTime:** - PRT가 마지막으로 업데이트된 시간(UTC)으로 설정합니다.
- **AzureAdPrtExpiryTime:** - PRT가 갱신되지 않으면 만료될 때 UTC의 시간으로 설정합니다.
- **AzureAdPrt권한:** - Azure AD 기관 URL
- **EnterprisePrt:** - 장치가 온-프레미스 ADFS에서 PRT가 있는 경우 "YES"로 설정합니다. 하이브리드 Azure AD 조인 장치의 경우 장치는 Azure AD 및 온-프레미스 AD모두에서 동시에 PRT를 가질 수 있습니다. 온-프레미스에 가입된 장치에는 엔터프라이즈 PRT만 있습니다.
- **EnterprisePrtUpdateTime:** - 엔터프라이즈 PRT가 마지막으로 업데이트된 시간으로 설정합니다.
- **EnterprisePrtExpiryTime:** - PRT가 갱신되지 않으면 만료될 때 UTC의 시간으로 설정합니다.
- **엔터프라이즈프런트 기관:** - ADFS 기관 URL

### <a name="sample-sso-state-output"></a>샘플 SSO 상태 출력

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>진단 데이터

### <a name="pre-join-diagnostics"></a>사전 조인 진단

이 섹션은 장치가 도메인에 가입되어 있고 Azure AD 조인을 하이브리드할 수 없는 경우에만 표시됩니다.

이 섹션에서는 조인 실패를 진단하는 데 도움이 되는 다양한 테스트를 수행합니다. 이 섹션에는 이전(?)의 세부 정보도 포함되어 있습니다. 이 정보에는 오류 단계, 오류 코드, 서버 요청 ID, 서버 응답 http 상태, 서버 응답 오류 메시지가 포함됩니다.

- **사용자 컨텍스트:** - 진단 프로그램이 실행되는 컨텍스트입니다. 가능한 값: 시스템, UN-상승 된 사용자, 상승 된 사용자. 

   > [!NOTE]
   > 실제 조인은 SYSTEM 컨텍스트에서 수행되므로 SYSTEM 컨텍스트에서 진단을 실행하는 것은 실제 조인 시나리오에 가장 가깝습니다. SYSTEM 컨텍스트에서 진단을 실행하려면 dsregcmd /status 명령을 상승된 명령 프롬프트에서 실행해야 합니다.

- **클라이언트 시간:** - UTC의 시스템 시간입니다.
- **AD 연결 테스트:** - 테스트는 도메인 컨트롤러에 대한 연결 테스트를 수행합니다. 이 테스트의 오류로 인해 사전 검사 단계에서 조인 오류가 발생할 수 있습니다.
- **AD 구성 테스트:** - SCP 개체가 온-프레미스 AD 포리스트에서 제대로 구성되었는지 여부를 테스트하고 확인합니다. 이 테스트의 오류로 인해 오류 코드 0x801c001d가 있는 검색 단계에서 조인 오류가 발생할 수 있습니다.
- **DRS 검색 테스트:** - 테스트에서 검색 메타데이터 끝점에서 DRS 끝점을 얻고 사용자 영역 요청을 수행합니다. 이 테스트의 오류로 인해 검색 단계에서 조인 오류가 발생할 수 있습니다.
- **DRS 연결 테스트:** - DRS 엔드포인트에 대한 기본 연결 테스트를 수행합니다.
- **토큰 획득 테스트:** - 사용자 테넌트가 페더레이션된 경우 테스트에서 Azure AD 인증 토큰을 얻으려고 시도합니다. 이 테스트의 오류로 인해 인증 단계에서 조인 오류가 발생할 수 있습니다. 아래 레지스트리 키 설정에서 대체를 명시적으로 사용하지 않도록 설정하지 않는 한 auth 동기화 조인이 대체로 시도됩니다.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **동기화 조인으로 돌아가기:** - 위의 레지스트리 키가 인증 실패와 동기화하는 대체조인을 방지하기 위해 "사용"으로 설정하지 않습니다. 이 옵션은 Windows 10 1803 이상에서 사용할 수 있습니다.
- **이전 등록:** - 이전 조인 시도가 발생한 시간입니다. 실패한 조인 시도만 기록됩니다.
- **오류 단계:** - 조인이 중단된 단계입니다. 가능한 값은 사전 확인, 검색, 인증, 조인입니다.
- **클라이언트 오류 코드:** - 클라이언트 오류 코드가 반환됨(HRESULT).
- **서버 오류 코드:** - 요청이 서버로 전송되고 서버가 오류 코드로 다시 응답한 경우 서버 오류 코드입니다. 
- **서버 메시지:** - 오류 코드와 함께 서버 메시지가 반환되었습니다.
- **HTTPS 상태:** - 서버에서 반환하는 Http 상태입니다.
- **요청 ID:** - 클라이언트 requestId가 서버로 전송되었습니다. 서버 측 로그와 상관 관계를 맺는 데 유용합니다.

### <a name="sample-pre-join-diagnostics-output"></a>샘플 사전 조인 진단 출력

다음 예제에서는 검색 오류로 실패한 진단 테스트가 표시됩니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

다음 예제에서는 진단 테스트가 통과하고 있지만 동기화 조인에 예상되는 디렉터리 오류로 등록 시도가 실패했습니다. Azure AD Connect 동기화 작업이 완료되면 장치가 조인될 수 있습니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

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

### <a name="post-join-diagnostics"></a>조인 후 진단

이 섹션에는 클라우드에 조인된 장치에서 수행된 온전도 검사의 출력이 표시됩니다.

- **AadRecoveryEnabled:** - "YES"인 경우 장치에 저장된 키를 사용할 수 없으며 장치가 복구용으로 표시됩니다. 다음 로그인은 복구 흐름을 트리거하고 장치를 다시 등록합니다.
- **KeySignTest:** - 장치 키가 "통과"되면 상태가 양호합니다. KeySignTest에 실패하면 일반적으로 장치가 복구용으로 표시됩니다. 다음 로그인은 복구 흐름을 트리거하고 장치를 다시 등록합니다. 하이브리드 Azure AD 조인 장치의 경우 복구가 무음입니다. Azure AD가 가입하거나 Azure AD가 등록된 동안 장치는 필요한 경우 사용자 인증을 복구하고 다시 등록하라는 메시지를 표시합니다. **KeySignTest에는 높은 권한이 필요합니다.**

#### <a name="sample-post-join-diagnostics-output"></a>조인 후 진단 출력 샘플

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC 필수 구성 조건 확인

이 섹션에서는 WHFB(비즈니스용 Windows Hello) 프로비저닝에 대한 필수 검사를 수행합니다. 

> [!NOTE]
> 사용자가 WHFB를 이미 성공적으로 구성한 경우 ngC 필수 확인 세부 정보가 dsregcmd /status에 표시되지 않을 수 있습니다.

- **IsDevice조인:** 장치가 Azure AD에 조인된 경우 "YES"로 설정합니다.
- **IsUserAzureAD:** - 로그인한 사용자가 Azure AD에 있는 경우 "YES"로 설정합니다.
- **정책 활성화:** 장치에서 WHFB 정책을 사용하도록 설정한 경우 "YES"로 설정합니다.
- **PostLogonEnabled:** - WHFB 등록이 기본적으로 플랫폼에 의해 트리거되는 경우 "YES"로 설정합니다. "NO"로 설정된 경우 비즈니스용 Windows Hello 등록이 사용자 지정 메커니즘에 의해 트리거된다는 것을 나타냅니다.
- **장치 적격:** 장치가 WHFB 등록을 위한 하드웨어 요구 사항을 충족하는 경우 "YES"로 설정합니다.
- **SessionIsNotRemote:** - 현재 사용자가 원격으로 가 아닌 장치에 직접 로그인한 경우 "YES"로 설정합니다.
- **인증서 등록:** - WHFB 인증서 트러스트 배포에 따라 WHFB에 대한 인증서 등록 권한을 나타냅니다. WHFB 정책의 소스가 그룹 정책인 경우 "등록 기관"으로 설정하면 소스가 MDM인 경우 "모바일 장치 관리"입니다. 그렇지 않으면 "없음"
- **AdfsRefreshToken:** - WHFB 인증서 신뢰 배포에 특정. CertEnrollment가 "등록 권한"인 경우에만 존재합니다. 장치에 사용자에 대한 엔터프라이즈 PRT가 있는지 나타냅니다.
- **AdfsRaIsReady:** - WHFB 인증서 신뢰 배포에 특정.  CertEnrollment가 "등록 권한"인 경우에만 존재합니다. ADFS가 WHFB를 지원하는 검색 메타데이터에 *표시되어 있고* 로그온 인증서 템플릿을 사용할 수 있는 경우 "YES"로 설정합니다.
- **LogonCertTemplate준비:** - WHFB 인증서 신뢰 배포에 특정. CertEnrollment가 "등록 권한"인 경우에만 존재합니다. 로그온 인증서 템플릿의 상태가 유효하고 ADFS RA 문제를 해결하는 데 도움이 되는 경우 "YES"로 설정합니다.
- **PreReqResult:** - 모든 WHFB 전제 조건 평가의 결과를 제공합니다. WHFB 등록이 다음에 사용자가 로그인할 때 로그온 후 작업으로 시작되는 경우 "프로비전"으로 설정합니다.

### <a name="sample-ngc-prerequisite-check-output"></a>샘플 NGC 필수 구성조건 검사 출력

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>다음 단계

질문은 [디바이스 관리 FAQ](faq.md)를 참조하세요.
