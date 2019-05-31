---
title: 보안 워크스테이션은 중요-Azure Active Directory
description: 조직 보안 Azure 관리 되는 워크스테이션 만들어야 하는 이유 알아보기
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
ms.openlocfilehash: b7e9ca9fa26e9744eb0a9bfafe692a096825b0b5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357043"
---
# <a name="building-secure-workstations"></a>보안 워크스테이션을 구축

보안된 격리 된 워크스테이션은 관리자, 개발자 및 중요 한 서비스의 연산자와 같은 중요 한 역할의 보안을 위해 매우 중요 합니다. 다른 많은 보안 컨트롤 및 보증은 실패 또는 기본 클라이언트 워크스테이션 보안이 손상 된 경우 효과가 없습니다.

이 문서에서는 보안 제어를 시작 하 게 설정 하는 방법 등 자세한 단계별 지침을 사용 하 여 안전한 클라이언트 워크스테이션을 구축 하는 데 걸리는 설명 합니다. 이 유형의 워크스테이션 때때로 권한 있는 액세스 워크스테이션 (PAW)이이 참조가 사용 되며 기반 이라고 합니다. 그러나 지침은은 서비스를 관리 하려면 클라우드 기반 기술로 수 있는지 확인 하 고 Windows 10RS5, Microsoft Defender ATP, Azure Active Directory 및 Intune부터 도입 된 보안 기능을 소개 합니다.

## <a name="why-securing-workstation-access-is-important"></a>왜 중요 한지 워크스테이션 액세스 보안

Cloud services 및 어디에서 나 사용할 수 있게 신속 하 게 채택 악용에 대 한 새 메서드를 만들었습니다. 공격자가 취약 한 보안 제어 관리자가 작업 하 고 권한 있는 리소스에 액세스할 수 있는 장치의 악용 하는 합니다.

