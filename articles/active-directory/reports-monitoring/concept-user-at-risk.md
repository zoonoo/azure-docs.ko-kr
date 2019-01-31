---
title: Azure Active Directory 포털의 위험에 대한 플래그가 지정된 사용자 보안 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 위험에 대한 플래그가 지정된 사용자 보안 보고서에 대해 알아보기
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8f9a4b95feadad873bb87cb9071dd98f597d839a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194001"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Azure Portal에서 위험 플래그가 지정된 사용자 보고서

Azure AD(Azure Active Directory)는 사용자 계정과 관련된 의심스러운 작업을 감지합니다. 작업이 감지된 경우 [위험 이벤트](concept-risk-events.md)라는 레코드가 만들어집니다.

[Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 블레이드를 선택하고 **보안** 섹션으로 이동하여 보안 보고서에 액세스할 수 있습니다. 

감지된 위험 이벤트를 계산하는 데 사용합니다.

- **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다. 

- **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 

이러한 위험 이벤트를 트리거하는 정책을 구성하는 방법을 알아보려면 [사용자 위험 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)을 참조하세요. 

![위험한 로그인](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>위험에 노출된 사용자 보고서에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?  

모든 Azure Active Directory 버전에서 위험 플래그가 지정된 사용자 보고서를 제공합니다. 그러나 보고서의 세분성 수준은 다음과 같이 버전에 따라 다릅니다. 

- **Azure Active Directory 평가판 및 기본 버전**에는 위험 플래그가 지정된 사용자 목록이 있습니다. 

- **Azure Active Directory Premium 1** 버전에서는 여기에 더해 각 보고서에서 감지된 일부 기본 위험 이벤트를 살펴볼 수 있습니다. 

- **Azure Active Directory Premium 2** 버전은 모든 기본 위험 이벤트에 대한 가장 자세한 정보를 제공하며, 구성된 위험 수준에 자동으로 응답하는 보안 정책을 구성할 수도 있습니다.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Azure AD 평가판 및 기본 버전에 대한 위험에 노출된 사용자 보고서

Azure AD 평가판 및 기본 버전의 위험 플래그가 지정된 사용자 보고서에는 손상되었을 수 있는 사용자 계정 목록이 제공됩니다. 

![위험한 로그인](./media/concept-user-at-risk/03.png)

사용자를 선택하면 로그인 정보를 제공합니다. 위험에 노출된 사용자에 대해 사용자의 로그인 기록을 검토하고 필요한 경우 암호를 다시 설정할 수 있습니다.

이 대화 상자는 다음과 같은 옵션을 제공합니다.

- 보고서 다운로드
- 사용자 검색

    ![위험한 로그인](./media/concept-user-at-risk/16.png)

자세한 내용을 보려면 프리미엄 라이선스가 필요합니다.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Azure AD 프리미엄 버전에 대한 위험에 노출된 사용자 보고서

Azure AD 프리미엄 버전에서 위험 플래그가 지정된 사용자 보고서에서는 다음을 제공합니다.

- 손상되었을 수 있는 사용자 계정 목록 

- 감지된 [위험 이벤트 유형](concept-risk-events.md)에 대해 집계된 정보

- 보고서를 다운로드할 수 있는 옵션

- [사용자 위험 수정 정책](../identity-protection/howto-user-risk-policy.md)을 구성할 수 있는 옵션  

![위험한 로그인](./media/concept-user-at-risk/71.png)

사용자를 선택하면 사용자에 대한 자세한 보고서 보기가 제공되고 다음과 같은 작업이 가능합니다.

- 모든 로그인 보기 열기

- 사용자 암호 재설정

- 모든 이벤트 해제

- 사용자에 대해 보고된 위험 이벤트 조사. 

![위험한 로그인](./media/concept-user-at-risk/324.png)

위험 이벤트를 조사하려면 목록에서 이벤트를 하나 선택하고 이 위험 이벤트에 대한 **세부 정보** 블레이드를 엽니다. **세부 정보** 블레이드에 위험 이벤트를 수동으로 닫거나 수동으로 닫은 위험 이벤트를 다시 활성화할 수 있는 옵션이 있습니다. 

![위험한 로그인](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>다음 단계

- [사용자 위험 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)
- [위험 수정 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory ID 보호](../active-directory-identityprotection.md)

