---
title: "Azure AD 특성 매핑 사용자 지정 | Microsoft Docs"
description: "Azure Active Directory의 Saas 앱에 대한 어떤 특성 매핑이 있고 어떻게 비즈니스 요구 사항에 맞게 수정하는지를 알아봅니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ca2fdc9c68ea0030d938eeaebd57aafa0e2790f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Azure Active Directory에서 SaaS 응용 프로그램에 대한 사용자 프로비전 특성 매핑 사용자 지정
Microsoft Azure AD는 Salesforce, Google Apps 등과 같은 타사 SaaS 응용 프로그램에 프로비전을 하는 사용자에 대한 지원을 제공합니다. 타사 SaaS 응용 프로그램을 프로비전하는 사용자가 있을 경우 Azure 관리 포털은 그 특성 값을 “특성 매핑”이라고 하는 구성 형태로 제어합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성된 특성 세트가 있습니다. 일부 앱은 그룹이나 연락처 같은 다른 유형의 개체를 관리합니다. <br> 
 비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 즉, 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다.

Azure AD 포털에서 **엔터프라이즈 응용 프로그램**의 **관리** 섹션에 있는 **프로비전** 아래 **매핑** 구성을 클릭하여 이 기능에 액세스할 수 있습니다.


![Salesforce][5] 

**매핑** 구성을 클릭하면 관련 **특성 매핑** 블레이드가 열립니다.  
SaaS 응용 프로그램에서 올바르게 작동하기 위해 요구되는 특성 매핑이 있습니다. 필수 특성인 경우 **삭제** 기능을 사용할 수 없습니다.


![Salesforce][6]  

위의 예제를 통해 Salesforce에서 관리된 개체의 **Username** 특성이 그 연결된 Azure Active Directory 개체의 **userPrincipalName** 값으로 채워지는 것을 볼 수 있습니다.

매핑을 클릭하여 기존 **특성 매핑**을 사용자 지정할 수 있습니다. 그러면 **특성 편집** 블레이드가 열립니다.

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>특성 매핑 유형 이해
특성 매핑으로 타사 SaaS 응용 프로그램에서 특성이 채워지는 법을 제어합니다. 4 가지의 다른 매핑 형식이 지원됩니다.

* **직접** – 대상 특성이 Azure AD에서 연결된 개체의 특성 값으로 채워집니다.
* **상수** – 대상 특성이 지정된 특정 문자열로 채워집니다.
* **식** - 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다. 
  자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)을 참조하세요.
* **None** - 대상 특성이 수정되지 않고 남아있습니다. 그러나 대상 특성이 비어 있으면 지정된 기본 값으로 채워집니다.

이 네 가지 기본 특성 맵 형식 외에도 사용자 지정 특성 매핑은 **기본** 값 할당(선택 사항)의 개념을 지원합니다. 기본 값 할당은 Azure AD에나 대상 개체에 값이 없을 경우 대상 특성이 값으로 채워지는지 확인해줍니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.


## <a name="understanding-attribute-mapping-properties"></a>특성 매핑 속성 이해

이전 섹션에서 특성 매핑 유형 속성에 대해 이미 소개한 바 있습니다.
이 속성 외에도, 특성 매핑은 다음 특성도 지원합니다.

- **원본 특성** - 원본 시스템의 사용자 특성(예: Azure Active Directory).
- **대상 특성** – 대상 시스템의 사용자 특성(예: ServiceNow).
- **이 특성을 사용하여 개체 일치** – 원본 및 대상 시스템 간에 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 나타냅니다. 일반적으로 대상 응용 프로그램에서 username 필드에 매핑되는 Azure AD의 userPrincipalName 또는 메일 특성에서 설정됩니다.
- **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.
- **이 매핑 적용**
    - **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.
    - **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.


## <a name="what-you-should-know"></a>알아야 할 사항

Microsoft Azure AD는 동기화 프로세스의 효과적인 구현을 제공합니다. 초기화된 환경에서 동기화 주기 중에는 업데이트가 필요한 개체만 처리됩니다. 특성 매핑 업데이트는 동기화 주기의 성능에 영향을 줍니다. 특성 매핑 구성의 업데이트는 모든 관리된 개체를 다시 평가해야 합니다. 특성 매핑에 대한 연속 변경 횟수를 최소로 유지하는 것이 가장 좋은 방법입니다.

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](active-directory-saas-app-provisioning.md)
* [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](active-directory-saas-scoping-filters.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
* [계정 프로비전 알림](active-directory-saas-account-provisioning-notifications.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

