---
title: "Windows 10 및 Windows Server 2016에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결 | Microsoft Docs"
description: "Windows 10 및 Windows Server 2016에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fe9fad2de3fbb63e51fa14215c6f240977c56c7b
ms.openlocfilehash: c72cebd04216b4e2bba26dd306354a7cd4e6c49a


---
# <a name="troubleshooting-the-auto-registration-of-azure-ad-domain-joined-computers-for-windows-10-and-windows-server-2016"></a>Windows 10 및 Windows Server 2016에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결

이 항목은 다음 클라이언트에 적용됩니다.

-   Windows 10
-   Windows Server 2016

기타 Windows 클라이언트의 경우 [Windows 하위 수준 클라이언트에 대한 Azure AD 도메인 조인 컴퓨터의 자동 등록 문제 해결](active-directory-conditional-access-automatic-device-registration-troubleshoot-windows-legacy.md)을 참조하세요.

이 항목에서는 다음 시나리오를 지원하기 위해 [Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 구성하는 방법](active-directory-conditional-access-automatic-device-registration-get-started.md)에 설명된 대로 도메인 조인 장치의 자동 등록을 구성했다고 가정합니다.

1.  [장치 기반 조건부 액세스](active-directory-conditional-access-automatic-device-registration-setup.md)

2.  [엔터프라이즈 설정 로밍](active-directory-windows-enterprise-state-roaming-overview.md)

3.  [비즈니스용 Windows Hello](active-directory-azureadjoin-passport-deployment.md)


이 문서에서는 잠재적인 문제를 해결하는 방법에 대한 문제 해결 지침을 제공합니다. 

등록은 Windows 10 2015년 11월 업데이트 이상에서 지원됩니다.  
위 시나리오를 사용하려면 기념일 업데이트를 사용하는 것이 좋습니다.

## <a name="step-1-retrieve-the-registration-status"></a>1단계: 등록 상태 검색 

**등록 상태를 검색하려면**

1. 관리자 권한으로 명령 프롬프트를 엽니다.

2. **dsregcmd /status**를 입력합니다.



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>2단계: 등록 상태 평가 

다음 필드를 검토하고 예상 값을 갖는지 확인합니다.

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

이 필드는 장치가 Azure AD에 등록되어 있는지 여부를 나타냅니다. 이 값이 'NO'로 표시되면 등록이 완료되지 않은 것입니다. 

**가능한 원인:**

1.  등록을 위한 컴퓨터 인증이 실패했습니다.

2.  조직에 컴퓨터에서 검색될 수 없는 HTTP 프록시가 있습니다.

3.  컴퓨터가 인증을 위해 Azure AD에, 또는 등록을 위해 Azure DRS에 연결할 수 없습니다.

4.  컴퓨터가 조직의 내부 네트워크 또는 온-프레미스 AD 도메인 컨트롤러의 직접 가시선 내에 있는 VPN에 있지 않습니다.

5.  컴퓨터에 TPM이 있는 경우 잘못된 상태일 수도 있습니다.

6.  이전에 문서에 설명된 것처럼 다시 확인해야 하는 잘못된 서비스 구성이 있을 수 있습니다. 일반적인 예제는 다음과 같습니다.

    1.  페더레이션 서버에서 Ws-Trust 끝점이 사용되도록 설정되어 있지 않습니다.

    2.  페더레이션 서버는 Windows 통합 인증을 사용한 네트워크 컴퓨터에서의 인바운드 인증을 허용하지 않을 수 있습니다.

    3.  컴퓨터가 속한 AD 포리스트의 Azure AD에서 확인된 도메인 이름을 가리키는 서비스 연결 지점 개체가 없습니다.

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

이 필드는 장치가 온-프레미스 Active Directory에 조인되는지 여부를 보여 줍니다. 값이 'NO'로 표시되면 장치는 Azure AD에 자동으로 등록될 수 없습니다. Azure AD에 등록할 수 있으려면 먼저 장치가 온-프레미스 Active Directory에 조인되는지 확인합니다. Azure AD에 컴퓨터를 직접 조인하려면 Azure Active Directory 조인 기능 알아보기로 이동하세요.

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

이 필드는 장치가 Azure AD에 개인 장치로 등록되어 있는지 여부를 보여 줍니다(“작업 영역 조인”으로 표시). 이 값이 Azure AD에 등록된 도메인 조인 컴퓨터에 대해 'NO'로 표시되어야 하는데 YES로 표시되면 컴퓨터 등록을 완료하기 전에 회사 또는 학교 계정이 추가되었다는 것을 의미합니다. 이 경우 Windows 10 기념일 업데이트 버전(1607 '실행' 창 또는 명령 프롬프트 창에서 WinVer 명령을 실행하는 경우 1607 표시)을 사용하면 이러한 계정은 무시됩니다.

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES 및 AzureADPrt : YES
  
이러한 필드는 장치에 로그인 시 Azure AD에서 성공적으로 인증되었음을 나타냅니다. ‘NO’가 표시될 경우 가능한 원인은 다음과 같습니다.

1.  등록 시 장치에 연결된 TPM에 잘못된 저장소 키(STK)가 있습니다(상승된 권한으로 실행하면서 KeySignTest 확인).

2.  대체 로그인 ID

3.  HTTP 프록시를 찾을 수 없음

## <a name="next-steps"></a>다음 단계

자세한 내용은 [자동 장치 등록 FAQ](active-directory-conditional-access-automatic-device-registration-faq.md)를 참조하세요. 


<!--HONumber=Feb17_HO1-->


