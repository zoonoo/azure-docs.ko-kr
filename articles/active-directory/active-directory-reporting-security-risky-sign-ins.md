---
title: "Azure Active Directory 포털 – 미리 보기의 위험한 로그인 보고서 | Microsoft Docs"
description: "Azure Active Directory 포털 – 미리 보기의 위험한 로그인 보고서에 대해 알아보기"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 349109e0c12a1394f96529a94ab884eeb451d242
ms.openlocfilehash: 69b2166dcbc3e4abd99084b47907c90e157791de
ms.lasthandoff: 02/22/2017


---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal---preview"></a>Azure Active Directory 포털 – 미리 보기의 위험한 로그인 보고서

Azure Active Directory [미리 보기](active-directory-preview-explainer.md)의 보안 보고를 사용하여 사용자 환경에서 손상된 사용자 계정의 확률에 대한 정보를 얻을 수 있습니다. 

Azure Active Directory는 사용자 계정에 관련된 의심스러운 작업을 감지합니다. 작업이 감지된 경우 *위험 이벤트*라는 레코드가 만들어집니다. 자세한 내용은 [Azure Active Directory 위험 이벤트](active-directory-identity-protection-risk-events.md)를 참조하세요. 

감지된 위험 이벤트를 계산하는 데 사용합니다.

- **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다. 자세한 내용은 [위험한 로그인](active-directory-identityprotection.md#risky-sign-ins)을 참조하세요. 

- **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 자세한 내용은 [위험 플래그가 지정된 사용자](active-directory-identityprotection.md#users-flagged-for-risk)를 참조하세요.  

Azure Portal의 **보안** 섹션에서 **Azure Active Directory** 블레이드에 대한 보안 보고서를 찾을 수 있습니다. 

![위험한 로그인](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory 무료 및 기본 버전

Azure Active Directory 무료 및 기본 버전은 사용자에 대해 감지된 위험한 로그인 목록을 제공합니다. 위험 이벤트 보고서는 다음을 제공합니다.

- **사용자** - 로그인 작업 중에 사용한 사용자의 이름
- **IP** - Azure Active Directory에 연결하는 데 사용한 장치의 IP 주소
- **위치** - Azure Active Directory에 연결하는 데 사용한 위치
- **로그인 시간** - 로그인을 수행한 시간
- **상태** - 로그인의 상태

이 보고서는 보고서 데이터를 다운로드하는 옵션을 제공합니다.

![위험한 로그인](./media/active-directory-reporting-security-risky-sign-ins/01.png)

위험한 로그인의 조사에 따르면 다음 작업의 형식으로 Azure Active Directory에 대한 피드백을 제공할 수 있습니다.

- 해결
- 가양성으로 표시
- 무시
- 다시 활성화

![위험한 로그인](./media/active-directory-reporting-security-risky-sign-ins/21.png)

자세한 내용은 [수동으로 위험 이벤트 닫기](active-directory-identityprotection.md#closing-risk-events-manually)를 참조하세요.

## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory Premium Edition

Azure Active Directory Premium Edition의 위험한 로그인 보고서에 제공되는 내용:

- 감지된 [위험 이벤트 유형](active-directory-identity-protection-risk-events.md)에 대해 집계된 정보

- 보고서를 다운로드할 수 있는 옵션


![위험한 로그인](./media/active-directory-reporting-security-risky-sign-ins/456.png)


위험 이벤트를 선택하면 위험 이벤트에 대한 자세한 보고서 보기가 제공되고 다음과 같은 작업이 가능합니다.

- [사용자 위험 수정 정책](active-directory-identityprotection.md#user-risk-security-policy)을 구성할 수 있는 옵션  

- 위험 이벤트에 대한 감지 타임라인 검토  

- 이 이벤트가 감지된 사용자 목록 검토

- [위험 이벤트를 수동으로 닫거나](active-directory-identityprotection.md#closing-risk-events-manually) 수동으로 닫은 위험 이벤트를 다시 활성화 


![위험한 로그인](./media/active-directory-reporting-security-risky-sign-ins/457.png)

사용자를 선택하면 사용자에 대한 자세한 보고서 보기가 제공되고 다음과 같은 작업이 가능합니다.

- 모든 로그인 보기 열기

- 사용자 암호 재설정

- 모든 이벤트 해제

- 사용자에 대해 보고된 위험 이벤트 조사. 


![위험한 로그인](./media/active-directory-reporting-security-risky-sign-ins/324.png)


위험 이벤트를 조사하려면 목록에서 위험 이벤트를 선택합니다.  
그러면 위험 이벤트에 대한 **세부 정보** 블레이드가 열립니다. **세부 정보** 블레이드에 [위험 이벤트를 수동으로 닫거나](active-directory-identityprotection.md#closing-risk-events-manually) 수동으로 닫은 위험 이벤트를 다시 활성화할 수 있는 옵션이 있습니다. 


![위험한 로그인](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>다음 단계

- Azure Active Directory ID 보호에 대한 자세한 내용은 [Azure Active Directory ID 보호](active-directory-identityprotection.md)를 참조하세요.


