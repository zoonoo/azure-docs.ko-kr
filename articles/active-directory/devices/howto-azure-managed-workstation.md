---
title: Azure 관리 되는 워크스테이션-Azure Active Directory를 배포 합니다.
description: 위험 잘못 된 구성 또는 손상으로 인해 보안 위험을 줄이기 위해 안전 하 고 Azure 관리 워크스테이션을 배포 하는 방법에 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550480"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>안전 하 고 Azure 관리 워크스테이션 배포

이제 [안전한 워크스테이션을 이해](concept-azure-managed-workstation.md), 배포 프로세스를 시작 하려면 차례입니다. 이 설명서와 함께 사용 하 여 정의 된 프로필 더욱 안전 하 게 시작 하는 워크스테이션을 만듭니다.

![보안 워크스테이션에 대 한 배포](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

솔루션을 배포 하려면 먼저 프로필을 선택 해야 합니다. 배포에서 동시에 여러 프로필을 사용 하 고 태그 또는 그룹을 사용 하 여 할당할 수 있습니다.
> [!NOTE]
> 사용자 요구 사항에 따라 필요에 따라 프로필 중 하나를 적용 합니다. Intune에서 할당 하 여 다른 프로필을 이동할 수 있습니다.

| 프로필 | 낮음 | 향상된 | 높음 | 특수화 | 보안 | 격리 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD의 사용자 | 예 | 예 | 예 | 예 | 예 | 예 |
| Intune 관리 | 예 | 예 | 예 | 예 | 예 | 예 |
| Azure AD 등록 장치 | 예 |  |  |  |  | |   |
| Azure AD 가입 장치 |   | 예 | 예 | 예 | 예 | 예 |
| Intune 보안 기준을 적용 |   | 예 <br> (확장) | 예 <br> (HighSecurity) | 예 <br> (NCSC) | 예 <br> (보안 됨) |  해당 없음 |
| 하드웨어 보안 Windows 10 표준을 충족합니다 |   | 예 | 예 | 예 | 예 | 예 |
| Microsoft Defender ATP 사용 |   | 예  | 예 | 예 | 예 | 예 |
| 관리 권한 제거 |   |   | 예  | 예 | 예 | 예 |
| Microsoft Autopilot을 사용 하 여 배포 |   |   | 예  | 예 | 예 | 예 |
| Intune을 통해 설치 된 앱 |   |   |   | 예 | 예 |예 |
| 승인 된 목록으로 제한 하는 Url |   |   |   | 예 | 예 |예 |
| 인터넷 (인바운드/아웃 바운드) 차단 |   |   |   |  |  |예 |

## <a name="license-requirements"></a>라이선스 요구 사항

이 가이드에서 설명 된 개념 Microsoft 365 Enterprise E5 또는 SKU를 해당 하는 것이 있다고 가정 합니다. 이 가이드에서 권장 하는 일부 더 낮은 Sku를 사용 하 여 구현할 수 있습니다. 자세한 내용은 [Microsoft 365 Enterprise 라이선스](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)합니다.

라이선스 프로 비전을 자동화 하는 것이 좋습니다 [그룹 기반 라이선스](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) 사용자에 대 한 합니다.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 구성

Azure Active Directory (Azure AD)는 사용자, 그룹 및 관리자 워크스테이션에 대 한 장치를 관리 합니다. Id 서비스와 기능을 사용 하도록 설정 해야 하는 [관리자 계정](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)합니다.

보안된 워크스테이션 관리자 계정을 만들 때 사용자의 현재 워크스테이션에 계정을 노출 합니다. 이 초기 구성과 모든 전역 구성 작업을 수행 하는 알려진된 안전 장치를 사용 해야 합니다. 첫 번째 환경에 대 한 공격 가능성을 줄이기 위해 고려 다음 합니다 [맬웨어 감염을 방지 하는 지침](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)합니다.

또한 multi-factor authentication을 관리자에 대해 적어도 요구할 해야 있습니다. 참조 [클라우드 기반 MFA 배포](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) 구현 지침에 대 한 합니다.

### <a name="azure-ad-users-and-groups"></a>Azure AD 사용자 및 그룹

1. Azure portal에서 **Azure Active Directory** > **사용자가** > **새 사용자**합니다.
1. 단계를 수행 하 여 장치 관리자를 만들 합니다 [만들기 사용자 자습서](https://docs.microsoft.com/Intune/quickstart-create-user)합니다.
1. 다음을 입력합니다.
   * **이름** -보안 워크스테이션 관리자
   * **사용자 이름** - `secure-ws-admin@identityitpro.com`
   * **디렉터리 역할** - **제한 된 관리자** 선택 합니다 **Intune 관리자** 역할입니다.
1. **만들기**를 선택합니다.

다음으로 두 개의 그룹을 만듭니다: 워크스테이션 장치와 워크스테이션 사용자입니다.

Azure portal에서 **Azure Active Directory** > **그룹** > **새 그룹**합니다.

1. 워크스테이션 사용자 그룹에 대해 구성 하려는 [그룹 기반 라이선스](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) 라이선스 사용자를 프로 비전을 자동화할 수 있습니다.
1. 워크스테이션 사용자 그룹에 대해 다음을 입력 합니다.
   * **그룹 종류** -보안
   * **그룹 이름** -워크스테이션 사용자 보호
   * **멤버 자격 유형** -할당
1. 보안 워크스테이션 관리자 사용자를 추가 합니다. `secure-ws-admin@identityitpro.com`
1. 보안 워크스테이션 관리 하는 다른 사용자를 추가할 수 있습니다.
1. **만들기**를 선택합니다.

1. 워크스테이션 장치 그룹에 대해 다음을 입력 합니다.
   * **그룹 종류** -보안
   * **그룹 이름** -보안 워크스테이션
   * **멤버 자격 유형** -할당
1. **만들기**를 선택합니다.

### <a name="azure-ad-device-configuration"></a>Azure AD 장치 구성

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Azure AD에 장치를 조인할 수 있는 사용자 지정

Active Directory에서 관리 보안 그룹에 도메인에 장치를 조인할 수 있도록 설정 하 여 장치를 구성 합니다. Azure portal에서이 설정을 구성 하려면

1. 로 이동 **Azure Active Directory** > **장치** > **장치 설정**합니다.
1. 선택 **선택한** 아래에서 **사용자가 Azure AD에 장치를 조인할 수 있습니다**, "보안 워크스테이션 사용자" 그룹을 선택 하 고 있습니다.

#### <a name="removal-of-local-admin-rights"></a>로컬 관리 권한 제거

이 메서드는 VIP, DevOps 및 보안 수준 워크스테이션의는 사용자가 자신의 컴퓨터에 없는 관리자 권한이 필요 합니다. Azure portal에서이 설정을 구성 하려면

1. 로 이동 **Azure Active Directory** > **장치** > **장치 설정**합니다.
1. 선택 **None** 아래에서 **조인 장치의 추가 로컬 관리자 Azure AD에서**합니다.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>장치에 연결 하려면 multi-factor authentication 필요

Azure AD에 장치를 조인 하는 과정을 더 강화:

1. 로 이동 **Azure Active Directory** > **장치** > **장치 설정**합니다.
1. 선택 **Yes** 아래에서 **장치에 연결 하려면 Multi-factor Auth 필요**합니다.
1. **저장**을 선택합니다.

#### <a name="configure-mdm"></a>MDM 구성

Azure 포털에서 다음을 수행합니다.

1. 이동할 **Azure Active Directory** > **이동성 (MDM 및 MAM)**  > **Microsoft Intune**합니다.
1. 변경 된 **MDM 사용자 범위** 설정을 **모든**합니다.
1. **저장**을 선택합니다.

이러한 단계를 사용 하면 Intune 사용 하 여 모든 장치를 관리할 수 있습니다. 자세한 내용은 참조 하세요. [Intune 빠른 시작: Windows 10 장치에 대 한 자동 등록 설정](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)합니다. 이후 단계에서 Intune 구성 및 규정 준수 정책을 만듭니다.

#### <a name="azure-ad-conditional-access"></a>Azure AD 조건부 액세스

Azure AD 조건부 액세스는 규격 장치에 권한 있는 관리 작업을 제한 하는 데 도움이 됩니다. 멤버를 미리 정의 된 합니다 **워크스테이션 사용자 보안** 그룹 클라우드 응용 프로그램에 로그인 할 때 multi-factor authentication을 수행 해야 합니다. 정책에서 응급 액세스 계정을 제외 하는 것이 좋습니다. 자세한 내용은 [Azure AD에서 응급 액세스 계정을 관리할](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)합니다.

Azure portal에서 조건부 액세스를 구성 합니다.

1. 로 이동 **Azure Active Directory** > **조건부 액세스** > **새 정책을**합니다.
1. 다음을 입력합니다.
   * **이름** -안전한 장치 필수 정책
   * 할당
     * **사용자 및 그룹**
       * -포함 **사용자 및 그룹** -를 선택 합니다 **워크스테이션 사용자 보안** 이전에 만든 그룹.
       * 제외할 **사용자 및 그룹** -조직의 응급 액세스 계정을 선택 합니다.
     * **클라우드 앱** -포함 **모든 클라우드 앱**합니다.
    * 액세스 제어
      * **권한 부여** 선택- **액세스 권한을 부여** 라디오 단추입니다.
        * **Multi-factor authentication 요구**합니다.
        * **장치가 규격으로 표시할**합니다.
        * -여러 컨트롤용 **선택된 된 컨트롤이 모두 필요**합니다.
    * 정책-를 사용 하도록 설정 **에서**합니다.

사용자는 회사 리소스에 액세스 하지 국가 차단 하는 정책을 만들 수가 있습니다. IP 위치 기반 조건부 액세스 정책에 대 한 자세한 내용은 참조 하십시오 [Azure Active Directory 조건부 액세스의 위치 조건](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)합니다.

## <a name="intune-configuration"></a>Intune 구성

### <a name="configure-enrollment-status"></a>등록 상태를 구성 합니다.

보안 워크스테이션 정리 신뢰할 수 있는 장치 인지 확인 하는 것이 반드시 합니다. 새 장치를 구입 하는 경우 수 것 이라고 주장 하기 팩터리로 증명이 [S 모드에서 Windows 10 Pro](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), 공급망 관리 하는 동안 보안 문제에 대 한 노출을 제한 하는 합니다. 공급자에 게 장치를 받은 후에 S 모드에서 변경 하려면 Autopilot을 사용할 수 있습니다. 다음 지침을 적용 변환 프로세스에 세부 정보를 제공 합니다.

Intune을 사용 하기 전에 장치가 완전히 구성 되어 있는지을 보장 하려면 수단을 제공 **모든 앱과 프로필 설치 될 때까지 장치 사용을 차단**합니다.

**Azure Portal**에서
1. 로 이동 **Microsoft Intune** > **장치 등록** > **Windows 등록** > **등록 상태 페이지** > **기본값** > **설정**합니다.
1. 설정 **앱 프로필 설치 진행률 표시** 하 **예**합니다.
1. 설정할 **모든 앱과 프로필 설치 될 때까지 장치 사용을 차단** 하 **예**합니다.

### <a name="create-an-autopilot-deployment-profile"></a>Autopilot 배포 프로필 만들기

장치 그룹을 만든 후 Autopilot 장치를 구성 하려면 배포 프로필을 만들어야 합니다.

Intune Azure portal에서:

1. 선택 **장치 등록** > **Windows 등록** > **배포 프로필** > **프로필 만들기** .
1. 다음을 입력합니다.
   * 이름- **워크스테이션 배포 프로필 보안**합니다.
   * 설명- **보안 워크스테이션 배포**합니다.
   * 설정 **Autopilot 모든 대상된 장치 변환할** 하 **예**합니다. 이렇게이 설정 하면 Autopilot 배포 서비스를 사용 하 여 목록의 모든 장치를 등록 합니다. 등록이 처리에 48 시간을 허용 합니다.
1. **다음**을 선택합니다.
   * 에 대 한 **배포 모드**, 선택 **(미리 보기)를 배포 하는 자체**합니다. 이 프로필이 있는 장치가 장치 등록 하 고 사용자와 연결 됩니다. 장치를 등록 하려면 사용자 자격 증명이 필요 합니다.
   * **으로 Azure AD에 조인** 상자에 표시 됩니다 **Azure AD 가입** 회색으로 표시 하 고 있습니다.
   * 선택 (지역), 사용자 계정 유형 프로그램 Langugage **표준**합니다. 
1. **다음**을 선택합니다.
   * 하나를 미리 구성한 경우 범위 태그를 선택 합니다.
1. **다음**을 선택합니다.
1. 선택할 **할당** > **할당할** > **선택 된 그룹**합니다. **포함할 그룹 선택**, 선택 **워크스테이션 사용자 보안**합니다.
1. **다음**을 선택합니다.
1. **만들기**를 선택하여 프로필을 만듭니다. Autopilot 배포 프로필을 장치에 할당 출시 되었습니다.

### <a name="configure-windows-update"></a>Windows 업데이트 구성

Windows 10을 최신 상태로 유지 하 여 수행할 수 있는 가장 중요 한 작업 중 하나입니다. Windows 보안 상태에서를 유지 하려면 속도 관리 하는 업데이트 링 배포 업데이트 워크스테이션에 적용 되는 합니다. 

이 지침 새 업데이트 링을 만든 다음 기본 설정을 변경 하는 것이 좋습니다.

Azure Portal에서 다음을 수행합니다.

1. 로 이동 **Microsoft Intune** > **소프트웨어 업데이트** > **Windows 10 업데이트 링**합니다.
1. 다음을 입력합니다.
   * 이름- **Azure 관리 워크스테이션 업데이트**
   * 서비스 채널- **Windows 참가자-초기**
   * 품질 업데이트 지연 기간 (일)- **3**
   * 기능 업데이트 지연 기간 (일)- **3**
   * 자동 업데이트 동작- **자동 설치 및 최종 사용자 제어 없이 다시 부팅**
   * Windows 업데이트를 일시 중지-에서 사용자를 차단 **블록**
   * 작업 시간-외부에서 다시 시작 하려면 사용자의 승인을 받아야 **필요**
   * 사용자를 (다시 참여)-다시 시작 허용 **필요**
   * 사용자 참여를 다시 시작 하는 자동 다시 시작 후 전환 (일)- **3**
   * 참여를 다시 시작 알림 다시 알림 기간 (일)- **3**
   * 최종 기한에 대 한 보류 중인 설정 (일)-다시 시작 **3**

1. **만들기**를 선택합니다.
1. 에 **할당** 탭에서 추가 된 **워크스테이션 보안** 그룹.

Windows 업데이트 정책에 대 한 자세한 내용은 참조 하세요. [CSP 정책 업데이트](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)합니다.

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 통합

Windows Defender ATP와 Microsoft Intune 보안 위반을 방지 하려면 함께 작동 합니다. 위반의 영향을 제한할 수도 있습니다. ATP 기능 실시간 위협 요소 탐지를 제공할 뿐만 아니라 광범위 한 감사 및 끝점 장치 로깅을 사용 하도록 설정 합니다.

Windows Defender ATP와 Intune의 통합을 구성 하려면 Azure portal로 이동 합니다.

1. 이동할 **Microsoft Intune** > **장치 준수** > **Windows Defender ATP**합니다.
1. 아래에서 1 단계에서 **Windows Defender ATP 구성**를 선택 **Windows Defender 보안 센터에서 Microsoft Intune에 Windows Defender ATP 연결**합니다.
1. Windows Defender 보안 센터:
   1. 선택 **설정을** > **고급 기능**합니다.
   1. 에 대 한 **Microsoft Intune 연결은**, 선택 **에서**합니다.
   1. 선택 **기본 설정 저장**합니다.
1. 연결이 설정 되 면 Intune 선택한 돌아갑니다 **새로 고침** 맨 위에 있는 합니다.
1. 설정 **연결 Windows 10.0.15063 장치 버전 이상 Windows Defender atp** 하 **에서**합니다.
1. **저장**을 선택합니다.

자세한 내용은 [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)합니다.

### <a name="finish-workstation-profile-hardening"></a>강화 된 워크스테이션 프로필 마침

솔루션의 보안 강화를 성공적으로 완료 하려면 다운로드 하 고 적절 한 스크립트를 실행 합니다. 원하는 항목에 대 한 다운로드 링크를 찾을 **프로필 수준**:

| 프로필 | 다운로드 위치 | Filename |
| --- | --- | --- |
| 낮은 보안 | N/A |  N/A |
| 향상된 보안 | https://aka.ms/securedworkstationgit | 고급--Windows10-워크스테이션 (1809).ps1 |
| 높은 수준의 보안  | https://aka.ms/securedworkstationgit | -Windows10-HighSecurityWorkstation (1809).ps1 |
| 특수화 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline.ps1 |
| 특수화 된 규정 준수 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| 보안 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* 특수화 된 호환성은 NCSC Windows10 SecurityBaseline에서 제공 하는 특수 한 구성 적용 하는 스크립트.

스크립트를 성공적으로 실행 한 후 프로필을 Intune에서 정책을 업데이트를 수행할 수 있습니다. 고급 및 보안 프로필에 대 한 스크립트 정책 및 프로필을 만들지만 정책을 할당 해야 하 **워크스테이션 보안** 그룹입니다.

* 스크립트에서 만든 Intune 장치 구성 프로필을 찾을 수 있는 다음과 같습니다. **Azure portal** > **Microsoft Intune** > **장치 구성을** > **프로필**합니다.
* 찾을 수 있는 Intune 장치 스크립트에서 만든 준수 정책은 다음과 같습니다. **Azure portal** > **Microsoft Intune** > **장치 준수** > **정책**합니다.

스크립트에서 변경한 내용을 검토 하려면 프로필을 내보낼 수 있습니다. 이러한 방식으로 SECCON 설명서에 설명 된 대로 필요할 수 있는 추가 보안 강화를 확인할 수 있습니다.

Intune 데이터 내보내기 스크립트 실행 `DeviceConfiguration_Export.ps1` 에서 합니다 [DeviceConfiguration GiuHub 리포지토리](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) 현재 모든 Intune 프로필을 내보내려면 합니다.

## <a name="additional-configurations-and-hardening-to-consider"></a>추가 구성 및 고려해 야 할 보안 강화

이 지침을 수행 하 여 안전한 워크스테이션을 배포 했습니다. 그러나 또한 고려해 야 컨트롤을 추가 합니다. 예를 들면 다음과 같습니다.

* 다른 브라우저에 대 한 액세스를 제한 합니다.
* 아웃 바운드 HTTP 허용
* 블록 선택 웹 사이트
* 사용자 지정 PowerShell 스크립트를 실행 하는 것에 대 한 사용 권한 설정

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>방화벽 구성 서비스 공급자 (CSP)에 규칙을 설정 합니다.

허용 및 차단 된 끝점에 대 한 필요에 따라 관리 인바운드 및 아웃 바운드 규칙에 추가 변경 가능 합니다. 보안 워크스테이션 강화 하기 위해 계속 함에 따라 모든 인바운드 및 아웃 바운드 트래픽을 거부 하는 제한을 완화 수 있습니다. 일반적이 고 신뢰할 수 있는 웹 사이트를 포함 하도록 허용 된 아웃 바운드 사이트를 추가할 수 있습니다. 자세한 내용은 [구성 서비스 방화벽](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)합니다.

제한 기본 권장 사항은 다음과 같습니다.

* 모든 인바운드 거부
* 모든 아웃 바운드 거부

Spamhaus 프로젝트 유지 관리 [는 도메인 블록 목록 (두 개)](https://www.spamhaus.org/dbl/): 사이트를 차단 하는 것에 대 한 시작 점으로 사용 하는 좋은 리소스입니다.

### <a name="manage-local-applications"></a>로컬 응용 프로그램 관리

보안 워크스테이션 생산성 응용 프로그램을 포함 하 여 로컬 응용 프로그램을 제거할 때 실제로 확정 된 상태로 이동 합니다. 여기에서 기본 브라우저로 Chrome를 추가한 및 Intune을 사용 하 여 사용자의 브라우저 및 해당 플러그 인을 수정할 수를 제한 합니다.

#### <a name="deploy-applications-using-intune"></a>Intune을 사용 하 여 응용 프로그램 배포

일부 경우에는 Google Chrome 브라우저와 같은 응용 프로그램 보안된 워크스테이션에 필요 합니다. 다음 예에서는 보안 그룹의 장치에 Chrome을 설치 하는 지침이 **워크스테이션 보안**합니다.

1. 오프 라인 설치 관리자를 다운로드 [Chrome 번들이 Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/)합니다.
1. 파일을 추출한 위치를 기록 합니다 `GoogleChromeStandaloneEnterprise64.msi` 파일입니다.
1. 에 **Azure portal** 이동할 **Microsoft Intune** > **클라이언트 앱** > **앱**  >  **추가**합니다.
1. 아래 **앱 유형**, 선택 **-업무**합니다.
1. 아래 **앱 패키지 파일**를 선택 합니다 `GoogleChromeStandaloneEnterprise64.msi` 압축 푼된 위치에서 파일을 선택 **확인**합니다.
1. 아래 **앱 정보**에 설명과 게시자를 제공 합니다. **확인**을 선택합니다.
1. **추가**를 선택합니다.
1. 에 **할당** 탭을 선택 **등록 된 장치에서 사용 가능** 아래의 **할당 유형**합니다.
1. 아래 **포함 된 그룹**를 추가 합니다 **워크스테이션 보안** 그룹.
1. **확인**을 선택하고 **저장**을 선택합니다.

Chrome 설정 구성에 대 한 자세한 지침을 참조 하세요 [Microsoft Intune을 사용 하 여 Chrome 브라우저 관리](https://support.google.com/chrome/a/answer/9102677)합니다.

#### <a name="configuring-the-company-portal-for-custom-apps"></a>사용자 지정 앱에 대 한 회사 포털 구성

보안된 모드에서 응용 프로그램 설치를 Intune 회사 포털에 제한 됩니다. 그러나 Microsoft Store 대 한 액세스를 필요 포털을 설치 합니다. 보안된 솔루션에 만들 수 있습니다 회사 포털을 오프 라인 모드를 통해 모든 장치에 사용할 수 있습니다.

Intune에서 관리 하는 복사본을 합니다 [회사 포털](https://docs.microsoft.com/Intune/store-apps-company-portal-app) 보안된 워크스테이션의 사용자에 게 푸시할 수 있는 추가 도구에 주문형 액세스를 제공 합니다.

Windows 32 비트 응용 프로그램 또는 해당 배포 특수 준비 해야 하는 다른 앱을 설치 해야 합니다. 이러한 경우에는 [Microsoft win32 콘텐츠 준비 도구](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) 준비-사용을 제공할 수 있습니다 `.intunewin` 설치에 대 한 서식 파일입니다.

### <a name="use-powershell-to-create-custom-settings"></a>PowerShell을 사용 하 여 사용자 지정 설정을 만들려면

PowerShell을 사용 하 여 호스트 관리 기능을 확장할 수도 있습니다. 이 예제 스크립트는 호스트의 기본 배경을 설정합니다. 또한 Azure portal 및 프로필을 통해 사용할 수 있는 기능을 것입니다. 예제 스크립트는 PowerShell 기능을 설명 하기 위해 사용 됩니다.

보안 워크스테이션에서 일부 사용자 지정 컨트롤 및 설정을 설정 해야 합니다. 이 예제에서는 Powershell의 기능을 사용 하 여 쉽게 즉시-사용, 보안 워크스테이션으로 장치를 식별 하 여 워크스테이션의 배경을 변경 합니다.

합니다 [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) 스크립트 Microsoft 스크립트 센터에서이 로드 하는 Windows를 사용 하 [무료, 일반 배경 이미지](https://i.imgur.com/OAJ28zO.png) 에서 시작 합니다.

1. 로컬 장치에 스크립트를 다운로드 합니다.
1. customerXXXX 및 배경 이미지의 다운로드 위치를 업데이트 합니다. 예에서 배경에 customerXXXX를 바꿉니다.  
1. 로 이동 합니다 **Azure portal** > **Microsoft Intune** > **장치 구성을** > **PowerShell 스크립트** > **추가**합니다.
1. 제공을 **이름** 스크립트에 대 한 지정 된 **스크립트 위치**.
1. **구성**을 선택합니다.
   1. 설정할 **로그인 자격 증명에 사용 하 여이 스크립트를 실행** 하 **예**합니다.
   1. **확인**을 선택합니다.
1. **만들기**를 선택합니다.
1. 선택 **할당이** > **그룹 선택**합니다.
   1. 보안 그룹 추가 **워크스테이션 보안**합니다.
   1. **저장**을 선택합니다.

## <a name="enroll-and-validate-your-first-device"></a>등록 하 고 첫 번째 장치를 확인 합니다.

1. 장치를 등록 하려면 다음 정보가 필요 합니다.
   * **일련 번호** -장치 섀시에 나와 있습니다.
   * **Windows 제품 ID** -에서 찾은 **System** > **에 대 한** Windows 설정 메뉴에서.
   * 실행할 수 있습니다 [Get WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) 장치 등록에 대 한 모든 필요한 정보를 사용 하 여 CSV 해시 파일을 가져오도록 합니다.
   
     실행 `Get-WindowsAutoPilotInfo – outputfile device1.csv` 에서 Intune로 가져올 수 있는 CSV 파일로 정보를 출력 합니다.

     > [!NOTE]
     > 스크립트에는 상승 된 권한이 필요합니다. 원격으로 실행 되도록 합니다. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` 명령 스크립트가 제대로 실행 되도록 허용 합니다.

   * Windows 10 버전 1809 또는 더 높은 장치에 로그인 하 여이 정보를 수집할 수 있습니다. 하드웨어 재판매인이이 정보를 제공할 수도 있습니다.
1. 에 **Azure portal**로 이동 하세요 **Microsoft Intune** > **장치 등록** > **Windows 등록**  >  **장치 관리 Windows Autopilot 장치**합니다.
1. 선택 **가져오기** CSV 파일을 선택 합니다.
1. 장치를 추가 합니다 **워크스테이션 보안** 보안 그룹입니다.
1. 구성 하려는 Windows 10 장치에서로 이동 **Windows 설정** > **업데이트 및 보안** > **복구**합니다.
   1. 선택할 **시작** 아래에서 **이 PC를 다시 설정**합니다.
   1. 지시에 따라 다시 설정 하 고 장치 구성 프로필 및 규정 준수 정책을 사용 하 여 다시 구성 합니다.

장치를 구성한 검토를 완료 한 후 구성을 확인 합니다. 배포를 계속 하기 전에 첫 번째 장치가 올바르게 구성 되어 있는지 확인 합니다.

## <a name="assign-and-monitor"></a>할당 및 모니터

장치 및 사용자에 할당 하려면 매핑할 필요 합니다 [선택한 프로필](https://docs.microsoft.com/intune/device-profile-assign) 보안 그룹에. 서비스에 대 한 권한이 필요로 하는 모든 새 사용자의 보안 그룹에 추가 되어야 합니다.

사용 하 여 프로필을 모니터링할 수 있습니다 [Intune 프로필 모니터링](https://docs.microsoft.com/intune/device-profile-monitor)합니다.

## <a name="next-steps"></a>다음 단계

* 에 대해 자세히 알아보세요 [Microsoft Intune](https://docs.microsoft.com/intune/index)합니다.
* 이해 [Azure AD](https://docs.microsoft.com/azure/active-directory/index)합니다.
