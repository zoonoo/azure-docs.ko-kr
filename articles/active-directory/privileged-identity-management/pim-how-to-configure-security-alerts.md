---
title: PIM의 Azure AD 역할에 대 한 보안 경고-Azure AD | Microsoft Docs
description: Azure Active Directory에서 Privileged Identity Management Azure AD 역할에 대 한 보안 경고를 구성 합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6273a5b62e5b84e53d5e54fdb840f32646939066
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743901"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대 한 보안 경고 구성

PIM (Privileged Identity Management)은 Azure Active Directory (Azure AD) 조직에 의심 스러운 또는 안전 하지 않은 활동이 있을 때 경고를 생성 합니다. 경고가 트리거되면 Privileged Identity Management 대시보드에 표시 됩니다. 경고를 선택하여 경고를 트리거하는 사용자 또는 역할을 나열하는 보고서를 확인합니다.

## <a name="determine-your-version-of-pim"></a>PIM 버전 확인

11 월 2019부터 Privileged Identity Management의 Azure AD 역할 부분은 Azure 리소스 역할의 환경과 일치 하는 새 버전으로 업데이트 됩니다. 그러면 [기존 API에 대 한 변경](azure-ad-roles-features.md#api-changes)뿐만 아니라 추가 기능이 생성 됩니다. 새 버전이 롤아웃 되는 동안이 문서에서 수행 하는 절차는 현재 보유 하 고 있는 Privileged Identity Management 버전에 따라 달라 집니다. 이 섹션의 단계에 따라 Privileged Identity Management 버전을 확인 합니다. Privileged Identity Management 버전을 확인 한 후에는이 문서에서 해당 버전과 일치 하는 절차를 선택할 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할에 있는 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. **Azure AD Privileged Identity Management**를 엽니다. 개요 페이지의 맨 위에 배너가 있는 경우이 문서의 **새 버전** 탭에 있는 지침을 따르세요. 그렇지 않으면 **이전 버전** 탭의 지침을 따릅니다.

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

이 문서의 단계에 따라 Azure AD 역할에 대 한 보안 경고를 조사 합니다.

# <a name="new-version"></a>[새 버전](#tab/new)

![Azure AD 역할-경고 및 심각도를 나열 하는 경고 창](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>보안 경고

이 섹션에는 Azure AD 역할에 대 한 모든 보안 경고와 해결 방법 및 방지 방법이 나와 있습니다. 심각도의 의미는 다음과 같습니다.

- **높음**: 정책 위반으로 인해 즉각적인 조치를 요구합니다.
- **보통**: 즉각적인 조치를 요구하지는 않지만 잠재적으로 정책이 위반될 수 있음을 나타냅니다.
- **낮음**: 즉각적인 조치를 요구하지는 않지만 정책 변경을 제안합니다.

### <a name="administrators-arent-using-their-privileged-roles"></a>관리자가 권한 있는 역할을 사용하지 않음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | 사용자에게 필요 없는 권한 있는 역할이 할당되면 공격 위험이 증대됩니다. 또한 잘 사용되지 않는 계정에서는 공격자가 드러나지 않을 가능성이 더 큽니다. |
| **해결 방법** | 목록의 사용자를 검토 하 고 필요 하지 않은 권한 있는 역할에서 제거 합니다. |
| **방지** | 비즈니스 근거를 가진 사용자 에게만 권한 있는 역할을 할당 합니다. </br>사용자가 액세스 권한이 계속 필요한지 확인하도록 정기적인 [액세스 검토](pim-how-to-start-security-review.md)를 예약합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **트리거** | 사용자가 역할을 활성화 하지 않고 지정 된 기간 (일) 동안 이동 하면 트리거됩니다. |
| **일 수** | 이 설정은 사용자가 역할을 활성화 하지 않고 이동할 수 있는 최대 일 수를 0에서 100까지 지정 합니다.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>활성화할 역할에 다단계 인증이 필요하지 않음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | Multi-factor authentication을 사용 하지 않으면 손상 된 사용자가 권한 있는 역할을 활성화할 수 있습니다. |
| **해결 방법** | 역할 목록을 검토 하 고 모든 역할에 대해 [multi-factor authentication을 요구](pim-how-to-change-default-settings.md) 합니다. |
| **방지** | [모든 역할에 대해 MFA를 요구합니다](pim-how-to-change-default-settings.md).  |
| **포털 내 완화 작업** | 권한 있는 역할을 활성화 하는 데 multi-factor authentication을 사용 해야 합니다. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>조직에 Azure AD Premium P2가 없습니다.

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | 현재 Azure AD 조직에 Azure AD Premium P2가 없습니다. |
| **해결 방법** | [Azure AD 버전](../fundamentals/active-directory-whatis.md)에 대한 정보를 검토합니다. Azure AD Premium P2로 업그레이드합니다. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>권한 있는 역할의 잠재적인 부실 계정

| | |
| --- | --- |
| **심각도** | 중간 |
| **이 경고가 표시된 이유는 무엇인가요?** | 권한 있는 역할의 계정이 지난 90 일 동안 암호를 변경 하지 않았습니다. 이러한 계정은 유지되지 않고 공격자에게 취약한 서비스 또는 공유 계정일 수 있습니다. |
| **해결 방법** | 목록에서 계정을 검토합니다. 액세스가 더 이상 필요하지 않으면 권한 있는 역할에서 제거합니다. |
| **방지** | 암호를 아는 사용자에게 변경 내용이 있으면 공유되는 계정에서 강력한 암호가 회전되는지 확인합니다. </br>정기적으로 [액세스 검토](pim-how-to-start-security-review.md)를 사용하여 권한 있는 역할이 있는 계정을 검토하고, 더 이상 필요하지 않은 역할 할당을 제거합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **모범 사례** | 암호를 사용 하 여 인증 하 고 전역 관리자 또는 보안 관리자와 같은 매우 권한 있는 관리 역할에 할당 된 공유, 서비스 및 응급 액세스 계정은 다음과 같은 경우에 암호를 회전 해야 합니다.<ul><li>관리 액세스 권한의 남용 또는 손상과 관련된 보안 인시던트 이후</li><li>사용자의 권한이 변경되어 더 이상 관리자가 아닌 경우(예: 관리자였던 직원이 IT 부서 또는 조직을 퇴직한 후)</li><li>IT 직원에 대한 알려진 위반 또는 변경 사항이 없는 경우에도 정기적으로(예: 분기별 또는 연간)</li></ul>여러 사용자가 이러한 계정의 자격 증명에 액세스할 수 있으므로 해당 역할을 떠난 사용자가 더 이상 계정에 액세스할 수 없도록 자격 증명을 회전시켜야 합니다. [계정 보안 설정에 대 한 자세한 정보](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>역할이 Privileged Identity Management 외부에서 할당 됨

| | |
| --- | --- |
| **심각도** | 높은 |
| **이 경고가 표시된 이유는 무엇인가요?** | Privileged Identity Management 외부에서 만든 권한 있는 역할 할당은 제대로 모니터링 되지 않으며 활성 공격을 나타낼 수 있습니다. |
| **해결 방법** | 목록에서 사용자를 검토 하 고 Privileged Identity Management 외부에서 할당 된 권한 있는 역할에서 해당 사용자를 제거 합니다. |
| **방지** | 사용자가 Privileged Identity Management 외부에서 권한 있는 역할을 할당 하는 위치를 조사 하 고 향후 할당을 금지 합니다. |
| **포털 내 완화 작업** | 권한 있는 역할에서 사용자를 제거 합니다. |

### <a name="there-are-too-many-global-administrators"></a>글로벌 관리자가 너무 많음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | 전역 관리자가 가장 높은 권한 있는 역할입니다. 전역 관리자가 손상 되 면 공격자는 모든 사용 권한에 대 한 액세스 권한을 얻게 되므로 전체 시스템의 위험을 초래할 수 있습니다. |
| **해결 방법** | 목록에서 사용자를 검토 하 고 전역 관리자 역할이 절대적으로 필요 하지 않은 모든 사용자를 제거 합니다. </br>대신 이러한 사용자에 게 더 낮은 권한 있는 역할을 할당 합니다. |
| **방지** | 사용자에게 필요한 최소한의 권한 있는 역할을 할당합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **트리거** | 서로 다른 두 조건이 충족되고, 그 두 조건을 모두 구성할 수 있을 때 트리거됩니다. 먼저 전역 관리자의 특정 임계값에 도달 해야 합니다. 둘째, 전체 역할 할당의 특정 백분율이 전역 관리자 여야 합니다. 이런 측정값의 하나만 충족된다면 경고가 표시되지 않습니다. |
| **최소 글로벌 관리자 수** | 이 설정은 Azure AD 조직에 대해 너무 적은 것으로 간주 되는 2에서 100 까지의 전역 관리자 수를 지정 합니다. |
| **글로벌 관리자 백분율** | 이 설정은 0%에서 100%까지 전역 관리자 인 관리자의 최소 비율을 지정 하며,이는 Azure AD 조직에서 dip를 사용할 수 없게 합니다. |

### <a name="roles-are-being-activated-too-frequently"></a>역할이 너무 자주 활성화됨

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | 동일한 사용자를 통해 같은 권한 있는 역할에 대해 여러 번 활성화하는 것은 공격의 징후입니다. |
| **해결 방법** | 해당 권한 있는 역할에 대한 [활성화 기간](pim-how-to-change-default-settings.md)이 작업을 수행할 만큼 충분히 길게 설정되어 있는지 확인합니다. |
| **방지** | 해당 권한 있는 역할에 대한 [활성화 기간](pim-how-to-change-default-settings.md)이 작업을 수행할 만큼 충분히 길게 설정되어 있는지 확인합니다.</br>여러 관리자가 공유한 계정이 있는 권한 있는 역할에 대해 [multi-factor authentication을 요구](pim-how-to-change-default-settings.md) 합니다. |
| **포털 내 완화 작업** | 해당 없음 |
| **트리거** | 사용자가 지정된 기간 내에 동일한 권한 있는 역할을 여러 번 활성화한다면 트리거됩니다. 기간 및 활성화 횟수를 모두 구성할 수 있습니다. |
| **활성화 갱신 기간** | 이 설정은 의심스러운 갱신을 추적하는 데 사용할 기간을 일, 시, 분, 초로 지정합니다. |
| **활성화 갱신 수** | 이 설정은 사용자가 선택한 기간 내에 통지를 받으려는 2에서 100 까지의 정품 인증 수를 지정 합니다. 슬라이더를 이동하거나 텍스트 상자에 숫자를 입력하여 이 설정을 변경할 수 있습니다. |

## <a name="configure-security-alert-settings"></a>보안 경고 설정 구성

경고 페이지에서 **설정**으로 이동합니다.

![설정이 강조 표시 된 경고 페이지](media/pim-how-to-configure-security-alerts/alert-settings.png)

환경 및 보안 목표로 작업하는 다양한 경고에서 설정을 사용자 지정합니다.

![설정을 사용 하도록 설정 하 고 구성 하는 경고에 대 한 페이지 설정](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[이전 버전](#tab/previous)

![Azure AD 역할-경고 및 심각도를 나열 하는 경고 창](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>보안 경고

이 섹션에는 Azure AD 역할에 대 한 모든 보안 경고와 해결 방법 및 방지 방법이 나와 있습니다. 심각도의 의미는 다음과 같습니다.

- **높음**: 정책 위반으로 인해 즉각적인 조치를 요구합니다.
- **보통**: 즉각적인 조치를 요구하지는 않지만 잠재적으로 정책이 위반될 수 있음을 나타냅니다.
- **낮음**: 즉각적인 조치를 요구하지는 않지만 정책 변경을 제안합니다.

### <a name="administrators-arent-using-their-privileged-roles"></a>관리자가 권한 있는 역할을 사용하지 않음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | 사용자에게 필요 없는 권한 있는 역할이 할당되면 공격 위험이 증대됩니다. 또한 잘 사용되지 않는 계정에서는 공격자가 드러나지 않을 가능성이 더 큽니다. |
| **해결 방법** | 목록의 사용자를 검토 하 고 필요 하지 않은 권한 있는 역할에서 제거 합니다. |
| **방지** | 비즈니스 근거를 가진 사용자 에게만 권한 있는 역할을 할당 합니다. </br>사용자가 액세스 권한이 계속 필요한지 확인하도록 정기적인 [액세스 검토](pim-how-to-start-security-review.md)를 예약합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **트리거** | 사용자가 역할을 활성화 하지 않고 지정 된 기간 (일) 동안 이동 하면 트리거됩니다. |
| **일 수** | 이 설정은 사용자가 역할을 활성화 하지 않고 이동할 수 있는 최대 일 수를 0에서 100까지 지정 합니다.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>활성화할 역할에 다단계 인증이 필요하지 않음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | Multi-factor authentication을 사용 하지 않으면 손상 된 사용자가 권한 있는 역할을 활성화할 수 있습니다. |
| **해결 방법** | 역할 목록을 검토 하 고 모든 역할에 대해 [multi-factor authentication을 요구](pim-how-to-change-default-settings.md) 합니다. |
| **방지** | [모든 역할에 대해 MFA를 요구합니다](pim-how-to-change-default-settings.md).  |
| **포털 내 완화 작업** | 권한 있는 역할을 활성화 하는 데 multi-factor authentication을 사용 해야 합니다. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>조직에 Azure AD Premium P2가 없습니다.

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | 현재 Azure AD 조직에 Azure AD Premium P2가 없습니다. |
| **해결 방법** | [Azure AD 버전](../fundamentals/active-directory-whatis.md)에 대한 정보를 검토합니다. Azure AD Premium P2로 업그레이드합니다. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>권한 있는 역할의 잠재적인 부실 계정

| | |
| --- | --- |
| **심각도** | 중간 |
| **이 경고가 표시된 이유는 무엇인가요?** | 권한 있는 역할의 계정이 지난 90 일 동안 암호를 변경 하지 않았습니다. 이러한 계정은 유지되지 않고 공격자에게 취약한 서비스 또는 공유 계정일 수 있습니다. |
| **해결 방법** | 목록에서 계정을 검토합니다. 액세스가 더 이상 필요하지 않으면 권한 있는 역할에서 제거합니다. |
| **방지** | 암호를 아는 사용자에게 변경 내용이 있으면 공유되는 계정에서 강력한 암호가 회전되는지 확인합니다. </br>정기적으로 [액세스 검토](pim-how-to-start-security-review.md)를 사용하여 권한 있는 역할이 있는 계정을 검토하고, 더 이상 필요하지 않은 역할 할당을 제거합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **모범 사례** | 암호를 사용 하 여 인증 하 고 전역 관리자 또는 보안 관리자와 같은 매우 권한 있는 관리 역할에 할당 된 공유, 서비스 및 응급 액세스 계정은 다음과 같은 경우에 암호를 회전 해야 합니다.<ul><li>관리 액세스 권한의 남용 또는 손상과 관련된 보안 인시던트 이후</li><li>사용자의 권한이 변경되어 더 이상 관리자가 아닌 경우(예: 관리자였던 직원이 IT 부서 또는 조직을 퇴직한 후)</li><li>IT 직원에 대한 알려진 위반 또는 변경 사항이 없는 경우에도 정기적으로(예: 분기별 또는 연간)</li></ul>여러 사용자가 이러한 계정의 자격 증명에 액세스할 수 있으므로 해당 역할을 떠난 사용자가 더 이상 계정에 액세스할 수 없도록 자격 증명을 회전시켜야 합니다. [자세한 정보](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>역할이 Privileged Identity Management 외부에서 할당 됨

| | |
| --- | --- |
| **심각도** | 높은 |
| **이 경고가 표시된 이유는 무엇인가요?** | Privileged Identity Management 외부에서 만든 권한 있는 역할 할당은 제대로 모니터링 되지 않으며 활성 공격을 나타낼 수 있습니다. |
| **해결 방법** | 목록에서 사용자를 검토 하 고 Privileged Identity Management 외부에서 할당 된 권한 있는 역할에서 해당 사용자를 제거 합니다. |
| **방지** | 사용자가 Privileged Identity Management 외부에서 권한 있는 역할을 할당 하는 위치를 조사 하 고 향후 할당을 금지 합니다. |
| **포털 내 완화 작업** | 권한 있는 역할에서 사용자를 제거 합니다. |

### <a name="there-are-too-many-global-administrators"></a>글로벌 관리자가 너무 많음

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | 전역 관리자가 가장 높은 권한 있는 역할입니다. 전역 관리자가 손상 되 면 공격자는 모든 사용 권한에 대 한 액세스 권한을 얻게 되므로 전체 시스템의 위험을 초래할 수 있습니다. |
| **해결 방법** | 목록에서 사용자를 검토 하 고 전역 관리자 역할이 절대적으로 필요 하지 않은 모든 사용자를 제거 합니다. </br>대신 이러한 사용자에 게 더 낮은 권한 있는 역할을 할당 합니다. |
| **방지** | 사용자에게 필요한 최소한의 권한 있는 역할을 할당합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **트리거** | 서로 다른 두 조건이 충족되고, 그 두 조건을 모두 구성할 수 있을 때 트리거됩니다. 먼저 전역 관리자의 특정 임계값에 도달 해야 합니다. 둘째, 전체 역할 할당의 특정 백분율이 전역 관리자 여야 합니다. 이런 측정값의 하나만 충족된다면 경고가 표시되지 않습니다. |
| **최소 글로벌 관리자 수** | 이 설정은 Azure AD 조직에 대해 너무 적은 것으로 간주 되는 2에서 100 까지의 전역 관리자 수를 지정 합니다. |
| **글로벌 관리자 백분율** | 이 설정은 0%에서 100%까지 전역 관리자 인 관리자의 최소 비율을 지정 하며,이는 Azure AD 조직에서 dip를 사용할 수 없게 합니다. |

### <a name="roles-are-being-activated-too-frequently"></a>역할이 너무 자주 활성화됨

| | |
| --- | --- |
| **심각도** | 낮음 |
| **이 경고가 표시된 이유는 무엇인가요?** | 동일한 사용자를 통해 같은 권한 있는 역할에 대해 여러 번 활성화하는 것은 공격의 징후입니다. |
| **해결 방법** | 해당 권한 있는 역할에 대한 [활성화 기간](pim-how-to-change-default-settings.md)이 작업을 수행할 만큼 충분히 길게 설정되어 있는지 확인합니다. |
| **방지** | 해당 권한 있는 역할에 대한 [활성화 기간](pim-how-to-change-default-settings.md)이 작업을 수행할 만큼 충분히 길게 설정되어 있는지 확인합니다.</br>여러 관리자가 공유한 계정이 있는 권한 있는 역할에 대해 [multi-factor authentication을 요구](pim-how-to-change-default-settings.md) 합니다. |
| **포털 내 완화 작업** | 해당 없음 |
| **트리거** | 사용자가 지정된 기간 내에 동일한 권한 있는 역할을 여러 번 활성화한다면 트리거됩니다. 기간 및 활성화 횟수를 모두 구성할 수 있습니다. |
| **활성화 갱신 기간** | 이 설정은 의심스러운 갱신을 추적하는 데 사용할 기간을 일, 시, 분, 초로 지정합니다. |
| **활성화 갱신 수** | 이 설정은 사용자가 선택한 기간 내에 통지를 받으려는 2에서 100 까지의 정품 인증 수를 지정 합니다. 슬라이더를 이동하거나 텍스트 상자에 숫자를 입력하여 이 설정을 변경할 수 있습니다. |

## <a name="configure-security-alert-settings"></a>보안 경고 설정 구성

Privileged Identity Management에서 조직의 요구 사항과 보안 목표를 사용 하 여 일부 보안 경고를 사용자 지정할 수 있습니다. 보안 경고 설정을 열려면 다음이 단계를 따릅니다.

1. Azure AD에서 **Privileged Identity Management** 를 엽니다.

1. **AZURE AD 역할**을 선택 합니다.

1. **설정** , **경고**를 차례로 선택 합니다.

    ![Azure AD 역할-경고가 선택 된 설정](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. 경고 이름을 선택 하 여 해당 경고에 대 한 설정을 구성 합니다.

    ![선택한 경고의 경우 보안 경고 설정 창](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
