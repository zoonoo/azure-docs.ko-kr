---
title: Azure 관리 워크스테이션 배포 - Azure Active 디렉터리
description: 잘못된 구성이나 손상으로 인한 위반 위험을 줄이기 위해 Azure에서 관리하는 안전한 워크스테이션을 배포하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672615"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>안전한 Azure 관리 워크스테이션 배포

[이제 보안 워크스테이션을 이해되었으므로](concept-azure-managed-workstation.md)배포 프로세스를 시작할 차례입니다. 이 지침을 사용하면 정의된 프로필을 사용하여 처음부터 보다 안전한 워크스테이션을 만들 수 있습니다.

![안전한 워크스테이션 배포](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

솔루션을 배포하기 전에 프로파일을 선택합니다. 배포에서 동시에 여러 프로필을 사용하고 태그 또는 그룹으로 할당할 수 있습니다.

> [!NOTE]
> 요구 사항에 따라 필요에 따라 프로파일을 적용합니다. Microsoft Intune에 할당하여 다른 프로필로 이동할 수 있습니다.

| 프로필 | 낮음 | 향상된 | 높음 | 특수화 | 보안 | 격리 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD의 사용자 | yes | yes | yes | yes | yes | yes |
| 인튠 관리 | yes | yes | yes | yes | yes | yes |
| 장치 - Azure AD 등록 | yes |  |  |  |  | |   |
| 장치 - Azure AD 조인 |   | yes | yes | yes | yes | yes |
| 인튠 보안 기준 적용 |   | yes <br> (향상된 기능) | yes <br> (하이시큐리티) | yes <br> (NCSC) | yes <br> (보안) | 해당 없음 |
| 보안 Windows 10 표준을 충족하는 하드웨어 |   | yes | yes | yes | yes | yes |
| 마이크로소프트 수비수 ATP 사용 |   | yes  | yes | yes | yes | yes |
| 관리자 권한 삭제 |   |   | yes  | yes | yes | yes |
| 마이크로소프트 오토파일럿을 사용한 배포 |   |   | yes  | yes | yes | yes |
| Intune에서만 설치된 앱 |   |   |   | yes | yes |yes |
| 승인된 목록으로 제한된 URL |   |   |   | yes | yes |yes |
| 인터넷 차단(인바운드/아웃바운드) |   |   |   |  |  |yes |

> [!NOTE]
> 보안 워크스테이션 안내 **장치에는** 프로필 및 정책이 할당됩니다. 사용자는 장치 공유(공유 장치)가 적용될 수 있도록 정책을 직접 적용할 수 없습니다. 배포에서 보안 워크스테이션이 공유되지 않거나 개별 사용자 정책이 필요한 경우 사용자 정책 프로필 할당을 사용자 및 장치에 할당할 수 있습니다. 

## <a name="license-requirements"></a>라이선스 요구 사항

이 가이드에서 다루는 개념은 Microsoft 365 엔터프라이즈 E5 또는 이에 상응하는 SKU를 가지고 있다고 가정합니다. 이 가이드의 권장 사항 중 일부는 낮은 SCO로 구현할 수 있습니다. 자세한 내용은 [Microsoft 365 엔터프라이즈 라이선스](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)를 참조하십시오.

라이센스 프로비저닝을 자동화하려면 사용자에 대한 [그룹 기반 라이선스를](../users-groups-roles/licensing-groups-assign.md) 고려하십시오.

## <a name="azure-active-directory-configuration"></a>Azure Active 디렉터리 구성

Azure Active Directory(Azure AD)는 관리자 워크스테이션의 사용자, 그룹 및 장치를 관리합니다. 관리자 계정으로 ID 서비스 및 기능을 사용하도록 [설정합니다.](../users-groups-roles/directory-assign-admin-roles.md)

보안 워크스테이션 관리자 계정을 만들 때 현재 워크스테이션에 계정을 노출합니다. 알려진 안전 장치를 사용하여 이 초기 구성 및 모든 전역 구성을 수행해야 합니다. 처음 발생하는 경험에 대한 공격 노출을 줄이려면 [맬웨어 감염을 방지하기 위한 지침을](/windows/security/threat-protection/intelligence/prevent-malware-infection)따르는 것이 좋습니다.

적어도 관리자에게는 다단계 인증이 필요합니다. 구현 지침은 [클라우드 기반 MFA 배포를](../authentication/howto-mfa-getstarted.md) 참조하십시오.

### <a name="azure-ad-users-and-groups"></a>Azure AD 사용자 및 그룹

1. Azure 포털에서 Azure **Active Directory** > **새** > **사용자로**검색합니다.
1. [사용자 만들기 자습서의](/Intune/quickstart-create-user)단계에 따라 장치 관리자를 만듭니다.
1. 다음을 입력합니다.

   * **이름** - 보안 워크스테이션 관리자
   * **사용자 이름** - `secure-ws-admin@identityitpro.com`
   * **디렉터리 역할** - **제한 관리자** 및 **Intune 관리자** 역할을 선택합니다.

1. **만들기**를 선택합니다.

그런 다음 워크스테이션 사용자와 워크스테이션 장치라는 두 그룹을 만듭니다.

Azure 포털에서 Azure **Active Directory** > **그룹** > **새 그룹으로**검색합니다.

1. 워크스테이션 사용자 그룹의 경우 사용자에게 라이선스 프로비저닝을 자동화하도록 [그룹 기반 라이선스를](../users-groups-roles/licensing-groups-assign.md) 구성할 수 있습니다.
1. 워크스테이션 사용자 그룹의 경우 다음을 입력합니다.

   * **그룹 유형** - 보안
   * **그룹 이름** - 안전한 워크스테이션 사용자
   * **멤버 자격 유형** - 할당

1. 보안 워크스테이션 관리자 사용자 추가:`secure-ws-admin@identityitpro.com`
1. 보안 워크스테이션을 관리하는 다른 사용자를 추가할 수 있습니다.
1. **만들기**를 선택합니다.
1. 워크스테이션 장치 그룹의 경우 다음을 입력합니다.

   * **그룹 유형** - 보안
   * **그룹 이름** - 보안 워크스테이션
   * **멤버 자격 유형** - 할당

1. **만들기**를 선택합니다.

### <a name="azure-ad-device-configuration"></a>Azure AD 장치 구성

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Azure AD에 장치를 조인할 수 있는 사용자 지정

관리 보안 그룹이 도메인에 장치를 조인할 수 있도록 Active Directory에서 장치 설정을 구성합니다. Azure 포털에서 이 설정을 구성하려면 다음을 수행하십시오.

1. Azure **Active 디렉터리** > **장치** > **장치 설정으로**이동합니다.
1. 사용자 에서 **선택 선택** **Azure AD에 장치를 조인**한 다음 "보안 워크스테이션 사용자" 그룹을 선택할 수 있습니다.

#### <a name="removal-of-local-admin-rights"></a>로컬 관리자 권한 제거

이 방법을 사용하려면 VIP, DevOps 및 보안 수준 워크스테이션 의 사용자가 컴퓨터에 관리자 권한이 없습니다. Azure 포털에서 이 설정을 구성하려면 다음을 수행하십시오.

1. Azure **Active 디렉터리** > **장치** > **장치 설정으로**이동합니다.
1. **Azure AD 조인 장치에서 추가 로컬 관리자**에서 **없음을** 선택합니다.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>장치를 조인하려면 다단계 인증이 필요합니다.

Azure AD에 장치를 조인하는 프로세스를 더욱 강화하려면 다음을 수행하십시오.

1. Azure **Active 디렉터리** > **장치** > **장치 설정으로**이동합니다.
1. **장치를 가입하려면 다중 요소 인증 요구에서** **예를** 선택합니다.
1. **저장**을 선택합니다.

#### <a name="configure-mobile-device-management"></a>모바일 장치 관리 구성

Azure 포털에서 다음을 수행합니다.

1. Azure **Active 디렉터리** > **이동성(MDM 및 MAM)** > **Microsoft Intune을**찾아보십시오.
1. **MDM 사용자 범위** 설정을 **모두로**변경합니다.
1. **저장**을 선택합니다.

이러한 단계를 통해 Intune을 사용하여 모든 장치를 관리할 수 있습니다. 자세한 내용은 [Intune 빠른 시작: Windows 10 장치에 대한 자동 등록 설정](/Intune/quickstart-setup-auto-enrollment)을 참조하십시오. 이후 단계에서 Intune 구성 및 규정 준수 정책을 만듭니다.

#### <a name="azure-ad-conditional-access"></a>Azure AD 조건부 액세스

Azure AD 조건부 액세스는 권한 있는 관리 작업을 호환 장치로 제한하는 데 도움이 될 수 있습니다. **보안 워크스테이션 사용자** 그룹의 미리 정의된 구성원은 클라우드 응용 프로그램에 로그인할 때 다단계 인증을 수행해야 합니다. 가장 좋은 방법은 정책에서 긴급 액세스 계정을 제외하는 것입니다. 자세한 내용은 [Azure AD의 긴급 액세스 계정 관리를](../users-groups-roles/directory-emergency-access.md)참조하십시오.

## <a name="intune-configuration"></a>인튠 구성

### <a name="configure-enrollment-status"></a>등록 상태 구성

안전한 워크스테이션이 신뢰할 수 있는 깨끗한 장치인지 확인하는 것이 중요합니다. 새 장치를 구입할 때 공급망 관리 중 취약점에 대한 노출을 제한하는 [Windows 10 Pro를 S 모드로](/Windows/deployment/Windows-10-pro-in-s-mode)설정했다고 주장할 수 있습니다. 공급업체에서 장치를 받은 후 자동 조종 장치를 사용하여 S 모드에서 변경할 수 있습니다. 다음 가이드에서는 변환 프로세스 적용에 대한 세부 정보를 제공합니다.

사용하기 전에 장치가 완전히 구성되도록 하기 위해 Intune은 **모든 앱과 프로필이 설치될 때까지 장치 사용을 차단하는**수단을 제공합니다.

Azure **포털에서**:

1. 마이크로 **소프트 인튠** > **장치 등록** > 윈도우**등록** > 상태**페이지** > **기본** > **설정으로**이동합니다.
1. **앱 프로필 설치 진행률 표시를** **예로**설정합니다.
1. 모든 앱 및 프로필이 **예로** **설치될 때까지 장치 차단 을** 설정합니다.

### <a name="create-an-autopilot-deployment-profile"></a>Autopilot 배포 프로필 만들기

장치 그룹을 만든 후 자동 조종 장치를 구성하려면 배포 프로필을 만들어야 합니다.

Azure 포털의 Intune에서:

1. 장치 등록**Windows 등록** > **배포 프로필** > **만들기 프로필**을 **선택합니다.** > 
1. 다음을 입력합니다.

   * 이름 - **보안 워크스테이션 배포 프로필.**
   * 설명 - **보안 워크스테이션 의 배포**.
   * **모든 대상 디바이스를 Autopilot으로 변환**을 **예**로 설정합니다. 이렇게 설정하면 목록의 모든 디바이스가 Autopilot 배포 서비스를 사용하여 등록됩니다. 등록을 처리하는 데 48시가 가량 걸립니다.

1. **다음**을 선택합니다.

   * **배포 모드의**경우 **자체 배포(미리 보기)를 선택합니다.** 이 프로필이 있는 장치는 장치를 등록하는 사용자와 연결됩니다. 디바이스를 등록하려면 사용자 자격 증명이 필요합니다. **자체 배포** 모드에서 장치를 배포하면 공유 모델에 랩톱을 배포할 수 있습니다. 장치가 처음으로 사용자에게 할당될 때까지 사용자 할당이 수행되지 않습니다. 따라서 사용자 할당이 완료될 때까지 BitLocker와 같은 사용자 정책은 활성화되지 않습니다. 보안 장치에 로그온하는 방법에 대한 자세한 내용은 [선택한 프로필을](/intune/device-profile-assign)참조하십시오.
   * **상자로 Azure AD에 조인은** **Azure AD가 조인되고** 회색으로 표시됩니다.
   * 언어(지역), 사용자 계정 유형 **표준을**선택합니다. 

1. **다음**을 선택합니다.

   * 미리 구성된 경우 범위 태그를 선택합니다.

1. **다음**을 선택합니다.
1. 선택한**그룹에****할당하는** >  **할당을 선택합니다.** >  **포함할 그룹 선택에서** **보안 워크스테이션을**선택합니다.
1. **다음**을 선택합니다.
1. **만들기**를 선택하여 프로필을 만듭니다. 이제 Autopilot 배포 프로필을 디바이스에 할당할 수 있습니다.

자동 조종 장치의 장치 등록은 장치 유형 및 역할에 따라 다른 사용자 환경을 제공합니다. 배포 예제에서는 보안 장치가 대량 배포되어 공유할 수 있지만 처음 사용할 때 장치가 사용자에게 할당되는 모델을 보여 줍니다. 자세한 내용은 [Intune 자동 조종 장치 등록을](/intune/device-enrollment)참조하십시오.

### <a name="configure-windows-update"></a>Windows 업데이트 구성

Windows 10을 최신 상태로 유지하는 것은 가장 중요한 일 중 하나입니다. Windows를 안전한 상태로 유지 하려면 업데이트 링을 배포하여 워크스테이션에 업데이트가 적용되는 속도를 관리합니다. 

이 가이드에서는 새 업데이트 링을 만들고 다음 기본 설정을 변경하는 것이 좋습니다.

Azure Portal에서 다음을 수행합니다.

1. 마이크로 **소프트 인튠** > **소프트웨어 업데이트** > 윈도우로 이동**10 업데이트 링**.
1. 다음을 입력합니다.

   * 이름 - **Azure 관리 워크스테이션 업데이트**
   * 서비스 채널 - **윈도우 인사이더 - 빠른**
   * 품질 업데이트 지연 (일) - **3**
   * 기능 업데이트 지연 기간(일) - **3**
   * 자동 업데이트 동작 - **최종 사용자 제어 없이 자동 설치 및 재부팅**
   * Windows 업데이트를 일시 중지하지 못하도록 차단 - **차단**
   * 근무 시간 이외에 다시 시작하려면 사용자의 승인이 필요 - **필수**
   * 사용자가 다시 시작(참여한 다시 시작)할 수 있도록 허용 - **필수**
   * 자동 다시 시작 후 참여 다시 시작으로 전환 사용자 (일) - **3**
   * 스누즈 참여 다시 시작 알림 (일) - **3**
   * 보류 중인 재시작 기한 설정(일 수) - **3**

1. **만들기**를 선택합니다.
1. 과제 탭에서 **보안 워크스테이션** 그룹을 **추가합니다.**

Windows 업데이트 정책에 대한 자세한 내용은 [정책 CSP - 업데이트](/windows/client-management/mdm/policy-csp-update)를 참조하십시오.

### <a name="windows-defender-atp-intune-integration"></a>윈도우 디펜더 ATP 인튠 통합

윈도우 디펜더 ATP와 마이크로 소프트 인튠은 보안 침해를 방지하기 위해 함께 작동합니다. 또한 위반의 영향을 제한할 수도 있습니다. ATP 기능은 실시간 위협 탐지를 제공할 뿐만 아니라 엔드포인트 디바이스의 광범위한 감사 및 로깅을 가능하게 합니다.

Windows Defender ATP 및 Intune의 통합을 구성하려면 Azure 포털로 이동하십시오.

1. 마이크로 **소프트 인튠** > **장치 준수** > **윈도우 디펜더 ATP를**찾아보십시오.
1. 윈도우 수비수 **ATP를 구성에서**1 단계에서, **윈도우 수비수 보안 센터에서 마이크로 소프트 인튠에 윈도우 수비수 ATP를 연결**선택 .
1. Windows Defender 보안 센터에서 다음을 수행합니다.

   1. **설정** > **고급 기능**선택 .
   1. **Microsoft 인튠 연결의**경우 **를 선택합니다.**
   1. **기본 설정 저장**을 선택합니다.

1. 연결이 설정되면 Intune으로 돌아가서 상단에서 **새로 고침을** 선택합니다.
1. **Windows 디바이스 버전 10.0.15063 이상을 Windows Defender ATP에 연결**을 **켜기**로 설정합니다.
1. **저장**을 선택합니다.

자세한 내용은 [Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)을 참조하세요.

### <a name="finish-workstation-profile-hardening"></a>워크스테이션 프로파일 경화 완료

솔루션의 강화를 성공적으로 완료하려면 적절한 스크립트를 다운로드하여 실행합니다. 원하는 **프로필 수준에**대한 다운로드 링크를 찾기 :

| 프로필 | 다운로드 위치 | 파일 이름 |
| --- | --- | --- |
| 낮은 보안 | 해당 없음 | 해당 없음 |
| 향상된 보안 | https://aka.ms/securedworkstationgit | 향상된 워크스테이션-윈도우10-(1809).ps1 |
| 높은 보안 | https://aka.ms/securedworkstationgit | 하이시큐리티워크스테이션-윈도우10-(1809).ps1 |
| 특수화 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) 보안 기준.ps1 |
| 전문 컴플라이언스* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-윈도우10(1803).ps1 |
| 보안 | https://aka.ms/securedworkstationgit | 보안 워크스테이션-윈도우10-(1809)-보안Baseline.ps1 |

