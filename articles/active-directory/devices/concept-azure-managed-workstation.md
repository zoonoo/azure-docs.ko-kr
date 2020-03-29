---
title: 안전한 Azure 관리 워크스테이션 이해 - Azure Active Directory
description: 안전한 Azure 관리 워크스테이션에 대해 알아보고 워크스테이션이 중요한 이유를 알아봅니다.
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
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672651"
---
# <a name="understand-secure-azure-managed-workstations"></a>안전한 Azure 관리 워크스테이션 이해

보안되고 격리된 워크스테이션은 관리자, 개발자 및 중요한 서비스 운영자와 같은 중요한 역할의 보안에 매우 중요합니다. 클라이언트 워크스테이션 보안이 손상되면 많은 보안 제어 및 보증이 실패하거나 비효율적일 수 있습니다.

이 문서에서는 권한 있는 액세스 워크스테이션(PAW)이라고도 하는 보안 워크스테이션을 구축하는 데 필요한 작업을 설명합니다. 이 문서에는 초기 보안 컨트롤을 설정하는 자세한 지침도 포함되어 있습니다. 이 가이드는 클라우드 기반 기술이 서비스를 관리하는 방법에 대해 설명합니다. Windows 10RS5, Microsoft Defender 고급 위협 보호(ATP), Azure Active Directory 및 Microsoft Intune에 도입된 보안 기능에 의존합니다.

> [!NOTE]
> 이 문서에서는 보안 워크스테이션의 개념과 그 중요성에 대해 설명합니다. 이 개념에 이미 익숙하고 배포를 건너뛰려면 [보안 워크스테이션 배포를](howto-azure-managed-workstation.md)방문하십시오.

## <a name="why-secure-workstation-access-is-important"></a>보안 워크스테이션 액세스가 중요한 이유

클라우드 서비스의 급속한 도입과 어디서나 작업할 수 있는 기능은 새로운 악용 방법을 만들었습니다. 공격자가 작업하는 장치에서 취약한 보안 제어를 악용하면 공격자가 권한 있는 리소스에 액세스할 수 있습니다.

