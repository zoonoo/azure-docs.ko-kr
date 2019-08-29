---
title: Azure Active Directory 포털의 위험한 로그인 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 위험한 로그인 보고서에 대해 알아보기
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72493400ba20ef3c838a636b66afdf26aa00ff62
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127430"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 위험한 로그인 보고서

Azure Active Directory(Azure AD)는 사용자 계정과 관련된 의심스러운 동작을 감지합니다. 검색 된 각 작업에 대해 **위험 검색** 이라는 레코드가 만들어집니다. 자세한 내용은 [AZURE AD 위험 탐지](concept-risk-events.md)를 참조 하세요. 

[Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 블레이드를 선택하고 **보안** 섹션으로 이동하여 보안 보고서에 액세스할 수 있습니다. 

위험 검색에 따라 계산 되는 두 가지 보안 보고서가 있습니다.

- **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 

![위험한 로그인](./media/concept-risky-sign-ins/10.png)

이러한 위험 검색을 트리거하는 정책을 구성 하는 방법에 대 한 자세한 내용은 [사용자 위험 정책을 구성](../identity-protection/howto-user-risk-policy.md)하는 방법을 참조 하세요.  

## <a name="who-can-access-the-risky-sign-ins-report"></a>위험한 로그인 보고서에 액세스할 수 있는 사용자

위험한 로그인 보고서는 다음 역할의 사용자에게 제공됩니다.

- 보안 관리자
- 전역 관리자
- 보안 읽기 권한자

Azure Active Directory에서 사용자에게 관리 역할을 할당하는 방법을 알아보려면 [Azure Active Directory에서 관리자 역할 보기 및 할당](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)을 참조하세요.

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>보안 보고서에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?  

위험한 로그인 보고서는 모든 Azure AD 버전에서 제공됩니다. 그러나 보고서의 세분성 수준은 다음과 같이 버전에 따라 다릅니다. 

- **Azure Active Directory Free 및 Basic 버전**에서는 위험한 로그인 목록을 볼 수 있습니다. 

- 또한 **Azure Active Directory Premium 1** 버전을 사용 하 여 각 보고서에 대해 검색 된 기본 위험 검색 중 일부를 검사할 수 있습니다. 

- **Azure Active Directory Premium 2** 버전은 모든 기본 위험 검색에 대 한 가장 자세한 정보를 제공 하며, 구성 된 위험 수준에 자동으로 응답 하는 보안 정책을 구성할 수도 있습니다.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Azure AD Free 및 Basic 버전의 위험한 로그인 보고서

Azure AD Free 및 Basic 버전에서는 사용자에 대해 감지된 위험한 로그인 목록이 제공됩니다. 각 레코드는 다음과 같은 특성을 포함합니다.

- **사용자** - 로그인 작업 중에 사용한 사용자의 이름.
- **IP** - Azure Active Directory에 연결하는 데 사용한 디바이스의 IP 주소.
- **위치** - Azure Active Directory에 연결하는 데 사용한 위치. 추적, 레지스트리 데이터, 역방향 조회 및 기타 정보를 기반으로 한 최상의 근사값입니다.
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

- 검색 된 [위험 검색 유형에](concept-risk-events.md) 대 한 집계 된 정보입니다. **Azure AD Premium P1 버전**을 사용 하 여 라이선스에 포함 되지 않은 검색은 **추가 위험이 검색**된 위험 검색 로그인으로 나타납니다. **Azure AD Premium P2 버전**에서 모든 기본 감지에 대한 가장 자세한 정보를 가져옵니다.

- 보고서를 다운로드할 수 있는 옵션

![위험한 로그인](./media/concept-risky-sign-ins/456.png)

위험 검색을 선택 하면 다음 작업을 수행할 수 있는이 위험 검색에 대 한 자세한 보고서 보기가 표시 됩니다.

- [사용자 위험 수정 정책](../identity-protection/howto-user-risk-policy.md)을 구성할 수 있는 옵션  

- 위험 검색에 대 한 검색 타임 라인 검토  

- 이 위험 검색이 검색 된 사용자 목록을 검토 합니다.

- 위험 감지를 수동으로 닫습니다. 

![위험한 로그인](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> 경우에 따라 [로그인 보고서](concept-sign-ins.md)에 해당 하는 로그인 항목 없이 위험 검색을 찾을 수 있습니다. entity Protection은 **대화형** 및 **비 대화형** 로그인의 위험을 모두 평가하는 반면, 로그인 보고서는 대화형 로그인만 보여주기 때문입니다.

사용자를 선택하면 사용자에 대한 자세한 보고서 보기가 제공되고 다음과 같은 작업이 가능합니다.

- 모든 로그인 보기 열기

- 사용자 암호 재설정

- 모든 이벤트 해제

- 사용자에 대해 보고 된 위험 검색을 조사 합니다. 

![위험한 로그인](./media/concept-risky-sign-ins/324.png)

위험 검색을 조사 하려면 목록에서 하나를 선택 합니다.  
그러면이 위험 검색에 대 한 **세부 정보** 블레이드가 열립니다. **세부 정보** 블레이드에서 수동으로 위험 감지를 닫거나 수동으로 종료 된 위험 검색을 다시 활성화 하는 옵션을 사용할 수 있습니다. 

![위험한 로그인](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>다음 단계

- [사용자 위험 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)
- [위험 수정 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)
- [위험 검색 유형](concept-risk-events.md)
