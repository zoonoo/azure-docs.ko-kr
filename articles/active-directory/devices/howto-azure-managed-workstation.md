---
title: 배포할 Azure 워크스테이션-Azure Active Directory 관리
description: 위반으로 인해 잘못 된 구성 또는 손상의 위험을 줄이려면 Azure 관리 되는 보안 워크스테이션을 배포 하는 방법 알아보기
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
ms.openlocfilehash: 242926c0821e4951d2a2bd2f858f63691baf1017
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307229"
---
# <a name="deploy-a-secure-workstation"></a>보안 워크스테이션 배포

이해 했으므로 [워크스테이션 액세스를 보호 하는 것이 중요 한 이유?](concept-azure-managed-workstation.md) 를 사용할 수 있는 도구를 사용 하 여 배포 프로세스를 시작 합니다. 이 설명서는 더욱 안전 하 게 시작 하는 워크스테이션을 만들려면 정의 된 프로필을 사용 합니다.

![보안 워크스테이션에 대 한 배포](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

솔루션을 배포 하기 전에 사용 하는 프로필을 선택 해야 합니다. 선택한 프로필을 적용 하 고 요구 사항에 따라 Intune에서 프로필을 할당 하 여 다른 서버로 이동할 수 있는지 확인 하는 것이 반드시 합니다. 여러 프로필 배포의 경우 동시에 사용 하 고 태그의 또는 그룹 할당을 사용 하 여 할당할 수 있습니다.

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
| 인터넷 (인바운드/아웃 바운드 차단) |   |   |   |  |  |예 |

## <a name="license-requirements"></a>라이선스 요구 사항

이 가이드에서 설명 된 개념은 Microsoft 365 Enterprise E5 또는 해당 SKU를 가정 합니다. 이 가이드에서 권장 하는 일부 더 낮은 Sku를 사용 하 여 구현할 수 있습니다. 추가 정보를 확인할 수 있습니다 [Microsoft 365 Enterprise 라이선스](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)합니다.

구성 하려는 [그룹 기반 라이선스](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) 사용자가 라이선스를 프로 비전을 자동화 합니다.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 구성

그룹은 사용자를 관리 하는 Azure Active Directory (Azure AD) 디렉터리를 구성 하 고 관리자 워크스테이션에 대 한 장치 id 서비스와 기능을 사용 해야는 [관리자 계정](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

보안된 워크스테이션 관리자 계정을 만들 때 계정 사용자의 현재 워크스테이션에 노출 합니다. 이 초기 구성과 알려진된 안전한 장치에서 모든 전역 구성 할 것이 좋습니다. 하기 위한 지침을 고려할 수 있습니다 [맬웨어 감염을 방지](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) 먼저 첫 번째 경험을 공격에 노출 될 위험을 줄일 수 있습니다.

조직 해당 관리자에 대 한 multi-factor authentication을를 이상이 필요 합니다. 참조 [클라우드 기반 MFA 배포](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) 구현 지침에 대 한 합니다.

### <a name="azure-ad-users-and-groups"></a>Azure AD 사용자 및 그룹

Azure portal에서 **Azure Active Directory** > **사용자가** > **새 사용자**합니다. [보안 워크스테이션 사용자를 만들려면](https://docs.microsoft.com/Intune/quickstart-create-user), 장치 관리자가 됩니다.

* **이름** -보안 워크스테이션 관리자
* **사용자 이름** - secure-ws-admin@identityitpro.com
* **디렉터리 역할** - **제한 된 관리자** 선택한 다음 관리 역할
   * **Intune 관리자**
* **만들기**

사용자 워크스테이션의 두 그룹 하나 및 자체 워크스테이션에 대 한 하나 만듭니다. Azure portal에서 **Azure Active Directory** > **그룹** > **새 그룹**

워크스테이션 사용자에 대 한 첫 번째 그룹입니다. 구성 하려는 [그룹 기반 라이선스](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) 라이선스 사용자를 프로 비전을 자동화 하려면이 그룹의 사용자에 대 한 합니다.

* **그룹 종류** -보안
* **그룹 이름** -워크스테이션 사용자 보호
* **멤버 자격 유형** -할당
* 보안 워크스테이션 관리자 사용자 그룹에 추가
   * secure-ws-admin@identityitpro.com
* 보안 워크스테이션의 그룹을 관리 하는 다른 사용자를 추가할 수 있습니다.
* **만들기**

워크스테이션 장치에 대 한 두 번째 그룹입니다.

* **그룹 종류** -보안
* **그룹 이름** -보안 워크스테이션
* **멤버 자격 유형** -할당
* **만들기**

### <a name="azure-ad-device-configuration"></a>Azure AD 장치 구성

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Azure AD에 장치를 조인할 수 있는 사용자 지정

장치를 구성 합니다. Active Directory에서 관리 보안 그룹에 도메인에 장치를 조인할 수 있도록 설정 합니다. Azure portal에서이 설정을 구성 하려면로 이동 **Azure Active Directory** > **장치** > **장치 설정**합니다. 선택할 **선택한** 아래에서 **사용자가 Azure AD에 장치를 조인할 수 있습니다** "보안 워크스테이션 사용자" 그룹을 선택 합니다.

#### <a name="removal-of-local-admin-rights"></a>로컬 관리 권한 제거

출시의 일부로 VIP, DevOps 및 보안 수준 워크스테이션의 워크스테이션 사용자는 자신의 컴퓨터에서 관리자 권한이 없는 갖게 됩니다. Azure portal에서이 설정을 구성 하려면로 이동 **Azure Active Directory** > **장치** > **장치 설정**합니다. 선택 **None** 아래에서 **조인 장치의 추가 로컬 관리자 Azure AD에서**합니다.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>장치에 연결 하려면 multi-factor authentication 필요

Azure AD에 장치를 조인 하는 프로세스를 더 강화로 이동 **Azure Active Directory** > **장치** > **장치 설정** 선택 **Yes** 아래에서 **장치에 연결 하려면 Multi-factor Auth 필요** 선택한 **저장**합니다.

#### <a name="configure-mdm"></a>MDM 구성

Azure portal에서 **Azure Active Directory** > **이동성 (MDM 및 MAM)**  > **Microsoft Intune**합니다. 설정을 변경 **MDM 사용자 범위** 하 **모든** 선택한 **저장** 이 시나리오에서는 Intune에서 관리 되는 모든 장치를 허용 하는 것입니다. 자세한 내용은 문서에서 찾을 수 있습니다 [Intune 빠른 시작: Windows 10 장치에 대 한 자동 등록 설정](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)합니다. 이후 단계에서 Intune 구성 및 규정 준수 정책을 만듭니다.

#### <a name="azure-ad-conditional-access"></a>Azure AD 조건부 액세스

Azure AD 조건부 액세스는 규격 장치에서 이러한 권한 있는 관리 태스크를 유지할 수 있습니다. 사용자의 구성원으로 정의한 합니다 **워크스테이션 사용자 보안** 그룹은 클라우드 응용 프로그램에 로그인 할 때 multi-factor authentication을 수행 해야 합니다. 최선의 방법 지침에 따라 되지 않으며이 응급 액세스 계정을 정책에서 제외 됩니다. 이 문서에서 추가 정보를 찾을 수 있습니다 [Azure AD에서 응급 액세스 계정 관리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

Azure portal에서 조건부 액세스를 구성 하려면 **Azure Active Directory** > **조건부 액세스** > **새 정책을**합니다.

* **이름** -안전한 장치 필수 정책
* 할당
   * **사용자 및 그룹**
      * -포함 **사용자 및 그룹** -를 선택 합니다 **워크스테이션 사용자 보안** 이전에 만든 그룹
      * 제외할 **사용자 및 그룹** -조직의 응급 액세스 계정 선택
   * **클라우드 앱**
      * 포함- **모든 클라우드 앱**
* 액세스 제어
   * **권한 부여** 선택- **액세스 권한을 부여** 라디오 단추
      * **다단계 인증 필요**
      * **디바이스를 준수 상태로 표시해야 함**
      * 여러 컨트롤- **선택된 된 컨트롤이 모두 필요 합니다.**
* 정책-를 사용 하도록 설정 **에서**

조직은 사용자는 회사 리소스에 액세스 하지 블록 국가 정책을 선택적으로 만들 수 있습니다. IP 위치 기반 조건부 액세스 정책에 대 한 자세한 내용은 문서에서 찾을 수 있습니다 [Azure Active Directory 조건부 액세스의 위치 조건 이란?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Intune 구성

### <a name="configure-enrollment-status"></a>등록 상태를 구성 합니다.

공급 체인 관리에서 설명한 보안 워크스테이션 정리 신뢰할 수 있는 장치 인지 확인 하는 것이 좋습니다는 장치가 출하 시로 설정 되어야 하는 새 장치를 구입 하는 경우 [S 모드에서 Windows 10 Pro](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), 노출 제한 및 공급망 관리 하는 동안 취약성을 추가 합니다. 장치 공급자에 게 수신 되 면 장치 Autopilot를 사용 하 여 S 모드에서 제거 됩니다. 다음 지침을 적용 변환 프로세스에 세부 정보를 제공 합니다.

장치 사용 하기 전에 완전히 구성 되어 있는지 확인 하려고 합니다. Intune를 통해 **모든 앱과 프로필 설치 될 때까지 장치 사용을 차단**합니다. 

1. **Azure portal** 로 이동 합니다.
1. **Microsoft Intune** > **장치 등록** > **Windows 등록** > **등록 상태 페이지 (미리 보기)**  >  **기본값** > **설정**합니다.
1. 설정 **앱 프로필 설치 진행률 표시** 하 **예**합니다.
1. 설정할 **모든 앱과 프로필 설치 될 때까지 장치 사용을 차단** 하 **예**합니다.

### <a name="create-an-autopilot-deployment-profile"></a>Autopilot 배포 프로필 만들기

장치 그룹을 만든 후 Autopilot 장치를 구성할 수 있도록 배포 프로필을 만들어야 합니다.

1. Azure portal의 intune **장치 등록** > **Windows 등록** > **배포 프로필**  >   **프로필 만들기**합니다.
1. 이름에 대 한 입력 **워크스테이션 배포 프로필 보안**합니다. 설명 입력 **보안 워크스테이션 배포**합니다.
1. 집합 예 Autopilot를 모든 대상된 장치를 변환 합니다. 이렇게이 설정 하면 Autopilot 배포 서비스를 사용 하 여 목록의 모든 장치를 등록 합니다.  등록이 처리에 48 시간을 허용 합니다.
1. 배포 모드 선택 **(미리 보기)를 배포 하는 자체**합니다. 이 프로필이 있는 장치가 장치를 등록 하는 사용자와 연결 됩니다. 장치를 등록 하려면 사용자 자격 증명이 필요 합니다.
1. 기본적으로 Azure AD에 조인 **Azure AD 가입** 선택 하 고 회색으로 표시 해야 합니다.
1. 기본 제공 환경 (OOBE) 선택, 다음 옵션 및 유지를 기본값으로 설정 하 고 선택한 다른 구성 **확인**:
   1. 사용자 계정 유형: **Standard**
1. **만들기**를 선택하여 프로필을 만듭니다. Autopilot 배포 프로필을 장치에 할당 출시 되었습니다.
1. 선택할 **할당이** > **할당할** > **그룹 선택**
   1. **포함할 그룹 선택** -워크스테이션 사용자 보호

### <a name="configure-windows-update"></a>Windows 업데이트 구성

조직 수행할 수는 가장 중요 한 작업 중 하나는 Windows 10을 최신 상태로 유지 합니다. Windows 10 보안 상태에서를 유지 하기 위해 워크스테이션에 업데이트를 적용할는 속도 관리 하는 업데이트 링을 배포 됩니다. 이 구성에서 찾을 수 있습니다 합니다 **Azure portal** > **Microsoft Intune** > **소프트웨어 업데이트**  >  **Windows 10 업데이트 링**합니다.

예정 **만들기** 다음 설정 사용 하 여 새 업데이트 링 기본값에서 변경 합니다.

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

클릭 **만들기** 그런 다음는 **할당** 탭 추가 합니다 **워크스테이션 보안** 포함된 그룹으로 그룹입니다.

Windows 업데이트 정책에 대 한 추가 정보를 찾을 수 있습니다 [CSP 정책 업데이트](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 통합

Windows Defender ATP와 Microsoft Intune 보안 위반을 방지 하기 위해 함께 작동 및 위반의 영향을 제한 하는 데 도움이 됩니다. 기능에서는 실시간 검색을 제공 합니다. ATP는 배포 광범위 한 감사 및 로깅 끝점 장치에 제공 합니다.

Azure portal에서 Windows Defender ATP Intune 통합을 구성 하려면로 이동 **Microsoft Intune** > **장치 준수** > **Windows Defender ATP** .

1. 아래에서 1 단계에서 **Windows Defender ATP 구성**, 클릭 **Windows Defender 보안 센터에서 Microsoft Intune에 Windows Defender ATP 연결**합니다.
1. Windows Defender 보안 센터:
   1. 선택 **설정을** > **고급 기능**
   1. 에 대 한 **Microsoft Intune 연결은**, 선택 **에서**
   1. 선택 **기본 설정 저장**
1. 연결이 설정 되 면 Intune 및 클릭 돌아갑니다 **새로 고침** 맨 위에 있는 합니다.
1. 설정 **연결 Windows 10.0.15063 장치 버전 이상 Windows Defender atp** 하 **에서**합니다.
1. **저장**

이 문서에서 추가 정보를 찾을 수 있습니다 [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)합니다.

### <a name="completing-hardening-of-the-workstation-profile"></a>워크스테이션 프로필의 보안 강화를 완료합니다.

솔루션의 보안 강화를 성공적으로 완료 하려면 다운로드 하 고 원하는 기반 스크립트를 실행할 **프로필 수준** 다음 차트에서.

| 프로필 | 다운로드 위치 | Filename |
| --- | --- | --- |
| 낮은 보안 | N/A |  N/A |
| 향상된 보안 | https://aka.ms/securedworkstationgit | 고급--Windows10-워크스테이션 (1809).ps1 |
| 높은 수준의 보안  | https://aka.ms/securedworkstationgit | -Windows10-HighSecurityWorkstation (1809).ps1 |
| 특수화 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline.ps1 |
| 특수화 된 규정 준수 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| 보안 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

특수화 된 규정 준수 *은 NCSC Windows10 SecurityBaseline에서 제공 하는 특수 한 구성 적용 하는 스크립트입니다.

선택한 스크립트를 성공적으로 실행 되 면 Microsoft Intune에서 프로필 및 정책에 대 한 업데이트를 만들 수 있습니다. 고급 및 보안 프로필에 대 한 스크립트 정책을 만들고 해도 수에 대 한 프로필에 정책을 할당 해야 합니다 하 **워크스테이션 보안** 그룹입니다.

* Intune 장치 구성 프로필 스크립트에 의해 생성에서 찾을 수 있습니다 합니다 **Azure portal** > **Microsoft Intune** > **장치구성**  >  **프로필**합니다.
* 스크립트에 의해 생성 하는 Intune 장치 준수 정책에서 찾을 수 있습니다 합니다 **Azure portal** > **Microsoft Intune** > **장치 준수**  >  **정책을**합니다.

변경 내용을 검토를 내보낼 수도 있습니다는 프로필 되며는 SECCON에 설명 된 대로 내보내기 파일에 변경 내용을 적용 설명서에 따라 강화 하는 추가 필요 합니다.

Intune 데이터 실행 스크립트를 내보낼 `DeviceConfiguration_Export.ps1` 에서 합니다 [DeviceConfiguration GiuHub 리포지토리](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) 기존 Intune 프로필의 모든 현재 내보내기를 제공 됩니다.

## <a name="additional-configurations-and-hardening-to-consider"></a>추가 구성 및 고려해 야 할 보안 강화

제공 된 지침 보안된 워크스테이션을 사용 하도록 설정한, 추가적인 제어도 고려해 야 대체 브라우저 액세스와 같은 아웃 바운드 HTTP 허용 및 웹 사이트 및 사용자 지정 PowerShell 스크립트를 실행 하는 기능을 차단 합니다.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>방화벽 구성 서비스 공급자 (CSP)의 제한적인 인바운드와 아웃 바운드 규칙

인바운드 및 아웃 바운드 규칙의 추가 관리 허용 및 차단 된 끝점을 반영 하도록 업데이트할 수 있습니다. 보안 워크스테이션 강화 하기 위해 계속으로 제한을 모두 인바운드 및 아웃 바운드 기본적으로 거부로 이동 하 고 일반적이 고 신뢰할 수 있는 웹 사이트를 반영 하도록 아웃 바운드에 대 한 허용 된 사이트를 추가 합니다. 방화벽 구성 서비스 공급자에 대 한 추가 구성 정보를 문서에서 찾을 수 있습니다 [방화벽 CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)합니다.

제한 기본 권장 사항은 다음과 같습니다.

* 모든 인바운드 거부
* 모든 아웃 바운드 거부

Spamhaus 프로젝트 조직 이라고 하는 사이트를 차단 하는 것에 대 한 시작 점으로 사용할 수 있는 좋은 목록을 유지 관리 [The 도메인 블록 목록 (두 개)](https://www.spamhaus.org/dbl/)합니다.

### <a name="managing-local-applications"></a>로컬 응용 프로그램 관리

로컬 응용 프로그램을 제거 생산성 응용 프로그램의 제거를 포함 하 여 보안 워크스테이션을 진정으로 확정 된 상태로 전환 합니다. 예제에서를 기본 브라우저로 Chrome을 추가 하 고 플러그 인을 포함 하 여 브라우저를 수정 하는 기능을 제한 Intune을 사용 합니다.

#### <a name="deploy-applications-using-intune"></a>Intune을 사용 하 여 응용 프로그램 배포

일부 경우에는 Google Chrome 브라우저와 같은 응용 프로그램 보안된 워크스테이션에 필요 합니다. 다음 예에서는 보안 그룹의 장치에 Chrome을 설치 하는 지침이 **워크스테이션 보안** 앞에서 만든 합니다.

1. 오프 라인 설치 관리자 다운로드 [Windows 64‑bit Chrome 번들](https://cloud.google.com/chrome-enterprise/browser/download/)
1. 파일을 추출한 위치를 기록 합니다 `GoogleChromeStandaloneEnterprise64.msi` Intune을 사용 하 여 설치 하려면
1. 에 **Azure portal** 이동할 **Microsoft Intune** > **클라이언트 앱** > **앱**  >  **추가**
1. 아래 **앱 유형**, 선택 **-업무**
1. 아래 **앱 패키지 파일**를 선택 합니다 `GoogleChromeStandaloneEnterprise64.msi` 클릭 하 고 압축 푼된 위치에서 **확인**
1. 아래 **앱 정보**게시자 및 설명이 제공 하 고 선택 **확인**
1. **추가**를 클릭합니다.
1. 에 **할당** 선택 **등록 된 장치에서 사용 가능** 아래에서 **할당 유형**
1. 아래 **포함 된 그룹**를 추가 합니다 **워크스테이션 보안** 이전에 만든 그룹
1. 클릭 **확인** 한 다음 **저장**

해당 지원 문서에서 Chrome 설정 구성에 대 한 추가 지침을 찾을 수 있습니다 [Microsoft Intune을 사용 하 여 Chrome 브라우저 관리](https://support.google.com/chrome/a/answer/9102677)합니다.

#### <a name="configuring-the-company-portal-for-custom-apps"></a>사용자 지정 앱에 대 한 회사 포털 구성

보안된 모드에서 응용 프로그램 설치 제한 됩니다 Intune 회사 포털에 있습니다. 그러나 Microsoft Store 대 한 액세스를 필요 포털을 설치 합니다. 보안된 솔루션에 만들겠습니다 포털을 회사 포털의 오프 라인 모드를 사용 하 여 모든 장치에 사용할 수 있습니다.

복사본을 관리 하는 Intune을 설치 합니다 [회사 포털](https://docs.microsoft.com/Intune/store-apps-company-portal-app) 보안된 워크스테이션의 사용자에 게 추가 도구 필요에 따라 푸시할 수 있는 기능을 허용 합니다.

일부 조직에서는 Windows 32 비트 응용 프로그램 또는 배포를 위한 다른 준비 작업을 필요로 하는 앱을 설치 해야 할 수 있습니다. 이러한 응용 프로그램에 대 한 합니다 [Microsoft win32 콘텐츠 준비 도구](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) 준비를 사용 하면 `.intunewin` 설치에 대 한 서식 파일입니다.

### <a name="setting-up-custom-settings-using-powershell"></a>PowerShell을 사용 하 여 사용자 지정 설정 설정

PowerShell을 사용 하 여 호스트를 관리 하는 확장성을 제공 하는 예제도 사용 됩니다. 스크립트는 호스트의 기본 배경을 설정 합니다. 이 기능은 프로필에서 사용할 수 있습니다 및 기능을 보여 주기 위해서만 사용 합니다.

솔루션의 보안 워크스테이션에서 일부 설정과 사용자 지정 컨트롤을 설정 해야 있을 수 있습니다. 예제에서는 Powershell을 사용 하 여 쉽게 사용할 수 있도록 보안 워크스테이션으로 장치를 식별할 수 있으려면 워크스테이션의 배경을 변경 합니다. 예제 PowerShell이이 작업을 완료 하려면을 사용 하는 동안 Azure portal에서 완료할 수도 있습니다.

이 예제는 다음을 사용 하는 것 [무료 제네릭 배경 이미지](https://i.imgur.com/OAJ28zO.png) 하며 [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) Windows 시작 메뉴의 배경색을 로드할 수 있도록 Microsoft 스크립트 센터에서.

1. 로컬 장치에 스크립트를 다운로드 합니다.
1. customerXXXX 및 배경 파일 및 폴더를 사용 하도록 배포를 반영 하도록 스크립트에 사용 하려는 배경의 다운로드 위치를 업데이트 합니다. 예에서 배경에 customerXXXX를 바꿉니다.  
1. 로 이동 합니다 **Azure portal** > **Microsoft Intune** > **장치 구성을** > **PowerShell 스크립트** > **추가**
1. 제공을 **이름** 스크립트에 대 한 지정 된 **스크립트 위치** 를 다운로드
1. **구성**을 선택합니다.
   1. 설정할 **로그인 자격 증명에 사용 하 여이 스크립트를 실행**를 **예**
   1. **확인**을 클릭합니다.
1. **만들기**
1. 선택 **할당이** > **그룹 선택**
   1. 보안 그룹 추가 **보안 워크스테이션** 를 클릭 하 고 이전에 만든 **저장**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>미리 보기를 사용 합니다. 2018 년 10 월에 대 한 MDM 보안 기준

Microsoft Intune 관리자는 일반적인 기준 보안 상태를 적용 하는 간단한 방법을 제공 하는 보안 기준 관리 기능을 도입 되었습니다. 기준선 고급 프로필 워크스테이션 아래로 잠긴 달성 하기 위해 이와 유사한 수단을 제공 합니다.

보안 워크스테이션에 대 한 보안 구성 배포를 사용 하 여이 기준 것으로 사용 되지 않는 구현 충돌 합니다.

|   |   |   |
| :---: | :---: | :---: |
| 위에서 잠금 | 장치 설치 | 원격 데스크톱 서비스 |
| 앱 런타임 | 장치 잠금 | 원격 관리 |
| 애플리케이션 관리 | Event Log Service | 원격 프로시저 호출 |
| 자동 재생 | 환경 | 검색 |
| BitLocker | Exploit Guard | 스마트 화면 |
| 브라우저 | 파일 탐색기 | 시스템 요구 사항|
| 연결 | Internet Explorer | Wi-Fi |
| 자격 증명 위임 | 로컬 정책 보안 옵션 | Windows 연결 관리자 |
| 자격 증명 UI | MS 보안 가이드 | Windows Defender|
| 데이터 보호 | MSS 레거시 | Windows Ink 작업 영역 |
| Device Guard | Power | Windows PowerShell |

이 미리 보기 기능에 대 한 자세한 내용은 문서에서 찾을 수 있습니다 [Intune에 대 한 Windows 보안 기준 설정을](https://docs.microsoft.com/Intune/security-baseline-settings-windows)합니다.

## <a name="enroll-and-validate-your-first-device"></a>등록 하 고 첫 번째 장치를 확인 합니다.

1. 장치를 등록 하려면 다음 정보가 필요 합니다.
   * **일련 번호** 장치 섀시에서 찾은-
   * **Windows 제품 ID** -에서 찾은 **System** > **에 대 한** Windows 설정 메뉴에서.
   * 실행 중인 [Get WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) 모든 필요한 정보를 사용 하 여 장치 등록을 위한 CSV 해시 파일을 제공 합니다. 
      * 실행 `Get-WindowsAutoPilotInfo – outputfile device1.csv` 에서 Intune로 가져올 수 있는 CSV 파일로 정보를 출력 합니다.

   > [!NOTE]
   > 스크립트는 상승 된 권한이 필요 하 고 원격으로 실행 되도록 합니다. 스크립트가 제대로 실행 되도록 허용 하려면 다음 명령을 사용할 수 있습니다. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  Windows 10 버전 1809 또는 더 높은 장치에 로그인 정보를 수집 하 여이 정보를 수집할 수 있습니다 또는 새 장치를 정렬할 때 하드웨어 재판매인이이 정보를 제공할 수 있습니다.
1. 필요한 정보를 수집 하 고 반환 된 **Azure portal**합니다. 이동할 **Microsoft Intune** > **장치 등록** > **Windows 등록** > **장치 Windows Autopilot 장치를 관리할**을 선택 **가져오기**, 만들거나 제공 된 CSV 파일을 선택 합니다.
1. 보안 그룹에 이제 등록된 된 장치를 추가 **워크스테이션 보안** 이전에 생성 합니다.
1. 구성 하려는 Windows 10 장치에서 찾습니다 **Windows 설정** > **업데이트 및 보안** > **복구**합니다. 선택 **시작** 아래에서 **이 PC를 다시 설정** 지시에 따라 다시 설정 하 고 구성 프로필 및 규정 준수 정책을 사용 하 여 장치를 다시 구성 하 고 있습니다.

장치에서 구성한 검토를 완료 한 후 구성을 확인 합니다. 첫 번째 장치에 배포를 계속 하기 전에 올바르게 구성 되었는지 확인 합니다.

## <a name="assignment-and-monitoring"></a>할당 및 모니터링

매핑의 장치 및 사용자를 할당 해야 합니다 [선택한 프로필](https://docs.microsoft.com/intune/device-profile-assign) 사용자의 보안 그룹 및 서비스에 대 한 사용 권한을 지정 될 모든 새 사용자가 해야도 보안 그룹에 추가할 수 있습니다.

프로필을 모니터링 할 수 있는 모니터링을 사용 하 여 [Microsoft Intune 프로필](https://docs.microsoft.com/intune/device-profile-monitor)합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Intune](https://docs.microsoft.com/intune/index) 설명서

[Azure AD](https://docs.microsoft.com/azure/active-directory/index) 설명서