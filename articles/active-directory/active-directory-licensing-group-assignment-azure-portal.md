---

title: "Azure Active Directory에서 그룹에 라이선스 할당 | Microsoft Docs"
description: "Azure Active Directory 그룹 기반 라이선스를 사용하여 라이선스를 할당하는 방법"
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
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: cd2c8b8bd006d3172cb75f12f4a16fef9b698974
ms.lasthandoff: 03/10/2017


---

# <a name="assign-licenses-to-a-group-of-users-in-azure-active-directory"></a>Azure Active Directory에서 사용자의 그룹에 라이선스 할당

이 문서에서는 Azure AD(Azure Active Directory)의 사용자 그룹에 제품 라이선스를 할당한 후 해당 그룹의 모든 구성원에게 라이선스가 올바르게 부여되었는지 확인하는 기본 시나리오를 살펴보겠습니다.

이 예에서는 테넌트에 *HR 부서*라는 보안 그룹이 포함되어 있습니다. 이 그룹에는 인사 부서의 모든 구성원(이 예에서는 약 1,000명의 사용자)이 포함되어 있습니다. 관리자는 부서 전체에 Office 365 Enterprise E3 라이선스를 할당하려고 합니다. 제품에 포함된 Yammer Enterprise 서비스는 나중에 해당 부서에서 사용할 준비를 갖출 때까지 일시적으로 사용하지 않도록 설정해야 합니다. 또한 관리자는 동일한 사용자 그룹에 Enterprise Mobility + Security 라이선스를 배포하려고 합니다.

## <a name="step-1-assign-the-required-licenses"></a>1단계: 필요한 라이선스 할당

