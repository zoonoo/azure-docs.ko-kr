---
title: Passwordless 보안 키 로그인 (미리 보기)-Azure Active Directory
description: FIDO2 보안 키 (미리 보기)를 사용 하 여 Azure AD에 암호 없는 보안 키 로그인 사용
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d9c4dff1e4a3ba7c7a2b11311e97eb5e66a1585
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994250"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>암호 없는 보안 키 로그인 사용 (미리 보기)

현재 암호를 사용 하 고 공유 PC 환경을 보유 하 고 있는 기업의 경우 보안 키를 사용 하면 사용자 이름 또는 암호를 입력 하지 않고도 작업자의 인증을 원활 하 게 할 수 있습니다. 보안 키는 작업자에 게 향상 된 생산성을 제공 하 고 보안을 강화 합니다.

이 문서에서는 보안 키 기반 암호 없는 인증을 사용 하도록 설정 하는 방법을 중점적으로 설명 합니다. 이 문서의 끝 부분에서 FIDO2 보안 키를 사용 하 여 Azure AD 계정으로 웹 기반 응용 프로그램에 로그인 할 수 있습니다.

> [!NOTE]
> FIDO2 보안 키는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대 한 자세한 내용은  [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="requirements"></a>요구 사항

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- [결합 된 보안 정보 등록 미리 보기](concept-registration-mfa-sspr-combined.md) 사용
- 호환 되는 [FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN에는 Windows 10 버전 1903 이상이 필요 합니다. * *

웹 앱 및 서비스에 로그인 하는 데 보안 키를 사용 하려면 WebAuthN 프로토콜을 지 원하는 브라우저가 있어야 합니다. 여기에는 Microsoft Edge, Chrome, Firefox 및 Safari가 포함 됩니다.

## <a name="prepare-devices-for-preview"></a>미리 보기용으로 장치 준비

를 사용 하 여 파일럿 할 Azure AD 조인 장치는 Windows 10 버전 1909 이상을 실행 해야 합니다. 최상의 환경은 Windows 10 버전 1903 이상에 있습니다.

하이브리드 Azure AD 조인 장치는 Windows 10 버전 2004 이상을 실행 해야 합니다.

## <a name="enable-passwordless-authentication-method"></a>암호 없는 인증 방법 사용

### <a name="enable-the-combined-registration-experience"></a>결합 된 등록 환경 사용

암호 없는 인증 방법에 대 한 등록 기능은 결합 된 등록 기능을 사용 합니다. 결합 된 [보안 정보 등록 (미리 보기)](howto-registration-mfa-sspr-combined.md)문서의 단계를 수행 하 여 결합 된 등록을 사용 하도록 설정 합니다.

### <a name="enable-fido2-security-key-method"></a>FIDO2 보안 키 사용 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**  >  **보안**  >  **인증 방법**  >  **인증 방법 정책 (미리 보기)** 으로 이동 합니다.
1. Method **FIDO2 Security 키** 아래에서 다음 옵션을 선택 합니다.
   1. **사용** -예 또는 아니요
   1. **대상** -모든 사용자 또는 사용자 선택
1. 구성을 **저장** 합니다.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 보안 키의 사용자 등록 및 관리

1. [https://myprofile.microsoft.com](https://myprofile.microsoft.com)으로 이동합니다.
1. 아직 로그인 하지 않은 경우 로그인 합니다.
1. **보안 정보** 를 클릭 합니다.
   1. 사용자가 이미 하나 이상의 Azure AD Multi-Factor Authentication 메서드를 등록 한 경우 FIDO2 보안 키를 즉시 등록할 수 있습니다.
   1. 하나 이상의 Azure AD Multi-Factor Authentication 메서드를 등록 하지 않은 경우에는 하나를 추가 해야 합니다.
1. **메서드 추가** 를 클릭 하 고 **보안 키** 를 선택 하 여 FIDO2 보안 키를 추가 합니다.
1. **USB 장치** 또는 **NFC 장치** 를 선택 합니다.
1. 키를 준비 하 고 **다음** 을 선택 합니다.
1. 상자가 표시 되 고 사용자에 게 보안 키에 대 한 PIN을 만들어 입력 하 라는 메시지가 표시 되 면 생체 인식 또는 터치를 사용 하 여 키에 대 한 필수 제스처를 수행 합니다.
1. 사용자는 결합 된 등록 환경으로 반환 되 고 키에 대 한 의미 있는 이름을 입력 하 라는 메시지가 표시 되므로 사용자가 여러 항목을 식별할 수 있습니다. **다음** 을 클릭합니다.
1. **완료** 를 클릭 하 여 프로세스를 완료 합니다.

## <a name="sign-in-with-passwordless-credential"></a>암호 없는 자격 증명을 사용 하 여 로그인

아래 예제에서는 사용자가 이미 FIDO2 보안 키를 프로 비전 했습니다. 사용자는 Windows 10 버전 1903 이상에서 지원 되는 브라우저 내에서 FIDO2 보안 키를 사용 하 여 웹에서 로그인 하도록 선택할 수 있습니다.

![보안 키 로그인 Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

이 기능을 미리 보는 동안 피드백을 공유 하거나 문제를 해결 하려는 경우 다음 단계를 사용 하 여 Windows 피드백 허브 앱을 통해 공유 하세요.

1. **피드백 허브** 를 시작 하 고 로그인 했는지 확인 합니다.
1. 다음 분류에 따라 사용자 의견을 제출 합니다.
   - 범주: 보안 및 개인 정보
   - 하위 범주: FIDO
1. 로그를 캡처하려면이 옵션을 사용 하 여 **문제를 다시 만드십시오** .

## <a name="known-issues"></a>알려진 문제

### <a name="security-key-provisioning"></a>보안 키 프로 비전

보안 키의 관리자 프로 비전 및 프로 비전 해제는 공개 미리 보기에서 사용할 수 없습니다.

### <a name="upn-changes"></a>UPN 변경

하이브리드 Azure AD 조인 및 Azure AD 조인 장치에서 UPN 변경을 허용 하는 기능을 지원 하기 위해 노력 하 고 있습니다. 사용자의 UPN이 변경 되 면 더 이상 FIDO2 보안 키를 수정 하 여 변경 내용을 고려 하지 않을 수 있습니다. 해결 방법은 장치를 다시 설정 하는 것으로, 사용자가 다시 등록 해야 합니다.

## <a name="next-steps"></a>다음 단계

[FIDO2 보안 키 Windows 10 로그인](howto-authentication-passwordless-security-key-windows.md)

[온-프레미스 리소스에 대 한 FIDO2 인증 사용](howto-authentication-passwordless-security-key-on-premises.md)

[장치 등록에 대 한 자세한 정보](../devices/overview.md)

[Azure AD Multi-Factor Authentication에 대 한 자세한 정보](../authentication/howto-mfa-getstarted.md)
