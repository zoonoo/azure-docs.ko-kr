---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135025"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>클라우드 HR 애플리케이션에서 Azure Active Directory로 사용자 프로비저닝

지금까지 IT 직원은 직원을 수동으로 만들고 업데이트하고 삭제하는 방법을 사용해 왔습니다. CSV 파일 업로드 또는 사용자 지정 스크립트와 같은 방법을 사용하여 직원 데이터를 동기화했습니다. 이러한 프로비저닝 프로세스는 오류가 발생하기 쉽고, 안전하지 않으며, 관리하기가 어렵습니다.

직원, 공급업체 또는 불확정 작업자의 ID 수명 주기를 관리하기 위해 [Azure AD(Azure Active Directory) 사용자 프로비저닝 서비스](../articles/active-directory/app-provisioning/user-provisioning.md)는 클라우드 기반 HR(인적 자원) 애플리케이션과의 통합을 제공합니다. 애플리케이션의 예로 Workday 또는 SuccessFactors가 있습니다.

Azure AD는 이 통합을 사용하여 다음과 같은 클라우드 HR 애플리케이션(앱) 워크플로를 사용하도록 설정합니다.

- **Active Directory에 사용자 프로비저닝:** 클라우드 HR 앱에서 선택한 사용자 집합을 하나 이상의 Active Directory 도메인으로 프로비저닝합니다.
- **Azure AD에 클라우드 전용 사용자 프로비저닝:** Active Directory가 사용되지 않는 시나리오에서는 사용자를 클라우드 HR 앱에서 Azure AD로 직접 프로비저닝합니다.
- **클라우드 HR 앱에 다시 쓰기:** Azure AD의 이메일 주소와 사용자 이름 특성을 클라우드 HR 앱에 다시 씁니다.


## <a name="enabled-hr-scenarios"></a>사용되는 HR 시나리오

Azure AD 사용자 프로비저닝 서비스를 사용하면 다음과 같은 HR 기반 ID 수명 주기 관리 시나리오를 자동화할 수 있습니다.

- **새 직원 고용:** 새 직원이 클라우드 HR 앱에 추가되면 이메일 주소와 사용자 이름 특성을 클라우드 HR 앱에 다시 쓰는 옵션을 사용하여 사용자 계정이 Active Directory 및 Azure AD에서 자동으로 만들어집니다.
- **직원 특성 및 프로필 업데이트:** 이름, 직위 또는 관리자와 같은 직원 레코드가 클라우드 HR 앱에서 업데이트되면 사용자 계정이 Active Directory 및 Azure AD에서 자동으로 업데이트됩니다.
- **직원 해고:** 직원이 클라우드 HR 앱에서 해고되면 해당 사용자 계정이 Active Directory 및 Azure AD에서 자동으로 사용하지 않도록 설정됩니다.
- **직원 재고용:** 직원이 클라우드 HR 앱에서 다시 고용되면 이전 계정이 자동으로 다시 활성화되거나 Active Directory 및 Azure AD로 다시 프로비저닝될 수 있습니다.

## <a name="who-is-this-integration-best-suited-for"></a>이 통합에 가장 적합한 사용자

Azure AD 사용자 프로비저닝과 클라우드 HR 앱의 통합은 다음과 같은 조직에 가장 적합합니다.

- 클라우드 HR 사용자 프로비저닝에 맞게 미리 빌드된 클라우드 기반 솔루션이 필요합니다.
- 사용자를 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 직접 프로비저닝해야 합니다.
- 클라우드 HR 앱에서 얻은 데이터를 사용하여 사용자를 프로비저닝해야 합니다.
- 클라우드 HR 앱에서 감지된 변경 정보만을 기반으로 하여 참가, 이동 및 탈퇴하는 사용자를 하나 이상의 Active Directory 포리스트, 도메인 및 OU에 동기화해야 합니다.
- Office 365를 이메일에 사용합니다.


### <a name="key-benefits"></a>주요 이점

HR 기반 IT 프로비저닝의 이 기능은 다음과 같은 중요한 비즈니스 이점을 제공합니다.

- **생산성 향상:** 이제 사용자 계정 및 Office 365 라이선스의 할당을 자동화하고 키 그룹에 대한 액세스를 제공할 수 있습니다. 할당을 자동화하면 새로 고용된 직원이 작업 도구에 즉시 액세스할 수 있고 생산성이 향상됩니다.
- **위험 관리:** 클라우드 HR 앱에서 들어오는 데이터를 사용하여 직원 상태 또는 그룹 멤버 자격에 따라 변경을 자동화하여 보안을 강화할 수 있습니다. 변경을 자동화하면 사용자가 조직에서 이동하거나 탈퇴할 때 사용자 ID와 주요 앱에 대한 액세스가 자동으로 업데이트됩니다.
- **주소 규정 준수 및 거버넌스:** Azure AD는 원본 및 대상 시스템 모두의 앱에서 수행하는 사용자 프로비저닝 요청에 대한 기본 감사 로그를 지원합니다. 감사를 사용하면 단일 화면에서 앱에 액세스할 수 있는 사용자를 추적할 수 있습니다.
- **비용 관리:** 자동 프로비저닝은 수동 프로비저닝과 관련된 비효율성 및 인간 오류를 방지하여 비용을 절감합니다. 레거시 플랫폼 및 오래된 플랫폼을 사용하여 시간이 지남에 따라 빌드되는 사용자 지정 개발 사용자 프로비저닝 솔루션에 대한 필요성을 줄입니다.