에 설명 된 대로 합니다 [Verizon 위협 보고서](https://enterprise.verizon.com/resources/reports/dbir/), 및 [Security Intelligence Report](https://aka.ms/sir) 권한 오용 하 고 공급 체인 공격은 위반 조직이 사용 하는 상위 5 개 메커니즘 중 하나 및 두 번째 2018 년에 보고 하는 인시던트에서 검색 방법은 가장 일반적으로 합니다.

대부분의 공격자는 아래에 경로 수행합니다.

* 정찰에서 방법을 찾기 위해 업계, 관련 자주 시작
* 인식된 낮은 값 워크스테이션에 대 한 액세스 (침입) 하는 최선의 수단을 식별 하는 수집 된 정보를 분석 합니다.
* 지 속성 및 이동 하는 방법 살펴보고 [가로로](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* 중요 한 기밀 데이터를 반출 할

공격자는 정찰 장치 위험 수준 낮음 보일 또는 단절에 자주 이미지입니다. 이러한 취약 한 장치는 횡 적 이동에 대 한 영업 기회를 찾고, 관리 권한이 있는 사용자 및 장치를 찾을 고 높은 중요 한 데이터를 반출 할 정보 이러한 권한 있는 사용자 역할을 축적 되 면 성공적으로 식별 한 다음 사용 됩니다.

![일반적인 손상 패턴](./media/concept-azure-managed-workstation/typical-timeline.png)

이 문서에는 덜 중요 한 생산성 장치에서 공격이 나 관리와 횡 적 이동 으로부터 보호 하기 위해 서비스를 격리 하 여 컴퓨팅 장치를 보호 하기 위해 솔루션을 제공 합니다. 디자인을 성공적으로 관리 하거나 중요 한 클라우드 리소스에 액세스 하는 데 장치의 침입 하기 전에 체인을 끊지에서 위반을 실행할 수를 줄일 수 있습니다. 설명 된 솔루션 포함 하는 Microsoft 365 Enterprise 스택에 포함 된 네이티브 Azure 서비스를 활용 됩니다.

* Intune 장치 관리를 응용 프로그램 등 URL 허용 목록에 대 한
* Autopilot 장치 설정 및 배포 및 새로 고침 
* 사용자 관리, 조건부 액세스 및 multi-factor authentication에 대 한 azure AD
* 장치 상태 증명 및 사용자 환경에 대 한 Windows 10 (현재 버전)
* Microsoft Defender ATP Advanced Threat Protection () 끝점 보호, 감지 및 클라우드 관리를 사용 하 여 응답에 대 한
* Azure AD PIM에서 JIT (Just Time)를 포함 하 여 권한 부여를 관리 하는 것에 대 한 리소스에 대 한 액세스 권한

## <a name="who-benefit-from-using-a-secure-workstation"></a>사용자가 안전한 워크스테이션을 사용 하 여 이점

모든 사용자 및 보안 워크스테이션을 사용 하 여 활용 하는 연산자입니다. 손상 된 PC 또는 장치 작업을 수행할 수 있는 공격자 몇 가지를 포함 하 여 모든 캐시 된 계정 및 사용 하 여 자격 증명에 로그인 한 장치에서 사용 되는 토큰 가장 합니다. 이 위험 보안 장치는 권한 있는 계정을 사용 하는 횡 적 이동 및 권한 상승 공격을 대상으로 중요 한 관리자 권한을 포함 한 모든 권한 있는 역할에 사용 되는 장치를 만듭니다. 이러한 계정은 같은 다양 한 자산에 대해 사용할 수 있습니다.

* 온-프레미스 및 클라우드 기반 시스템의 관리자
* 중요 한 시스템에 대 한 개발자 워크스테이션
* 심각도 높은 위험 소셜 미디어 계정 관리자
* SWIFT 결제 터미널 같은 매우 중요 한 워크스테이션
* 비밀을 처리 하는 워크스테이션

이러한 계정은 위험을 줄이기 위해 사용 되는 위치는 권한 있는 워크스테이션에 대 한 향상 된 보안 제어를 구현 하는 것이 좋습니다. 추가 지침을 찾을 수 있습니다 합니다 [Azure Active Directory 기능 배포 가이드](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)를 [Office 365 로드맵](https://aka.ms/o365secroadmap), 및 [권한 있는 액세스 보안 로드맵](https://aka.ms/sparoadmap)).

## <a name="why-dedicated-workstations"></a>왜 전용 워크스테이션

보안을 기존 장치를 추가할 수 있지만, 안전한 기반을 시작 하는 것이 좋습니다. 부터 알려진된 적합 한 장치 및 알려진된 보안 컨트롤은 집합을 사용 하 여 조직의 가장 좋은 위치는 유지 관리를 보안 수준이 증가 합니다. 일반 전자 메일 및 웹 브라우저에서 허용 하는 공격 벡터가 점점 번호로 어렵습니다 점점 장치는 신뢰할 수 있도록 합니다. 표준 생산성, 검색에서 구분 되는 전용된 워크스테이션이 가정에 따라이 가이드 작동 하 고 전자 메일 작업이 완료 됩니다. 생산성, 웹 검색 및 장치에서 전자 메일의 제거 생산성에 저하 될 수 있습니다 하지만이 보호 작업을 사용 하 여 명시적으로 요구 하지 않습니다 하 고 보안 인시던트에 대 한 위험 높습니다 시나리오에 대 한 일반적으로 허용 됩니다.

> [!NOTE]
> 웹 브라우저를 사용 하 여 Azure, Office 365, 다른 클라우드 공급자 및 SaaS 같은 서비스에 대 한 잘 알려진 관리 웹 사이트 수가 적은 액세스를 분명히 다른 높은 위험이 있는 임의의 웹 사이트에 대 한 일반 액세스 가리킵니다 웹 여기 검색 응용 프로그램입니다.

억제 전략 악의적 사용자가 중요 한 자산에 액세스 하기 위해 반드시 해결 해야 하는 컨트롤의 종류와 수를 늘려 향상 된 보안 보증을 제공 합니다. 여기에서 제안 된 모델 제공의 계층화 된 권한 모델을 사용 하 여 특정 장치에 관리 권한 포함 합니다.

## <a name="supply-chain-management"></a>공급망 관리

필수 보안된 워크스테이션에는 공급망 솔루션을 사용 하면 워크스테이션 신뢰할 수 있는, 'root 신뢰'. 이 솔루션의 신뢰를 사용 하 여 루트 해결 합니다 [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) 기술 합니다. 보안된 워크스테이션에 대 한 Microsoft Autopilot 알려진된 좋은 상태로 제조업체에서 제공 하는 Microsoft OEM에 최적화 된 Windows 10 장치를 활용 하는 기능을 제공 합니다. 신뢰할 수 있는 장치를 이미지로 다시 설치 하는 대신 Microsoft Autopilot 변환할 수 있습니다 Windows 장치를 "비즈니스 준비" 상태로 앱 설치 설정 및 정책을 적용 하 고 (예를 들어 사용 되는 Windows 10의 버전 변경 고급 기능을 지 원하는 Windows 10 Enterprise Windows 10 Pro).

![워크스테이션 보안 수준](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>장치 역할 및 프로필

설명서, 전체에서 여러 보안 프로필 및 역할과 사용자, 개발자 및 IT 운영 담당자에 대 한 보다 안전한 솔루션을 구축할 해결 될 예정입니다. 이러한 프로필 유용성 및 위험을 분산 하는 동안 향상 된, 또는 보안 워크스테이션을 활용할 수 있는 조직에서 일반적인 사용자를 지원 하도록 정렬 된 있어야 합니다. 지침에는 허용 하는 산업 표준에 따라 설정의 구성을 제공 합니다. 이 지침과 관련 된 보안 기능 및 위험을 관리할 수 있도록 정책 및 기술을 사용 하 여 장치 또는 사용자 손상 위험을 줄이고 Windows 10 보안 강화의 메서드를 설명 하는 데 사용 됩니다.
![워크스테이션 보안 수준](./media/concept-azure-managed-workstation/seccon-levels.png)

* **낮은 보안** – 관리 되는 표준 워크스테이션 대부분의 가정 및 소기업 사용 하기 위한 좋은 출발점을 제공 합니다. 이러한 장치를 등록 하는 Azure AD 및 Intune에서 관리 합니다. 프로필은 사용자가 모든 응용 프로그램을 실행 하 여 모든 웹 사이트 찾아보기 허용 합니다. 맬웨어 방지 솔루션을 같은 [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) 활성화 해야 합니다.
* **향상 된 보안** – 일반 개발자 뿐만 아니라 개인 사용자, 소규모 기업 사용자에 대 한 항목 수준 보호 솔루션을 good 됩니다.
   * 강화 된 워크스테이션 낮은 보안 프로필의 보안을 강화 하는 정책 기반 방법을 제공 합니다. 고객 데이터를 사용 하 여 전자 메일 및 웹 검색 검사와 같은 생산성 도구를 사용할 수 있는 안전한 수단이 대 한이 프로필을 사용 하면 됩니다. 감사 정책을 사용 하도록 설정 하 고 Intune에 대 한 로깅 하 여 사용자 동작 및 워크스테이션에 대 한 프로필 사용을 감사 하는 강화 된 워크스테이션을 사용할 수 있습니다. 이 프로필에서 워크스테이션 보안 제어를 통해 및 정책에서 콘텐츠를 설명 및 강화 된 워크스테이션-Windows10 (1809) 스크립트 배포 합니다. 배포도 활용 고급 맬웨어 보호를 사용 하 여 [Threat Protection (ATP (Advanced)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **높은 수준의 보안** – 워크스테이션의 공격 표면을 줄이기 위해 가장 효과적인 수단 self-administer 워크스테이션에는 기능을 제거 하는 것입니다. 로컬 관리 권한 제거 보안을 강화 하는 단계 이며 올바르게 구현 하는 경우 생산성 영향을 줄 수 있습니다. 상당한 변경, 로컬 관리자의 제거를 사용 하 여 향상 된 보안 프로필을 기반으로 하는 높은 보안 프로필 이 프로필은 사용자가 급여, 서비스 및 프로세스의 승인을 등 중요 한 데이터를 사용 하 여 경영진 등 상위 프로필 사용자 있을 수 있습니다 또는 수 있는 사용자에 게 문의 하는 데 도움이 하도록 설계 되었습니다.
   * 높은 수준의 보안 사용자 프로필 메일과 웹 검색 환경을 사용 하는 간단한 유지 관리 하는 동안 같은 해당 생산성 작업을 수행 하는 동안 더 높은 제어 된 환경을 요청 합니다. 사용자는 쿠키, 즐겨찾기 및 작동에 다른 바로 가기 같은 기능을 예상 합니다. 그러나 이러한 사용자 수정 또는 장치를 디버그 하는 기능이 필요한 지 여부 및 드라이버를 설치할 필요가 없습니다. 높은 보안 프로필은 높은 수준의 보안-Windows10 (1809) 스크립트를 사용 하 여 배포 됩니다.
* **특수화 된** – 개발자 및 IT 관리자는 공격자에 게는 매력적인 대상 이러한 역할은 공격자에 게 관심 시스템을 변경할 수 있습니다. 특수 한 워크스테이션 ActiveX와 같은 로컬 응용 프로그램을 관리 하 고 인터넷 웹 사이트 제한 높은 생산성 기능을 제한 하 여 보안 위험 추가 emphases 고 높은 수준의 보안 워크스테이션에 배포 된 활동 사용 Java, 브라우저 플러그 인의, 및 Windows 장치에 있는 다른 몇 가지 알려진된 위험 수준이 높은 컨트롤입니다. 이 프로필에서 워크스테이션 보안 제어를 통해 및 정책에서 콘텐츠를 설명 및 Windows10 DeviceConfiguration_NCSC-(1803) SecurityBaseline 스크립트 배포 합니다.
* **보안** – 관리 계정 손상 될 수 있는 공격자 일반적으로 손상을 줄 수 있습니다 중요 한 비즈니스 데이터 도난, 데이터 변경 또는 서비스 중단으로 합니다. 이 강화 된 상태의 워크스테이션 모든 보안 제어 및 로컬 응용 프로그램 관리의 직접적인 통제권을 제한 하는 정책을 사용 하도록 설정 됩니다 하 고 생산성 도구 제거 됩니다. 결과적으로, 장치를 손상 시 키 지 어렵습니다 메일로 및 소셜 미디어는 가장 일반적인 방법은 피싱 공격 성공 수를 반영 하는 차단 됩니다.  보안된 워크스테이션 보안 워크스테이션-Windows10 (1809) SecurityBaseline 스크립트를 사용 하 여 배포할 수 있습니다.

   ![보안된 워크스테이션](./media/concept-azure-managed-workstation/secure-workstation.png)

   보안 워크스테이션 관리자 일반 응용 프로그램 제어 및 응용 프로그램 가드 있는 강화 된 워크스테이션을 제공 합니다. 워크스테이션 악의적인 동작에서 호스트를 보호 하기 위해 자격 증명, 장치 및 exploit guard를 사용 합니다. 또한 모든 로컬 디스크는 Bitlocker 암호화를 사용 하 여 암호화 됩니다.

* **격리 된** – 사용자 지정이 오프 라인 시나리오 (이 경우 설치 스크립트가 제공 됩니다) 스펙트럼의 극단적인 끝을 나타냅니다. 조직에서는 격리 된 비즈니스 중요 한 함수를 높은 값 생산 라인 수명 지원 시스템 등 지원 되지 않는/패치되지 않은 이전 운영 체제를 요구 하 관리 해야 합니다. 보안은 매우 중요 하 고 클라우드 서비스를 사용할 수 없는, 때문에 조직 수 수동으로 이러한 컴퓨터를 관리/업데이트 합니다. 또는 관리 하기 위해 격리 된 Active Directory 포리스트 아키텍처 등의 향상 된 보안 관리자 환경 (아키텍처인 ESAE ())를 사용 합니다. 기본 Intune 및 ATP를 제외한 모든 액세스 권한을 제거 해도 이러한 상황에서 상태 검사 고려해 야 합니다.
   * [Intune 네트워크 통신 요구 사항](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [ATP 네트워크 통신 요구 사항](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>다음 단계

[보안 Azure 관리 워크스테이션 배포](howto-azure-managed-workstation.md)
