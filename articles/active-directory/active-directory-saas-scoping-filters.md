---
title: "범위 지정 필터를 사용하여 앱 프로비전 | Microsoft Docs"
description: "개체가 비즈니스 요구 사항을 충족하지 못하는 경우 프로비전하는 자동화된 사용자를 지원하는 앱의 개체가 실제로 프로비전되지 않도록 하기 위한 지정 범위 필터 사용 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: e40ba3d3035d5c04017a8ed558981b01fae40a13
ms.contentlocale: ko-kr
ms.lasthandoff: 09/27/2017

---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>범위 지정 필터를 사용한 특성 기반 응용 프로그램 프로비전
이 섹션의 목적은 범위 지정 필터를 사용하여 어떤 사용자를 응용 프로그램에 프로비전할지 결정하는 특성 기반 규칙을 정의하는 방법을 설명합니다.

## <a name="scoping-filter-use-cases"></a>범위 지정 필터 사용 사례

범위 지정 필터를 사용하면 Azure AD가 특정 값에 부합하는 특성을 갖는 사용자를 포함하거나 제외하여 서비스를 프로비전할 수 있습니다. 예를 들어 영업 팀에서 사용하는 SaaS 응용 프로그램에 Azure AD 사용자를 프로비전할 때, "부서" 속성이 "영업"인 사용자만 프로비전 범위에 포함되도록 지정할 수 있습니다.

프로비전 커넥터 유형에 따라 범위 지정 필터를 다르게 사용할 수 있습니다.

* **Azure AD에서 SaaS 응용 프로그램으로 아웃바운드 프로비전** - Azure AD가 원본 시스템일 때 [사용자 및 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)이 프로비전 범위에 포함될 사용자를 결정하는 가장 일반적인 방법입니다. 이러한 할당은 Single Sign-On 활성화에도 사용되며 액세스 및 프로비전을 관리하는 한 방법을 제공합니다. 범위 지정 필터는 할당과 함께 또는 할당을 대체하여 선택적으로 사용하여 특성 값에 따라 사용자를 필터링할 수 있습니다.

>[!TIP]
> 프로비전 설정의 **[범위](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)** 메뉴를 **모든 사용자 및 그룹 동기화**로 설정하여 엔터프라이즈 응용 프로그램에 대한 할당을 기준으로 프로비전을 사용하지 않을 수 있습니다. 이 옵션을 특성 기준 범위 지정 필터와 함께 사용하면 그룹 기반 할당에서보다 더 빠른 성능을 제공합니다.  

* **HCM 응용 프로그램에서 Azure AD 및 Active Directory로의 인바운드 프로비전** - [Workday 같은 HCM 응용 프로그램](active-directory-saas-workday-tutorial.md)이 원본 시스템일 경우, 범위 지정 필터가 HCM 응용 프로그램이 Active Directory 또는 Azure Active Directory에 프로비전할 사용자를 판단하는 기본 방법이 됩니다.

기본적으로 Azure AD 프로비전 커넥터에는 특성 기반 범위 지정 필터가 구성되어 있지 않습니다. 

## <a name="scoping-filter-construction"></a>범위 지정 필터 구축

범위 지정 필터는 하나 이상의 **절**로 구성됩니다. 절은 각 사용자의 특성을 평가하여 어떤 사용자가 범위 지정 필터를 통과할 수 있는지를 파악합니다. 예를 들어, 사용자의 "상태" 특성이 뉴욕이어야 하는 절을 가졌기 때문에 "뉴욕"의 사용자만 응용 프로그램에 프로비전됩니다. 

단일 절은 단일 특성 값에 대한 단일 조건을 정의합니다. 단일 범위 지정 필터에서 여러 절이 생성될 경우 “AND” 논리로 함께 평가됩니다. 즉 사용자에게 프로비전되려면 모든 절이  "true"로 평가되어야 합니다.

마지막으로, 여러 범위 지정 필터를 단일 응용 프로그램에 대해 만들 수 있습니다. 범위 지정 필터가 여러 개 있으면 함께 "OR" 논리를 통해 평가됩니다. 즉 구성된 범위 지정 필터 중 하나라도  "true"로 평가되면 사용자에게 프로비전됩니다.

