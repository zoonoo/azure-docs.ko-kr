---
title: "Azure Active Directory ID 보호 알림 | Microsoft Docs"
description: "알림에서 조사 활동을 지원하는 방법에 대해 알아봅니다."
services: active-directory
keywords: "Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.translationtype: Human Translation
ms.sourcegitcommit: ce3379d5b5e883c6601c40aca191e8b84e3ad8d3
ms.openlocfilehash: 0170b5d2435f6e856478ee9e55ae26c626288f75
ms.contentlocale: ko-kr
ms.lasthandoff: 12/22/2016

---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory ID 보호 알림
Azure AD ID 보호는 두 가지 유형의 자동화된 알림 전자 메일을 보내어 사용자 위험과 위험 이벤트를 관리합니다.

* 사용자 손상된 경고 전자 메일
* 주간 다이제스트 전자 메일

## <a name="user-compromised-alert-email"></a>사용자 손상된 경고 전자 메일
Azure AD ID 보호가 계정이 손상되었다고 식별하는 경우 사용자 손상된 전자 메일 경고를 생성합니다. 메일은 ID 보호 대시보드에서 위험 보고서에 플래그가 지정된 사용자에 대한 링크를 포함합니다. 손상된 계정에 대한 알림을 즉시 조사하는 것이 좋습니다.

## <a name="weekly-digest-email"></a>주간 다이제스트 전자 메일
주 단위 요약 메일은 새로운 위험 이벤트의 요약을 포함합니다.<br>
다음을 포함합니다.

* 위험에 노출된 사용자
* 의심스러운 활동
* 감지된 취약점
* ID 보호에서 관련된 보고서에 대한 링크

<br>
![재구성](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br>

주 단위 요약 메일 보내기를 끌 수 있습니다.
<br><br>
![사용자 위험](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>

**관련 구성 대화 상자를 열려면**

1. **Azure AD ID 보호** 블레이드에서 **설정**을 클릭합니다.
   <br><br>
   ![사용자 위험 정책](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
   <br>
2. **일반** 섹션에서 **알림**을 클릭합니다.
   <br><br>
   ![사용자 위험 정책](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
   <br>

## <a name="see-also"></a>참고 항목
* [Azure Active Directory ID 보호](active-directory-identityprotection.md)

