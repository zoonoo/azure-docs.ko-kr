---
title: 포털에서 위험한 로그인 보고서 | 마이크로 소프트 문서
description: Azure Active Directory 포털의 위험한 로그인 보고서에 대해 알아보기
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273833"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 위험한 로그인 보고서

Azure Active Directory(Azure AD)는 사용자 계정과 관련된 의심스러운 동작을 감지합니다. 검색된 각 작업에 대해 **위험 검색이라는** 레코드가 만들어집니다. 자세한 내용은 [Azure AD 위험 검색을](concept-risk-events.md)참조하십시오. 

[Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 블레이드를 선택하고 **보안** 섹션으로 이동하여 보안 보고서에 액세스할 수 있습니다. 

위험 검색을 기반으로 계산되는 두 가지 보안 보고서가 있습니다.

- **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 

![위험한 로그인](./media/concept-risky-sign-ins/10.png)

이러한 위험 검색을 트리거하는 정책을 구성하는 방법에 대해 알아보려면 [사용자 위험 정책을 구성하는 방법을](../identity-protection/howto-user-risk-policy.md)참조하세요.  

## <a name="who-can-access-the-risky-sign-ins-report"></a>위험한 로그인 보고서에 액세스할 수 있는 사용자

위험한 로그인 보고서는 다음 역할의 사용자에게 제공됩니다.

- 보안 관리자
- 전역 관리자
- 보안 판독기

Azure Active Directory에서 사용자에게 관리 역할을 할당하는 방법을 알아보려면 [Azure Active Directory에서 관리자 역할 보기 및 할당](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)을 참조하세요.

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>보안 보고서에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?  

위험한 로그인 보고서는 모든 Azure AD 버전에서 제공됩니다. 그러나 보고서의 세분성 수준은 다음과 같이 버전에 따라 다릅니다. 

- Azure **Active Directory 무료 버전에서는**위험한 로그인 목록을 얻을 수 있습니다. 

- 또한 Azure **Active Directory Premium 1** 버전을 사용하면 각 보고서에 대해 검색된 일부 기본 위험 검색을 검사할 수 있습니다. 

- **Azure Active Directory Premium 2** 에디션은 모든 기본 위험 검색에 대한 가장 자세한 정보를 제공하며 구성된 위험 수준에 자동으로 응답하는 보안 정책을 구성할 수도 있습니다.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Azure AD 무료 버전에 대한 위험한 로그인 보고서

Azure AD 무료 버전은 사용자에 대해 검색된 위험한 로그인 목록을 제공합니다. 각 레코드는 다음과 같은 특성을 포함합니다.

- **사용자** - 로그인 작업 중에 사용된 사용자의 이름입니다.
- **IP** - Azure Active Directory에 연결하는 데 사용된 장치의 IP 주소입니다.
- **위치** - Azure Active Directory에 연결하는 데 사용되는 위치입니다. 추적, 레지스트리 데이터, 역방향 조회 및 기타 정보를 기반으로 한 최상의 근사값입니다.
- **로그인 시간** - 로그인을 수행한 시간
- **상태** - 로그인의 상태

![위험한 로그인](./media/concept-risky-sign-ins/01.png)

위험한 로그인에 대한 조사를 바탕으로 다음과 같은 동작을 수행하여 Azure AD에 피드백을 제공할 수 있습니다.

- 해결
- 가양성으로 표시
- 무시
- 다시 활성화

![위험한 로그인](./media/concept-risky-sign-ins/21.png)

이 보고서는 다음과 같은 옵션도 제공합니다.

- 리소스 검색
- 보고서 데이터 다운로드

![위험한 로그인](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Azure AD Premium 버전의 위험한 로그인 보고서

Azure AD Premium 버전의 위험한 로그인 보고서에서는 다음과 같은 사항이 제공됩니다.

- 검색된 위험 [검색 유형에](concept-risk-events.md) 대한 집계된 정보입니다. Azure **AD Premium P1 버전을**사용하면 라이센스가 적용되지 않는 검색이 **추가 위험이 감지된**위험 감지 로그인으로 나타납니다. **Azure AD Premium P2 버전**에서 모든 기본 감지에 대한 가장 자세한 정보를 가져옵니다.

- 보고서를 다운로드할 수 있는 옵션

![위험한 로그인](./media/concept-risky-sign-ins/456.png)

위험 검색을 선택하면 다음을 수행할 수 있는 이 위험 검색에 대한 자세한 보고서 보기가 확인됩니다.

- [사용자 위험 수정 정책](../identity-protection/howto-user-risk-policy.md)을 구성할 수 있는 옵션  

- 위험 감지를 위한 탐지 일정 검토  

- 이 위험 탐지가 감지된 사용자 목록 검토

- 위험 탐지를 수동으로 닫습니다. 

![위험한 로그인](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> 경우에 따라 로그인 보고서에서 해당 로그인 항목 없이 위험 검색을 찾을 수 [있습니다.](concept-sign-ins.md) entity Protection은 **대화형** 및 **비 대화형** 로그인의 위험을 모두 평가하는 반면, 로그인 보고서는 대화형 로그인만 보여주기 때문입니다.

사용자를 선택하면 사용자에 대한 자세한 보고서 보기가 제공되고 다음과 같은 작업이 가능합니다.

- 모든 로그인 보기 열기

- 사용자 암호 재설정

- 모든 이벤트 해제

- 사용자에 대해 보고된 위험 검색을 조사합니다. 

![위험한 로그인](./media/concept-risky-sign-ins/324.png)

위험 검색을 조사하려면 목록에서 하나를 선택합니다.  
그러면 이 위험 감지를 위한 **세부 정보** 블레이드가 열립니다. 세부 **정보** 블레이드에서 위험 검색을 수동으로 닫거나 수동으로 닫힌 위험 검색을 다시 활성화할 수 있습니다. 

![위험한 로그인](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>다음 단계

- [사용자 위험 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)
- [위험 수정 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)
- [위험 탐지 유형](concept-risk-events.md)