1. 관리자 계정으로 [**Azure Portal**](https://portal.azure.com)에 로그인합니다. 라이선스를 관리하려면 계정이 전역 관리자 역할 또는 사용자 계정 관리자 역할이어야 합니다.

2. 왼쪽 탐색 창에서 **더 많은 서비스**를 선택하고 **Azure Active Directory**를 선택합니다. 별 모양 아이콘을 클릭하거나 이 블레이드를 포털 대시보드에 고정하여 이 블레이드를 "즐겨찾기"에 추가할 수 있습니다.

3. **Azure Active Directory** 블레이드에서 **라이선스**를 선택합니다. 이렇게 하면 테넌트에서 사용이 허가된 모든 제품을 보고 관리할 수 있는 블레이드가 열립니다.

4. **모든 제품**에서 제품 이름을 선택하여 Office 365 Enterprise E3 및 Enterprise Mobility + Security를 둘 다 선택합니다. 블레이드 맨 위에 있는 **할당**을 선택하여 할당을 시작합니다.

  ![모든 제품, 라이선스 할당](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. **라이선스 할당** 블레이드에서 **사용자 및 그룹**을 클릭하여 **사용자 및 그룹** 블레이드를 엽니다. 그룹 이름 *HR Department*를 검색하고 그룹을 선택한 후 블레이드 맨 아래에서 **선택**을 클릭하여 확인합니다.

  ![그룹 선택](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. **라이선스 할당** 블레이드에서 **할당 옵션(선택 사항)**을 클릭하여 앞서 선택한 두 가지 제품에 포함된 모든 서비스 계획을 표시합니다. **Yammer Enterprise**를 찾은 후 **해제**하여 제품 라이선스에서 해당 서비스를 사용하지 않도록 설정합니다. **할당 옵션** 맨 아래에서 **확인**을 클릭하여 확인합니다.

  ![할당 옵션](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. 마지막으로 **라이선스 할당** 블레이드 맨 아래에서 **할당**을 클릭하여 할당을 완료합니다.

8. 오른쪽 위 모서리에 프로세스의 상태와 결과를 보여 주는 알림이 표시됩니다. 그룹에 대한 할당을 완료할 수 없으면(예를 들어 그룹의 기존 라이선스 때문에) 알림을 클릭하여 실패 세부 정보를 확인합니다.

이제 HR Department 그룹의 라이선스 템플릿이 지정되었습니다. Azure AD에서 해당 그룹의 모든 기존 멤버를 처리하기 위한 백그라운드 프로세스가 시작되었습니다. 이 초기 작업은 그룹의 현재 크기에 따라 약간 시간이 걸릴 수 있습니다. 다음 단계에서는 프로세스 완료 여부와 문제 해결을 위해 추가적인 관리가 필요한지 여부를 확인하는 방법을 설명할 것입니다.

> [!NOTE]
> 대체 위치인 Azure AD의 **사용자 및 그룹**에서 동일한 할당을 시작할 수 있습니다. **Azure Active Directory** > **사용자 및 그룹** > **모든 그룹**으로 이동합니다. 그 후 그룹을 찾아서 선택한 다음 **라이선스** 탭으로 이동합니다. 블레이드 위쪽의 **할당** 단추를 클릭하면 라이선스 할당 블레이드가 열립니다.

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>2단계: 초기 할당이 완료되었는지 확인

1. **Azure Active Directory** > **사용자 및 그룹** > **모든 그룹**으로 이동합니다. 그런 다음 라이선스가 할당된 *HR Department* 그룹을 찾습니다.

2. *HR Department* 그룹 블레이드에서 **라이선스**를 선택하여 사용자에게 라이선스가 완전히 할당되었는지, 확인해야 할 오류가 있는지 신속하게 확인합니다. 다음과 같은 정보가 제공됩니다.

  - 현재 그룹에 할당된 제품 라이선스의 목록입니다. 사용하도록 설정된 특정 서비스를 표시하고 변경을 수행할 항목을 선택합니다.

  - 그룹의 최신 라이선스 변경 상태(모든 사용자 구성원에 대해 변경 내용을 처리 중인지 아니면 처리가 완료되었는지).

  - 라이선스를 할당할 수 없어서 오류 상태인 사용자에 대한 정보.

  ![할당 옵션](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. 라이선스처리에 대한 자세한 내용은 **Azure Active Directory** > **사용자 및 그룹** > *그룹 이름* > **감사 로그**를 참조하세요. 다음 활동에 유의하세요.

  - 활동: **사용자에게 그룹 기반 라이선스 할당 시작**. 시스템이 그룹에 대한 라이선스 할당 변경을 선택하고 모든 사용자 구성원에게 적용하기 시작하면 로깅됩니다. 수행된 변경 내용에 대한 정보가 포함됩니다.

  - 활동: **사용자에게 그룹 기반 라이선스 할당 완료**. 시스템이 그룹의 모든 사용자에 대한 처리를 완료하면 로깅됩니다. 성공적으로 처리된 사용자 수와 그룹 라이선스를 할당하지 못한 사용자 수에 대한 요약 정보가 포함되어 있습니다.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3단계: 라이선스 문제 확인 및 해결

1. **Azure Active Directory** > **사용자 및 그룹** > **모든 그룹**으로 이동한 후 라이선스가 할당된 *HR Department* 그룹을 찾습니다.
2. **HR Department** 그룹 블레이드에서 **라이선스**를 선택합니다. 블레이드 맨 위에 라이선스를 할당하지 못한 사용자가 10명이라는 알림이 표시됩니다. 이 알림을 클릭하면 이 그룹에서 라이선스 오류 상태인 모든 사용자 목록이 열립니다.
3. **할당 실패** 열에는 두 제품 라이선스 모두 사용자에게 할당하지 못했다는 내용이 표시됩니다. **상위 실패 원인**에는 실패의 원인이 포함되어 있습니다. 이 예에서는 **충돌하는 서비스 계획**이 실패의 원인입니다.

  ![할당 실패](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. 사용자를 선택하여 **라이선스** 블레이드를 엽니다. 이 블레이드에는 현재 사용자에게 할당된 모든 라이선스가 표시됩니다. 이 예에서는 사용자에게 **키오스크 사용자** 그룹에서 상속된 Office 365 Enterprise E1 라이선스가 있음을 확인할 수 있습니다. 이 라이선스는 시스템에서 **HR Department** 그룹에 적용하려고 하는 E3 라이선스와 충돌합니다. 결과적으로 해당 그룹의 어떠한 라이선스도 사용자에게 할당되지 않았습니다.

  ![사용자의 라이선스 보기](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. 이 문제를 해결하기 위해 **키오스크 사용자** 그룹에서 해당 사용자를 제거합니다. Azure AD에서 변경 내용을 처리한 후에 **HR Department** 라이선스가 올바르게 할당됩니다.

  ![잘못 할당된 라이선스](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>다음 단계

그룹의 라이선스를 관리할 수 있는 기능 집합에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Active Directory의 그룹 기반 라이선스란?](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](active-directory-licensing-group-advanced.md)

