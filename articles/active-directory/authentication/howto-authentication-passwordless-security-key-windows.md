---
title: Passwordless 보안 키 로그인 Windows - Azure Active Directory
description: FIDO2 보안 키를 사용해 Azure Active Directory에 암호 없는 보안 키 로그인을 사용하도록 설정하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 190e9c857f1ec9d19eb89493dc4b4a9fb68fac87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653510"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory"></a>Azure Active Directory를 통해 Windows 10 디바이스에 대한 암호 없는 보안 키 로그인 사용 설정 

본 문서에서는 Windows 10 디바이스에 암호 없는 인증 기반 FIDO2 보안 키를 활성화하도록 설정하는 방법을 집중적으로 알아봅니다. 본 문서를 마칠 때쯤이면 Azure AD와 하이브리드 Azure AD 조인된 Windows 10 디바이스에 FIDO2 보안키를 사용해 Azure AD 계정으로 로그인할 수 있게 될 것입니다.

## <a name="requirements"></a>요구 사항

| 디바이스 유형 | Azure AD 가입 | 하이브리드 Azure AD 가입 |
| --- | --- | --- |
| [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [결합된 보안 정보 등록](concept-registration-mfa-sspr-combined.md) | X | X |
| 호환 되는 [FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN에는 Windows 10 버전 1903 이상이 필요 | X | X |
| [Azure AD 조인된 디바이스](../devices/concept-azure-ad-join.md)에는 Windows 10 버전 1909 이상이 필요 | X |   |
| [하이브리드 Azure AD 조인된 디바이스](../devices/concept-azure-ad-join-hybrid.md)에는 Windows 10 버전 2004 이상이 필요 |   | X |
| Windows Server 2016/2019 도메인 컨트롤러를 완전히 패치했습니다. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 버전 1.4.32.0 이상 |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune)(옵션) | X | X |
| 프로비저닝 패키지(옵션) | X | X |
| 그룹 정책(옵션) |   | X |

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 지원되지 않습니다.

- Windows Server AD DS(Active Directory Domain Services) 도메인 조인(온-프레미스 전용 디바이스) 배포
- 보안 키를 사용하는 RDP, VDI 및 Citrix 시나리오
- 보안 키를 사용하는 S/MIME
- 보안 키를 사용하는 “다음 계정으로 실행”
- 보안 키를 사용하여 서버에 로그인
- 온라인 상태에서 디바이스에 로그인하기 위해 보안 키를 사용하지 않은 경우에는 이를 사용하여 로그인하거나 오프라인을 잠금 해제할 수 없습니다.
- 여러 Azure AD 계정이 있는 보안 키로 Windows 10 디바이스 로그인 또는 잠금 해제. 본 시나리오에서는 보안 키에 추가한 마지막 계정을 사용합니다. 사용자는 WebAuthN을 통해 사용할 계정 키를 선택할 수 있습니다.
- Windows 10 버전 1809를 실행하는 디바이스를 잠금 해제. 최상의 환경을 위해 Windows 10 버전 1903 이상을 사용합니다.

## <a name="prepare-devices"></a>디바이스 준비

Azure AD 조인된 디바이스는 Windows 10 버전 1909 이상을 실행해야 합니다.

하이브리드 Azure AD 조인된 디바이스는 Windows 10 버전 2004 이상을 실행해야 합니다.

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows 로그인을 위한 보안 키 활성화

조직에서는 해당 조직의 요구 사항에 따라 Windows 로그인용 보안키를 사용하도록 설정하기 위해 다음 방법 중 하나 이상을 사용하도록 선택할 수 있습니다.

- [Intune으로 활성화](#enable-with-intune)
- [대상 Intune 배포](#targeted-intune-deployment)
- [프로비저닝 패키지로 활성화](#enable-with-a-provisioning-package)
- [그룹 정책으로 활성화(하이브리드 Azure AD 조인된 디바이스에만 해당)](#enable-with-group-policy)

> [!IMPORTANT]
> **하이브리드 Azure AD 조인된 디바이스** 를 사용하는 조직은 **또한** 반드시 [온-프레미스 리소스에 대한 FIDO2 인증 활성화](howto-authentication-passwordless-security-key-on-premises.md) 문서 상의 단계들을 Windows 10 FIDO2 보안 키 인증 작업 전에 완료해야 합니다.
>
> **Azure AD 조인된 디바이스** 를 사용하는 조직은 반드시 디바이스가 FIDO2 보안 키를 통해 온-프레미스 리소스를 인증할 수 있게 되기 전에 이를 실시해야 합니다.

### <a name="enable-with-intune"></a>Intune으로 활성화

Intune을 사용해 보안 키 사용을 활성화하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Microsoft Intune** > **디바이스 등록** > **Windows 등록** > **비즈니스용 Windows Hello** > **속성** 으로 이동합니다.
1. **설정** 에서 **로그인에 보안 키 사용** 을 **사용** 으로 설정합니다.

로그인을 위한 보안 키 구성은 비즈니스용 Windows Hello 구성에 종속되지 않습니다.

### <a name="targeted-intune-deployment"></a>대상 Intune 배포

자격 증명 공급자를 사용하도록 특정 디바이스 그룹을 지정하려면, Intune을 통해 다음 사용자 지정 설정을 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Microsoft Intune** > **디바이스 구성** > **프로필** > **프로필 만들기** 로 이동합니다.
1. 새 프로필을 다음 설정으로 구성합니다.
   - 이름: Windows 로그인 보안 키
   - 설명: Windows 로그인 도중 사용할 FIDO 보안 키 사용
   - 플랫폼: Windows 10 이상
   - 프로필 형식: 사용자 지정
   - 사용자 지정 OMA-URI 설정:
      - 이름: Windows 로그인용 FIDO 보안 키 켜기
      - OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - 데이터 형식: 정수
      - 값: 1
1. 이 정책은 특정 사용자, 디바이스 또는 그룹에 할당할 수 있습니다. 자세한 내용은 [Microsoft Intune에서 사용자 및 디바이스 프로필 할당](/intune/device-profile-assign) 문서를 참조하세요.

![Intune 사용자 지정 디바이스 구성 정책 만들기](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>프로비저닝 패키지로 활성화

Intune으로 관리하지 않는 디바이스의 경우, 해당 기능을 사용하기 위해 프로비저닝 패키지를 설치할 수 있습니다. [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)에서 Windows 구성 디자이너 앱을 설치할 수 있습니다. 프로비저닝 패키지를 만들려면 다음 단계를 완료합니다.

1. Windows 구성 디자이너를 시작합니다.
1. **파일** > **새 프로젝트** 를 선택합니다.
1. 프로젝트에 이름을 지정하고 프로젝트 생성 경로를 기록한 뒤 **다음** 을 선택합니다.
1. *프로비저닝 패키지* 를 **선택한 프로젝트 워크플로** 가 선택된 상태로 두고 **다음** 을 선택합니다.
1. *모든 Windows 데스크톱 버전* 을 **표시 및 구성할 설정 선택** 에서 선택한 뒤 **다음** 을 선택합니다.
1. **마침** 을 선택합니다.
1. 새로 만든 프로젝트에서 **런타임 설정** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn** 으로 이동합니다.
1. **UseSecurityKeyForSignIn** 을 *사용* 으로 설정합니다.
1. **내보내기** > **프로비저닝 패키지** 를 선택
1. **빌드** 창은 **프로비저닝 패키지 설명** 아래의 기본값을 그대로 두고 **다음** 을 선택합니다.
1. **빌드** 창은 **프로비저닝 패키지 보안 세부 정보 선택** 아래의 기본값을 그대로 두고 **다음** 을 선택합니다.
1. **빌드** 창의 경로는 **프로비저닝 패키지 저장 위치 선택** 아래에 경로를 기록하거나 변경한 뒤 **다음** 을 선택합니다.
1. **빌드** 를 **프로비저닝 패키지 빌드** 페이지에서 선택합니다.
1. 생성된 파일 두 개(*ppkg* 및 *cat*)를 나중에 머신에 적용할 수 있는 위치에 저장합니다.
1. 만든 프로비저닝 패키지를 적용하려면 [프로비저닝 패키지 적용](/windows/configuration/provisioning-packages/provisioning-apply-package)을 참조하세요.

> [!NOTE]
> Windows 10 버전 1903을 실행하는 디바이스가 공유 PC 모드(*EnableSharedPCMode*)도 사용하도록 설정해야 합니다. 해당 기능 사용 설정과 관련된 자세한 내용은 [Windows 10을 사용하여 공유 또는 게스트 PC 설정하기](/windows/configuration/set-up-shared-or-guest-pc)를 참조하세요.

### <a name="enable-with-group-policy"></a>그룹 정책으로 활성화

**하이브리드 Azure AD 조인된 디바이스** 의 경우, 조직에서 다음의 그룹 정책 설정을 구성하여 FIDO 보안키 로그인을 사용할 수 있습니다. **컴퓨터 구성** > **관리 템플릿** > **시스템** > **로그온** > **보안 키 로그인 켜기** 에서 설정을 확인할 수 있습니다.

- 이 정책을 **사용** 으로 설정하면 사용자가 보안 키를 통해 로그인할 수 있습니다.
- 이 정책을 **사용하지 않음** 또는 **구성되지 않음** 으로 설정하면 사용자가 보안 키를 통해 로그인할 수 없게 됩니다.

해당 그룹 정책 설정에는 업데이트된 버전의 `CredentialProviders.admx` 그룹 정책 템플릿이 필요합니다. 이 새 템플릿은 Windows Server 및 Windows 10 20H1 다음 버전에서 사용할 수 있습니다. 해당 설정은 Windows 새 버전을 실행하는 디바이스를 통해 관리하거나 [Windows 그룹 정책 관리 템플릿 중앙 저장소 제작 및 관리 방법](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)이라는 지원 토픽의 지침에 따라 중앙에서 관리할 수 있습니다.

## <a name="sign-in-with-fido2-security-key"></a>FIDO2 보안 키로 로그인

다음 예제에서는 Bala Sandhu라는 이름의 사용자가 이전 문서인 [암호 없는 보안 키 로그인 사용 설정](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)의 단계를 사용해 FIDO2 보안 키를 이미 프로비저닝한 상태입니다. 하이브리드 Azure AD 조인된 디바이스의 경우, 이미 [온-프레미스 리소스에 대해 암호 없는 보안 키 로그인 사용을 설정](howto-authentication-passwordless-security-key-on-premises.md)하였는지 확인합니다. Bala는 Windows 10 잠금 화면에서 보안 키 자격 증명 공급자를 선택하고 Windows에 로그인하도록 보안 키를 삽입할 수 있습니다.

![Windows 10 잠금 화면의 보안 키 로그인](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>보안 키 생체 인식, PIN 관리 또는 보안 키 다시 설정

* Windows 10 버전 1903 이상
   * 사용자는 **Windows 설정을** 디바이스 > **계정** > **보안 키** 에서 열 수 있습니다.
   * 사용자는 자신의 PIN을 변경하고, 생체 인식을 업데이트하거나 보안 키를 다시 설정할 수 있습니다.

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

본 기능과 관련된 피드백을 공유하고자 하거나 문제가 발생한 경우 다음 단계에 따라 Windows 피드백 허브 앱을 통해 공유합니다.

1. **피드백 허브** 를 시작하고 로그인했는지 확인합니다.
1. 다음 분류에 따라 피드백을 제출합니다.
   - 범주: 보안 및 개인 정보
   - 하위 범주: FIDO
1. 로그를 캡처하려면 옵션을 통해 **문제 다시 만들기** 를 실행합니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 및 하이브리드 Azure AD 조인된 디바이스에 대한 온-프레미스 리소스에 액세스 사용](howto-authentication-passwordless-security-key-on-premises.md)

[디바이스 등록에 대해 자세히 알아보기](../devices/overview.md)

[Azure AD Multi-Factor Authentication에 대해 자세히 알아보기](../authentication/howto-mfa-getstarted.md)