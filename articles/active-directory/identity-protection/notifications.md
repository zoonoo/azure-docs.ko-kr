---
title: Azure Active Directory ID 보호 알림 | Microsoft Docs
description: 알림에서 조사 활동을 지원하는 방법에 대해 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 애플리케이션 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0065ec03695ee977133ae2ec43aafba7d5bfff78
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784353"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory ID 보호 알림

Azure AD ID 보호는 두 가지 유형의 자동화된 알림 전자 메일을 보내어 사용자 위험과 위험 이벤트를 관리합니다.

- 위험에 노출된 사용자가 검색된 전자 메일
- 주간 다이제스트 전자 메일

이 문서에는 두 알림 전자 메일의 개요가 나와 있습니다.


## <a name="users-at-risk-detected-email"></a>위험에 노출된 사용자가 검색된 전자 메일

위험에 노출된 계정 검색에 대한 응답으로 Azure AD ID 보호는 **위험에 노출된 사용자 검색됨**이란 제목의 전자 메일 경고를 생성합니다. 전자 메일에는 **[위험 플래그가 지정된 사용자](../reports-monitoring/concept-user-at-risk.md)** 보고서에 대한 링크가 포함됩니다. 모범 사례로, 위험에 노출된 사용자를 즉시 조사해야 합니다.

이 경고에 대 한 구성을 사용 하면 경고를 생성 하려는 사용자 위험 수준을 지정할 수 있습니다. 사용자의 위험 수준이 지정한;에 도달 하면 전자 메일 생성 됩니다. 그러나이 사용자 위험 수준으로 이동한 뒤에이 사용자에 대 한 전자 메일 경고를 검색 하는 위험에 노출 된 새 사용자 수신 됩니다. 예를 들어, 중간 사용자 위험과 위험 수준 보통를 이동 하 여 사용자에 대 한 경고를 정책으로 설정한 경우 받게 전자 메일 검색 된 위험에 노출 된 사용자 john. 그러나 다음 John 위험 수준이 높은 이동 하거나 추가 위험 이벤트에 위험이 감지 됨 경고에 두 번째 사용자 수신 됩니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/notifications/01.png)


### <a name="configuration"></a>구성

관리자로서 다음을 설정할 수 있습니다.

- **이 전자 메일의 생성을 트리거하는 사용자 위험 수준** -기본적으로 위험 수준은 "높음" 위험을 설정 합니다.
- **이 전자 메일의 받는 사람** - 기본적으로 받는 사람에는 모든 전역 관리자가 포함됩니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.  


관련된 대화 상자를 열려면 **ID 보호** 페이지에서 **설정** 섹션의 **경고**를 클릭합니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>주간 다이제스트 전자 메일

주 단위 요약 메일은 새로운 위험 이벤트의 요약을 포함합니다.  
 다음을 포함합니다.

- 위험에 노출된 사용자

- 의심스러운 활동

- 감지된 취약점

- ID 보호에서 관련된 보고서에 대한 링크

    ![수정](./media/notifications/400.png "수정")

### <a name="configuration"></a>구성

관리자로서, 주 단위 요약 메일 보내기를 끌 수 있습니다.

![사용자 위험](./media/notifications/62.png "사용자 위험")

관련 대화 상자를 열려면 **ID 보호** 페이지에서 **설정** 섹션의 **주 단위 요약**을 클릭합니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/notifications/04.png)


## <a name="see-also"></a>참고 항목

- [Azure Active Directory ID 보호](../active-directory-identityprotection.md)
