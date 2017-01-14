---
title: "범위 지정 필터를 사용한 특성 기반 앱 프로비전 | Microsoft Docs"
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
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4b95782793956470fd147a98c17759a1a1a3a0de


---
# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>범위 지정 필터를 사용한 특성 기반 앱 프로비전
이 섹션의 목적은 범위 지정 필터를 사용하여 어떤 사용자를 응용 프로그램에 프로비전할지 결정하는 특성 기반 규칙을 정의하는 방법을 설명합니다.

## <a name="clauses-and-scope-groups"></a>절 및 범위 그룹
![범위 지정 필터][1] 

범위 지정 필터는 하나 이상의 **그룹 범위**에 의해 정의되며, 각 그룹 범위는 하나 이상의 **절**을 유지하고 있습니다. 특정 범위 그룹에 대한 절을 보려면 그룹 이름의 왼쪽에 있는 화살표를 클릭하여 확장합니다.

**절** 은 각 사용자의 특성을 평가하여 어떤 사용자가 범위 지정 필터를 통과할 수 있는지를 파악합니다. 예를 들어, 사용자의 '상태' 특성이 뉴욕이어야 하는, 즉 뉴욕의 사용자만 응용 프로그램에 프로비전될 수 있는 절을 가질 수 있습니다.

![범위 지정 그룹 이름][2] 

위의 스크린샷에 나타난 것처럼, 각 **범위 그룹**은 하나의 필수 **절**을 가지고 시작합니다. 이 절은 사용자가 지정 범위 필터에 의해 평가되기 전에 먼저 응용 프로그램에 할당되어있어야 한다는 것을 보여줍니다. 이 절은 삭제하거나 수정할 수 없습니다.

적절한 단추를 눌러 새 절이나 새 범위 그룹을 추가할 수 있습니다. **범위 그룹 이름** 속성을 편집하여 각 범위 그룹에 이름을 지정할 수 있습니다.

## <a name="how-scoping-filters-are-evaluated"></a>범위 지정 필터 평가 방법
프로비전을 하는 동안 모든 할당된 사용자를 범위 지정 필터에 대해 테스트하여 응용 프로그램에 액세스 권한이 있는지 결정합니다. 사용자가 필터링되지 않기 위해 각 절이 반드시 통과해야 하는 테스트를 받고 있는 것으로 생각할 수 있습니다. 

정의된 지정 범위가 여러 개 있을 경우, 각 사용자는 응용 프로그램에 접근하기 위해 최소 한 개의 지정 범위를 통과해야 합니다. 그러나 각 범위 그룹 내에서 사용자는 해당 특정 범위 그룹을 통과하기 위해 모든 절을 통과해야만 합니다. 

즉, 범위 그룹이 함께 OR 처리되고 있으며 절들은 함께 AND 처리됨으로써 그 안에 있다고 생각할 수 있습니다. 예를 들어 다음의 지정 범위 필터를 고려해 보십시오.

![범위 지정 그룹 이름][2]  

범위 지정 필터에 따라 사용자는 프로비전을 하기 위해 다음 조건을 충족해야 합니다.

1. 응용 프로그램에 할당되어야 합니다.
2. 엔지니어링 부서에서 근무해야 합니다.
3. 샌프란시스코 또는 캐나다에서 근무해야 합니다.

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [SaaS 응용 프로그램에 자동화된 사용자 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)
* [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [계정 프로비전 알림](active-directory-saas-account-provisioning-notifications.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png



<!--HONumber=Dec16_HO2-->


