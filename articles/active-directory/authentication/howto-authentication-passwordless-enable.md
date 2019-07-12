---
title: Azure Active Directory 암호 없는 로그인 (미리 보기)를 구성 합니다.
description: 암호 없는 로그인 FIDO2 보안 키 또는 Microsoft Authenticator 앱 (미리 보기)를 사용 하 여 Azure AD 사용 하도록 설정
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712073"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>Azure AD (미리 보기)에 대 한 암호 없는 로그인을 사용 하도록 설정

## <a name="requirements"></a>요구 사항

* Azure Multi-Factor Authentication
* 등록 미리 보기를 결합합니다.
* FIDO2 보안 키 미리 보기에는 호환 FIDO2 보안 키에 필요
* WebAuthN 필요한 Windows 10 버전 1809 이상에서 Microsoft Edge
* Azure AD를 필요로 하는 Windows 로그인 기반된 FIDO2 가입 Windows 10 버전 1809 이상

## <a name="prepare-devices-for-preview"></a>미리 보기에 대 한 장치를 준비 합니다.

1809 이상 장치를 사용 하 여 파일럿 실행은 Windows 10 버전을 실행 합니다. 최상의 환경을 Windows 10 버전이 1903 이상 켜져 있습니다.

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows 로그인에 대 한 보안 키를 사용 하도록 설정

조직은 하나를 사용 하도록 선택할 수 있습니다 또는 Windows에 대 한 보안 키를 사용 하도록 설정 하려면 다음 방법 중에 로그인 합니다.

### <a name="enable-credential-provider-via-intune"></a>Intune 통해 자격 증명 공급자를 사용 하도록 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 이동할 **Microsoft Intune** > **장치 등록** > **Windows 등록** > **Windows Hello 비즈니스용** > **속성**합니다.
1. 아래 **설정을** 설정 **로그인에 대 한 보안 키를 사용 하 여** 하 **사용**합니다.

로그인을 위해 보안 키의 구성을 구성 Windows Hello for Business에 종속 되지 않습니다.

#### <a name="enable-targeted-intune-deployment"></a>Intune 배포 사용

자격 증명 공급자를 사용 하도록 설정 하는 특정 장치 그룹을 대상으로 Intune 통해 다음 사용자 지정 설정을 사용 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 이동할 **Microsoft Intune** > **장치 구성을** > **프로필** > **프로필만들기**.
1. 다음 설정을 사용 하 여 새 프로필을 구성 합니다.
   1. 이름: Windows 로그인에 대 한 보안 키
   1. 설명: Windows 로그인 하는 동안 사용할 FIDO 보안 키를 사용 하도록 설정
   1. 플랫폼: Windows 10 이상
   1. 플랫폼 유형: 사용자 지정
   1. 사용자 지정 OMA-URI 설정:
      1. 이름: FIDO 보안 키의 Windows 로그인에 대 한 설정
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. 데이터 형식: 정수
      1. 값: 1 
