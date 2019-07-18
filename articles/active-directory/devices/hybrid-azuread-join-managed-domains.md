---
title: 관리되는 도메인용 하이브리드 Azure Active Directory 조인 구성 | Microsoft Docs
description: 관리되는 도메인용 하이브리드 Azure Active Directory 조인 구성
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b24888934d7e89a13b1b07b7138be476575fc306
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204607"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>자습서: 관리되는 도메인용 하이브리드 Azure Active Directory 조인 구성

조직의 사용자와 같이 디바이스는 보호하려는 핵심 ID입니다. 디바이스의 ID를 사용하여 언제 어디서든 리소스를 보호할 수 있습니다. 다음 방법 중 하나를 사용하여 디바이스 ID를 Azure AD(Azure Active Directory)로 가져와서 관리하면 이 목표를 달성할 수 있습니다.

- Azure AD 조인
- 하이브리드 Azure AD 조인
- Azure AD 등록

Azure AD에 디바이스를 가져오면 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다. 동시에 [조건부 액세스](../active-directory-conditional-access-azure-portal.md)를 사용하여 클라우드 및 온-프레미스 리소스에 대한 액세스를 보호할 수 있습니다.

이 자습서에서는 관리형 환경의 Active Directory 도메인 조인 컴퓨터 디바이스에 대한 하이브리드 Azure AD 조인을 구성하는 방법을 알아봅니다. 

관리형 환경은 [Seamless Single Sign-On](../hybrid/how-to-connect-sso.md)을 사용하여 [PHS(암호 해시 동기화)](../hybrid/whatis-phs.md) 또는 [PTA(통과 인증)](../hybrid/how-to-connect-pta.md)를 통해 배포할 수 있습니다. 이러한 시나리오는 인증용 페더레이션 서버를 구성할 필요가 없습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 하이브리드 Azure AD 조인 구성
> * Windows 하위 수준 디바이스 설정
> * 가입 장치 확인
> * 문제 해결

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 사용자가 다음 항목에 대해 잘 알고 있다고 가정합니다.

- [디바이스 ID란?](overview.md)
- [하이브리드 Azure AD 조인 구현을 계획하는 방법](hybrid-azuread-join-plan.md)
- [하이브리드 Azure AD 조인의 제어된 유효성 검사를 수행하는 방법](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD는 관리형 도메인에서 스마트 카드나 인증서를 지원하지 않습니다.

이 문서의 시나리오를 구성하려면 [최신 버전의 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)(1.1.819.0 이상)를 설치해야 합니다.

Azure AD Connect에서 Azure AD에 조인된 하이브리드 Azure AD가 될 디바이스의 컴퓨터 개체를 동기화했는지 확인합니다. 컴퓨터 개체가 특정 OU(조직 구성 단위)에 속한 경우 Azure AD Connect에서도 이러한 OU에 동기화를 구성해야 합니다. Azure AD Connect를 사용하여 컴퓨터 개체를 동기화하는 방법을 자세히 알아보려면 [Azure AD Connect를 사용하여 필터링 구성](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)을 참조하세요.

버전 1.1.819.0부터 Azure AD Connect는 하이브리드 Azure AD 조인을 구성하는 데 사용할 수 있는 마법사를 제공합니다. 마법사를 사용하면 구성 프로세스를 크게 간소화할 수 있습니다. 마법사는 디바이스 등록을 위한 SCP(서비스 연결 지점)를 구성합니다.

이 문서의 구성 단계는 Azure AD Connect의 이 마법사를 기준으로 합니다.

하이브리드 Azure AD 조인을 위해서는 디바이스가 조직의 네트워크 내에서 다음 Microsoft 리소스에 액세스할 수 있어야 합니다.  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (seamless SSO를 사용하거나 사용할 계획이 있는 경우)

조직에서 아웃바운드 프록시를 통해 인터넷에 액세스해야 하는 경우에 Microsoft는 Windows 10 컴퓨터에서 디바이스를 Azure AD에 등록할 수 있도록 [WPAD(웹 프록시 자동 검색)를 구현](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10))하는 것을 권장합니다. WPAD를 구성하고 관리하는 문제가 발생하는 경우 [자동 검색 문제 해결](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10))을 참조하세요. 

