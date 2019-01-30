---
title: Azure Active Directory B2B 공동 작업 사용자 감사 및 보고 | Microsoft Docs
description: Azure Active Directory B2B 공동 작업에서 구성 가능한 게스트 사용자 속성
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: a31f9a5a0f613d6c70e8c95e584d8caca87e93be
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434158"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>B2B 공동 작업 사용자 감사 및 보고
게스트 사용자를 사용하여 구성원 사용자와 유사한 감사 기능을 얻을 수 있습니다. 

## <a name="access-reviews"></a>액세스 검토
액세스 검토를 사용하여 게스트 사용자가 사용자의 리소스에 계속 액세스해야 하는지 여부를 주기적으로 확인할 수 있습니다. **액세스 검토** 기능은 **Azure Active Directory**의 **관리** > **조직 관계**에서 사용할 수 있습니다. Azure Portal의 **모든 서비스**에서 "액세스 검토"를 검색할 수도 있습니다. 액세스 검토를 사용하는 방법은 [Azure AD 액세스 검토를 사용하여 게스트 액세스 관리](../governance/manage-guest-access-with-access-reviews.md)를 참조하세요.

## <a name="audit-logs"></a>감사 로그

Azure AD 감사 로그는 게스트 사용자가 시작한 작업을 비롯하여 시스템 및 사용자 작업에 대한 기록을 제공합니다. 감사 로그에 액세스하려면 **Azure Active Directory**의 **모니터링** 아래에서 **감사 로그**를 선택합니다. 다음은 초대한 Sam Oogle의 초대 및 상환 기록에 대한 예제입니다.

![감사 로그](./media/auditing-and-reporting/audit-log.png)

이러한 각 이벤트를 심층 분석하여 자세한 정보를 얻을 수 있습니다. 예를 들어 승인 세부 정보를 살펴보겠습니다.

![활동 세부 정보](./media/auditing-and-reporting/activity-details.png)

Azure AD에서 이러한 로그를 내보낸 후 원하는 보고 도구를 사용하여 익숙한 보고서를 얻을 수도 있습니다.

### <a name="next-steps"></a>다음 단계

- [B2B 공동 작업 사용자 속성](user-properties.md)

