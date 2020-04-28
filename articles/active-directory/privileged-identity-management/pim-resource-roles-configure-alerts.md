---
title: Privileged Identity Management-Azure Active Directory에서 Azure 리소스 역할에 대 한 보안 경고 구성 Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 보안 경고를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74023081"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대 한 보안 경고 구성

PIM (Privileged Identity Management)은 Azure Active Directory (Azure AD) 조직에 의심 스러운 또는 안전 하지 않은 활동이 있을 때 경고를 생성 합니다. 경고가 트리거될 때 경고 페이지에 표시됩니다.

![Azure 리소스-경고, 위험 수준 및 수를 나열 하는 경고 페이지](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>경고 검토

경고를 선택 하 여 수정 지침과 함께 경고를 트리거한 사용자 또는 역할을 나열 하는 보고서를 표시 합니다.

![마지막 검사 시간, 설명, 완화 단계, 유형, 심각도, 보안 영향 및 다음 시간을 방지 하는 방법 등을 보여 주는 경고 보고서](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>경고

| 경고 | 심각도 | 트리거 | 권장 |
| --- | --- | --- | --- |
| **너무 많은 소유자를 리소스에 할당** |보통 |너무 많은 사용자가가 소유자 역할을 보유하고 있습니다. |목록에서 사용자를 검토하고 권한이 적은 역할에 다시 할당합니다. |
| **너무 많은 영구 소유자를 리소스에 할당** |보통 |너무 많은 사용자가 역할에 영구적으로 할당되었습니다. |목록에서 사용자를 검토하고 일부를 다시 할당하여 역할 사용을 위한 활성화를 요구합니다. |
| **만든 역할 중복** |보통 |여러 역할에는 동일한 조건이 있습니다. |이러한 역할 중 하나만 사용합니다. |

### <a name="severity"></a>심각도

- **높음**: 정책 위반으로 인해 즉각적인 조치를 요구합니다. 
- **보통**: 즉각적인 조치를 요구하지는 않지만 잠재적으로 정책이 위반될 수 있음을 나타냅니다.
- **낮음**: 즉각적인 조치를 요구하지는 않지만 정책 변경을 제안합니다.

## <a name="configure-security-alert-settings"></a>보안 경고 설정 구성

경고 페이지에서 **설정**으로 이동합니다.

![설정이 강조 표시 된 경고 페이지](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

환경 및 보안 목표로 작업하는 다양한 경고에서 설정을 사용자 지정합니다.

![설정을 사용 하도록 설정 하 고 구성 하는 경고에 대 한 페이지 설정](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