WPAD를 사용하지 않고 컴퓨터에서 프록시 설정을 구성해야 하는 경우 Windows 10 1709로 시작할 수 있습니다. 자세한 내용은 [GPO(그룹 정책 개체)를 사용하여 WinHTTP 설정 구성](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)을 참조하세요.

> [!NOTE]
> WinHTTP 설정을 사용하여 컴퓨터에서 프록시 설정을 구성하는 경우 구성된 프록시에 연결할 수 없는 모든 컴퓨터는 인터넷에 연결할 수 없습니다.

조직에서 인증된 아웃바운드 프록시를 통해 인터넷에 액세스해야 하는 경우 Windows 10 컴퓨터에서 아웃바운드 프록시를 성공적으로 인증할 수 있는지 확인해야 합니다. Windows 10 컴퓨터는 머신 컨텍스트를 사용하여 디바이스 등록을 실행하므로 머신 컨텍스트를 사용하여 아웃바운드 프록시 인증을 구성해야 합니다. 아웃바운드 프록시 공급자와 함께 구성 요구 사항을 준수하세요.

## <a name="configure-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인 구성

Azure AD Connect를 사용하여 하이브리드 Azure AD 조인을 구성하려면 다음이 필요합니다.

- Azure AD 테넌트에 대한 글로벌 관리자의 자격 증명
- 각 포리스트에 대한 엔터프라이즈 관리자 자격 증명

**Azure AD Connect를 사용하여 하이브리드 Azure AD 조인을 구성하려면**

1. Azure AD Connect를 시작한 다음, **구성**을 선택합니다.

   ![시작](./media/hybrid-azuread-join-managed-domains/11.png)

1. **추가 작업** 페이지에서 **디바이스 옵션 구성**을 선택한 다음, **다음**을 선택합니다.

   ![추가 작업](./media/hybrid-azuread-join-managed-domains/12.png)

1. **개요** 페이지에서 **다음**을 선택합니다.

   ![개요](./media/hybrid-azuread-join-managed-domains/13.png)

1. **Azure AD에 연결** 페이지에서 Azure AD 테넌트에 대한 전역 관리자 자격 증명을 입력합니다.  

   ![Azure에 연결](./media/hybrid-azuread-join-managed-domains/14.png)

1. **디바이스 옵션** 페이지에서 **하이브리드 Azure AD 조인 구성**을 선택한 다음, **다음**을 선택합니다.

   ![디바이스 옵션](./media/hybrid-azuread-join-managed-domains/15.png)

1. **SCP** 페이지에서 Azure AD Connect로 SCP를 구성하려는 각 포리스트에 대해 다음 단계를 완료한 다음, **다음**을 선택합니다.

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. 포리스트를 선택합니다.
   1. 인증 서비스를 선택합니다.
   1. **추가**를 선택하여 엔터프라이즈 관리자 자격 증명을 입력합니다.

1. **디바이스 운영 체제** 페이지에서 Active Directory 환경의 디바이스에서 사용되는 운영 체제를 선택한 다음, **다음**을 선택합니다.

   ![디바이스 운영 체제](./media/hybrid-azuread-join-managed-domains/17.png)

1. **구성 준비** 페이지에서 **구성**을 선택합니다.

   ![구성 준비](./media/hybrid-azuread-join-managed-domains/19.png)

