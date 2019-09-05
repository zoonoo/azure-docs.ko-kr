---
title: Azure AD에 대해 암호 없는 보안 키 로그인 사용 (미리 보기)-Azure Active Directory
description: FIDO2 보안 키 (미리 보기)를 사용 하 여 Azure AD에 암호 없는 보안 키 로그인 사용
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316a523a6216354ae5b6166be55e183a4e050766
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305068"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>Azure AD에 대해 암호 없는 보안 키 로그인 사용 (미리 보기)

## <a name="requirements"></a>요구 사항

* Azure Multi-Factor Authentication
* SSPR에 대해 사용 하도록 설정 된 사용자와 결합 된 등록 미리 보기
* FIDO2 security key preview에는 호환 되는 FIDO2 보안 키가 필요 합니다.
* WebAuthN에는 Windows 10 버전 1809 이상의 Microsoft Edge가 필요 합니다.
* FIDO2 based Windows 로그인에는 Azure AD에 조인 된 Windows 10 버전 1809 이상이 필요 합니다.

## <a name="prepare-devices-for-preview"></a>미리 보기용으로 장치 준비

파일럿을 적용할 장치는 Windows 10 버전 1809 이상을 실행 해야 합니다. 최상의 환경은 Windows 10 버전 1903 이상에 있습니다.

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows 로그인에 대 한 보안 키 사용

조직에서는 Windows 로그인에 보안 키를 사용할 수 있도록 다음 방법 중 하나 이상을 사용 하도록 선택할 수 있습니다.

### <a name="enable-credential-provider-via-intune"></a>Intune을 통해 자격 증명 공급자 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Microsoft Intune**장치등록 > windows 등록비즈니스용 > windows Hello 속성으로 이동 합니다. >  > 
1. **설정** 에서 **로그인에 대 한 보안 키 사용** 을 사용 **으로 설정**합니다.

로그인에 대 한 보안 키 구성은 비즈니스용 Windows Hello 구성에 종속 되지 않습니다.

#### <a name="enable-targeted-intune-deployment"></a>대상 Intune 배포 사용

특정 장치 그룹을 대상으로 자격 증명 공급자를 사용 하도록 설정 하려면 Intune을 통해 다음 사용자 지정 설정을 사용 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Microsoft Intune**장치구성 > **프로필**프로필**만들기**로 이동 합니다. >  > 
1. 다음 설정을 사용 하 여 새 프로필을 구성 합니다.
   1. 이름: Windows 로그인에 대 한 보안 키
   1. 설명: Windows 로그인 중에 FIDO 보안 키를 사용할 수 있도록 합니다.
   1. 플랫폼: Windows 10 이상
   1. 프로필 유형: 사용자 지정
   1. 사용자 지정 OMA-URI 설정:
      1. 이름: Windows 로그인에 대 한 FIDO 보안 키 설정
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. 데이터 형식: 정수
      1. 값: 1 