\*특수 규정 준수는 NCSC Windows10 보안 기준선에서 제공하는 특수 구성을 적용하는 스크립트입니다.

스크립트가 성공적으로 실행되면 Intune에서 프로필 및 정책을 업데이트할 수 있습니다. 고급 및 보안 프로필용 스크립트는 정책 및 프로필을 만들지만 **보안 워크스테이션** 장치 그룹에 정책을 할당해야 합니다.

* 다음은 스크립트에서 만든 Intune 장치 구성 프로필을 찾을 수 있는 위치: **Azure 포털** > **Microsoft Intune** > **장치 구성** > **프로필**입니다.
* 다음은 스크립트에서 만든 Intune 장치 준수 정책을 찾을 수 있는 위치: **Azure 포털** > **Microsoft 인튠** > **장치 준수** > **정책**입니다.

스크립트에서 변경한 내용을 검토하려면 프로파일을 내보낼 수 있습니다. 이렇게 하면 [SECCON 문서에](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)설명된 대로 필요할 수 있는 추가 강화를 결정할 수 있습니다.

`DeviceConfiguration_Export.ps1` [DeviceConfiguration GiuHub 리포지토리에서](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) Intune 데이터 내보내기 스크립트를 실행하여 현재 모든 Intune 프로필을 내보냅니다.

