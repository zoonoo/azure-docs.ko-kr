---
title: 암호없는 보안 키 로그인 윈도우 - Azure 활성 디렉터리
description: FIDO2 보안 키를 사용하여 Azure Active Directory에 암호 없는 보안 키 로그인을 활성화하는 방법 알아보기(미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6ef244a887e75a0d8b9bb663d5325a33cd1e89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263908"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Azure Active Directory를 사용하여 Windows 10 장치에 암호 없는 보안 키 로그인 사용(미리 보기)

이 문서에서는 Windows 10 장치에서 FIDO2 보안 키 기반 암호 없는 인증을 사용하도록 설정하는 데 중점을 둡니다. 이 문서의 끝에서, 당신은 당신의 Azure AD와 하이브리드 Azure AD 모두에 로그인 할 수 있습니다 윈도우 10 FIDO2 보안 키를 사용하여 Azure AD 계정으로 장치 가입.

|     |
| --- |
| FIDO2 보안 키는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="requirements"></a>요구 사항

| 디바이스 유형 | Azure AD 가입 | 하이브리드 Azure AD 가입 |
| --- | --- | --- |
| [Azure Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [결합된 보안 정보 등록 미리 보기](concept-registration-mfa-sspr-combined.md) | X | X |
| 호환 [FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN 은 윈도우 10 버전 1809 이상필요 | X | X |
| [Azure AD 조인 된 장치에는](../devices/concept-azure-ad-join.md) Windows 10 버전 1903 이상이 필요합니다. | X |   |
| [하이브리드 Azure AD 조인 된 장치에는](../devices/concept-azure-ad-join-hybrid.md) Windows 10 내부자 빌드 18945 이상이 필요합니다. |   | X |
| 완전히 패치 된 Windows 서버 2016/2019 도메인 컨트롤러. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 버전 1.4.32.0 이상 |   | X |
| [마이크로소프트 인튠](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (선택 사항) | X | X |
| 프로비저닝 패키지(선택 사항) | X | X |
| 그룹 정책(선택 사항) |   | X |

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 지원되지 않습니다.

- Windows 서버 활성 디렉터리 도메인 서비스(AD DS) 도메인 조인(온-프레미스 전용 장치) 배포.
- 보안 키를 사용하여 RDP, VDI 및 Citrix 시나리오.
- 보안 키를 사용하는 S/MIME.
- 보안 키를 사용하여 "로 실행"합니다.
- 보안 키를 사용하여 서버에 로그인합니다.
- 온라인 상태에서 보안 키를 사용하여 기기에 로그인하지 않은 경우 로그인하거나 오프라인으로 잠금을 해제하는 데 사용할 수 없습니다.
- 여러 Azure AD 계정이 포함된 보안 키가 있는 Windows 10 장치에 로그인하거나 잠금을 해제합니다. 이 시나리오는 보안 키에 추가 된 마지막 계정을 사용 합니다. WebAuthN을 사용하면 사용자가 사용하려는 계정을 선택할 수 있습니다.
- Windows 10 버전 1809를 실행하는 장치의 잠금을 해제합니다. 최상의 환경을 위해 Windows 10 버전 1903 이상을 사용하십시오.

## <a name="prepare-devices-for-preview"></a>미리 보기를 위한 장치 준비

기능 미리 보기 중에 시험 운용중인 Azure AD 조인 장치는 Windows 10 버전 1809 이상을 실행해야 합니다. 최상의 경험은 윈도우에 10 버전 1903 이상.

하이브리드 Azure AD 조인 된 장치는 Windows 10 내부자 빌드 18945 또는 최신을 실행해야 합니다.

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows 로그인에 대한 보안 키 사용

조직은 조직의 요구 사항에 따라 Windows 로그인에 대한 보안 키를 사용할 수 있도록 다음 방법 중 하나 이상을 사용하도록 선택할 수 있습니다.

- [Intune사용](#enable-with-intune)
- [대상 인튠 배포](#targeted-intune-deployment)
- [프로비저닝 패키지로 사용](#enable-with-a-provisioning-package)
- [그룹 정책으로 사용(하이브리드 Azure AD 조인 된 장치에만)](#enable-with-group-policy)

> [!IMPORTANT]
> 하이브리드 **Azure AD 조인 장치를** 가진 조직은 Windows 10 FIDO2 보안 키 인증이 작동하기 전에 [온-프레미스 리소스에 FIDO2 인증 을 사용하도록 설정하는](howto-authentication-passwordless-security-key-on-premises.md) **문서의** 단계를 완료해야 합니다.
>
> Azure **AD조 장치가 있는** 조직은 장치가 FIDO2 보안 키를 사용하여 온-프레미스 리소스에 인증하기 전에 이 작업을 수행해야 합니다.

### <a name="enable-with-intune"></a>Intune사용

Intune을 사용하여 보안 키를 사용할 수 있도록 하려면 다음 단계를 완료하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 비즈니스 > **속성에**대 한 **마이크로소프트 인튠** > **장치 등록** > **윈도우 안녕하세요** > **안녕하세요**찾아보십시오.
1. **설정에서** **사용 하려면** **로그인에 보안 키 를 사용** 설정 합니다.

로그인을 위한 보안 키 구성은 비즈니스용 Windows Hello 구성에 의존하지 않습니다.

### <a name="targeted-intune-deployment"></a>대상 인튠 배포

자격 증명 공급자를 사용하도록 특정 장치 그룹을 대상으로 지정하려면 Intune을 통해 다음 사용자 지정 설정을 사용합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 찾기 마이크로 **소프트 인튠** > **장치 구성** > **프로필** > **프로필 만들기 .**
1. 다음 설정으로 새 프로필을 구성합니다.
   - 이름: Windows 로그인용 보안 키
   - 설명: Windows 로그인 중에 FIDO 보안 키를 사용할 수 있습니다.
   - 플랫폼: 윈도우 10 이상
   - 종단 유형: 사용자 지정
   - 사용자 지정 OMA-URI 설정:
      - 이름: Windows 로그인용 FIDO 보안 키 켜기
      - OMA-URI: ./장치/공급업체/MSFT/패스포트워크/보안키/유스시큐어사인
      - 데이터 유형: 정수
      - 값: 1
1. 이 정책은 특정 사용자, 장치 또는 그룹에 할당할 수 있습니다. 자세한 내용은 [Microsoft Intune의 사용자 및 장치 프로필 할당을](https://docs.microsoft.com/intune/device-profile-assign)참조하십시오.

![인튠 사용자 지정 장치 구성 정책 만들기](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>프로비저닝 패키지로 사용

Intune에서 관리하지 않는 장치의 경우 프로비저닝 패키지를 설치하여 기능을 활성화할 수 있습니다. Windows 구성 디자이너 앱은 [Microsoft 스토어에서](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)설치할 수 있습니다. 프로비저닝 패키지를 만들려면 다음 단계를 완료합니다.

1. Windows 구성 디자이너를 시작합니다.
1. **파일** > **새 프로젝트**선택 .
1. 프로젝트에 이름을 지정하고 프로젝트가 생성된 경로를 기록한 다음 **다음을**선택합니다.
1. *프로비저닝 패키지를* **선택한 프로젝트 워크플로로** 선택하고 **다음을**선택합니다.
1. **아래에서**모든 Windows *데스크톱 버전을* 선택한 다음 **다음**을 선택합니다.
1. **마침**을 선택합니다.
1. 새로 만든 프로젝트에서 **런타임 설정으로** > 찾아보기**윈도우헬로포비즈니스** > **보안키** > **사용보안키키.**
1. **사용 보안키들어 로그인을** *사용 설정합니다.*
1. **내보내기** > **프로비저닝 패키지** 선택
1. **프로비저닝 패키지 설명**아래의 **빌드** 창에 기본값을 그대로 두고 **다음을 선택합니다.**
1. **프로비저닝 패키지에 대한 보안 세부 정보 선택** **아래의 빌드** 창에 기본값을 두고 **다음을**선택합니다.
1. **프로비저닝 패키지를 저장할 위치 선택** 아래의 **빌드** 창에서 경로를 기록하거나 변경하고 **다음을 선택합니다.**
1. **프로비저닝 패키지 빌드** 페이지에서 **빌드를** 선택합니다.
1. 생성된 두*파일(ppkg* 및 *cat)을*나중에 컴퓨터에 적용할 수 있는 위치에 저장합니다.
1. 만든 프로비저닝 패키지를 적용하려면 [프로비저닝 패키지 적용을](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)참조하십시오.

> [!NOTE]
> Windows 10 버전 1809를 실행하는 장치는 공유 PC*모드(사용 가능 공유PCMode)도*활성화해야 합니다. 이 기능을 사용하도록 설정하는 자세한 내용은 [Windows 10에서 공유 또는 게스트 PC 설정을](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)참조하십시오.

### <a name="enable-with-group-policy"></a>그룹 정책으로 사용

**하이브리드 Azure AD 조인 장치의**경우 조직은 FIDO 보안 키 로그인을 사용하도록 다음 그룹 정책 설정을 구성할 수 있습니다. 이 설정은 **컴퓨터 구성** > **관리 템플릿** > **시스템** > **로그온** > **켜기 보안 키 로그인에서**찾을 수 있습니다.

- 이 정책을 **사용 설정으로** 설정하면 사용자가 보안 키로 로그인할 수 있습니다.
- 이 정책을 **사용 안 함** 또는 **구성 되지 않음으로** 설정하면 사용자가 보안 키로 로그인하지 못하게 됩니다.

이 그룹 정책 설정에는 그룹 `credentialprovider.admx` 정책 템플릿의 업데이트된 버전이 필요합니다. 이 새 템플릿은 다음 버전의 Windows 서버와 Windows 10 20H1에서 사용할 수 있습니다. 이 설정은 이러한 최신 버전 중 하나를 실행하는 장치로 관리하거나 Windows에서 그룹 정책 [관리 템플릿에 대한 중앙 저장소를 만들고 관리하는 방법](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)지원 항목의 지침에 따라 중앙에서 관리할 수 있습니다.

## <a name="sign-in-with-fido2-security-key"></a>FIDO2 보안 키로 로그인

아래 예제에서 Bala Sandhu라는 사용자는 이전 문서의 단계인 암호 없는 보안 키 [로그인 을 사용하도록 설정하여](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)FIDO2 보안 키를 이미 프로비전했습니다. 하이브리드 Azure AD 조인 장치의 경우 [온-프레미스 리소스에 암호없는 보안 키 로그인도 사용하도록 설정했는지](howto-authentication-passwordless-security-key-on-premises.md)확인합니다. Bala는 Windows 10 잠금 화면에서 보안 키 자격 증명 공급자를 선택하고 보안 키를 삽입하여 Windows에 로그인할 수 있습니다.

![Windows 10 잠금 화면에서 보안 키 로그인](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>보안 키 생체 인식, PIN 또는 보안 키 재설정 관리

* 윈도우 10 버전 1903 이상
   * 사용자는 자신의 장치에서 **Windows 설정을** 열 수 있습니다 > **계정** > **보안 키**
   * 사용자는 PIN을 변경하거나, 생체 인식을 업데이트하거나, 보안 키를 재설정할 수 있습니다.

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

이 기능을 미리 볼 때 피드백을 공유하거나 문제가 발생하려면 다음 단계를 사용하여 Windows 피드백 허브 앱을 통해 공유하십시오.

1. **피드백 허브를** 시작하고 로그인했는지 확인합니다.
1. 다음 분류에 따라 피드백을 제출합니다.
   - 카테고리: 보안 및 개인 정보 보호
   - 하위 범주: FIDO
1. 로그를 캡처하려면 **내 문제를 다시 만드는** 옵션을 사용합니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 및 하이브리드 Azure AD 조인 장치에 대한 온-프레미스 리소스에 대한 액세스 활성화](howto-authentication-passwordless-security-key-on-premises.md)

[기기 등록에 대해 자세히 알아보기](../devices/overview.md)

[Azure Multi-Factor Authentication에 대해 자세히 알아보기](../authentication/howto-mfa-getstarted.md)
