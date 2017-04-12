---
title: "Azure Active Directory 조건부 액세스 FAQ | Microsoft Docs"
description: "조건부 액세스에 대해 자주 묻는 질문과 대답  "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3980b99206c9f0c81ba3d03778777878e25db7c8
ms.lasthandoff: 12/29/2016


---
# <a name="azure-active-directory-conditional-access-faq"></a>Azure Active Directory 조건부 액세스 FAQ
## <a name="which-applications-work-with-conditional-access-policies"></a>조건부 액세스 정책이 적용되는 응용 프로그램은 무엇입니까?
**A:**[조건부 액세스 - 지원되는 응용 프로그램](active-directory-conditional-access-supported-apps.md)항목을 참조하세요.

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>B2B 공동 작업과 게스트 사용자에게 조건부 액세스 정책이 적용됩니까?
**A:** B2B 공동 작업 사용자에 대한 정책이 적용됩니다. 그러나 경우에 따라 사용자가 정책 요구 사항을 충족하지 못할 수 있습니다. 예를 들어, 조직이 다단계 인증을 지원하지 않는 경우가 해당합니다. 

현재 이 정책은 SharePoint 게스트 사용자에게 적용되지 않습니다. 게스트 관계는 SharePoint 내에서 유지됩니다. 인증 서버에서 게스트 사용자 계정은 액세스 정책에 적용되지 않습니다. 게스트 사용자는 SharePoint에서 관리할 수 있습니다.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online 정책도 비즈니스용 OneDrive에 적용됩니까?
**A:** 예.

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Word나 Outlook 등의 클라이언트 앱에 정책을 설정할 수 없는 이유는 무엇입니까?
**A:** 조건부 액세스 정책은 서비스 액세스 요구 사항을 설정하고, 해당 서비스에서 인증하는 경우에 적용됩니다. 클라이언트 응용 프로그램에서 정책이 설정되는 것이 아니라 서비스가 호출될 때 적용됩니다. 예를 들어, SharePoint에서 설정된 정책은 SharePoint를 호출하는 클라이언트에, Exchange에서 설정된 정책은 Outlook에 적용됩니다.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>조건부 액세스 정책은 서비스 계정을 적용됩니까?
**A:** 조건부 액세스 정책은 모든 사용자 계정을 적용됩니다. 여기에는 서비스 계정으로 사용되는 사용자 계정을 포함합니다. 대부분의 경우, 무인으로 실행되는 서비스 계정은 정책을 충족하지 못합니다. MFA가 필요할 경우 이 경우가 예에 해당되는 사례입니다. 이 경우, 조건부 액세스 정책 관리 설정을 사용하여 서비스 계정을 정책에서 제외할 수 있습니다. 여기에서 사용자에게 정책 적용에 대해 알아보세요.