## <a name="additional-configurations-and-hardening-to-consider"></a>고려할 추가 구성 및 강화

이 지침에 따라 보안 워크스테이션을 배포했습니다. 그러나 추가 컨트롤도 고려해야 합니다. 예를 들어:

* 대체 브라우저에 대한 액세스 제한
* 아웃바운드 HTTP 허용
* 웹 사이트 선택 차단
* 사용자 지정 PowerShell 스크립트 실행에 대한 사용 권한 설정

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>방화벽 구성 서비스 공급자(CSP)에서 규칙 설정

허용된 끝점과 차단된 엔드포인트에 필요한 경우 인바운드 및 아웃바운드 규칙 관리를 추가로 변경할 수 있습니다. 보안 워크스테이션을 계속 강화하면 모든 인바운드 및 아웃바운드 트래픽을 거부하는 제한을 완화할 수 있습니다. 공통 및 신뢰할 수 있는 웹 사이트를 포함하도록 허용된 아웃바운드 사이트를 추가할 수 있습니다. 자세한 내용은 [방화벽 구성 서비스](/Windows/client-management/mdm/firewall-csp)를 참조하십시오.

제한적인 URL 트래픽 관리에는 다음이 포함됩니다.

* 모든 인바운드 트래픽 거부 - 보안 워크스테이션 프로필 스크립트에 설정합니다.
* Azure 클라우드 셸 및 Azure 암호 재설정을 허용하는 기능을 포함하여 선택한 Azure 및 Microsoft 서비스를 제외한 모든 아웃바운드 거부

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

