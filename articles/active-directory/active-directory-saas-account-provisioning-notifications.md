---
title: "계정 프로비전 알림 | Microsoft Docs"
description: "계정 프로비전 알림 활성화를 통해 주의가 필요한 사용자 프로비전 관련 문제에 대한 알림을 받는 방법을 알아보세요."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: a637aac7-f06b-48ef-a66d-639835a8edec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: e15f3740fbf7cd97002b0b7efda34e968db4fe3c
ms.lasthandoff: 12/29/2016


---
# <a name="account-provisioning-notifications"></a>계정 프로비전 알림
사용자 프로비전으로 타사 SaaS 응용 프로그램에서 사용자 관리 프로세스를 자동화할 수 있습니다. <br>
 자동화 프로세스이지만 때때로 조작이 필요합니다. <br>
 예를 들어 이 경우는 타사 SaaS 응용 프로그램과 데이터를 교환하기 위해 구성된 계정의 암호가 만료되었습니다. 

계정 프로비전 알림을 활성화하면 주의가 필요한 사용자 프로비전 관련 문제에 대해 알림을 받을 수 있습니다.

타사 SaaS 응용 프로그램에 대한 사용자 프로비전 구성으로 계정 프로비전 알림을 활성화 또는 비활성화할 수 있습니다.

![사용자 프로비저닝][1] 

계정 프로비전 알림을 활성화하려면 **확인** 대화 상자 페이지에서 해당 확인란을 선택하고 받는 사람의 메일 별칭을 입력합니다.

![계정 프로비전 알림][2]

배포 목록을 받는 사람으로 입력할 수 있습니다. 하지만 알림 메일에는 Azure AD 관리자에 의해서만 액세스할 수 있는 보고서 링크가 포함되어 있다는 사실을 참고해야 합니다.

계정 프로비전 알림을 활성화하면 사용자 프로비전과 관련된 중요한 문제에 대한 메일을 받게 됩니다. 그러나 메일 오버로드를 방지하기 위해, 알림 메일이 활성화된 각 SaaS 응용 프로그램에 대하여 하루에 한 개의 알림 메일만 받습니다.

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](active-directory-saas-app-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)
* [특성 매핑에 대한 식 작성](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](active-directory-saas-scoping-filters.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

