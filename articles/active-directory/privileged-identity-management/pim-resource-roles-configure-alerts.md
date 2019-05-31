---
title: PIM-Azure Active Directory에서에서 Azure 리소스 역할에 대 한 보안 경고 구성 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 보안 경고를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 827c42763eee39c717cedc90469ae765cc331272
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66253830"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스에 대한 보안 경고 구성
Azure Active Directory (Azure AD) Privileged Identity Management (PIM)은 사용자 환경에서 의심 스러운 또는 안전 하지 않은 활동이 있을 때 경고를 생성 합니다. 경고가 트리거될 때 경고 페이지에 표시됩니다. 

![경고 페이지](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>경고 검토
경고를 선택하여 경고나 재구성 조언을 트리거하는 사용자 또는 역할을 나열하는 보고서를 확인합니다.

![경고 보고서](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>경고
| 경고 | Severity | 트리거 | 권장 사항 |
| --- | --- | --- | --- |
| **너무 많은 소유자를 리소스에 할당** |중간 |너무 많은 사용자가가 소유자 역할을 보유하고 있습니다. |목록에서 사용자를 검토하고 권한이 적은 역할에 다시 할당합니다. |
| **너무 많은 영구 소유자를 리소스에 할당** |중간 |너무 많은 사용자가 역할에 영구적으로 할당되었습니다. |목록에서 사용자를 검토하고 일부를 다시 할당하여 역할 사용을 위한 활성화를 요구합니다. |
| **만든 역할 중복** |중간 |여러 역할에는 동일한 조건이 있습니다. |이러한 역할 중 하나만 사용합니다. |


### <a name="severity"></a>Severity
* **높음**: 정책 위반으로 인해 즉각적인 조치가 필요합니다. 
* **보통**: 즉각적인 조치가 필요하지는 않지만 잠재적으로 정책이 위반될 수 있음을 나타냅니다.
* **낮음**: 즉각적인 조치를 요구하지는 않지만 정책 변경을 제안합니다.

## <a name="configure-security-alert-settings"></a>보안 경고 설정 구성
경고 페이지에서 **설정**으로 이동합니다.
![설정](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

환경 및 보안 목표로 작업하는 다양한 경고에서 설정을 사용자 지정합니다.
![설정 사용자 지정](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