1. 이 정책은 특정 사용자, 장치 또는 그룹에 할당할 수 있습니다. 자세한 내용은 문서에서 찾을 수 있습니다 [Microsoft Intune에서 사용자 및 장치 프로필 할당](https://docs.microsoft.com/intune/device-profile-assign)합니다.

![Intune 사용자 지정 장치 구성 정책 만들기](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>프로 비전 패키지를 통해 자격 증명 공급자를 사용 하도록 설정

Intune에서 관리 되지 않는 장치에 대 한 기능을 사용 하려면 프로 비전 패키지를 설치할 수 있습니다. Windows 구성 디자이너 앱에서 설치 합니다 [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)합니다.

1. Windows 구성 디자이너를 시작 합니다.
1. 선택 **파일** > **새 프로젝트**합니다.
1. 프로젝트 이름을 지정 하 고 기록해 둡니다 경로의 프로젝트가 만들어진 키를 누릅니다.
1. **다음**을 선택합니다.
1. 둡니다 **프로 비전 패키지** 으로 선택한 합니다 **선택한 프로젝트 워크플로** 선택한 **다음**합니다.
1. 선택 **모든 Windows 데스크톱 버전** 아래에서 **보기 및 구성 하는 설정을 선택** 선택한 **다음**합니다.
1. **마침**을 선택합니다.
1. 새로 만든된 프로젝트에서 찾은 **런타임 설정을** > **WindowsHelloForBusiness** > **SecurityKeys**  >  **UseSecurityKeyForSignIn**합니다.
1. 설정할 **UseSecurityKeyForSignIn** 하 **활성화**합니다.
1. 선택 **내보낼** > **프로 비전 패키지**
1. 기본값을 그대로 합니다 **빌드** 창의 **프로 비전 패키지를 설명** 선택한 **다음**합니다.
1. 기본값을 그대로 합니다 **빌드** 창의 **프로 비전 패키지에 대 한 보안 세부 정보 선택** 선택한 **다음**합니다.
1. 메모 또는에서 경로 변경 합니다 **빌드** 아래에서 windows **프로 비전 패키지를 저장할 위치를 선택** 선택한 **다음**합니다.
1. 선택 **빌드** 에 **프로 비전 패키지를 빌드하고** 페이지입니다.
1. 두 파일 생성 (ppkg 및 cat) 적용할 수 있는 해당 컴퓨터에 나중에 위치에 저장 합니다.
1. 문서의 지침을 따릅니다 [프로 비전 패키지 적용](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package), 만든 프로 비전 패키지 적용 합니다.

## <a name="obtain-fido2-security-keys"></a>FIDO2 보안 키를 가져오려면

문서의 FIDO2 보안 키 섹션을 참조 하세요 [암호 없는 란?](concept-authentication-passwordless.md) 지원 되는 키 및 제조업체에 대 한 자세한 내용은 합니다.

> [!NOTE]
> 구입 하 고 NFC 기반 보안 키를 사용 하도록 계획 하는 경우에 지원 되는 NFC 판독기를 해야 합니다.

## <a name="enable-passwordless-authentication-methods"></a>암호 없는 인증 방법을 사용 하도록 설정

### <a name="enable-the-combined-registration-experience"></a>결합 된 등록 환경을 사용 하도록 설정

FIDO2 보안 키에 대 한 등록 기능은 결합 된 등록 미리 보기에 의존합니다. 결합 된 등록 미리 보기를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure 포털](https://portal.azure.com)
1. 이동할 **Azure Active Directory** > **사용자 설정**
   1. 클릭 **액세스 패널 미리 보기 기능에 대 한 설정을 관리 합니다.**
   1. 아래 **사용자는 등록 하 고 보안 정보-향상 된 관리에 대 한 미리 보기 기능을 사용할 수 있습니다**합니다.
      1. 선택할 **선택한** 미리 보기에 참여 하는 사용자 그룹을 선택 합니다.
      1. 선택 하거나 **모든** 디렉터리의 모든 사용자에 사용할 수 있도록 합니다.
1. **저장**을 클릭합니다.

### <a name="enable-new-passwordless-authentication-methods"></a>새 암호 없는 인증 방법을 사용 하도록 설정

1. [Azure 포털](https://portal.azure.com)
1. 이동할 **Azure Active Directory** > **인증 방법** > **인증 메서드 정책 (미리 보기)**
1. 각 노드에서 **메서드**, 다음 옵션을 선택 합니다.
   1. **사용 하도록 설정** -예 또는 아니요
   1. **대상** -모든 사용자 또는 사용자 선택
1. **저장** 각 메서드

> [!WARNING]
> "제한 정책 키" 작동 하지 않습니다 아직 FIDO2 합니다. 이 기능은 일반 공급 전에 사용할 수 있습니다, 그리고 이러한 정책을 기본값에서 변경 하지 마십시오.

> [!NOTE]
> 암호 없는 메서드는 모두를 옵트인 할 필요가 없습니다 (하나의 암호 없는 메서드를 미리 보려면 하려는 경우 설정할 수 있습니다. 방법만). 좋습니다 고유한 이점을 모두 없으므로 두 방법 모두 사용해 보세요.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>사용자 등록 및 FIDO2 보안 키 관리

1. [https://myprofile.microsoft.com](https://myprofile.microsoft.com)으로 이동합니다.
1. 않은 경우 로그인
1. 클릭 **보안 정보**
   1. 사용자에 등록 하는 하나 이상의 Azure Multi-factor Authentication 방법을 이미 있으면 FIDO2 보안 키를 즉시 등록할 수 있습니다.
   1. 하나 이상의 Azure Multi-factor Authentication 방법을 등록 되지 않은 경우 하나 추가 해야 합니다.
1. 클릭 하 여 FIDO2 보안 키를 추가할 **메서드를 추가** 선택 하 고 **보안 키**
1. 선택할 **USB 장치가** 또는 **nfc 지원 장치**
1. 준비 및 선택 키가 **다음**
1. 상자는 표시 및 만들기/PIN을 입력 하는 보안 키를 묻는 메시지를 다음 넌 트 키에 대 한 필수 제스처를 수행 생체 인식 또는 터치 합니다.
1. 결합 된 등록 환경에 반환 및 여러 개 있는 경우 어느 식별할 수 있도록 토큰에 대 한 의미 있는 이름을 입력 하 라는 메시지가 표시 되어야 합니다. **다음**을 클릭합니다.
1. 클릭 **수행** 프로세스를 완료 하려면

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>보안 키 생체 인식, PIN, 관리 또는 보안 키를 다시 설정

* Windows 10 버전 1809
   * 주요 보안 공급 업체에서 제공 되는 소프트웨어 필요
* Windows 10 버전이 1903 이상
   * 사용자가 열 수 **Windows 설정** 장치의 > **계정** > **보안 키**
   * 사용자가 PIN을 변경, 생체 인식, 업데이트 하거나, 해당 보안 키를 다시 설정할 수 있습니다.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>사용자 등록 및 Microsoft Authenticator 앱을 관리

휴대폰 로그인을 위해 Microsoft Authenticator 앱을 구성 하려면 문서의 지침을 따릅니다 [Microsoft Authenticator 앱을 사용 하 여 계정에 로그인](../user-help/user-help-auth-app-sign-in.md)합니다.

## <a name="sign-in-with-passwordless-credentials"></a>암호 없는 자격 증명으로 로그인

### <a name="sign-in-at-the-lock-screen"></a>잠금 화면에서 로그인

사용자 아래 예제에서는 Bala Sandhu가 이미 프로 비전 FIDO2 보안 키에 해당 합니다. Bala는 Windows 10 잠금 화면에서 보안 키 자격 증명 공급자를 선택 하 고 Windows에 로그인 하려면 보안 키를 삽입할 수 있습니다.

![보안 키가 Windows 10 잠금 화면에서 로그인](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>웹에 로그인

사용자 아래 예제에서는 이미 프로 비전 FIDO2 보안 키에 해당 합니다. Windows 10 1809 이상 버전의 Microsoft Edge 브라우저 내에서 해당 FIDO2 보안 키를 사용 하 여 웹에서 로그인을 선택할 수 있습니다.

![Microsoft Edge의 보안 키 로그인](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

Microsoft Authenticator 앱을 사용 하 여 로그인에 대 한 자세한 문서를 참조 하세요 [Microsoft Authenticator 앱을 사용 하 여 계정에 로그인](../user-help/user-help-auth-app-sign-in.md)합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="fido2-security-keys"></a>FIDO2 보안 키

#### <a name="security-key-provisioning"></a>보안 키 프로 비전

관리자 프로비저닝 및 해제 보안 키의 공개 미리 보기에서 제공 되지 않습니다.

#### <a name="hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인

암호 없는 로그인 Microsoft Authenticator 앱에 하이브리드 조인 해야 Windows 10에서 SSO의 이점을 누릴 수 또는 관리 되는 자격 증명을 사용 하는 WIA SSO에 의존 하는 사용자 같은 FIDO2 보안 키. 그러나 보안 키는 이제 Azure Active Directory 가입 된 컴퓨터에 대해서만 작동 합니다. 순수 Azure Active Directory 가입 된 컴퓨터에서 Windows 잠금 화면에 대 한 보안 키 FIDO2만 시도 하는 것이 좋습니다. 웹에 대 한이 제한이 적용 되지 않습니다.

#### <a name="upn-changes"></a>UPN 변경

하이브리드 AADJ UPN 변경 및 AADJ 장치를 허용 하는 기능을 지원 하기 위해 노력 합니다. 사용자의 UPN을 변경 하는 경우 FIDO2 보안 키에 대 한 계정에 더 이상 수정할 수 없습니다. 따라서 유일한 방법은 하는 장치를 다시 설정 및 사용자가 다시 등록 해야 합니다.

### <a name="authenticator-app"></a>인증자 앱

#### <a name="ad-fs-integration"></a>AD FS 통합

사용자가 Microsoft Authenticator 암호 없는 자격 증명을 사용하도록 설정하면 해당 사용자의 인증은 항상 승인을 위해 알림을 보내는 것으로 기본 설정됩니다. 이 논리 향하기 ADFS 로그인 확인을 위해 추가 단계를 수행 하는 사용자 없이 "암호를 대신 사용 합니다." 클릭에서 하이브리드 테 넌 트에서 사용자를 수 없습니다. 또한 이 프로세스는 온-프레미스 조건부 액세스 정책 및 통과 인증 흐름을 무시합니다. 이 프로세스에 예외가 login_hint 이면, 지정 된 사용자 AD FS에 자동으로 전달 되며 암호 없는 자격 증명을 사용 하는 옵션을 무시 합니다.

#### <a name="azure-mfa-server"></a>Azure MFA 서버

여전히 최종 사용자에 게 MFA에 대 한 조직의 온-프레미스 Azure MFA 서버를 통해 사용 하도록 설정 하는 만들고 단일 암호 없는 전화 로그인 자격 증명에 사용할 수 있습니다. 사용자가 자격 증명으로 Microsoft Authenticator의 여러 설치(5개 이상)를 업그레이드하려고 시도하면 이 변경으로 인해 오류가 발생할 수 있습니다.  

#### <a name="device-registration"></a>디바이스 등록

이 새롭고 강력한 자격 증명을 만들기 위한 필수 구성 요소 중 하나는 상주하는 디바이스가 Azure AD 테넌트 내에 개별 사용자에 대해 등록되어 있어야 합니다. 디바이스 등록 제한으로 인해 디바이스는 단일 테넌트에만 등록할 수 있습니다. 이 제한은 휴대폰 로그인을 위해 Microsoft Authenticator 앱에서 회사 또는 학교 계정 하나만 사용할 수는 것을 의미 합니다.

## <a name="next-steps"></a>다음 단계

[디바이스 등록에 대한 자세한 정보](../devices/overview.md)

[Azure Multi-Factor Authentication에 대한 자세한 정보](../authentication/howto-mfa-getstarted.md)