차단 규칙에 보다 세분성을 제공하려는 경우 [도메인 차단 목록(DBL)을](https://www.spamhaus.org/dbl/)유지하는 Spamhaus 프로젝트를 참조하여 사이트를 차단하기 위해 구현할 고급 규칙 집합으로 사용할 수 있는 좋은 리소스를 참조할 수 있습니다.

### <a name="manage-local-applications"></a>로컬 응용 프로그램 관리

보안 워크스테이션은 생산성 응용 프로그램을 포함하여 로컬 응용 프로그램을 제거할 때 진정으로 강화된 상태로 이동합니다. 여기서 Chrome을 기본 브라우저로 추가하고 Intune을 사용하여 브라우저와 플러그인을 수정하는 사용자의 기능을 제한합니다.

#### <a name="deploy-applications-using-intune"></a>Intune을 사용하여 응용 프로그램 배포

경우에 따라 보안 워크스테이션에서 Google 크롬 브라우저와 같은 응용 프로그램이 필요합니다. 다음 예제에서는 보안 그룹 **보안 워크스테이션의**장치에 Chrome을 설치하는 지침을 제공합니다.

1. [윈도우 64 비트에 대한](https://cloud.google.com/chrome-enterprise/browser/download/)오프라인 설치 프로그램 크롬 번들을 다운로드합니다.
1. 파일을 추출하고 파일의 위치를 기록합니다. `GoogleChromeStandaloneEnterprise64.msi`
1. Azure **포털에서** Microsoft **Intune** > **클라이언트 앱** > **Apps** > **앱 추가로**검색합니다.
1. **앱 유형에서** **업무 시간(이하 '영업시간')을**선택합니다.
1. **앱 패키지 파일에서** `GoogleChromeStandaloneEnterprise64.msi` 추출된 위치에서 파일을 선택하고 **확인을**선택합니다.
1. **앱 정보에서**설명과 게시자를 제공합니다. **확인**을 선택합니다.
1. **추가**를 선택합니다.
1. 과제 탭에서 **과제 유형에서**등록된 장치에 사용할 수 **없음을** **선택합니다.**
1. **포함된 그룹**에서 보안 워크스테이션 그룹을 **추가합니다.**
1. **확인**을 선택하고 **저장**을 선택합니다.

크롬 설정 구성에 대한 자세한 내용은 [Microsoft Intune의 크롬 브라우저 관리를](https://support.google.com/chrome/a/answer/9102677)참조하십시오.

#### <a name="configuring-the-company-portal-for-custom-apps"></a>사용자 지정 앱에 대한 회사 포털 구성

보안 모드에서 응용 프로그램 설치는 Intune 회사 포털로 제한됩니다. 그러나 포털을 설치하려면 Microsoft 스토어에 액세스해야 합니다. 보안 솔루션에서 오프라인 모드를 통해 모든 장치에서 회사 포털을 사용할 수 있도록 할 수 있습니다.

[회사 포털의](/Intune/store-apps-company-portal-app) Intune 관리 복사본을 사용하면 보안 워크스테이션 의 사용자에게 푸시할 수 있는 추가 도구에 대한 온디맨드 액세스를 제공합니다.

배포에 특별한 준비가 필요한 Windows 32비트 앱 또는 기타 앱을 설치해야 할 수 있습니다. 이러한 경우 [Microsoft win32 콘텐츠 준비 도구는](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) 설치를 `.intunewin` 위해 즉시 사용할 수 있는 형식 파일을 제공할 수 있습니다.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>조건부 액세스는 Azure 포털에 액세스하는 보안 워크스테이션 기능만 허용합니다.

Azure AD는 Azure 클라우드 관리 포털에 액세스할 수 있는 사용자 및 액세스 권한을 관리하고 제한할 수 있는 기능을 제공합니다. [조건부 액세스를](../conditional-access/overview.md) 사용하도록 설정하면 보안 워크스테이션만 리소스를 관리하거나 변경할 수 있습니다. 이 기능을 배포하는 동안 [긴급 액세스](../users-groups-roles/directory-emergency-access.md) 기능을 극단적인 경우및 정책을 통해 관리하는 계정에만 사용할 수 있거나 사용해야 하는 경우 고려해야 합니다.

> [!NOTE]
> 사용자 그룹을 만들고 조건부 액세스 정책을 우회할 수 있는 긴급 사용자를 포함해야 합니다. 우리의 예를 들어 우리는 **비상 브레이크 유리라는** 보안 그룹이

1. **Azure 포털** > **Microsoft Intune** > **조건부 액세스 - 정책** > **새 정책으로**이동합니다.
1. 정책에 대한 **이름을** 제공합니다.
1. **사용자 및 그룹** > **선택 사용자 및 그룹 선택 사용자 및 그룹** 
1. **디렉터리 역할** **>** > 글로벌 관리자, 권한 있는 역할 관리자, 권한 있는 인증 관리자, 보안 관리자, 규정 준수 관리자, 조건부 액세스 관리자, 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, Intune 서비스 관리자> 역할을 선택 합니다.
1. 사용자 및 **그룹** **선택** > 선택 > **선택 제외된 사용자** 선택 > 선택> **선택)> 선택합니다.**
1. **모든** 클라우드 앱 선택 > 클라우드 **앱 또는 작업** 선택
1. **조건** 선택 > **장치 플랫폼** 선택 > 선택 **예** > **선택 장치 플랫폼 선택** **Windows**
1. **액세스 컨트롤** 을 선택 > 권한 **부여 액세스** **예** > 선택 **장치를 준수로 표시하려면**선택합니다. 
1. **정책** > 사용**설정 선택**
 
이 정책 집합은 관리자가 Intune 및 WDATP에서 설정한 호환 되는 Windows 장치를 사용 해야 합니다. 

또한 조직은 해당 환경에서 레거시 인증 프로토콜을 차단하는 것도 고려해야 합니다. 레거시 인증 프로토콜 차단에 대한 자세한 내용은 [조건부 액세스를 사용하여 Azure AD에 대한 레거시 인증을 차단하는](../conditional-access/block-legacy-authentication.md)방법에 대한 자세한 내용은 여러 가지 방법으로 수행됩니다.

### <a name="use-powershell-to-create-custom-settings"></a>PowerShell을 사용하여 사용자 지정 설정 만들기

PowerShell을 사용하여 호스트 관리 기능을 확장할 수도 있습니다. 이 예제 스크립트는 호스트의 기본 배경을 설정합니다. Azure 포털 및 프로필을 통해서도 사용할 수 있는 기능입니다. 예제 스크립트는 PowerShell 기능을 설명하는 데만 제공됩니다.

보안 워크스테이션에서 일부 사용자 지정 컨트롤 및 설정을 설정해야 할 수 있습니다. 이 예제에서는 Powershell의 기능을 사용하여 장치를 즉시 사용할 수 있는 안전한 워크스테이션으로 쉽게 식별할 수 있도록 하여 워크스테이션의 배경을 변경합니다.

마이크로소프트 스크립팅 센터에서 [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) 스크립트를 사용하면 Windows가 시작 시 이 [무료, 일반 배경 이미지를](https://i.imgur.com/OAJ28zO.png) 로드할 수 있습니다.

1. 로컬 장치에 스크립트를 다운로드합니다.
1. 고객XXXX 및 배경 이미지의 다운로드 위치를 업데이트합니다. 이 예제에서는 customerXXXX를 배경으로 바꿉니다.
1. **Azure 포털을** > 찾아보기**마이크로 소프트 인튠** > **장치 구성** > **PowerShell 스크립트** > **추가**.
1. 스크립트의 **이름을** 제공하고 스크립트 **위치를**지정합니다.
1. **구성을**선택합니다.
   1. **로그온된 자격 증명을 사용하여 이 스크립트를** **예로**설정합니다.
   1. **확인**을 선택합니다.
1. **만들기**를 선택합니다.
1. **과제** > 선택**그룹 선택**.
   1. 보안 그룹 **보안 워크스테이션을 추가합니다.**
   1. **저장**을 선택합니다.

## <a name="enroll-and-validate-your-first-device"></a>첫 번째 장치 등록 및 유효성 검사

1. 장치를 등록하려면 다음 정보가 필요합니다.
   * **일련 번호** - 장치 섀시에 있습니다.
   * **윈도우 제품 ID** - 윈도우 설정 메뉴에서 **시스템** > **정보에서** 찾을 수 있습니다.
   * [Get-WindowsAutoPilotInfo를](https://aka.ms/Autopilotshell) 실행하여 장치 등록에 필요한 모든 정보가 있는 CSV 해시 파일을 얻을 수 있습니다.
   
     Intune에 가져올 수 있는 CSV 파일로 정보를 출력하려면 실행합니다. `Get-WindowsAutoPilotInfo – outputfile device1.csv`

     > [!NOTE]
     > 스크립트에는 높은 권한이 필요합니다. 원격 서명으로 실행됩니다. 이 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` 명령을 사용하면 스크립트를 올바르게 실행할 수 있습니다.

   * Windows 10 버전 1809 이상의 장치에 로그인하여 이 정보를 수집할 수 있습니다. 하드웨어 리셀러도 이 정보를 제공할 수 있습니다.
1. Azure **포털에서**Microsoft **Intune** > **장치 등록** > **Windows 등록** > **장치 - Windows 자동 조종 장치 관리로**이동합니다.
1. **가져오기를** 선택하고 CSV 파일을 선택합니다.
1. 보안 워크스테이션 보안 그룹에 장치를 **추가합니다.**
1. Windows10 장치에서 구성하려는 경우 Windows **설정** > **업데이트 & 보안** > **복구로**이동합니다.
   1. 이 PC **재설정에서 시작하기를 선택합니다.** **Get started**
   1. 프롬프트에 따라 구성된 프로필 및 규정 준수 정책으로 장치를 재설정하고 다시 구성합니다.

장치를 구성한 후 검토를 완료하고 구성을 확인합니다. 배포를 계속하기 전에 첫 번째 장치가 올바르게 구성되어 있는지 확인합니다.

## <a name="assign-devices"></a>장치 할당

장치와 사용자를 할당하려면 [선택한 프로필을](/intune/device-profile-assign) 보안 그룹에 매핑해야 합니다. 서비스에 대한 사용 권한이 필요한 모든 새 사용자도 보안 그룹에 추가되어야 합니다.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Sentinel 및 Windows Defender ATP를 사용하여 보안 인시던트를 모니터링하고 대응

보안 워크스테이션 배포를 모니터링하는 것은 [Sentinel]을 사용하도록 설정하고 [위협 및 취약성 관리를](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) 활용하여 수행할 수 있습니다.

**Azure Sentinel을** 사용하여 다음을 수행합니다. 

* 사용자 및 장치 모니터링을 위해 Intune, Azure 포털 및 Azure AD에서 데이터 수집
* 사용자 및 장치 구성 의심스러운 활동 조사 도움말
* 그리고 WDATP를 사용하여 보안 조사를 탐색 할 수있는 능력을 구동

Sentinel 모니터링을 사용하려면 Azure AD와 같은 데이터 원본에 대한 커넥터를 설정해야 합니다.

1. Azure **포털에서** **추가** 선택 > **Azure Sentinel(미리 보기)로** 이동합니다.
1. Azure **Sentinel에 추가할 작업 영역 선택에서** **새 작업 영역 만들기 선택**
1. 다음을 입력합니다.
   * **로그 분석 작업 영역** - '보안 워크스테이션 모니터링'
   * **구독** - 활성 구독 선택
   * **리소스 그룹** - ** 만들기 새** > 보안 워크스테이션 RG > **확인**
   * **위치** - 배포에 가장 적합한 위치 선택
   * **가격 계층** - GB당 선택 **(2018)**
1. **확인을 선택합니다.**

다음으로 사용 가능한 보안 워크스테이션 데이터 원본을 모니터링에 연결합니다.

1. Azure **포털에서**Azure **Sentinel 작업 영역** > **보안 워크스테이션 모니터링** 작업 영역 선택으로 이동합니다.
1. **데이터 커넥터** 선택
1. 필수 구성 > 검토한 후 **azure active Directory** > 열기 커넥터 페이지를 > 선택합니다. 구성으로 진행하여 Azure AD 로그인 로그와 Azure AD 감사 로그 모두에 대해 **연결을** 선택합니다.
1. Azure **활동** > 커넥터 열기 페이지 > 필수 구성 검토한 후 선택합니다. Azure 활동 로그 구성으로 진행 > 구독 > **선택 연결** 선택

데이터가 Sentinel에 의해 수집되면 **Azure 포털을**선택하여 활동을 **Azure Sentinel Overview** 관찰 할 수 있습니다. 

우리는 **윈도우 수비수 ATP (WDATP)를** 사용합니다 :

* 취약점 및 잘못된 구성을 지속적으로 관찰하고 모니터링
* WDATP를 활용하여 야생의 동적 위협우선 순위 지정
* 엔드포인트 탐지 및 응답(EDR) 경고로 취약점과의 상관 관계를 유도합니다.
* 대시보드를 사용하여 조사 중 컴퓨터 수준 취약점 식별
* Intune에 대한 수정 을 푸시

수비수 [ATP 대시보드를](https://securitycenter.windows.com/machines)구성합니다. [위협 & 취약점 관리 대시보드 개요에서](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights)지침을 사용 합니다.

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>마이크로 소프트 모니터링 에이전트 (MMA)를 사용하여 응용 프로그램 활동을 모니터링
특수 워크스테이션에서 시작하여 앱 사물함은 워크스테이션에서 응용 프로그램 활동을 모니터링할 수 있도록 설정되어 있습니다. 모니터링이 Log Analytics 작업 영역에 통합되려면 MMA 에이전트와 구성을 따라야 합니다. 

> [!NOTE]
> 특수 워크스테이션 프로필에는 AppLocker 모니터링 정책이 포함되어 있습니다. 클라이언트에서 응용 프로그램 활동을 모니터링하려면 정책 배포가 필요합니다.

인튠 파워쉘 스크립트를 통해 MMA 에이전트 배포

1. [설정 스크립트를 로컬 장치에 다운로드합니다.](https://aka.ms/securedworkstationgit)
1. 매개 변수, **$WorkSpaceID** 및 **$WorkSpaceKey** 업데이트
1. **Azure 포털을** > 찾아보기**마이크로 소프트 인튠** > **장치 구성** > **PowerShell 스크립트** > **추가**.
1. 스크립트의 **이름을** 제공하고 스크립트 **위치를**지정합니다.
1. **구성을**선택합니다.
   1. **로그온된 자격 증명을 사용하여 이 스크립트를** **예로**설정합니다.
   1. **확인**을 선택합니다.
1. **만들기**를 선택합니다.
1. **과제** > 선택**그룹 선택**.
   1. 보안 그룹 **보안 워크스테이션을 추가합니다.**
   1. **저장**을 선택합니다.

다음으로 새 로그를 받으려면 로그 애널리틱스를 설정해야 합니다.
1. Azure **포털에서** **로그 분석 작업 영역** > 선택 - '보안 워크스테이션 모니터링' 으로 이동합니다.
1. **고급 설정** > **데이터** > **Windows 이벤트 로그** 선택
1. **다음 이벤트 로그에서 이벤트 수집** 
1. 다음을 입력합니다.
   * ' 마이크로소프트-윈도-애플 록커/EXE 와 DLL' > **선택** 정보
   * ' 마이크로소프트-윈도-애플 록커/MSI와 스크립트' > **선택** 정보
   * ' 마이크로소프트-윈도-애플 록커/패키지된 애플 리 케이 션 배포' > **선택** 정보
   * ' 마이크로소프트-윈도-애플 록커/패키지 된 애플 리 케이 션 실행' > **선택** 정보
1. **저장** 선택

응용 프로그램 로깅은 선택한 로그 분석 작업 영역에서 사용할 수 있습니다.

## <a name="monitoring"></a>모니터링

* [Azure Sentinel을 사용하여 위협 을 탐지하는](/azure/sentinel/tutorial-detect-threats) 방법 알아보기
* [Azure 센티넬로 인시던트 조사](/azure/sentinel/tutorial-investigate-cases)
* [Azure Sentinel에서 자동화된 위협 응답 설정](/azure/sentinel/tutorial-respond-threats-playbook)
* [노출 점수](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score) 검토 방법 이해하기
* [보안 권장 사항](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation) 검토
* 보안 [문제 해결](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) 관리
* [엔드포인트 감지 및 대응](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response) 관리
* [Intune 프로파일 모니터링을](/intune/device-profile-monitor)통해 프로파일을 모니터링합니다.

## <a name="next-steps"></a>다음 단계

* [마이크로소프트 인튠에](/intune/index)대해 자세히 알아보십시오.
* [Azure AD](../index.yml)이해 .
* Microsoft [Defender 고급 위협 보호](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 작업
* [Azure 센티넬](/azure/sentinel/) 검색
