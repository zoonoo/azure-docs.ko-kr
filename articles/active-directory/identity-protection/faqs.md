---
title: Azure Active Directory ID 보호 FAQ | Microsoft Docs
description: Azure AD ID 보호에 대한 질문과 대답입니다.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bea455371938ccc4d512bdb7ffddd72eb9508da
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522233"
---
# <a name="azure-active-directory-identity-protection-faq"></a>Azure Active Directory ID 보호 FAQ

이 문서에는 Azure AD(Azure Active Directory) Identity Protection에 대한 질문과 대답이 포함되어 있습니다. 자세한 내용은 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)을 참조하세요. 


## <a name="why-do-some-risk-events-have-closed-system-status"></a>일부 위험 이벤트에 "닫힘(시스템)" 상태가 있는 이유는 무엇인가요?

**A:** 이러한 위험 이벤트는 Identity Protection에서 검색되었지만 위험한 것으로 더 이상 간주되지 않으므로 나중에 닫힙니다. 사용자의 위험 수준으로 계산되지 않습니다. 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>Azure Portal에서 ID 보호를 사용하려면 전역 관리자여야 하나요?
**A:** 아니요. 보안 읽기 권한자, 보안 관리자 또는 전역 관리자는 ID 보호를 사용할 수 있습니다.

---

## <a name="how-do-i-get-identity-protection"></a>ID 보호를 얻으려면 어떻게 해야 하나요?

**A:** 이 질문에 대한 답변은 [Azure Active Directory Premium 시작](../fundamentals/active-directory-get-started-premium.md)을 참조하세요.

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>'위험에 대한 플래그가 지정된 사용자'의 사용자를 어떻게 정렬할 수 있나요?

**A:** 위험 플래그가 지정된 사용자 보고서는 **위험 플래그가 지정된 사용자** 페이지의 위쪽에서 **다운로드** 를 클릭하여 다운로드합니다. 그런 다음, [마지막으로 업데이트한 날짜(UTC)]를 포함한 사용 가능한 필드에 따라 다운로드된 데이터를 정렬할 수 있습니다.

---
