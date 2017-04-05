---
title: "기존 NPS 서버를 사용하여 Azure MFA 기능 제공 | Microsoft Docs"
description: "Azure Multi-Factor Authentication용 NPS(네트워크 정책 서버) 확장은 기존 인증 인프라에 클라우드 기반 2단계 인증 기능을 추가하는 간단한 솔루션입니다."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 34919221a82a024bd3a1d09c1def6040ff6c55e1
ms.lasthandoff: 03/23/2017

---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication---public-preview"></a>기존 NPS 인프라를 Azure Multi-Factor Authentication과 통합 - 공개 미리 보기

Azure MFA의 NPS(네트워크 정책 서버) 확장은 기존 서버를 사용하여 인증 인프라에 클라우드 기반 MFA 기능을 추가합니다. NPS 확장을 사용하면 새 서버를 설치, 구성 및 유지할 필요 없이 전화 통화, SMS 또는 휴대폰 앱 인증을 기존 인증 흐름에 추가할 수 있습니다. 
 
Azure MFA용 NPS 확장을 사용하면 인증 흐름에 다음 구성 요소가 포함됩니다. 

1. **NAS/VPN 서버** - VPN 클라이언트로부터 요청을 받고, 이 요청을 NPS 서버에 대한 RADIUS 요청으로 변환합니다. 
2. **NPS 서버** - Active Directory에 연결하여 RADIUS 요청에 대한 기본 인증을 수행하고, 성공하면 요청을 설치된 모든 확장에 전달합니다.  
3. **NPS 확장** - 보조 인증을 위해 Azure MFA에 요청을 트리거합니다. 확장에서 응답을 받고 MFA 요청이 성공하면 Azure STS에서 발급한 MFA 클레임이 포함된 보안 토큰을 NPS 서버에 제공하여 인증 요청을 완료합니다.  
4. **Azure MFA** - Azure Active Directory와 통신하여 사용자의 세부 정보를 검색하고 구성된 인증 방법을 사용하여 사용자에게 보조 인증을 수행합니다.

다음 다이어그램에서는 이러한 높은 수준의 인증 요청 흐름을 보여 줍니다. 

