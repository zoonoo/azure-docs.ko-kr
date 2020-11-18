---
title: NPS를 사용 하 여 Azure AD Multi-Factor Authentication 사용-Azure Active Directory
description: 기존 NPS (네트워크 정책 서버) 인증 인프라에서 Azure AD Multi-Factor Authentication 기능을 사용 하는 방법에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 576b9c11f167f7c0d5fcb06e484347c643589a66
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839066"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>기존 NPS (네트워크 정책 서버) 인프라를 Azure AD Multi-Factor Authentication와 통합

Azure AD Multi-Factor Authentication의 NPS (네트워크 정책 서버) 확장은 기존 서버를 사용 하 여 인증 인프라에 클라우드 기반 MFA 기능을 추가 합니다. NPS 확장을 사용하면 새 서버를 설치, 구성 및 유지할 필요 없이 전화 통화, 문자 메시지 또는 휴대폰 앱 인증을 기존 인증 흐름에 추가할 수 있습니다.

NPS 확장은 페더레이션 사용자 또는 동기화 된 사용자에 대 한 두 번째 인증 단계를 제공 하기 위해 RADIUS 및 클라우드 기반 Azure AD Multi-Factor Authentication 간의 어댑터 역할을 합니다.

## <a name="how-the-nps-extension-works"></a>NPS 확장 작동 방법

Azure AD Multi-Factor Authentication의 NPS 확장을 사용 하는 경우 인증 흐름에는 다음 구성 요소가 포함 됩니다.

1. **NAS/VPN 서버** - VPN 클라이언트로부터 요청을 받고, 이 요청을 NPS 서버에 대한 RADIUS 요청으로 변환합니다.
2. **NPS 서버** 는 Active Directory Domain Services (AD DS)에 연결 하 여 RADIUS 요청에 대 한 기본 인증을 수행 하 고, 성공 하면 설치 된 모든 확장에 요청을 전달 합니다.  
3. **NPS 확장** 은 보조 인증을 위해 Azure AD Multi-Factor Authentication에 대 한 요청을 트리거합니다. 확장에서 응답을 받고 MFA 요청이 성공하면 Azure STS에서 발급한 MFA 클레임이 포함된 보안 토큰을 NPS 서버에 제공하여 인증 요청을 완료합니다.
4. **AZURE AD MFA** 는 Azure Active Directory (azure ad)와 통신 하 여 사용자의 세부 정보를 검색 하 고 사용자에 게 구성 된 인증 방법을 사용 하 여 보조 인증을 수행 합니다.

다음 다이어그램에서는 이러한 높은 수준의 인증 요청 흐름을 보여 줍니다.

