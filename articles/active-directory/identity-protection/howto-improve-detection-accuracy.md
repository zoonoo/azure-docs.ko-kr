---
title: Azure Active Directory Identity Protection에서 탐지 정확도를 개선하는 방법(새로 고침) | Microsoft Docs
description: Azure Active Directory Identity Protection에서 탐지 정확도를 개선하는 방법(새로 고침)을 설명합니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 애플리케이션 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7724d69a9294b420ca061d5ad26ad64826372203
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60453296"
---
# <a name="how-to-improve-the-detection-accuracy"></a>방법: 탐지 정확도 개선 

Identity Protection에는 운영 환경에서 탐지된 위험에 관해 Azure AD에 피드백을 제공하는 메커니즘이 있습니다. 피드백을 제공하려면 탐지된 위험한 사용자 또는 로그인 이벤트의 상태를 확인하면 됩니다. Microsoft는 이 피드백을 사용하여 현재 탐지된 위험에 대한 조치를 취하고 향후 탐지의 정확도를 개선합니다. 


## <a name="what-is-detection"></a>탐지란 무엇인가요?

탐지는 사용자 계정과 함께 의심스러운 활동을 식별하는 프로세스입니다. Azure AD가 탐지할 수 있는 의심스러운 활동을 [위험 이벤트](../reports-monitoring/concept-risk-events.md)라고 합니다. 탐지 프로세스는 사용자의 위험 이벤트를 탐지하기 위한 적응형 기계 학습 알고리즘 및 추론을 기반으로 합니다.

탐지 결과는 사용자 및 로그인이 위험에 처했는지 여부를 판별하는 데 사용됩니다. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>탐지 정확도를 개선하려면 어떻게 할까요?

탐지는 자동화된 프로세스이므로 Azure AD에서 가양성을 보고할 수 있습니다. 탐지 결과에 대한 피드백을 Azure AD에 제공하여 탐지 정확도를 개선할 수 있습니다.

탐지 정확도를 개선하는 세 가지 방법은 손상된 로그인을 확인하고, 안전 로그인을 확인하고, 사용자 위험을 해제하는 것입니다. 다음 보고서에서 이 작업을 수행할 수 있습니다.

- **위험한 로그인 보고서 -** 위험한 로그인 보고서에서 로그인이 안전한지 또는 손상되었는지 확인할 수 있습니다.

- **위험한 사용자 보고서 -** 위험 사용자 보고서에서 사용자 위험을 해제할 수 있습니다. 

Azure AD에서 피드백을 처리하여 탐지 결과의 정확도를 개선합니다. 일반적으로 사용자 위험 또는 로그인 위험 조사의 일부로 피드백을 제공합니다. 자세한 내용은 [위험한 사용자 및 로그인을 조사하는 방법](howto-investigate-risky-users-signins.md)을 참조하세요.


## <a name="confirm-compromised"></a>손상됨 확인

로그인 이벤트를 손상됨으로 확인하면 ID 소유자가 로그인에 권한을 부여하지 않았다는 신호가 Azure AD에 전달됩니다. “손상됨 확인”을 선택하면 Azure AD에서 다음을 수행합니다.

- 영향을 받는 사용자의 위험 수준을 높음으로 높입니다.

- 위험 이벤트를 탐지하는 기계 학습을 최적화해 줍니다.
 
- 추가 조치를 취해 조직을 더욱 강력하게 보호합니다.



손상된 로그인을 확인하려면 다음을 수행합니다.

- **위험한 로그인 보고서** - 이 옵션을 사용하면 하나 이상의 로그인 이벤트에 대한 손상된 로그인을 확인할 수 있습니다.

    ![사용자 위험 해제](./media/howto-improve-detection-accuracy/07.png)

- **위험한 로그인 보고서의 세부 정보 보기** - 이 옵션을 사용하면 위험한 로그인 보고서에서 선택한 로그인 이벤트에 대한 손상된 계정을 확인할 수 있습니다. 

    ![사용자 위험 해제](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>안전 확인


로그인 이벤트를 안전으로 확인하면 각 ID 소유자가 로그인에 권한을 부여**했다**는 신호가 Azure AD에 전달됩니다. “안전 확인”을 선택하면 Azure AD가 다음을 수행합니다.

- 선택한 로그인의 사용자 위험 기여도를 되돌립니다.

- 기본 위험 이벤트를 닫습니다.

- 위험 이벤트를 탐지하는 기계 학습을 최적화해 줍니다.

- 추가 조치를 취해 조직을 더욱 강력하게 보호합니다.
 

안전 로그인을 확인하려면 다음을 수행합니다.

- **위험한 로그인 보고서** - 이 옵션을 사용하면 하나 이상의 로그인 이벤트에 대한 안전 로그인을 확인할 수 있습니다.

    ![사용자 위험 해제](./media/howto-improve-detection-accuracy/08.png)

- **위험한 로그인 보고서의 세부 정보 보기** - 이 옵션을 사용하면 위험한 로그인 보고서에서 선택한 로그인 이벤트에 대한 안전 로그인을 확인할 수 있습니다. 

    ![사용자 위험 해제](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>사용자 위험 해제

위험한 사용자에 대한 문제 해결 조치를 이미 취했거나 위험한 것으로 잘못 표시되었다고 판단되면 사용자의 위험을 해제할 수 있습니다. 사용자 위험을 해제하면 사용자가 위험하지 않은 상태로 복원됩니다. 선택한 사용자에 해당하는 과거의 모든 위험한 로그인 및 위험 이벤트가 해제됩니다.


다음에서 보고된 사용자 위험을 해제할 수 있습니다.

- **위험한 사용자 보고서** - 이 옵션을 사용하면 선택된 사용자 한 명 이상의 위험을 해제할 수 있습니다.

    ![사용자 위험 해제](./media/howto-improve-detection-accuracy/02.png)

- **세부 정보 보기** - 이 옵션을 사용하면 사용자 위험 보고서에서 선택된 사용자의 위험을 해제할 수 있습니다. 

    ![사용자 위험 해제](./media/howto-improve-detection-accuracy/01.png)


**알아야 할 사항:**

- 이 작업은 되돌릴 수 없습니다.

- 이 작업을 완료하는 데 몇 분이 걸릴 수 있으므로 요청을 다시 제출하면 안 됩니다.

- AD가 사용자의 자격 증명을 관리하는 경우에만 이 작업을 수행할 수 있습니다. 



## <a name="best-practices"></a>모범 사례

사용자 위험 해제는 사용자가 사용자 위험 정책에 의해 차단되었고 암호 재설정 및/또는 MFA를 사용하도록 설정하지 않아 직접 수정할 수 없는 경우 사용자의 차단을 해제하는 한 가지 방법입니다. 이 경우 사용자가 암호 재설정 및 MFA에 등록하여 향후 발생하는 모든 위험 이벤트를 직접 수정할 수 있도록 하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview-v2.md)를 참조하세요.