![인증 흐름 다이어그램](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="prerequisites"></a>필수 조건

NPS 확장은 기존 인프라와 함께 사용할 수 있습니다. 시작하기 전에 다음 필수 조건을 갖추고 있는지 확인합니다.

### <a name="licenses"></a>라이선스

Azure AD Premium, EMS 또는 MFA 구독에 포함된 [Azure Multi-Factor Authentication 라이선스](multi-factor-authentication.md)를 가진 고객은 Azure MFA용 NPS 확장을 사용할 수 있습니다.

### <a name="software"></a>소프트웨어

NPS 구성 요소를 사용하도록 설정된 Windows Server 2008 R2 SP1 이상

### <a name="libraries"></a>라이브러리

-    [Visual Studio 2013(X64)용 Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Windows PowerShell용 Microsoft Azure Active Directory 모듈 버전 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

NPS 확장을 사용하는 모든 사용자는 Azure AD Connect를 사용하여 Azure Active Directory와 동기화해야 하며 MFA를 사용하도록 설정해야 합니다.

확장을 설치할 때 Azure AD 테넌트에 대한 디렉터리 ID와 관리자 자격 증명이 필요합니다. [Azure Portal](https://portal.azure.com)에서 디렉터리 ID를 찾을 수 있습니다. 관리자로 로그인하고 왼쪽의 **Azure Active Directory** 아이콘을 선택한 다음 **속성**을 선택합니다. **디렉터리 ID** 상자에서 GUID를 복사하고 저장합니다.

![Azure Active Directory 속성에서 디렉터리 ID 찾기](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="install-the-nps-extension"></a>NPS 확장 설치

> [!IMPORTANT]
> VPN 액세스 지점 이외의 다른 서버에 NPS 확장을 설치합니다. 

Azure MFA용 NPS 확장을 설치하려면

1.    Microsoft 다운로드 센터에서 [NPS 확장을 다운로드합니다](https://aka.ms/npsmfa).
2.    이진 파일을 구성할 NPS(네트워크 정책 서버)에 복사합니다.
3.    *setup.exe*를 실행하고 설치 지침을 따릅니다.

설치를 완료하면 설치 관리자에서 `C:\Program Files\Microsoft\AzureMfa\Config`(여기서 C: \는 설치 드라이브임) 위치에 PowerShell 스크립트를 만듭니다. 이 PowerShell 스크립트는 다음 작업을 수행합니다.

-    자체 서명된 인증서를 만듭니다.
-    인증서의 공개 키를 Azure AD의 서비스 주체에 연결합니다.
-    로컬 컴퓨터 인증서 저장소에 인증서를 저장합니다.
-    네트워크 사용자에게 인증서의 개인 키에 대한 액세스 권한을 부여합니다.
-    NPS를 다시 시작합니다.

PowerShell 스크립트에서 생성하는 자체 서명된 인증서 대신 사용자 고유의 인증서를 사용하려는 경우가 아니면 PowerShell 스크립트를 실행하여 설치를 완료합니다. 여러 서버에서 확장을 설치하는 경우 인증서를 갱신할 때 가동 중지 시간이 없도록 서버마다 자체 인증서가 있어야 합니다. 

## <a name="configure-your-nps-extension"></a>NPS 확장 구성

이 섹션에는 성공적인 NPS 확장 배포를 위한 디자인 고려 사항 및 제안 사항이 포함되어 있습니다.

### <a name="configurations-limitations"></a>구성 제한 사항

- NPS 확장은 새 배포에 사용되며 기존 배포에는 사용되지 않습니다. 이러한 이유로 Azure MFA용 NPS 확장에는 사용자 및 설정을 MFA 서버에서 클라우드로 마이그레이션하는 도구가 없습니다.

- NPS 확장은 온-프레미스 Active Directory의 UPN을 사용하여 Azure MFA에서 보조 인증을 수행하는 사용자를 식별합니다. 확장은 대체 로그인 ID 또는 UPN 이외의 사용자 지정 AD 필드와 같은 다른 식별자를 사용하도록 구성될 수 없습니다.  

### <a name="control-radius-clients-that-require-mfa"></a>MFA가 필요한 RADIUS 클라이언트 제어

NPS 확장을 사용하여 RADIUS 클라이언트에 대해 MFA를 사용하도록 설정하면 이 클라이언트에 대한 모든 인증에서 MFA를 수행해야 합니다. 일부 RADIUS 클라이언트에만 MFA를 사용하고 다른 클라이언트에는 MFA를 사용하지 않도록 설정하려면 두 개의 NPS 서버를 구성하고 그 중 하나에만 확장을 설치합니다. MFA에서 확장을 사용하여 구성된 NPS 서버로 요청을 보내고 다른 RADIUS 클라이언트는 확장을 사용하여 구성되지 않은 NPS 서버로 요청을 보내도록 RADIUS 클라이언트를 구성합니다.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>MFA에 등록되지 않은 사용자를 위한 준비

MFA에 등록되지 않은 사용자가 있는 경우 인증을 시도할 때 수행할 작업을 결정할 수 있습니다. *HKLM\Software\Microsoft\AzureMFA* 레지스트리 경로에서 *REQUIRE_USER_MATCH* 레지스트리 설정을 사용하여 기능 동작을 제어합니다. 이 설정에는 다음과 같은 단일 구성 옵션이 있습니다.

| 키 | 값 | 기본값 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | 설정되지 않음(TRUE와 동일) |

이 설정은 사용자가 MFA에 등록되지 않은 경우 수행할 작업을 결정하기 위한 것입니다. 키가 없거나, 설정되지 않았거나, TRUE로 설정되고 사용자가 등록되지 않은 경우 확장은 MFA 요청에 실패합니다. 키가 FALSE로 설정되고 사용자가 등록되지 않은 경우 MFA를 수행하지 않은 채 인증이 진행됩니다.

이 키를 만들고 사용자 온보딩 중에 FALSE로 설정하도록 선택할 수 있습니다. 키를 설정하면 MFA에 등록되지 않은 사용자가 아무런 제지 없이 로그인할 수 있으므로 프로덕션 환경으로 이동하기 전에 이 키를 제거해야 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>클라이언트 인증서가 예상대로 설치되었는지 어떻게 확인합니까?

설치 관리자에서 만든 자체 서명된 인증서를 인증서 저장소에서 찾고, 사용자에게 부여된 **네트워크 서비스** 권한이 개인 키에 있는지 확인합니다. 인증서에는 **CN \<tenantid\>, OU = Microsoft NPS Extension** 주체 이름이 있습니다.

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>내 클라이언트 인증서가 Azure Active Directory에 있는 내 테넌트와 연결되어 있는지 어떻게 확인할 수 있습니까?

PowerShell 명령 프롬프트를 열고 다음 명령을 실행합니다.

```
> import-module MSOnline
> Connect-MsolService
> Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

이 명령은 PowerShell 세션에서 NPS 확장의 인스턴스와 테넌트를 연결하는 인증서를 모든 출력합니다. 클라이언트 인증서를 개인 키 없이 "Base-64 encoded X.509(.cer)" 파일로 내보내 인증서를 찾고 PowerShell의 목록과 비교합니다.

명령이 둘 이상의 인증서를 반환하면 사람이 읽을 수 있는 형식의 Valid-From(유효 기간 시작) 및 Valid-Until(유효 기간) 타임스탬프를 사용하여 확실한 부적격 항목을 필터링할 수 있습니다.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>내 요청이 ADAL 토큰 오류로 인해 실패하는 이유는 무엇입니까?

이 오류는 몇 가지 이유 중 하나로 발생할 수 있습니다. 다음 단계를 사용하여 문제를 해결합니다.

1. NPS 서버를 다시 시작합니다.
2. 클라이언트 인증서가 예상대로 설치되었는지 확인합니다.
3. 인증서가 Azure AD의 테넌트와 연결되어 있는지 확인합니다.
4. 확장을 실행하는 서버에서 https://login.windows.net/에 액세스할 수 있는지 확인합니다.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>사용자를 찾을 수 없다고 하는 HTTP 로그 오류로 인해 인증이 실패하는 이유는 무엇입니까?

AD Connect가 실행 중이고 사용자가 Windows Active Directory와 Azure Active Directory 모두에 있는지 확인합니다.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>내 모든 인증이 실패한 상태의 로그에 HTTP 연결 오류가 표시되는 이유는 무엇입니까?

NPS 확장을 실행하는 서버에서 https://adnotifications.windowsazure.com에 연결할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure MFA를 [Active Directory](multi-factor-authentication-get-started-server-dirint.md), [RADIUS 인증](multi-factor-authentication-get-started-server-radius.md) 및 [LDAP 인증](multi-factor-authentication-get-started-server-ldap.md)과 통합하는 방법을 참조하세요.