![NPS 서버와 Azure AD Multi-Factor Authentication NPS 확장에 대 한 VPN 서버를 통해 사용자를 인증 하는 인증 흐름 다이어그램](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>RADIUS 프로토콜 동작 및 NPS 확장

RADIUS는 UDP 프로토콜 이므로 발신자는 패킷 손실을 가정 하 고 응답을 기다립니다 합니다. 일정 시간 후에 연결이 시간 초과 될 수 있습니다. 이 경우 패킷이 대상에 도달 하지 않은 것으로 간주 하 여 패킷이 다시 전송 됩니다. 이 문서의 인증 시나리오에서 VPN 서버는 요청을 보내고 응답을 기다립니다. 연결 시간이 초과 되 면 VPN 서버에서 요청을 다시 보냅니다.

![NPS 서버에서 응답 하는 동안 RADIUS UDP 패킷 흐름 및 요청의 시간 초과 후의 다이어그램](./media/howto-mfa-nps-extension/radius-flow.png)

NPS 서버는 MFA 요청을 계속 처리할 수 있으므로 연결 시간이 초과 되기 전에 VPN 서버의 원래 요청에 응답 하지 않을 수 있습니다. 사용자가 MFA 프롬프트에 성공적으로 응답 하지 않아 Azure AD Multi-Factor Authentication NPS 확장에서 해당 이벤트가 완료 되기를 기다리고 있습니다. 이 경우 NPS 서버는 추가 VPN 서버 요청을 중복 요청으로 식별 합니다. NPS 서버는 이러한 중복 된 VPN 서버 요청을 삭제 합니다.

![RADIUS 서버에서 중복 요청을 취소 하는 NPS 서버 다이어그램](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

NPS 서버 로그를 살펴보면 이러한 추가 요청을 삭제 하는 것을 볼 수 있습니다. 이 동작은 최종 사용자가 단일 인증 시도에 대 한 여러 요청을 가져오는 것을 방지 하기 위한 것입니다. Nps 서버 이벤트 로그에서 삭제 된 요청은 nps 서버 또는 Azure AD Multi-Factor Authentication NPS 확장에 문제가 있음을 나타내지 않습니다.

삭제 된 요청을 최소화 하려면 VPN 서버를 60 초 이상으로 구성 하는 것이 좋습니다. 필요한 경우 이벤트 로그에서 삭제 된 요청을 줄이기 위해 VPN 서버 제한 시간 값을 90 또는 120 초로 늘릴 수 있습니다.

이 UDP 프로토콜 동작으로 인해 사용자가 이미 초기 요청에 응답 한 후에도 NPS 서버에서 중복 요청을 수신 하 고 다른 MFA 프롬프트를 보낼 수 있습니다. 이러한 타이밍 조건을 방지 하기 위해 Azure AD Multi-Factor Authentication NPS 확장은 성공적인 응답이 VPN 서버로 전송 된 후 최대 10 초 동안 중복 요청을 계속 필터링 하 고 삭제 합니다.

![성공적인 응답이 반환 된 후 10 초 동안 VPN 서버에서 중복 요청을 계속 취소 하는 NPS 서버에 대 한 다이어그램](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Azure AD Multi-Factor Authentication 프롬프트가 성공한 경우에도 NPS 서버 이벤트 로그에 삭제 된 요청이 표시 될 수 있습니다. 이는 예상 된 동작이 며 NPS 서버 또는 Azure AD Multi-Factor Authentication NPS 확장에 문제가 없음을 의미 합니다.

## <a name="plan-your-deployment"></a>배포 계획

NPS 확장은 자동으로 중복을 처리하므로 특별한 구성이 필요하지 않습니다.

Azure AD Multi-Factor Authentication 지원 NPS 서버는 필요한 만큼 만들 수 있습니다. 여러 서버를 설치한 경우 중 각각에 대해 다른 클라이언트 인증서를 사용해야 합니다. 각 서버에 대 한 인증서를 만들면 각 인증서를 개별적으로 업데이트할 수 있으며 모든 서버에서 가동 중지 시간에 대해 걱정 하지 않아도 됩니다.

VPN 서버는 인증 요청을 라우트 하므로 새 Azure AD Multi-Factor Authentication 사용 가능 NPS 서버를 인식 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

NPS 확장은 기존 인프라와 함께 사용할 수 있습니다. 시작하기 전에 다음 필수 조건을 갖추고 있는지 확인합니다.

### <a name="licenses"></a>라이선스

Azure ad Multi-Factor Authentication의 NPS 확장은 [AZURE ad Multi-Factor Authentication에 대 한 라이선스가](multi-factor-authentication.md)있는 고객에 게 제공 됩니다. 사용자 단위 또는 인증 라이선스와 같은 Azure AD Multi-Factor Authentication의 사용량 기반 라이선스는 NPS 확장과 호환 되지 않습니다.

### <a name="software"></a>소프트웨어

Windows Server 2012 이상

### <a name="libraries"></a>라이브러리

다음 라이브러리를 수동으로 설치 해야 합니다.

- [Visual Studio 2015용 Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=48145)

다음 라이브러리는 확장과 함께 자동으로 설치 됩니다.

- [Visual Studio 2013(X64)용 Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=40784)
- [Windows PowerShell용 Microsoft Azure Active Directory 모듈 버전 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

또한 설치 프로세스의 일부로 실행 하는 구성 스크립트 (아직 없는 경우)를 통해 Windows PowerShell용 Microsoft Azure Active Directory 모듈 설치 됩니다. 아직 설치 되지 않은 경우이 모듈을 미리 설치할 필요가 없습니다.

### <a name="azure-active-directory"></a>Azure Active Directory

NPS 확장을 사용 하는 모든 사용자는 Azure AD Connect를 사용 하 여 Azure AD에 동기화 해야 하며 MFA에 등록 해야 합니다.

확장을 설치 하는 경우 Azure AD 테 넌 트에 대 한 *테 넌 트 ID* 및 관리자 자격 증명이 필요 합니다. 테 넌 트 ID를 가져오려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 Azure 테넌트의 전역 관리자로 로그인합니다.
1. **Azure Active Directory** 를 검색 하 고 선택 합니다.
1. **개요** 페이지에서 *테 넌 트 정보가* 표시 됩니다. 다음 예제 스크린샷에 표시 된 것 처럼 *테 넌 트 ID* 옆에 있는 **복사** 아이콘을 선택 합니다.

   ![Azure Portal에서 테 넌 트 ID 가져오기](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>네트워크 요구 사항

NPS 서버는 포트 80 및 443를 통해 다음 Url과 통신할 수 있어야 합니다.

* *https: \/ /adnotifications.windowsazure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /credentials.azure.com*

또한 [제공 된 PowerShell 스크립트를 사용 하 여 어댑터 설정을](#run-the-powershell-script)완료 하려면 다음 url에 연결 해야 합니다.

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.net*

## <a name="prepare-your-environment"></a>환경 준비

NPS 확장을 설치 하기 전에 인증 트래픽을 처리할 환경을 준비 합니다.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>도메인에 가입된 서버에서 NPS 역할 사용

NPS 서버는 Azure AD에 연결 하 고 MFA 요청을 인증 합니다. 이 역할에 대한 서버를 한 개 선택하세요. NPS 확장이 RADIUS가 아닌 모든 요청에 대해 오류를 throw하므로 다른 서비스의 요청을 처리하지 않는 서버를 선택하는 것이 좋습니다. 사용자 환경에 대 한 기본 및 보조 인증 서버로 NPS 서버를 설정 해야 합니다. 다른 서버에 대 한 RADIUS 요청을 프록시 할 수 없습니다.

1. 서버에서 **서버 관리자** 를 엽니다. *빠른* 시작 메뉴에서 **역할 및 기능 추가 마법사** 를 선택 합니다.
2. 설치 유형에서 **역할 기반 또는 기능 기반 설치** 를 선택 합니다.
3. **네트워크 정책 및 액세스 서비스** 서버 역할을 선택합니다. 이 역할을 실행 하는 데 필요한 추가 기능을 알려주는 창이 표시 될 수 있습니다.
4. *확인* 페이지가 나타날 때까지 마법사를 계속 진행 합니다. 준비가 되 면 **설치** 를 선택 합니다.

NPS 서버 역할을 설치 하는 데 몇 분 정도 걸릴 수 있습니다. 완료 되 면 다음 섹션을 계속 진행 하 여 VPN 솔루션에서 들어오는 RADIUS 요청을 처리 하도록이 서버를 구성 합니다.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>VPN 솔루션이 NPS 서버와 통신하도록 구성

사용하는 VPN 솔루션에 따라 RADIUS 인증 정책을 구성하는 단계가 달라집니다. RADIUS NPS 서버를 가리키도록 VPN 정책을 구성 합니다.

### <a name="sync-domain-users-to-the-cloud"></a>도메인 사용자 클라우드로 동기화

이 단계는 테넌트에서 이미 완료되었을 수 있지만 Azure AD Connect가 최근에 데이터베이스를 동기화했는지 다시 한 번 확인하는 것이 좋습니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Azure AD Connect** 선택
3. 동기화 상태가 **사용** 이고 마지막 동기화가 1시간 미만인지 확인합니다.

새 동기화 라운드를 시작 해야 하는 경우 [Azure AD Connect sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)를 참조 하세요.

### <a name="determine-which-authentication-methods-your-users-can-use"></a>사용자가 사용할 수 있는 인증 방법을 결정합니다.

어떤 인증 방법을 NPS 확장 배포와 함께 사용할 수 있는지에 영향을 미치는 두 가지 요소가 있습니다.

* RADIUS 클라이언트(VPN, Netscaler 서버 또는 기타)와 NPS 서버 간에 사용되는 암호 암호화 알고리즘입니다.
   - **PAP** 는 클라우드에서 Azure AD Multi-Factor Authentication의 모든 인증 방법, 전화 통화, 단방향 문자 메시지, 모바일 앱 알림, OATH 하드웨어 토큰 및 모바일 앱 확인 코드를 지원 합니다.
   - **CHAPV2** 및 **EAP** 는 전화 통화 및 모바일 앱 알림을 지원합니다.

    > [!NOTE]
    > NPS 확장을 배포하는 경우 이러한 요소를 사용하여 사용자가 사용할 수 있는 방법이 무엇인지 평가합니다. RADIUS 클라이언트가 PAP를 지원하지만, 클라이언트 UX에 확인 코드에 대한 입력 필드가 없는 경우에는 전화 통화 및 모바일 앱 알림의 두 옵션이 지원됩니다.
    >
    > 또한 사용 되는 인증 프로토콜 (PAP, CHAP 또는 EAP)에 관계 없이 MFA 방법이 텍스트 기반 (SMS, 모바일 앱 확인 코드 또는 OATH 하드웨어 토큰)이 고 사용자가 VPN 클라이언트 UI 입력 필드에 코드 또는 텍스트를 입력 해야 하는 경우 인증이 성공할 수 있습니다. *하지만* 네트워크 액세스 정책에 구성 된 모든 radius 특성은 radius 클라이언트 (VPN 게이트웨이와 같은 네트워크 액세스 장치)로 전달 *되지 않습니다* . 그 결과, VPN 클라이언트에는 사용 하거나 액세스 하거나 액세스 하지 않는 것 보다 더 많은 액세스 권한이 있을 수 있습니다.

* 클라이언트 애플리케이션(VPN, Netscaler 서버 또는 기타)이 처리할 수 있는 입력 방법입니다. 예를 들어 VPN 클라이언트에 사용자가 텍스트 또는 모바일 앱의 인증 코드를 입력할 수 있는 몇 가지 방법이 있습니까?

Azure에서 [지원되지 않는 인증 방법을 사용하지 않도록 설정](howto-mfa-mfasettings.md#verification-methods)할 수 있습니다.

### <a name="register-users-for-mfa"></a>MFA에 사용자 등록

NPS 확장을 배포 하 고 사용 하기 전에 Azure AD Multi-Factor Authentication를 수행 하는 데 필요한 사용자를 MFA에 등록 해야 합니다. 배포할 때 확장을 테스트 하려면 Azure AD Multi-Factor Authentication에 대해 완전히 등록 된 테스트 계정도 하나 이상 필요 합니다.

테스트 계정을 만들고 구성 해야 하는 경우 다음 단계를 사용 합니다.

1. 테스트 계정으로 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에 로그인합니다.
2. 지시에 따라 확인 방법을 설정합니다.
3. 관리자 권한으로 Azure Portal에서 테스트 계정에 대 한 multi-factor authentication을 요구 하 [는 조건부 액세스 정책을 만듭니다](howto-mfa-getstarted.md#create-conditional-access-policy) .

> [!IMPORTANT]
>
> 사용자가 Azure AD Multi-Factor Authentication에 성공적으로 등록 했는지 확인 합니다. 사용자가 이전에 SSPR(셀프 서비스 암호 재설정)에 등록한 경우 해당 계정에 대해 *StrongAuthenticationMethods* 가 사용하도록 설정되어 있을 것입니다. Azure AD Multi-Factor Authentication는 사용자가 SSPR에 등록 한 경우에도 *StrongAuthenticationMethods* 가 구성 된 경우에 적용 됩니다.
>
> SSPR 및 Azure AD Multi-Factor Authentication를 동시에 구성 하는 결합 된 보안 등록을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [Azure Active Directory에서 결합된 보안 정보 등록 사용](howto-registration-mfa-sspr-combined.md)을 참조하세요.
>
> 사용자가 이전에 SSPR만 사용하도록 설정한 경우 [강제로 인증 방법을 다시 등록하도록](howto-mfa-userdevicesettings.md#manage-user-authentication-options) 할 수도 있습니다.

## <a name="install-the-nps-extension"></a>NPS 확장 설치

> [!IMPORTANT]
> VPN 액세스 지점 이외의 다른 서버에 NPS 확장을 설치합니다.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Azure AD MFA에 대 한 NPS 확장 다운로드 및 설치

NPS 확장을 다운로드 하 고 설치 하려면 다음 단계를 완료 합니다.

1. Microsoft 다운로드 센터에서 [NPS 확장을 다운로드합니다](https://aka.ms/npsmfa).
1. 이진 파일을 구성할 NPS(네트워크 정책 서버)에 복사합니다.
1. *setup.exe* 를 실행하고 설치 지침을 따릅니다. 오류가 발생 하는 경우 [필수 구성 요소 섹션의 라이브러리가](#libraries) 성공적으로 설치 되었는지 확인 합니다.

#### <a name="upgrade-the-nps-extension"></a>NPS 확장 업그레이드

나중에 기존 NPS 확장 설치를 업그레이드 하는 경우 기본 서버를 다시 부팅 하지 않으려면 다음 단계를 완료 합니다.

1. 기존 버전을 제거 합니다.
1. 새 설치 관리자를 실행 합니다.
1. *네트워크 정책 서버 (IAS)* 서비스를 다시 시작 합니다.

### <a name="run-the-powershell-script"></a>PowerShell 스크립트 실행

설치 관리자는에서 PowerShell 스크립트를 만듭니다 `C:\Program Files\Microsoft\AzureMfa\Config` `C:\` . 여기서는 설치 드라이브입니다. 이 PowerShell 스크립트는 실행할 때마다 다음 작업을 수행 합니다.

* 자체 서명된 인증서를 만듭니다.
* 인증서의 공개 키를 Azure AD의 서비스 주체에 연결합니다.
* 로컬 컴퓨터 인증서 저장소에 인증서를 저장 합니다.
* 네트워크 사용자에 게 인증서의 개인 키에 대 한 액세스 권한을 부여 합니다.
* NPS 서비스를 다시 시작합니다.

PowerShell 스크립트가 생성 하는 자체 서명 된 인증서 대신 자체 인증서를 사용 하려는 경우가 아니면 PowerShell 스크립트를 실행 하 여 NPS 확장 설치를 완료 합니다. 여러 서버에 확장을 설치 하는 경우 각 서버에 자체 인증서가 있어야 합니다.

부하 분산 기능 또는 중복성을 제공 하려면 추가 NPS 서버에서 필요에 따라 다음 단계를 반복 합니다.

1. 관리자 권한으로 Windows PowerShell 프롬프트를 엽니다.
1. 설치 관리자가 PowerShell 스크립트를 만든 위치로 디렉터리를 변경 합니다.

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. 설치 관리자가 만든 PowerShell 스크립트를 실행합니다.

   > [!IMPORTANT]
   > Azure Government 또는 Azure 중국 21Vianet 클라우드를 사용 하는 고객의 경우 먼저 `Connect-MsolService` *AzureMfaNpsExtnConfigSetup.ps1* 스크립트에서 cmdlet을 편집 하 여 필요한 클라우드에 대 한 *azureenvironment* 매개 변수를 포함 합니다. 예를 들어 *-Azureenvironment USGovernment* 또는 *-azureenvironment AzureChinaCloud* 를 지정 합니다.
   >
   > 자세한 내용은 [connect-msolservice 매개 변수 참조](/powershell/module/msonline/connect-msolservice#parameters)를 참조 하세요.

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. 메시지가 표시 되 면 관리자 권한으로 Azure AD에 로그인 합니다.
1. PowerShell이 테넌트 ID에 대해 메시지를 표시합니다. 필수 조건 섹션의 Azure Portal에서 복사한 *테 넌 트 ID* GUID를 사용 합니다.
1. 스크립트가 완료 되 면 성공 메시지가 표시 됩니다.  

이전 컴퓨터 인증서가 만료되었고 새 인증서가 생성된 경우에는 만료된 인증서를 모두 삭제해야 합니다. 만료된 인증서를 그대로 두면 NPS 확장을 시작하는 데 문제가 발생할 수 있습니다.

> [!NOTE]
> PowerShell 스크립트로 인증서를 생성하는 대신 자체 인증서를 사용할 경우 NPS 명명 규약을 따르도록 합니다. 주체 이름은 **CN = \<TenantID\> , OU = Microsoft NPS Extension** 이어야 합니다.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure Government 또는 Azure 중국 21Vianet 추가 단계

Azure Government 또는 Azure 중국 21Vianet 클라우드를 사용 하는 고객의 경우 각 NPS 서버에 다음과 같은 추가 구성 단계가 필요 합니다.

> [!IMPORTANT]
> Azure Government 또는 Azure 중국 21Vianet 고객 인 경우에만 이러한 레지스트리 설정을 구성 하세요.

1. Azure Government 또는 Azure 중국 21Vianet 고객이 라면 NPS 서버에서 **레지스트리 편집기** 를 엽니다.
1. `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`로 이동합니다.
1. Azure Government 고객의 경우 다음과 같은 키 값을 설정 합니다.

    | 레지스트리 키       | 값 |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Azure 중국 21Vianet 고객의 경우 다음 키 값을 설정 합니다.

    | 레지스트리 키       | 값 |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. 위의 두 단계를 반복하여 각 NPS 서버에 대한 레지스트리 키 값을 설정합니다.
1. 각 NPS 서버에 대한 NPS 서비스를 다시 시작합니다.

    영향을 최소화하기 위해 각 NPS 서버를 NLB 순환에서 한번에 하나씩 가져와 모든 연결이 드레이닝될 때까지 기다립니다.

### <a name="certificate-rollover"></a>인증서 롤오버

NPS 확장의 릴리스 *1.0.1.32* 를 사용 하 여 여러 인증서 읽기가 지원 됩니다. 이 기능은 만료 되기 전에 롤링 인증서 업데이트를 용이 하 게 해줍니다. 조직에서 이전 버전의 NPS 확장을 실행 하는 경우 버전 *1.0.1.32* 이상으로 업그레이드 합니다.

`AzureMfaNpsExtnConfigSetup.ps1` 스크립트에서 만든 인증서는 2년 동안 유효합니다. 인증서가 만료 될 때까지 모니터링 합니다. NPS 확장에 대 한 인증서는 *개인* 의 *로컬 컴퓨터* 인증서 저장소에 배치 되 고 설치 스크립트에 제공 된 테 넌 트 ID *에 발급* 됩니다.

인증서가 만료 날짜에 도달하면 해당 인증서 대신 새 인증서를 만들어야 합니다.  이 프로세스는 `AzureMfaNpsExtnConfigSetup.ps1`을 다시 실행하고 확인 메시지가 표시될 때 동일한 테넌트 ID를 유지하는 방식으로 진행됩니다. 사용자 환경의 각 NPS 서버에서 이 프로세스를 반복해야 합니다.

## <a name="configure-your-nps-extension"></a>NPS 확장 구성

사용자 환경 준비가 완료 되 고 이제 NPS 확장이 필요한 서버에 설치 된 경우 확장을 구성할 수 있습니다.

이 섹션에는 성공적인 NPS 확장 배포를 위한 디자인 고려 사항 및 제안 사항이 포함되어 있습니다.

### <a name="configuration-limitations"></a>구성 제한 사항

- Azure AD Multi-Factor Authentication의 NPS 확장에는 사용자 및 설정을 MFA 서버에서 클라우드로 마이그레이션하는 도구가 포함 되어 있지 않습니다. 이러한 이유로 기존 배포가 아닌 새 배포에 대한 확장을 사용하는 것이 좋습니다. 기존 배포에서 확장을 사용하는 경우 사용자는 증명을 다시 수행하여 클라우드에 MFA 세부 정보를 채워야 합니다.  
- NPS 확장은 온-프레미스 AD DS 환경의 UPN을 사용 하 여 Azure AD Multi-Factor Authentication에서 보조 인증을 수행 하는 사용자를 식별 합니다. 대체 로그인 ID 또는 UPN 이외의 사용자 지정 AD DS 필드와 같은 다른 식별자를 사용 하도록 확장을 구성할 수 있습니다. 자세한 내용은 [Multi-Factor Authentication에 대한 NPS 확장을 위한 고급 구성 옵션](howto-mfa-nps-extension-advanced.md) 문서를 참조하세요.
- 모든 암호화 프로토콜이 모든 확인 메서드를 지원하는 것은 아닙니다.
   - **PAP** 는 전화 통화, 단방향 문자 메시지, 모바일 앱 알림 및 모바일 앱 확인 코드를 지원합니다.
   - **CHAPV2** 및 **EAP** 는 전화 통화 및 모바일 앱 알림을 지원합니다.

### <a name="control-radius-clients-that-require-mfa"></a>MFA가 필요한 RADIUS 클라이언트 제어

NPS 확장을 사용 하 여 RADIUS 클라이언트에 대해 MFA를 사용 하도록 설정 하면이 클라이언트에 대 한 모든 인증이 MFA를 수행 해야 합니다. 일부 RADIUS 클라이언트에만 MFA를 사용하고 다른 클라이언트에는 MFA를 사용하지 않도록 설정하려면 두 개의 NPS 서버를 구성하고 그 중 하나에만 확장을 설치합니다.

MFA에서 확장을 사용하여 구성된 NPS 서버로 요청을 보내고 다른 RADIUS 클라이언트는 확장을 사용하여 구성되지 않은 NPS 서버로 요청을 보내도록 RADIUS 클라이언트를 구성합니다.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>MFA에 등록되지 않은 사용자를 위한 준비

MFA에 등록되지 않은 사용자가 있는 경우 인증을 시도할 때 수행할 작업을 결정할 수 있습니다. 이 동작을 제어 하려면 *만들고이* 레지스트리 경로에서 *REQUIRE_USER_MATCH* 설정을 사용 합니다. 이 설정에는 다음과 같은 단일 구성 옵션이 있습니다.

| 키 | 값 | 기본값 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | 설정되지 않음(TRUE와 동일) |

이 설정은 사용자가 MFA에 등록 되지 않은 경우 수행할 작업을 결정 합니다. 키가 없거나, 설정 되지 않았거나,가 *TRUE* 로 설정 되 고, 사용자가 등록 되지 않은 경우에는 확장에서 MFA 챌린지에 실패 합니다.

키가 *FALSE* 로 설정 되 고 사용자가 등록 되지 않은 경우 MFA를 수행 하지 않고 인증이 진행 됩니다. 사용자가 MFA에 등록 된 경우 *REQUIRE_USER_MATCH* 가 *FALSE* 로 설정 된 경우에도 mfa를 사용 하 여 인증 해야 합니다.

사용자가 온 보 딩 중에이 키를 만들고 *FALSE* 로 설정 하 여 아직 Azure AD Multi-Factor Authentication에 등록 하지 않을 수 있습니다. 그렇지만 키를 설정하면 MFA에 등록되지 않은 사용자가 로그인할 수 있으므로 프로덕션 환경으로 이동하기 전에 이 키를 제거해야 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="nps-extension-health-check-script"></a>NPS 확장 상태 검사 스크립트

다음 스크립트는 NPS 확장 문제를 해결할 때 기본 상태 검사 단계를 수행하는 데 사용할 수 있습니다.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>클라이언트 인증서가 예상대로 설치되었는지 어떻게 확인합니까?

설치 관리자에서 만든 자체 서명된 인증서를 인증서 저장소에서 찾고, 사용자에게 부여된 *네트워크 서비스* 권한이 프라이빗 키에 있는지 확인합니다. 인증서의 주체 이름이 **CN \<tenantid\> , OU = Microsoft NPS Extension** 입니다.

스크립트에 의해 생성 되는 자체 서명 된 인증서는 `AzureMfaNpsExtnConfigSetup.ps1` 유효 기간이 2 년입니다. 인증서가 설치 되어 있는지 확인 하는 경우에도 인증서가 만료 되지 않았는지 확인 해야 합니다.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>내 클라이언트 인증서가 Azure AD의 내 테 넌 트에 연결 되어 있는지 확인 하려면 어떻게 해야 하나요?

PowerShell 명령 프롬프트를 열고 다음 명령을 실행합니다.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

이 명령은 PowerShell 세션에서 NPS 확장의 인스턴스와 테넌트를 연결하는 인증서를 모든 출력합니다. 클라이언트 인증서를 개인 키 없이 *Base-64로 인코딩된 x.509 (.cer)* 파일로 내보내 인증서를 찾고 PowerShell의 목록과 비교 합니다.

다음 명령은 *C:* 드라이브의 루트에 *npscertificate* 라는 파일을 *만듭니다.*

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

이 명령을 실행 한 후에는 *C:* 드라이브의 루트로 이동 하 여 파일을 찾은 다음 해당 파일을 두 번 클릭 합니다. 자세히로 이동 하 고 "지문"로 스크롤합니다. 서버에 설치 된 인증서의 지문을이 인증서와 비교 합니다. 인증서 지문이 일치해야 합니다.

사용자가 읽을 수 있는 형식으로 된 *유효한 시작* 및 *유효* 타임 스탬프를 사용 하 여 명령이 두 개 이상의 인증서를 반환 하는 경우 분명 하 게 일치 하는 항목을 필터링 할 수 있습니다.

### <a name="why-cannot-i-sign-in"></a>로그인할 수 없는 이유는 무엇입니까?

암호가 만료되지 않았는지 확인합니다. NPS 확장은 로그인 워크플로의 일부로 암호 변경을 지원 하지 않습니다. 추가 지원이 필요하면 조직의 IT 직원에게 문의하세요.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>내 요청이 ADAL 토큰 오류로 인해 실패하는 이유는 무엇입니까?

이 오류는 몇 가지 이유 중 하나로 발생할 수 있습니다. 다음 단계를 사용 하 여 문제를 해결 합니다.

1. NPS 서버를 다시 시작합니다.
2. 클라이언트 인증서가 예상대로 설치되었는지 확인합니다.
3. 인증서가 Azure AD의 테넌트와 연결되어 있는지 확인합니다.
4. 확장을 실행하는 서버에서 `https://login.microsoftonline.com/`에 액세스할 수 있는지 확인합니다.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>사용자를 찾을 수 없다고 하는 HTTP 로그 오류로 인해 인증이 실패하는 이유는 무엇입니까?

AD Connect가 실행 중이 고 사용자가 온-프레미스 AD DS 환경과 Azure AD에 모두 있는지 확인 합니다.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>내 모든 인증이 실패한 상태의 로그에 HTTP 연결 오류가 표시되는 이유는 무엇입니까?

NPS 확장을 실행하는 서버에서 https://adnotifications.windowsazure.com 에 액세스할 수 있는지 확인합니다.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>유효한 인증서가 있지만 인증이 작동하지 않는 이유는 무엇인가요?

이전 컴퓨터 인증서가 만료 되 고 새 인증서가 생성 된 경우 만료 된 인증서를 모두 삭제 합니다. 인증서가 만료 되 면 NPS 확장을 시작 하는 동안 문제가 발생할 수 있습니다.

유효한 인증서가 있는지 확인 하려면 MMC를 사용 하 여 로컬 *컴퓨터 계정의 인증서 저장소* 를 확인 하 고 인증서가 만료 날짜를 통과 하지 않았는지 확인 합니다. 새로 유효한 인증서를 생성 하려면 [PowerShell 설치 관리자 스크립트 실행](#run-the-powershell-script)의 단계를 다시 실행 합니다.

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>NPS 서버 로그에서 삭제 된 요청이 표시 되는 이유는 무엇 인가요?

제한 시간 값이 너무 낮으면 VPN 서버가 NPS 서버에 반복 되는 요청을 보낼 수 있습니다. NPS 서버는 이러한 중복 요청을 검색 하 고 삭제 합니다. 이 동작은 의도적으로 설계 되었으며 NPS 서버 또는 Azure AD Multi-Factor Authentication NPS 확장에 대 한 문제를 나타내지는 않습니다.

NPS 서버 로그에서 삭제 된 패킷이 표시 되는 이유에 대 한 자세한 내용은이 문서의 시작 부분에 있는 [RADIUS 프로토콜 동작 및 NPS 확장](#radius-protocol-behavior-and-the-nps-extension) 을 참조 하세요.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>TLS/SSL 프로토콜 및 암호 그룹 관리

조직에서 요구 하지 않는 한 이전 및 약한 암호 그룹을 사용 하지 않도록 설정 하거나 제거 하는 것이 좋습니다. 이 작업을 완료 하는 방법에 [대 한 정보는 AD FS에 대 한 SSL/TLS 프로토콜 및 암호 그룹 관리](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) 문서에서 찾을 수 있습니다.

### <a name="additional-troubleshooting"></a>추가적인 문제 해결

추가 문제 해결 지침 및 가능한 해결 방법은 [AZURE AD Multi-Factor Authentication에 대 한 NPS 확장의 오류 메시지 해결](howto-mfa-nps-extension-errors.md)문서에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Windows Server의 네트워크 정책 서버 개요 및 구성](/windows-server/networking/technologies/nps/nps-top)

- [Multi-Factor Authentication에 대한 NPS 확장을 위한 고급 구성 옵션](howto-mfa-nps-extension-advanced.md)에서 2단계 확인을 수행하지 않아야 하는 IP 예외 목록 설정 또는 로그인에 대한 대체 ID 구성

- NPS 확장을 사용하여 [원격 데스크톱 게이트웨이](howto-mfa-nps-extension-rdg.md) 및 [VPN 서버](howto-mfa-nps-extension-vpn.md)를 통합하는 방법 알아보기

- [Azure AD Multi-Factor Authentication의 NPS 확장에서 오류 메시지를 확인 합니다.](howto-mfa-nps-extension-errors.md)
