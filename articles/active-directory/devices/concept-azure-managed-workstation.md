---
title: 안전 하 고 Azure 관리 되는 워크스테이션-Azure Active Directory 이해
description: 안전 하 고 Azure 관리 워크스테이션에 대해 알아보고 중요 하는 이유를 이해 합니다.
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
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491587"
---
# <a name="understand-secure-azure-managed-workstations"></a>안전 하 고 Azure 관리 되는 워크스테이션 이해

안전 하며 격리 된 워크스테이션은 관리자, 개발자 및 중요 한 서비스 연산자와 같은 중요 한 역할의 보안을 위해 매우 중요 합니다. 클라이언트 워크스테이션의 보안을 손상 되 면 많은 보안 컨트롤 및 보증 수 장애가 있거나 없는 경우 유효 합니다.

이 문서를 권한 있는 액세스 워크스테이션 (PAW) 라고도 하는 보안 워크스테이션을 빌드하기 위해 필요한 항목을 설명 합니다. 문서는 또한 초기 보안 컨트롤을 설정 하는 자세한 지침을 포함 합니다. 이 지침은 설명 하는 방법의 클라우드 기반 기술로 서비스를 관리할 수 있습니다. Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory 및 Intune에서 도입 된 보안 기능에 의존 합니다.

> [!NOTE]
> 이 문서에서는 보안 워크스테이션 및 해당 중요도의 개념을 설명 합니다. 이미 익숙한 개념을 건너뛰려면 배포를 방문 [보안 워크스테이션 배포](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation)합니다.

## <a name="why-secure-workstation-access-is-important"></a>보안 워크스테이션 액세스 중요 한 이유는

Cloud services 및 어디에서 나 사용할 수 있게 신속 하 게 채택 새 악용 메서드를 만들었습니다. 여기서 관리자는 장치에서 취약 한 보안 제어를 악용 하 여 공격자가 권한 있는 리소스에 액세스할을 수 있습니다.