Azure AD 프로비전 서비스에서 처리한 각 사용자 또는 그룹은 항상 각 범위 지정 필터에 대해 개별적으로 평가됩니다.

예를 들어, 다음의 지정 범위 필터를 고려해 보겠습니다.

![범위 지정 필터](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

프로비전되는 범위 지정 필터에 따라 사용자는 다음 조건을 충족해야 합니다.

1. 뉴욕에 있어야 합니다.
2. 엔지니어링 부서에서 근무해야 합니다.
3. 회사 직원 ID가 1000000에서 2000000 사이 여야 합니다.
4. 직위가 null이나 공백이 아니어야 합니다.

## <a name="creating-scoping-filters"></a>범위 지정 필터 만들기
범위 지정 필터는 각 Azure AD 사용자 프로비전 커넥터에 대해 특성 매핑의 일부로 구성됩니다. 아래 절차에서는 이미 [지원되는 응용 프로그램 중 하나](active-directory-saas-tutorial-list.md)에 대해 자동 프로비전을 설정했고 거기에 범위 지정 필터를 추가한다고 가정합니다.

### <a name="to-create-a-scoping-filter"></a>범위 지정 필터를 만들려면:
1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory > 엔터프라이즈 응용 프로그램 > 모든 응용 프로그램**으로 이동합니다.
2. 자동 프로비전을 구성한 응용 프로그램을 선택합니다. 예: "ServiceNow"
3. **프로비전** 탭을 선택합니다.
4. **매핑** 섹션에서 범위 지정 필터를 구성할 대상이 되는 매핑을 선택합니다. 예: "Synchronize Azure Active Directory Users to ServiceNow"
5. **원본 개체 범위** 메뉴를 선택합니다.
6. **범위 지정 필터 추가**를 선택합니다.
7. 비교 대상이 될 **특성 이름**, **연산자**, **특성 값**을 선택하여 절을 정의합니다. 지원되는 연산자는 다음과 같습니다.
   * **EQUALS** - 평가된 특성이 입력 문자열 값과 정확히 일치하면(대소문자 구분) "true"를 반환합니다.
   * **NOT EQUALS** - 평가된 특성이 입력 문자열 값과 일치하지 않으면(대소문자 구분) "true"를 반환합니다.
   * **IS TRUE** - 평가된 특성이 부울 값 true를 포함하면 "true"를 반환합니다.
   * **IS FALSE** - 평가된 특성이 부울 값 false를 포함하면 "true"를 반환합니다.
   * **IS NULL** - 평가된 특성이 비어 있으면 "true"를 반환합니다.
   * **IS NOT NULL** - 평가된 특성이 비어 있지 않으면 "true"를 반환합니다.
   * **REGEX MATCH** - 평가된 특성이 정규식 패턴과 일치하면 "true"를 반환합니다. 예: ([1-9][0-9])는 10~99 범위의 모든 숫자와 일치합니다.
   * **NOT REGEX MATCH** - 평가된 특성이 정규식 패턴과 일치하지 않으면 "true"를 반환합니다.
8. **새 범위 지정 절 추가**를 선택합니다.
9. 필요에 따라 7-8단계를 반복하여 다른 범위 지정 절을 추가합니다.
10. **범위 지정 필터 제목**에서 범위 지정 필터의 이름을 추가합니다.
11. **확인**을 선택합니다.
12. 범위 지정 필터 화면에서 **확인**을 다시 선택합니다(또는 6~11단계를 반복하여 다른 범위 지정 필터 추가).
13. 특성 매핑 화면에서 **저장**을 선택합니다. 

>[!IMPORTANT] 
> 새 범위 지정 필터를 저장하면 응용 프로그램의 새로운 전체 동기화가 트리거되며 여기서 원본 시스템의 모든 사용자를 새 범위 지정 필터에 대해 다시 평가합니다. 응용 프로그램의 사용자가 프로비전 범위에서 이전에 평가되었으나 범위를 벗어난 경우 해당 계정은 사용되지 않거나 응용 프로그램에서 프로비전 해제됩니다.


## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [SaaS 응용 프로그램에 자동화된 사용자 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)
* [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [계정 프로비전 알림](active-directory-saas-account-provisioning-notifications.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)