1. 이 정책은 특정 사용자, 장치 또는 그룹에 할당할 수 있습니다. 자세한 내용은 [Microsoft Intune에서 사용자 및 장치 프로필 할당](https://docs.microsoft.com/intune/device-profile-assign)문서에서 찾을 수 있습니다.

![Intune 사용자 지정 장치 구성 정책 만들기](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>프로 비전 패키지를 통해 자격 증명 공급자 사용

Intune에서 관리 되지 않는 장치의 경우 기능을 사용 하도록 프로 비전 패키지를 설치할 수 있습니다. [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22)에서 Windows 구성 디자이너 앱을 설치할 수 있습니다.

1. Windows 구성 디자이너를 시작 합니다.
1. **파일** > **새로 만들기 프로젝트**를 선택 합니다.
1. 프로젝트에 이름을 지정 하 고 프로젝트를 만든 경로를 기록해 둡니다.
1. **다음**을 선택합니다.
1. **선택한 프로젝트 워크플로** 로 **프로 비전 패키지** 를 선택 된 채로 두고 **다음**을 선택 합니다.
1. **모든 Windows 데스크톱 버전** 선택에서 **확인 및 구성할 설정을** 선택 하 고 **다음**을 선택 합니다.
1. **마침**을 선택합니다.
1. 새로 만든 프로젝트에서 **런타임 설정** > **WindowsHelloForBusiness** > **securitykeys** > **UseSecurityKeyForSignIn**로 이동 합니다.
1. **UseSecurityKeyForSignIn** 을 **Enabled**로 설정 합니다.
1. **프로 비전 패키지** **내보내기** > 를 선택 합니다.
1. **빌드** 창에서 **프로 비전 패키지 설명** 아래의 기본값을 그대로 두고 **다음**을 선택 합니다.
1. **빌드** 창에서 **프로 비전 패키지에 대 한 보안 세부 정보 선택** 의 기본값을 그대로 두고 **다음**을 선택 합니다.
1. **프로 비전 패키지를 저장할 위치 선택** 에서 **빌드** 창의 경로를 확인 하거나 변경 하 고 **다음**을 선택 합니다.
1. **프로 비전 패키지 빌드** 페이지에서 **빌드** 를 선택 합니다.
1. 만든 두 파일 (ppkg 및 cat)을 나중에 컴퓨터에 적용할 수 있는 위치에 저장 합니다.
1. [프로 비전 패키지 적용](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)문서의 지침에 따라 만든 프로 비전 패키지를 적용 합니다.

## <a name="obtain-fido2-security-keys"></a>FIDO2 보안 키 가져오기

지원 되는 키와 제조업체에 대 한 자세한 내용은 [passwordless?](concept-authentication-passwordless.md) 문서에서 FIDO2 보안 키 섹션을 참조 하세요.

> [!NOTE]
> 를 구매 하 고 NFC 기반 보안 키를 사용할 계획인 경우 지원 되는 NFC 판독기가 필요 합니다.

## <a name="enable-passwordless-authentication-method"></a>암호 없는 인증 방법 사용

### <a name="enable-the-combined-registration-experience"></a>결합 된 등록 환경 사용

암호 없는 인증 방법에 대 한 등록 기능은 결합 된 등록 미리 보기를 사용 합니다. 결합 된 [보안 정보 등록 (미리 보기)](howto-registration-mfa-sspr-combined.md)문서의 단계에 따라 결합 된 등록 미리 보기를 사용 하도록 설정 합니다.

### <a name="enable-new-passwordless-authentication-method"></a>새 암호 없는 인증 방법 사용

1. [Azure 포털](https://portal.azure.com)
1. **Azure Active Directory** > **보안** **인증 방법**인증 방법**정책 (미리 보기)** 으로 이동 합니다. >  > 
1. 각 **방법**에서 다음 옵션을 선택 합니다.
   1. **사용** -예 또는 아니요
   1. **대상** -모든 사용자 또는 사용자 선택
1. 각 메서드 **저장**

> [!WARNING]
> FIDO2 "키 제한 정책"이 아직 작동 하지 않습니다. 일반 공급 이전에는이 기능을 사용할 수 있습니다. 이러한 정책을 기본에서 변경 하지 마세요.

> [!NOTE]
> 암호 없는 메서드를 모두 옵트인 (opt in) 할 필요는 없습니다. 하나의 암호 없는 메서드만 미리 보려면 해당 메서드만 사용 하도록 설정할 수 있습니다. 두 방법 모두 자체 혜택이 있으므로 두 방법을 모두 사용해 보는 것이 좋습니다.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 보안 키의 사용자 등록 및 관리

1. [https://myprofile.microsoft.com](https://myprofile.microsoft.com)으로 이동합니다.
1. 아직 로그인 하지 않은 경우 로그인
1. **보안 정보** 를 클릭 합니다.
   1. 사용자가 이미 하나 이상의 Azure Multi-Factor Authentication 메서드를 등록 한 경우 FIDO2 보안 키를 즉시 등록할 수 있습니다.
   1. 하나 이상의 Azure Multi-Factor Authentication 메서드를 등록 하지 않은 경우 하나를 추가 해야 합니다.
1. **메서드 추가** 를 클릭 하 고 **보안 키** 를 선택 하 여 FIDO2 보안 키를 추가 합니다.
1. **USB 장치** 또는 **NFC 장치** 선택
1. 키를 준비 하 고 **다음** 을 선택 합니다.
1. 보안 키에 대 한 PIN을 생성/입력 하 라는 상자가 표시 되 면 생체 인식 또는 터치에서 키에 대 한 필수 제스처를 수행 합니다.
1. 결합 된 등록 환경으로 반환 되 고 토큰에 대 한 의미 있는 이름을 입력 하 라는 메시지가 표시 됩니다 .이를 통해 여러가 있는 경우이를 식별할 수 있습니다. **다음**을 클릭합니다.
1. **완료** 를 클릭 하 여 프로세스를 완료 합니다.

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>보안 키 생체 인식, PIN 또는 다시 설정 보안 키를 관리 합니다.

* Windows 10 버전 1809
   * 보안 키 공급 업체의 도우미 소프트웨어가 필요 합니다.
* Windows 10 버전 1903 이상
   * 사용자는 자신의 장치 > **계정** > **보안 키** 에서 **Windows 설정을** 열 수 있습니다.
   * 사용자가 PIN을 변경 하거나, 생체 인식을 업데이트 하거나, 보안 키를 다시 설정할 수 있습니다.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator 앱의 사용자 등록 및 관리

휴대폰 로그인에 대 한 Microsoft Authenticator 앱을 구성 하려면 [Microsoft Authenticator 앱을 사용 하 여 계정에 로그인](../user-help/user-help-auth-app-sign-in.md)문서의 지침을 따르세요.

## <a name="sign-in-with-passwordless-credential"></a>암호 없는 자격 증명을 사용 하 여 로그인

### <a name="sign-in-at-the-lock-screen"></a>잠금 화면에서 로그인

아래 예제에서 사용자 Bala Sandhu는 이미 FIDO2 보안 키를 프로 비전 했습니다. Bala는 Windows 10 잠금 화면에서 보안 키 자격 증명 공급자를 선택 하 고 Windows에 로그인 하는 보안 키를 삽입할 수 있습니다.

![Windows 10 잠금 화면에서 보안 키 로그인](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>웹에 로그인

아래 예제에서는 사용자가 이미 FIDO2 보안 키를 프로 비전 했습니다. 사용자는 Windows 10 버전 1809 이상의 Microsoft Edge 브라우저 내에서 FIDO2 보안 키를 사용 하 여 웹에서 로그인 하도록 선택할 수 있습니다.

![Microsoft Edge의 보안 키 로그인](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>알려진 문제

### <a name="security-key-provisioning"></a>보안 키 프로 비전

보안 키의 관리자 프로 비전 및 프로 비전 해제는 공개 미리 보기에서 사용할 수 없습니다.

### <a name="hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인

FIDO2 보안 키 또는 암호 없는 Microsoft Authenticator 로그인과 같은 관리 되는 자격 증명을 사용 하는 WIA sso를 사용 하는 사용자는 sso의 이점을 얻기 위해 Windows 10에서 하이브리드 조인 해야 합니다. 그러나 보안 키는 지금은 Azure Active Directory 연결 된 컴퓨터에 대해서만 작동 합니다. 순수한 Azure Active Directory 연결 된 컴퓨터에서 Windows 잠금 화면에 대 한 FIDO2 보안 키만 사용해 보는 것이 좋습니다. 이 제한은 웹에는 적용 되지 않습니다.

### <a name="upn-changes"></a>UPN 변경

하이브리드 AADJ 및 AADJ 장치에서 UPN 변경을 허용 하는 기능을 지원 하기 위해 노력 하 고 있습니다. 사용자의 UPN이 변경 되 면 더 이상 FIDO2 보안 키를 수정할 수 없습니다. 따라서 장치를 다시 설정 하 고 사용자가 다시 등록 해야 합니다.

## <a name="next-steps"></a>다음 단계

[디바이스 등록에 대한 자세한 정보](../devices/overview.md)

[Azure Multi-Factor Authentication에 대한 자세한 정보](../authentication/howto-mfa-getstarted.md)