권한 오용 하 고 공급 체인 공격은 공격자가 위반 조직이 사용 하는 상위 5 개의 메서드 중 하나입니다. 또한 두 번째 하기도 가장 일반적으로 전략에 따라 2018 년에 보고 된 문제 감지 합니다 [Verizon 위협 보고서](https://enterprise.verizon.com/resources/reports/dbir/), 및 [Security Intelligence Report](https://aka.ms/sir)합니다.

대부분의 공격자가 이러한 단계를 수행합니다.

1. 정찰 방법을 찾기 위해에서는 종종 업계에 특정 합니다.
1. 분석 및 정보를 수집할 가장 좋은 방법은 낮은 값으로 인식 되는 워크스테이션 이미지를 식별 합니다.
1. 지 속성을 이동 하는 것을 찾도록 [가로로](https://en.wikipedia.org/wiki/Network_Lateral_Movement)합니다.
1. 중요 한 기밀 데이터 반출입니다.

정찰 중 공격자가 자주 위험이 낮은 것 또는 단절 하는 장치를 이미지입니다. 횡 적 이동에 대 한 영업 기회를 찾으려고 하는 관리 사용자 및 장치를 찾을 이러한 취약 한 장치를 사용 합니다. 권한 있는 사용자 역할에 대 한 액세스를 축적 한 후 공격자가 가치가 높은 데이터 및 식별 성공적으로 반출 할 데이터입니다.

![일반적인 손상 패턴](./media/concept-azure-managed-workstation/typical-timeline.png)

이 문서에서는 이러한 측면 공격 으로부터 컴퓨팅 장치를 보호 하는 데 도움이 되는 솔루션을 설명 합니다. 솔루션 관리 및 중요 한 클라우드 리소스에 액세스할 수 있는 장치 수 쉽습니다 전에 체인을 끊지 덜 중요 한 생산성 장치에서 서비스를 격리 합니다. 솔루션에서는 Microsoft 365 Enterprise 스택의 일부인 기본 Azure 서비스를 사용 합니다.

* Intune 장치 관리 및 응용 프로그램 및 Url의 수신 허용 목록에 대 한
* Autopilot 장치 설치, 배포 및 새로 고침
* 사용자 관리, 조건부 액세스 및 multi-factor authentication에 대 한 azure AD
* 장치 상태 증명 및 사용자 환경에 대 한 Windows 10 (현재 버전)
* Defender ATP 클라우드 관리 endpoint protection, 검색 및 응답
* 권한 부여 및 시간 (JIT)를 관리 하는 것에 대 한 azure AD PIM 리소스에 대 한 액세스 권한

## <a name="who-benefits-from-a-secure-workstation"></a>보안 워크스테이션에서 기능의 사용자?

모든 사용자 및 연산자 안전한 워크스테이션을 사용 하는 경우를 활용 합니다. PC 또는 장치를 손상 시키면 공격자는 캐시 된 모든 계정을 가장할 수 있습니다. 장치에 로그인 하는 경우 자격 증명 및 토큰도 사용할 수 있습니다. 이 이렇게 하면 중요 한 관리 권한을 비롯 한 권한 있는 역할에 사용 되는 보안 장치에 있습니다. 권한 있는 계정 사용 하 여 장치를 사용 하면 횡 적 이동 및 권한 상승 공격에 대 한 대상이 됩니다. 이러한 계정은 같은 다양 한 자산에 대해 사용할 수 있습니다.

* 온-프레미스 또는 클라우드 기반 시스템의 관리자
* 중요 한 시스템에 대 한 개발자 워크스테이션
* 심각도 높은 위험 소셜 미디어 계정 관리자
* 터미널 SWIFT 지불 같은 매우 중요 한 워크스테이션
* 워크스테이션 처리 거래 비밀

위험을 줄이려면 이러한 계정을 사용 하는 권한 있는 워크스테이션에 대 한 향상 된 보안 제어를 구현 해야 합니다. 자세한 내용은 참조는 [Azure Active Directory 기능 배포 가이드](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)를 [Office 365 로드맵](https://aka.ms/o365secroadmap), 및 [권한 있는 액세스 보안 로드맵](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>전용된 워크스테이션을 사용 하는 이유

보안을 기존 장치를 추가할 수 있지만, 안전한 기반을 시작 하는 것이 좋습니다. 조직 높은 보안 수준을 유지 하기 위해 최적의 위치에 삽입할 안전한 지를 확인 하는 장치를 시작 하 고 알려진된 보안 컨트롤의 집합을 구현 합니다.

점점 더 많은 전자 메일 및 웹 검색을 통해 공격 하기가 점점 더 어렵습니다 장치를 신뢰할 수 있는지 확인 해야 합니다. 이 가이드는 전용된 워크스테이션이 표준 생산성, 찾아보기 및 전자 메일에서 격리 된 있다고 가정 합니다. 생산성, 웹 검색 및 장치에서 전자 메일의 제거에는 생산성에 부정적인 영향을 줄을 수 있습니다. 그러나이 보호 작업을 사용 하 여 명시적으로 요구 하지 않습니다 하 고 보안 인시던트에 대 한 위험 높습니다 시나리오에 대 한 일반적으로 허용 됩니다.

> [!NOTE]
> 웹 검색 여기 위험 수준이 높은 작업을 수 있는 임의의 웹 사이트에 대 한 일반 액세스를 가리킵니다. 이러한 검색 하는 것은 적은 수의 Azure, Office 365, 다른 클라우드 공급자 및 SaaS 응용 프로그램 같은 서비스에 대 한 잘 알려진 관리 웹 사이트에 액세스 하려면 웹 브라우저를 사용 하 여 고유 하 게 다릅니다.

억제 전략 공격자를 방지 하기 위해 중요 한 자산 액세스 하지 못하도록 하는 컨트롤의 종류와 수를 증가 시켜 보안을 강화 합니다. 이 문서에 설명 된 계층화 된 권한 디자인을 사용 하 여 모델과 특정 장치에 관리 권한을 제한 합니다.

## <a name="supply-chain-management"></a>공급망 관리

필수 보안된 워크스테이션에는 '루트 신뢰'를 호출 하는 신뢰할 수 있는 워크스테이션을 사용 하는 공급 체인 솔루션입니다. 루트 트러스트에 사용 하 여이 솔루션에 대 한 [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) 기술 합니다. 워크스테이션을 보호 하려면 Autopilot Microsoft OEM에 최적화 된 Windows 10 장치를 활용할 수 있습니다. 이러한 장치 제조업체에서 알려진된 좋은 상태에 제공 됩니다. 잠재적으로 안전 하지 않은 장치를 이미지로 다시 설치 하는 대신 Autopilot "비즈니스 준비" 상태로 Windows 장치를 변환할 수 있습니다. 설정 및 정책 적용, 앱을 설치 하 고도 Windows 10의 버전을 변경 합니다. 예를 들어 Autopilot 변경할 수 있습니다 장치의 Windows 설치 Windows 10 Pro에서 Windows 10 Enterprise 고급 기능을 사용할 수 있도록 합니다.

![워크스테이션 보안 수준](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>장치 역할 및 프로필

이 지침은 여러 보안 프로필을 참조 하 고 역할 도움이 될 수 있는 사용자, 개발자 및 IT 직원에 대 한 보다 안전한 솔루션을 만듭니다. 이러한 프로필 유용성 및 향상 된 또는 보안 워크스테이션을 활용할 수 있는 일반 사용자에 대 한 위험을 분산 합니다. 여기에 제공 된 구성을 허용 하는 업계 표준을 기반으로 설정 합니다. 이 지침과 관련 된 장치 또는 사용자 손상 위험을 줄이고 Windows 10 보안을 강화 하는 방법을 보여 줍니다. 보안 기능 및 위험을 관리할 수 있도록 정책 및 기술을 사용 하 여 수행 합니다.
![워크스테이션 보안 수준](./media/concept-azure-managed-workstation/seccon-levels.png)

* **낮은 보안** – 관리 되는 표준 워크스테이션 대부분의 가정 및 소기업 사용 하기 위한 좋은 출발점을 제공 합니다. 이러한 장치가 Azure AD에 등록 되 고 Intune을 사용 하 여 관리 합니다. 이 프로필은 사용자가 모든 응용 프로그램을 실행 하 여 모든 웹 사이트 찾아보기 허용 합니다. 맬웨어 방지 솔루션을 같은 [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) 활성화 해야 합니다.

* **향상 된 보안** –이 초급, 보호 된 솔루션은 개인 사용자, 소규모 비즈니스 사용자 및 일반 개발자가 적합 합니다.

   강화 된 워크스테이션은 낮은 보안 프로필의 보안을 강화 하는 정책 기반 방법입니다. 또한 전자 메일 및 웹 검색와 같은 생산성 도구를 사용 하 여 고객 데이터를 사용 하 여 작업할 수 있는 안전한 수단이 제공 합니다. 사용자 동작 및 프로필 사용에 대 한 향상 된 워크스테이션을 모니터링 하려면 감사 정책 및 Intune을 사용할 수 있습니다. Windows10 (1809) 스크립트를 사용 하 여 강화 된 워크스테이션 프로필을 배포 하 고 고급 맬웨어 보호를 사용 하 여 활용 [Threat Protection (ATP (Advanced)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)합니다.

* **높은 수준의 보안** – 워크스테이션의 공격 표면을 줄이기 위해 가장 효과적인 수단 self-administer 워크스테이션에는 기능을 제거 하는 것입니다. 보안을 개선 하는 단계는 로컬 관리 권한 제거 하지만 올바르게 구현 하는 경우에 이러한 생산성을 영향을 줄 수 있습니다. 하나의 상당한 변경 내용 사용 하 여 향상 된 보안 프로필을 기반으로 하는 높은 보안 프로필: 로컬 관리자 제거 이 프로필은 중요 한 비즈니스 사용자를 위한: 임원, 급여 및 중요 한 데이터 사용자, 서비스 및 프로세스에 대 한 승인자 합니다.

   높은 수준의 보안 사용자 전자 메일 및 웹 사용이 단순 환경에서 검색 하는 등의 작업을 수행 하는 동안 더 제어 된 환경을 요구 합니다. 사용자는 쿠키, 즐겨찾기 및 기타 바로 가기 작업 같은 기능을 예상 합니다. 그러나 이러한 사용자는 수정 하거나 자신의 장치를 디버그 하는 기능이 필요 하지 있습니다. 또한 드라이버를 설치할 필요가 없습니다. 높은 보안 프로필은 높은 수준의 보안-Windows10 (1809) 스크립트를 사용 하 여 배포 됩니다.

* **특수화 된** – 공격자가 대상 개발자 및 IT 관리자는 공격자에 게 관심 시스템을 변경할 수 있습니다 때문입니다. 로컬 응용 프로그램을 관리 하 고 웹 사이트를 제한 하 여 높은 수준의 보안 워크스테이션의 정책에 특수 한 워크스테이션 확장 됩니다. 또한 다른 Windows 컨트롤과 ActiveX, Java, 브라우저 플러그 인 등 위험 수준이 높은 생산성 기능을 제한합니다. DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline 스크립트를 사용 하 여이 프로필을 배포 합니다.

* **보안** – 관리 계정 손상 시키면 공격자에는 데이터 도난, 데이터 변경 또는 서비스 중단으로 중요 한 비즈니스 손상 될 수 있습니다. 이 강화 된 상태에서 워크스테이션에는 모든 보안 제어 및 로컬 응용 프로그램 관리의 직접적인 통제권을 제한 하는 정책 수 있습니다. 보안된 워크스테이션에 생산성 도구가 없습니다. 따라서 장치를 손상 시키는 더 어렵습니다. 피싱 공격에 대 한 가장 일반적인 벡터 차단: 전자 메일 및 소셜 미디어입니다.  보안된 워크스테이션 보안 워크스테이션-Windows10 (1809) SecurityBaseline 스크립트를 사용 하 여 배포할 수 있습니다.

   ![보안된 워크스테이션](./media/concept-azure-managed-workstation/secure-workstation.png)

   안전한 워크스테이션을 일반 응용 프로그램 제어 및 응용 프로그램 가드 있는 강화 된 워크스테이션을 사용 하 여 관리자를 제공 합니다. 워크스테이션 악의적인 동작에서 호스트를 보호 하기 위해 credential guard, device guard 및 exploit guard를 사용 합니다. 모든 로컬 디스크가 BitLocker로 암호화 됩니다.

* **격리 된** –이 사용자 지정, 오프 라인 시나리오 스펙트럼의 극단적인 끝을 나타냅니다. 이 경우 설치 스크립트가 제공 됩니다. 지원 되지 않거나 패치가 적용 되지 않은 레거시 운영 체제를 필요로 하는 비즈니스에 중요 한 함수를 관리 해야 합니다. 예를 들어, 높은 값 생산 라인 또는 수명-지원 시스템입니다. 보안은 매우 중요 하 고 클라우드 서비스를 사용할 수 없는, 때문에 관리 하 고 수동으로 또는 격리 된 Active Directory 포리스트 아키텍처 등의 향상 된 보안 관리 환경 아키텍처인 ESAE ()를 사용 하 여 이러한 컴퓨터를 업데이트할 수 있습니다. 이러한 상황에서는 기본 Intune 및 ATP 상태 검사를 제외한 모든 액세스를 제거 하는 것이 좋습니다.

  * [Intune 네트워크 통신 요구 사항](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [ATP 네트워크 통신 요구 사항](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>다음 단계

[보안 Azure 관리 워크스테이션 배포](howto-azure-managed-workstation.md)합니다.
