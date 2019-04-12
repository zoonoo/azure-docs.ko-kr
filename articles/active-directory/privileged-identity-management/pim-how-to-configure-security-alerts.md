---
title: PIM-Azure Active Directory에서에서 Azure AD 역할에 대 한 보안 경고 구성 | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할에 대 한 보안 경고를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0d99fb283be8cbeba6f8a7954ff49161a2d511
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496712"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-pim"></a>PIM에서 Azure AD 역할에 대 한 보안 경고를 구성 합니다.

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)은 사용자 환경에서 의심 스러운 또는 안전 하지 않은 활동이 있을 때 경고를 생성 합니다. 경고가 트리거될 때 PIM 대시보드에 표시됩니다. 경고를 선택하여 경고를 트리거하는 사용자 또는 역할을 나열하는 보고서를 확인합니다.

![PIM 보안 경고 - 스크린 샷](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>보안 경고

이 섹션에서는 Azure AD 역할을 수정 하는 방법 및 방지 하는 방법에 대 한 모든 보안 경고를 나열 합니다. 심각도의 의미는 다음과 같습니다.

* **높음**: 정책 위반으로 인해 즉각적인 조치가 필요합니다.
* **보통**: 즉각적인 조치가 필요하지는 않지만 잠재적으로 정책이 위반될 수 있음을 나타냅니다.
* **낮음**: 즉각적인 조치가 필요하지는 않지만 정책 변경을 제안합니다.

### <a name="administrators-arent-using-their-privileged-roles"></a>관리자가 권한 있는 역할을 사용하지 않음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고는 이유는 무엇 인가요?** | 사용자에게 필요 없는 권한 있는 역할이 할당되면 공격 위험이 증대됩니다. 또한 잘 사용되지 않는 계정에서는 공격자가 드러나지 않을 가능성이 더 큽니다. |
| **문제를 해결 하려면 어떻게 하나요?** | 목록의 사용자를 검토하고 필요 없는 권한 있는 역할에서 제거합니다. |
| **방지** | 업무 근거가 있는 사용자에게만 권한 있는 역할을 할당합니다. </br>사용자가 액세스 권한이 계속 필요한지 확인하도록 정기적인 [액세스 검토](pim-how-to-start-security-review.md)를 예약합니다. |
| **포털에서 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **트리거** | 사용자가 특정 시간 동안 역할을 활성화하지 않는다면 트리거됩니다. |
| **날짜 수** | 이 설정은 사용자가 역할을 활성화하지 않고 지낼 수 있는 일 수를 0 과 100 사이에서 지정합니다.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>활성화할 역할에 다단계 인증이 필요하지 않음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고는 이유는 무엇 인가요?** | MFA가 없으면 손상된 사용자가 권한 있는 역할을 활성화할 수 있습니다. |
| **문제를 해결 하려면 어떻게 하나요?** | 역할 목록을 검토하고 모든 역할에 [MFA를 요구](pim-how-to-change-default-settings.md)합니다. |
| **방지** | [모든 역할에 대해 MFA를 요구합니다](pim-how-to-change-default-settings.md).  |
| **포털에서 완화 작업** | 권한 있는 역할의 활성화에 MFA가 필요하도록 지정합니다. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>테넌트에 Azure AD Premium P2가 없음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고는 이유는 무엇 인가요?** | 현재 테넌트에 Azure AD Premium P2가 없습니다. |
| **문제를 해결 하려면 어떻게 하나요?** | [Azure AD 버전](../fundamentals/active-directory-whatis.md)에 대한 정보를 검토합니다. Azure AD Premium P2로 업그레이드합니다. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>권한 있는 역할의 잠재적인 부실 계정

| | |
| --- | --- |
| **심각도** | 중간 |
| **이 경고는 이유는 무엇 인가요?** | 지난 90일 동안에서 자신의 암호를 변경하지 않은 권한 있는 역할의 계정입니다. 이러한 계정은 유지되지 않고 공격자에게 취약한 서비스 또는 공유 계정일 수 있습니다. |
| **문제를 해결 하려면 어떻게 하나요?** | 목록에서 계정을 검토합니다. 액세스가 더 이상 필요하지 않으면 권한 있는 역할에서 제거합니다. |
| **방지** | 암호를 아는 사용자에게 변경 내용이 있으면 공유되는 계정에서 강력한 암호가 회전되는지 확인합니다. </br>정기적으로 [액세스 검토](pim-how-to-start-security-review.md)를 사용하여 권한 있는 역할이 있는 계정을 검토하고, 더 이상 필요하지 않은 역할 할당을 제거합니다. |
| **포털에서 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **모범 사례** | 암호를 사용하여 인증하고 글로벌 관리자 또는 보안 관리자와 같이 권한이 높은 관리 역할에 할당된 공유, 서비스 및 응급 액세스 계정은 다음과 같은 경우에 자체의 암호를 회전시켜야 합니다.<ul><li>관리 액세스 권한의 남용 또는 손상과 관련된 보안 인시던트 이후</li><li>사용자의 권한이 변경되어 더 이상 관리자가 아닌 경우(예: 관리자였던 직원이 IT 부서 또는 조직을 퇴직한 후)</li><li>IT 직원에 대한 알려진 위반 또는 변경 사항이 없는 경우에도 정기적으로(예: 분기별 또는 연간)</li></ul>여러 사용자가 이러한 계정의 자격 증명에 액세스할 수 있으므로 해당 역할을 떠난 사용자가 더 이상 계정에 액세스할 수 없도록 자격 증명을 회전시켜야 합니다. [자세한 정보](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>역할이 PIM 외부에서 할당됨

| | |
| --- | --- |
| **심각도** | 높음 |
| **이 경고는 이유는 무엇 인가요?** | PIM 외부에서 수행된 권한 있는 역할 할당이 제대로 모니터링되지 않고 있으며 활성 공격을 나타낼 수 있습니다. |
| **문제를 해결 하려면 어떻게 하나요?** | 목록의 사용자를 검토하고 PIM 외부에서 할당된 권한 있는 역할에서 제거합니다. |
| **방지** | 사용자가 PIM 외부에서 권한 있는 역할이 할당되는 위치를 조사하고 향후 해당 위치에서의 할당을 금지합니다. |
| **포털에서 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |

### <a name="there-are-too-many-global-administrators"></a>글로벌 관리자가 너무 많음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고는 이유는 무엇 인가요?** | 글로벌 관리자는 가장 높은 권한 있는 역할입니다. 글로벌 관리자가 손상되면 공격자가 모든 권한에 대한 액세스를 확보하게 되므로 시스템 전체가 위험합니다. |
| **문제를 해결 하려면 어떻게 하나요?** | 목록에서 사용자를 검토하고 글로벌 관리자 역할이 절대적으로 필요하지 않은 모든 사용자를 제거합니다. </br>이런 사용자에게는 낮은 권한의 역할을 할당합니다. |
| **방지** | 사용자에게 필요한 최소한의 권한 있는 역할을 할당합니다. |
| **포털에서 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **트리거** | 서로 다른 두 조건이 충족되고, 그 두 조건을 모두 구성할 수 있을 때 트리거됩니다. 첫째, 글로벌 관리자의 특정 임계값에 도달해야 합니다. 둘째, 총 역할 할당의 특정 비율이 글로벌 관리자여야 합니다. 이런 측정값의 하나만 충족된다면 경고가 표시되지 않습니다. |
| **전역 관리자 최소 수** | 안전하지 않다고 생각하는 글로벌 관리자 수를 2와 100 사이에서 지정합니다. |
| **전역 관리자 백분율** | 사용자 환경에 안전하지 않는 글로벌 관리자 비율을 0%과 100% 사이에서 지정합니다. |

### <a name="roles-are-being-activated-too-frequently"></a>역할이 너무 자주 활성화됨

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고는 이유는 무엇 인가요?** | 동일한 사용자를 통해 같은 권한 있는 역할에 대해 여러 번 활성화하는 것은 공격의 징후입니다. |
| **문제를 해결 하려면 어떻게 하나요?** | 해당 권한 있는 역할에 대한 [활성화 기간](pim-how-to-change-default-settings.md)이 작업을 수행할 만큼 충분히 길게 설정되어 있는지 확인합니다. |
| **방지** | 해당 권한 있는 역할에 대한 [활성화 기간](pim-how-to-change-default-settings.md)이 작업을 수행할 만큼 충분히 길게 설정되어 있는지 확인합니다.</br>여러 관리자가 공유하는 계정을 갖는 권한 있는 역할에 대해 [MFA를 요구합니다](pim-how-to-change-default-settings.md). |
| **포털에서 완화 작업** | N/A |
| **트리거** | 사용자가 지정된 기간 내에 동일한 권한 있는 역할을 여러 번 활성화한다면 트리거됩니다. 기간 및 활성화 횟수를 모두 구성할 수 있습니다. |
| **활성화 갱신 기간** | 이 설정은 의심스러운 갱신을 추적하는 데 사용할 기간을 일, 시, 분, 초로 지정합니다. |
| **활성화 갱신 수** | 이 설정은 선택한 기간 내에 경고할만하다고 생각되는 활성화 회수를 2~100 사이로 지정합니다. 슬라이더를 이동하거나 텍스트 상자에 숫자를 입력하여 이 설정을 변경할 수 있습니다. |

## <a name="configure-security-alert-settings"></a>보안 경고 설정 구성

PIM의 일부 보안 경고를 환경 및 보안 목표에 맞게 사용자 지정할 수 있습니다. 보안 경고 설정을 열려면 다음이 단계를 따릅니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. **설정**을 클릭한 다음, **경고**를 클릭합니다.

    ![보안 경고 설정으로 이동](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. 경고 이름을 클릭하여 해당 경고에 대해 설정을 구성합니다.

    ![보안 경고 설정](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