특권 오용 및 공급망 공격은 공격자가 조직을 위반하는 데 사용하는 상위 5가지 방법 중 하나입니다. 또한 [Verizon 위협 보고서](https://enterprise.verizon.com/resources/reports/dbir/)및 보안 인텔리전스 [보고서에](https://aka.ms/sir)따르면 2018년에 보고된 사건에서 두 번째로 가장 일반적으로 감지되는 전술입니다.

대부분의 공격자는 다음 단계를 따릅니다.

1. 정찰은 종종 산업에 특정 한 방법을 찾을 수 있습니다.
1. 분석하여 정보를 수집하고 낮은 값으로 인식되는 워크스테이션에 침투하는 가장 좋은 방법을 식별합니다.
1. [측면으로](https://en.wikipedia.org/wiki/Network_Lateral_Movement)이동할 수 있는 방법을 찾는 지속성 .
1. 기밀 및 민감한 데이터의 유출.

정찰 중에 공격자는 위험이 낮거나 저평가된 것처럼 보이는 장치에 자주 침투합니다. 이러한 취약한 장치를 사용하여 측면 이동 기회를 찾고 관리 사용자 및 장치를 찾습니다. 권한이 있는 사용자 역할에 액세스하면 공격자는 높은 가치의 데이터를 식별하고 해당 데이터를 성공적으로 유출합니다.

![일반적인 타협 패턴](./media/concept-azure-managed-workstation/typical-timeline.png)

이 문서에서는 이러한 측면 공격으로부터 컴퓨팅 장치를 보호하는 데 도움이 되는 솔루션을 설명합니다. 이 솔루션은 덜 가치 있는 생산성 장치로부터 관리 및 서비스를 격리하여 중요한 클라우드 리소스에 액세스할 수 있는 장치가 침투하기 전에 체인을 끊습니다. 이 솔루션은 Microsoft 365 엔터프라이즈 스택의 일부인 네이티브 Azure 서비스를 사용합니다.

* 장치 관리 및 응용 프로그램 및 URL의 안전한 목록을 위한 인튠
* 장치 설정, 배포 및 새로 고침을 위한 자동 조종 장치
* 사용자 관리, 조건부 액세스 및 다단계 인증을 위한 Azure AD
* 장치 상태 증명 및 사용자 환경을 위한 Windows 10(현재 버전)
* 클라우드 관리 엔드포인트 보호, 탐지 및 대응을 위한 수비수 ATP
* 리소스에 대한 권한 있는 JIT(권한 부여) 액세스 권한을 관리하는 Azure AD PIM
* 모니터링 및 경고를 위한 로그 분석 및 센티넬

## <a name="who-benefits-from-a-secure-workstation"></a>안전한 워크스테이션의 혜택을 누리는 사람은 누구입니까?

모든 사용자와 운영자는 보안 워크스테이션을 사용할 때 이점을 누릴 수 있습니다. PC 또는 장치를 손상시키는 공격자는 캐시된 모든 계정을 가장할 수 있습니다. 장치에 로그인하면 자격 증명 및 토큰을 사용할 수도 있습니다. 이러한 위험을 감수하면 관리 권한을 포함하여 권한 있는 역할에 사용되는 장치를 보호하는 것이 중요합니다. 권한이 있는 계정이 있는 장치는 측면 이동 및 권한 에스컬레이션 공격의 대상입니다. 이러한 계정은 다음과 같은 다양한 자산에 사용할 수 있습니다.

* 온-프레미스 또는 클라우드 기반 시스템 관리자
* 중요한 시스템을 위한 개발자 워크스테이션
* 노출도가 높은 소셜 미디어 계정 관리자
* SWIFT 결제 단말기와 같이 매우 민감한 워크스테이션
* 영업 비밀을 처리하는 워크스테이션

위험을 줄이려면 이러한 계정을 사용하는 권한 있는 워크스테이션에 대해 높은 보안 제어를 구현해야 합니다. 자세한 내용은 Azure [Active Directory 기능 배포 가이드,](../fundamentals/active-directory-deployment-checklist-p2.md) [Office 365 로드맵](https://aka.ms/o365secroadmap)및 [권한 있는 액세스 로드맵 보안)을](https://aka.ms/sparoadmap)참조하십시오.

## <a name="why-use-dedicated-workstations"></a>전용 워크스테이션을 사용해야 하는 이유

기존 장치에 보안을 추가할 수 있지만 보안 기반부터 시작하는 것이 좋습니다. 높은 보안 수준을 유지하기 위해 조직을 최상의 위치에 두려면 보안이 보장되는 장치부터 시작하여 알려진 보안 제어 집합을 구현합니다.

이메일 및 웹 브라우징을 통한 공격 벡터의 수가 증가함에 따라 장치를 신뢰할 수 있는지 확인하는 것이 점점 더 어려워지고 있습니다. 이 가이드에서는 전용 워크스테이션이 표준 생산성, 브라우징 및 전자 메일에서 격리되어 있다고 가정합니다. 장치에서 생산성, 웹 브라우징 및 전자 메일을 제거하면 생산성에 부정적인 영향을 줄 수 있습니다. 그러나 이 보호 는 일반적으로 작업 작업에 명시적으로 필요하지 않고 보안 인시던트의 위험이 높은 시나리오에서 허용됩니다.

> [!NOTE]
> 여기에서 웹 브라우징은 고위험 활동이 될 수 있는 임의의 웹 사이트에 대한 일반적인 액세스를 말합니다. 이러한 브라우징은 Azure, Office 365, 기타 클라우드 공급자 및 SaaS 응용 프로그램과 같은 서비스에 대해 잘 알려진 소수의 관리 웹 사이트에 액세스하기 위해 웹 브라우저를 사용하는 것과 는 분명히 다릅니다.

억제 전략은 공격자가 액세스에 민감한 자산을 얻는 것을 막는 컨트롤의 수와 유형을 늘려 보안을 강화합니다. 이 문서에서 설명하는 모델은 계층화된 권한 디자인을 사용하고 관리 권한을 특정 장치로 제한합니다.

## <a name="supply-chain-management"></a>공급망 관리

보안 워크스테이션에 필수적인 것은 '신뢰의 뿌리'라고 불리는 신뢰할 수 있는 워크스테이션을 사용하는 공급망 솔루션입니다. 트러스트 하드웨어의 루트 선택에서 고려해야 하는 기술에는 최신 랩톱에 포함된 다음 기술이 포함되어야 합니다. 

* [신뢰할 수 있는 플랫폼 모듈(TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker 드라이브 암호화](/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI 보안 부팅](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Windows 업데이트를 통해 분산된 드라이버 및 펌웨어](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [가상화 및 HVCI 사용](/windows-hardware/design/device-experiences/oem-vbs)
* [드라이버 및 앱 HVCI 지원](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA I/O 보호](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [시스템 가드](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [모던 스탠바이](/windows-hardware/design/device-experiences/modern-standby)

이 솔루션의 경우 최신 기술 요구 사항을 충족하는 하드웨어를 사용하여 [Microsoft 자동 조종 기술을](/windows/deployment/windows-autopilot/windows-autopilot) 사용하여 신뢰의 루트를 배포합니다. 워크스테이션을 보호하기 위해 오토파일럿을 사용하면 Microsoft OEM에 최적화된 Windows 10 장치를 활용할 수 있습니다. 이러한 장치는 제조업체에서 알려진 양호한 상태로 제공됩니다. 자동 조종 장치는 잠재적으로 안전하지 않은 장치를 다시 이미징하는 대신 Windows 장치를 "비즈니스 준비" 상태로 변환할 수 있습니다. 설정 및 정책을 적용하고, 앱을 설치하고, Windows 10 버전을 변경하기도 합니다. 예를 들어, 자동 조종 장치는 고급 기능을 사용할 수 있도록 윈도우에서 장치의 Windows 설치를 변경할 수 있습니다 10 프로 10 엔터프라이즈.

![안전한 워크스테이션 수준](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>장치 역할 및 프로필

이 가이드는 사용자, 개발자 및 IT 담당자를 위한 보다 안전한 솔루션을 만드는 데 도움이 되는 몇 가지 보안 프로필 및 역할을 참조합니다. 이러한 프로필은 향상된 또는 보안 워크스테이션의 이점을 누릴 수 있는 일반 사용자의 유용성과 위험의 균형을 유지합니다. 여기에 제공된 설정 구성은 업계에서 인정하는 표준을 기반으로 합니다. 이 가이드에서는 Windows 10을 강화하고 장치 또는 사용자 손상과 관련된 위험을 줄이는 방법을 보여 주어집니다. 최신 하드웨어 기술과 신뢰 장치의 루트를 활용하기 위해 **높은 보안** 프로필에서 시작하여 활성화되는 장치 [상태 증명을](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643)사용합니다. 이 기능은 장치를 초기 부팅하는 동안 공격자가 지속되지 않도록 하기 위해 존재합니다. 정책과 기술을 사용하여 보안 기능 및 위험을 관리합니다.
![안전한 워크스테이션 수준](./media/concept-azure-managed-workstation/seccon-levels.png)

* **기본 보안** – 관리되는 표준 워크스테이션은 대부분의 가정 및 소규모 비즈니스 에서 사용하기에 좋은 출발점을 제공합니다. 이러한 장치는 Azure AD에 등록되고 Intune으로 관리됩니다. 이 프로필을 사용하면 사용자가 모든 응용 프로그램을 실행하고 웹 사이트를 탐색할 수 있습니다. [Microsoft Defender와](https://www.microsoft.com/windows/comprehensive-security) 같은 맬웨어 방지 솔루션을 사용하도록 설정해야 합니다.

* **향상된 보안** - 이 엔트리 레벨의 보호된 솔루션은 가정 사용자, 소규모 비즈니스 사용자 및 일반 개발자에게 적합합니다.

   향상된 워크스테이션은 낮은 보안 프로필의 보안을 강화하는 정책 기반 방법입니다. 이메일 및 웹 브라우징과 같은 생산성 도구를 사용하는 동시에 고객 데이터로 작업할 수 있는 안전한 수단을 제공합니다. 감사 정책과 Intune을 사용하여 사용자 동작 및 프로필 사용에 대한 향상된 워크스테이션을 모니터링할 수 있습니다. Windows10(1809) 스크립트를 사용하여 향상된 워크스테이션 프로필을 배포하고 [ATP(지능형 위협 보호)를](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)사용하여 고급 맬웨어 보호를 활용합니다.

* **높은 보안** - 워크스테이션의 공격 표면을 줄이는 가장 효과적인 방법은 워크스테이션을 자체 관리하는 기능을 제거하는 것입니다. 로컬 관리 권한을 제거하는 것은 보안을 향상시키는 단계이지만 잘못 구현되면 생산성에 영향을 미칠 수 있습니다. 높은 보안 프로필은 로컬 관리자 제거라는 한 가지 상당한 변경 사항으로 향상된 보안 프로필을 기반으로 합니다. 이 프로필은 임원, 급여 및 민감한 데이터 사용자, 서비스 및 프로세스 승인자 등 유명 사용자를 위해 설계되었습니다.

   보안이 높은 사용자는 이메일 및 웹 브라우징과 같은 작업을 간단한 사용 환경에서 수행할 수 있는 동시에 보다 제어된 환경을 요구합니다. 사용자는 쿠키, 즐겨찾기 및 기타 바로 가기와 같은 기능이 작동하기를 기대합니다. 그러나 이러한 사용자는 장치를 수정하거나 디버깅할 수 있는 기능이 필요하지 않을 수 있습니다. 또한 드라이버를 설치할 필요가 없습니다. 높은 보안 프로필은 높은 보안 - Windows10(1809) 스크립트를 사용하여 배포됩니다.

* **특수** – 공격자는 공격자에게 관심 있는 시스템을 변경할 수 있으므로 개발자와 IT 관리자를 대상으로 합니다. 특수 워크스테이션은 로컬 응용 프로그램을 관리하고 웹 사이트를 제한하여 높은 보안 워크스테이션의 정책을 확장합니다. 또한 ActiveX, Java, 브라우저 플러그인 및 기타 Windows 컨트롤과 같은 고위험 생산성 기능도 제한합니다. DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline 스크립트를 사용하여 이 프로필을 배포합니다.

* **보안** - 관리 계정을 손상시키는 공격자는 데이터 도난, 데이터 변경 또는 서비스 중단으로 인해 심각한 비즈니스 손상을 입을 수 있습니다. 이 강화된 상태에서워크스테이션을 사용하면 로컬 응용 프로그램 관리에 대한 직접 제어를 제한하는 모든 보안 제어 및 정책을 사용할 수 있습니다. 보안 워크스테이션에는 생산성 도구가 없으므로 장치를 손상시키기가 더 어려워요. 이메일과 소셜 미디어와 같은 피싱 공격의 가장 일반적인 벡터를 차단합니다. 보안 워크스테이션은 보안 워크스테이션 - Windows10(1809) SecurityBaseline 스크립트로 배포할 수 있습니다.

   ![보안 워크스테이션](./media/concept-azure-managed-workstation/secure-workstation.png)

   보안 워크스테이션은 관리자에게 명확한 응용 프로그램 제어 및 응용 프로그램 가드가 있는 강화된 워크스테이션을 제공합니다. 워크스테이션은 자격 증명 가드, 장치 가드 및 악용 가드를 사용하여 호스트를 악의적인 동작으로부터 보호합니다. 모든 로컬 디스크도 BitLocker로 암호화됩니다.

* **격리** - 이 사용자 지정 오프라인 시나리오는 스펙트럼의 극단적인 끝을 나타냅니다. 이 경우 설치 스크립트가 제공되지 않습니다. 지원되지 않거나 패치가 적용되지 않은 레거시 운영 체제가 필요한 업무상 중요한 기능을 관리해야 할 수 있습니다. 예를 들어, 고부가가치 생산 라인 또는 생명 유지 시스템. 보안이 중요하고 클라우드 서비스를 사용할 수 없기 때문에 이러한 컴퓨터를 수동으로 관리및 업데이트하거나 ESAE(강화 보안 관리 환경)와 같은 격리된 Active Directory 포리스트 아키텍처를 사용하여 관리할 수 있습니다. 이러한 상황에서는 기본 Intune 및 ATP 상태 확인을 제외한 모든 액세스를 제거하는 것이 좋습니다.

   * [인튠 네트워크 통신 요구 사항](/intune/network-bandwidth-use)
   * [ATP 네트워크 통신 요구 사항](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>다음 단계

[안전한 Azure 관리 워크스테이션을 배포합니다.](howto-azure-managed-workstation.md)
