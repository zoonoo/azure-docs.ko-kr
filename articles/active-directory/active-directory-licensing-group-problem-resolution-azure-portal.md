---

title: "Azure Active Directory에서 그룹에 대한 라이선스 문제 해결 | Microsoft Docs"
description: "Azure Active Directory 그룹 기반 라이선스를 사용할 때 라이선스 할당 문제를 식별하고 해결하는 방법"
services: active-directory
keywords: "Azure AD 라이선스"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 68155ebaa6af36500bfe856c9bcd49f5efb6cbc2
ms.lasthandoff: 03/23/2017


---

# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결

Azure AD(Azure Active Directory)의 그룹 기반 라이선스에는 라이선스 오류 상태의 사용자에 대한 개념이 도입되었습니다. 이 문서에서는 사용자가 이 상태가 되는 이유를 설명합니다.

그룹 기반 라이선스를 사용하지 않고 개별 사용자에게 직접 라이선스를 할당하면 할당 작업이 실패할 수 있습니다. 예를 들어 사용자에 대해 `Set-MsolUserLicense` PowerShell cmdlet을 실행하면 비즈니스 논리와 관련된 여러 가지 이유로 cmdlet이 실패할 수 있습니다. 라이선스 수가 부족하거나 동시에 할당할 수 없는 두 서비스 간에 충돌이 있는 경우를 예로 들 수 있습니다. 문제는 즉시 사용자에게 보고됩니다.

그룹 기반 라이선스를 사용하는 경우 같은 오류가 발생할 수 있지만 Azure AD 서비스에서 라이선스를 할당하면 문제가 백그라운드에서 발생합니다. 이러한 이유로 사용자에게 즉시 오류를 전달 수 없습니다. 대신 오류가 사용자 개체에 기록된 후 관리 포털을 통해 보고됩니다. 사용자에게 라이선스를 부여하는 원래의 의도는 절대 없어지지 않지만 향후 조사 및 문제 해결을 위해 라이선스를 오류 상태로 기록됩니다.

각 그룹의 오류 상태에 있는 사용자를 찾으려면 각 그룹에 대한 블레이드를 엽니다. 오류 상태인 사용자가 있는 경우 **라이선스**에 알림이 표시됩니다. 알림을 선택하면 영향을 받는 모든 사용자 목록이 열립니다. 사용자를 개별적으로 보고 근본 문제를 파악할 수 있습니다. 이 문서에서는 잠재적인 각 문제 및 문제 해결 방법을 설명합니다.

## <a name="not-enough-licenses"></a>라이선스 부족

**문제:** 그룹에 지정된 제품 중 하나에 사용 가능한 라이선스가 충분하지 않습니다. 제품에 대한 추가 라이선스를 구매하거나 다른 사용자 또는 그룹에서 사용하지 않는 라이선스를 해제해야 합니다.

사용할 수 있는 라이선스 수를 확인하려면 **Azure Active Directory** > **라이선스** > **모든 제품**으로 이동합니다.

라이선스를 사용 중인 사용자와 그룹을 확인하려면 제품을 클릭합니다. **사용이 허가된 사용자**에는 라이선스가 직접 할당되었거나 하나 이상의 그룹을 통해 할당된 모든 사용자가 표시됩니다. **사용이 허가된 그룹**에는 제품이 할당된 모든 그룹이 표시됩니다.

**PowerShell:** PowerShell cmdlet은 이 오류를 _CountViolation_으로 보고합니다.

## <a name="conflicting-service-plans"></a>서비스 계획 충돌

**문제:** 그룹에 지정된 제품 중 하나에 이미 다른 제품을 통해 사용자에게 할당된 다른 서비스 계획과 충돌하는 서비스 계획이 포함되어 있습니다. 일부 서비스 계획은 관련된 다른 서비스 계획과 동일한 사용자에게 할당할 수 없는 방식으로 구성되어 있습니다.

아래 예제를 고려해 보세요. 모든 계획을 사용하도록 설정된 Office 365 Enterprise **E1**의 라이선스가 한 사용자에게 직접 할당되었습니다. 이 사용자는 Office 365 Enterprise **E3** 제품이 할당된 그룹에 추가되었습니다. 이 제품에는 E1에 포함된 계획과 중복될 수 없는 서비스 계획이 포함되어 있으므로 그룹 라이선스 할당은 "서비스 계획 충돌" 오류로 인해 실패합니다. 이 예제에서 충돌하는 서비스 계획은 다음과 같습니다.

-   SharePoint Online(계획 2)은 SharePoint Online(계획 1)과 충돌합니다.
-   Exchange Online(계획 2)은 Exchange Online(계획 1)과 충돌합니다.

