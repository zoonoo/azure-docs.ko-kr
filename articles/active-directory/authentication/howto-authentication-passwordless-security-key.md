---
title: 암호 없는 보안 키 로그인 - Azure Active Directory
description: FIDO2 보안 키를 사용한 Azure AD에 대한 암호 없는 보안 키 로그인 사용
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/03/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3373c1f9a82f79782ed1758fd09c83bcfbe6fc03
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963719"
---
# <a name="enable-passwordless-security-key-sign-in"></a>암호 없는 보안 키 로그인 사용 

현재 암호를 사용하고 공유 PC 환경을 보유한 기업의 경우 보안 키를 사용하면 사용자 이름 또는 암호를 입력하지 않고도 작업자의 인증을 원활하게 지원합니다. 보안 키는 작업자에게 향상된 생산성을 제공하며 보안이 더 뛰어납니다.

이 문서에서는 보안 키 기반 암호 없는 인증을 사용하도록 설정하는 방법을 중점적으로 설명합니다. 이 문서를 읽고 난 후에는 FIDO2 보안 키를 사용하여 Azure AD 계정으로 웹 기반 애플리케이션에 로그인할 수 있습니다.

## <a name="requirements"></a>요구 사항

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- [결합된 보안 정보 등록](concept-registration-mfa-sspr-combined.md) 사용
- 호환되는 [FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN에는 Windows 10 버전 1903 이상이 필요**

웹앱 및 서비스에 로그인하는 데 보안 키를 사용하려면 WebAuthN 프로토콜을 지원하는 브라우저가 있어야 합니다. Microsoft Edge, Chrome, Firefox 및 Safari 등이 있습니다.

## <a name="prepare-devices"></a>디바이스 준비

Azure AD 조인 디바이스에 대한 최상의 환경은 Windows 10 버전 1903 이상입니다.

하이브리드 Azure AD 조인 디바이스는 Windows 10 버전 2004 이상을 실행해야 합니다.

## <a name="enable-passwordless-authentication-method"></a>암호 없는 인증 사용 방법

### <a name="enable-the-combined-registration-experience"></a>결합된 등록 환경 사용

암호 없는 인증 방법에 대한 등록 기능은 결합된 등록 기능을 사용합니다. [결합된 보안 정보 등록 사용](howto-registration-mfa-sspr-combined.md) 문서의 단계를 따라 결합된 등록을 사용합니다.

### <a name="enable-fido2-security-key-method"></a>FIDO2 보안 키 방법 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **보안** > **인증 방법** > **인증 방법 정책** 으로 이동합니다.
1. **FIDO2 보안 키** 방법에서 다음 옵션을 선택합니다.
   1. **사용** - 예 또는 아니요
   1. **대상** - 모든 사용자 또는 사용자 선택
1. 구성을 **저장** 합니다.


### <a name="fido-security-key-optional-settings"></a>FIDO 보안 키 옵션 설정 

테넌트별로 보안 키를 관리하기 위한 몇 가지 옵션 설정이 있습니다.  

![FIDO2 보안 키 옵션의 스크린샷](media/howto-authentication-passwordless-security-key/optional-settings.png) 

**일반**

- **셀프 서비스 설정 허용** 은 **예** 로 설정된 상태를 유지해야 합니다. 아니요로 설정하면 인증 방법 정책으로 FIDO 키를 사용 설정했더라도 사용자는 MySecurityInfo 포털을 통해 FIDO 키를 등록할 수 없습니다.  
- **증명 적용** 을 **예** 로 설정하려면 FIDO Alliance Metadata Service를 사용하여 FIDO 보안 키 메타데이터를 게시하고 확인해야 하며, 해당 메타데이터는 Microsoft의 추가 유효성 검사 테스트도 통과해야 합니다. 자세한 내용은 [Microsoft 호환 보안 키란?](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)을 참조하세요.

**키 제한 정책**

- 조직에서 AAGuid로 식별되는 특정 FIDO 보안 키만 허용하거나 허용하지 않으려는 경우에만 **키 제한 적용** 을 **예** 로 설정해야 합니다. 보안 키 공급자와 협력하여 해당 디바이스의 AAGuid를 확인할 수 있습니다. 키가 이미 등록된 경우 사용자별로 키의 인증 방법 세부 정보를 확인하여 AAGUID를 찾을 수도 있습니다. 


## <a name="disable-a-key"></a>키 비활성화 

사용자 계정과 연결된 FIDO2 키를 제거하려면 사용자의 인증 방법에서 키를 삭제합니다.

1. Azure AD 포털에 로그인하고 FIDO 키를 제거할 사용자 계정을 검색합니다.
1. **인증 방법** > **FIDO2 보안 키** 를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 클릭합니다. 

    ![인증 방법 세부 정보 보기](media/howto-authentication-passwordless-deployment/security-key-view-details.png)

## <a name="security-key-authenticator-attestation-guid-aaguid"></a>보안 키 AAGUID(인증자 증명 GUID)

FIDO2 사양을 사용하려면 각 보안 키 공급자가 증명 과정에 AAGUID(인증자 증명 GUID)를 제공해야 합니다. AAGUID는 make 및 model과 같은 키 형식을 나타내는 128비트 식별자입니다. 

>[!NOTE]
>제조업체는 AAGUID가 해당 제조업체에서 만든, 실질적으로 동일한 모든 키에서는 동일하고 다른 모든 키 유형의 AAGUID와는 높은 확률로 다름을 보장해야 합니다. 이렇게 하려면 지정된 유형의 보안 키용 AAGUID를 임의로 생성해야 합니다. 자세한 내용은 [웹 인증: 퍼블릭 키 자격 증명 액세스용 API - 2단계(w3.org)](https://w3c.github.io/webauthn/)를 참조하세요.

AAGUID를 얻는 방법은 두 가지입니다. 보안 키 공급자에게 문의하거나 사용자별로 키의 인증 방법 세부 정보를 보면 됩니다.

![보안 키 AAGUID 보기](media/howto-authentication-passwordless-deployment/security-key-aaguid-details.png)

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO 보안 키의 사용자 등록 및 관리

1. [https://myprofile.microsoft.com](https://myprofile.microsoft.com) 으로 이동합니다.
1. 아직 로그인하지 않은 경우 로그인합니다.
1. **보안 정보** 를 클릭합니다.
   1. 사용자가 이미 하나 이상의 Azure AD Multi-Factor Authentication 방법을 등록한 경우 FIDO2 보안 키를 즉시 등록할 수 있습니다.
   1. 하나 이상의 Azure AD Multi-Factor Authentication 방법을 등록하지 않은 경우에는 방법을 추가해야 합니다.
1. **방법 추가** 를 클릭하고 **보안 키** 를 선택하여 FIDO2 보안 키를 추가합니다.
1. **USB 디바이스** 또는 **NFC 디바이스** 를 선택합니다.
1. 키를 준비하고 **다음** 을 선택합니다.
1. 상자가 표시되고, 사용자에게 보안 키의 PIN을 만들거나 입력하도록 요청한 다음, 키에 필요한 제스처인 생체 인식 또는 터치를 수행합니다.
1. 사용자는 결합된 등록 환경으로 돌아가고 키의 의미 있는 이름을 입력하라는 메시지가 표시되므로 여러 키를 보유한 경우 사용자가 해당 키를 식별할 수 있습니다. **다음** 을 클릭합니다.
1. **마침** 을 클릭하여 프로세스를 완료합니다.

## <a name="sign-in-with-passwordless-credential"></a>암호 없는 자격 증명으로 로그인

아래 예제에서는 사용자가 이미 FIDO2 보안 키를 프로비저닝했습니다. 사용자는 Windows 10 버전 1903 이상에서 지원되는 브라우저 내에서 FIDO2 보안 키를 사용하여 웹에서 로그인하도록 선택할 수 있습니다.

![보안 키 로그인 Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

이 기능을 사용하여 피드백을 공유하거나 문제를 해결하려는 경우 다음 단계를 사용하여 Windows 피드백 허브 앱을 통해 공유하세요.

1. **피드백 허브** 를 시작하고 로그인했는지 확인합니다.
1. 다음 분류에 따라 피드백을 제출합니다.
   - 범주: 보안 및 개인 정보
   - 하위 범주: FIDO
1. 로그를 캡처하려면 옵션을 통해 **문제 다시 만들기** 를 실행합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="security-key-provisioning"></a>보안 키 프로비전

보안 키의 관리자 프로비전 및 프로비전 해제를 사용할 수 없습니다.


### <a name="upn-changes"></a>UPN 변경

사용자의 UPN이 변경되면 더 이상 FIDO2 보안 키를 수정하여 변경을 고려하지 않을 수 있습니다. FIDO2 보안 키를 이용하는 사용자를 위한 해결 방법은 MySecurityInfo에 로그인하여 이전 키를 삭제하고 새 키를 추가하는 것입니다.

## <a name="next-steps"></a>다음 단계

[FIDO2 보안 키 Windows 10 로그인](howto-authentication-passwordless-security-key-windows.md)

[온-프레미스 리소스에 대한 FIDO2 인증 사용](howto-authentication-passwordless-security-key-on-premises.md)

[디바이스 등록에 대해 자세히 알아보기](../devices/overview.md)

[Azure AD Multi-Factor Authentication에 대해 자세히 알아보기](../authentication/howto-mfa-getstarted.md)