1. **구성 완료** 페이지에서 **끝내기**를 선택합니다.

   ![구성 완료](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows 하위 수준 디바이스 사용

도메인에 가입된 디바이스 중 일부가 Windows 하위 수준 디바이스인 경우 다음을 수행해야 합니다.

- 디바이스 등록에 대한 로컬 인트라넷 설정 구성
- Seamless SSO 구성
- Windows 하위 수준 컴퓨터용 Microsoft Workplace Join 설치

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>디바이스 등록에 대한 로컬 인트라넷 설정 구성

Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인을 성공적으로 완료하고 디바이스가 Azure AD를 인증할 때 인증서 프롬프트를 표시하지 않으려면 도메인에 가입된 디바이스에 정책을 푸시하여 Internet Explorer의 로컬 인트라넷 영역에 다음 URL을 추가할 수 있습니다.

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

또한, 사용자의 로컬 인트라넷 영역에서 **스크립트를 통해 상태 표시줄 업데이트 허용**을 사용하도록 설정해야 합니다.

### <a name="configure-seamless-sso"></a>Seamless SSO 구성

Azure AD 클라우드 인증 방법으로 [PHS]../hybrid/whatis-phs.md) 또는 [PTA](../hybrid/how-to-connect-pta.md)를 사용하는 관리형 도메인에서 Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인을 완료하려면 [Seamless SSO도 구성](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)해야 합니다.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Windows 하위 수준 컴퓨터용 Microsoft Workplace Join 설치

Windows 하위 수준 디바이스를 등록하려면 조직에서는 [비 Windows 10 컴퓨터용 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)을 설치해야 합니다. 비 Windows 10 컴퓨터용 Microsoft Workplace Join은 Microsoft 다운로드 센터에서 사용할 수 있습니다.

 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) 같은 소프트웨어 배포 시스템을 사용하여 패키지를 배포할 수 있습니다. 패키지는 `quiet` 매개 변수를 사용하여 표준 자동 설치 옵션을 지원합니다. 구성 관리자의 현재 분기는 완료된 등록을 추적하는 기능과 같은 이전 버전보다 나은 이점이 추가로 제공됩니다.

설치 관리자는 사용자 컨텍스트에서 실행되는 예약된 작업을 시스템에 만듭니다. 사용자가 Windows에 로그인할 때 이 작업이 트리거됩니다. 이 작업은 Azure AD를 사용하여 인증한 후 사용자 자격 증명을 사용하여 Azure AD에 디바이스를 자동으로 조인합니다.

## <a name="verify-the-registration"></a>등록 확인

Azure 테넌트에서 디바이스 등록 상태를 확인하려면 [Azure Active Directory PowerShell 모듈](/powershell/azure/install-msonlinev1?view=azureadps-2.0)에서 **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** cmdlet을 사용할 수 있습니다.

**Get-MSolDevice** cmdlet을 사용하여 서비스 세부 정보를 확인하려는 경우 다음이 적용됩니다.

- **디바이스 ID**가 Windows 클라이언트의 ID와 일치하는 개체가 있어야 합니다.
- **DeviceTrustType** 값은 **도메인 가입됨**이어야 합니다. 이 설정은 Azure AD 포털에서 **디바이스** 페이지의 **하이브리드 Azure AD 조인**과 같습니다.
- 조건부 액세스에 사용되는 디바이스의 경우 **Enabled** 값이 **True**이고 **DeviceTrustLevel**이 **Managed**여야 합니다.

**서비스 세부 정보를 확인하려면**:

1. 관리자 권한으로 Windows PowerShell을 엽니다.
1. `Connect-MsolService`를 입력하여 Azure 테넌트에 연결합니다.  
1. `get-msoldevice -deviceId <deviceId>`을 입력합니다.
1. **Enabled**가 **True**로 설정되어 있는지 확인인합니다.

## <a name="troubleshoot-your-implementation"></a>구현 문제 해결

도메인 조인 Windows 디바이스에 대한 하이브리드 Azure AD 조인을 완료할 때 문제가 발생하면 다음을 참조하세요.

- [Windows 최신 디바이스의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-current.md)
- [Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 디바이스 ID를 관리](device-management-azure-portal.md)하는 방법을 알아봅니다.