이 충돌을 해결하려면 사용자에게 직접 할당된 E1 라이선스에서 두 계획을 사용하지 않도록 설정해야 합니다. 또는 전체 그룹 라이선스 할당을 수정하고 E3 라이선스에서 계획을 사용하지 않도록 설정해야 합니다. 또는 E1 라이선스가 E3 라이선스의 컨텍스트에서 중복될 경우 사용자에게서 E1 라이선스를 제거하기로 결정할 수 있습니다.

충돌하는 제품 라이선스 문제를 해결하는 방법은 항상 관리자가 결정합니다. Azure AD는 라이선스 충돌을 자동으로 해결하지 않습니다.

**PowerShell:** PowerShell cmdlet은 이 오류를 _MutuallyExclusiveViolation_으로 보고합니다.

## <a name="other-products-depend-on-this-license"></a>기타 제품은 이 라이선스에 종속됨

**문제:** 그룹에 지정된 제품 중 하나에 다른 제품의 다른 서비스 계획이 작동하기 위해 사용되어야 하는 서비스 계획이 포함되어 있습니다. 이 오류는 Azure AD가 기본 서비스 계획을 제거하려고 시도할 때 발생합니다. 예를 들어 그룹에서 사용자가 제거된 결과로 이 오류가 발생할 수 있습니다.

이 문제를 해결하려면 다른 메서드를 통해 필수 계획을 사용자에게 이미 할당하거나 해당 사용자에 대한 종속 서비스를 비활성화했는지 확인해야 합니다. 그 후 해당 사용자에서 그룹 라이선스를 적절하게 제거할 수 있습니다.

**PowerShell:** PowerShell cmdlet은 이 오류를 _DependencyViolation_으로 보고합니다.

## <a name="usage-location-isnt-allowed"></a>사용 위치가 허용되지 않음

**문제:** 현지법 및 규정으로 인해 지역에 따라 일부 Microsoft 서비스가 제공되지 않을 수 있습니다. 사용자에게 라이선스를 할당하려면 먼저 사용자에 대한 "사용 위치" 속성을 지정해야 합니다. 이 작업은 Azure Portal의 **사용자** > **프로필** > **설정**에서 수행할 수 있습니다.

Azure AD가 사용 위치가 지원되지 않는 사용자에게 그룹 라이선스를 할당하려고 하면 작업이 실패하고 사용자에게 이 오류를 기록합니다.

이 문제를 해결하려면 라이선스가 부여된 그룹의 지원되지 않는 위치에서 사용자를 제거하면 됩니다. 또는 현재 사용 위치 값이 실제 사용자의 위치를 나타내지 않는 경우에는 다음에 라이선스가 정확히 할당되도록 사용 위치 값을 수정하면 됩니다(새 위치가 지원되는 경우).

**PowerShell:** PowerShell cmdlet은 이 오류를 _ProhibitedInUsageLocationViolation_으로 보고합니다.

> [!NOTE]
> Azure AD가 그룹 라이선스를 할당하면 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다. 현지법 및 규정을 준수하는 그룹 기반 라이선스를 사용하기 전에 관리자가 올바른 사용 위치 값을 사용자에게 설정하는 것이 좋습니다.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>한 그룹에 두 개 이상의 제품 라이선스가 있으면 어떻게 되나요?

한 그룹에 두 개 이상의 제품 라이선스를 할당할 수 있습니다. 예를 들어 그룹에 Office 365 Enterprise E3 및 Enterprise Mobility + Security를 할당하면 해당 사용자에 대해 포함된 모든 서비스를 쉽게 사용하도록 설정할 수 있습니다.

Azure AD는 그룹에 지정된 모든 라이선스를 각 사용자에게 할당하려고 시도합니다. Azure AD는 비즈니스 논리 문제(예를 들어 라이선스가 부족하거나 사용자에 대해 사용하도록 설정된 다른 서비스와 충돌이 있는 경우)로 인해 제품 중 하나를 할당할 수 없는 경우 그룹의 다른 라이선스를 할당하지 않습니다.

할당이 실패한 사용자를 확인하고 이로 인해 영향을 받는 제품을 확인할 수 있습니다.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>오류를 해결하기 위해 그룹의 라이선스를 강제로 처리하는 방법

오류를 해결하기 위해 수행한 단계에 따라 그룹의 처리를 수동으로 트리거하여 사용자 상태를 업데이트해야 할 수 있습니다.

예를 들어 사용자에게서 직접 라이선스 할당을 제거하여 라이선스를 확보한 경우 모든 사용자 구성원에게 완전한 사용 권한을 부여하기 위해 이전에 실패한 그룹 처리를 트리거해야 합니다. 이렇게 하려면 그룹 블레이드를 찾은 후 **라이선스**를 선택하고 도구 모음에서 **다시 처리** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계

그룹을 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Azure Active Directory에서 그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory의 그룹 기반 라이선스란?](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](active-directory-licensing-group-advanced.md)

