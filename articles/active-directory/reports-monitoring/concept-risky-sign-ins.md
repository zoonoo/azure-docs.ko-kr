---
title: Azure Active Directory 포털의 위험한 로그인 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 위험한 로그인 보고서에 대해 알아보기
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e4406a75ea1d9f1968d994ae2294b39ca7613d5
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623862"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 위험한 로그인 보고서

Azure Active Directory(Azure AD)는 사용자 계정과 관련된 의심스러운 동작을 감지합니다. 감지된 각 동작에 대해 **위험 이벤트**라는 레코드가 생성됩니다. 자세한 내용은 [Azure AD 위험 이벤트](concept-risk-events.md)를 참조하세요. 

[Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 블레이드를 선택하고 **보안** 섹션으로 이동하여 보안 보고서에 액세스할 수 있습니다. 

위험 이벤트를 바탕으로 계산되는 보안 보고서에는 두 가지 유형이 있습니다.

- **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 

![위험한 로그인](./media/concept-risky-sign-ins/10.png)

이러한 위험 이벤트를 트리거하는 정책을 구성하는 방법을 알아보려면 [사용자 위험 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)을 참조하세요.  

## <a name="who-can-access-the-risky-sign-ins-report"></a>위험한 로그인 보고서에 액세스할 수 있는 사용자

위험한 로그인 보고서는 다음 역할의 사용자에게 제공됩니다.

- 보안 관리자
- 전역 관리자
- 보안 읽기 권한자

Azure Active Directory에서 사용자에게 관리 역할을 할당하는 방법을 알아보려면 [Azure Active Directory에서 관리자 역할 보기 및 할당](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)을 참조하세요.

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>보안 보고서에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?  

위험한 로그인 보고서는 모든 Azure AD 버전에서 제공됩니다. 그러나 보고서의 세분성 수준은 다음과 같이 버전에 따라 다릅니다. 

- **Azure Active Directory Free 및 Basic 버전**에서는 위험한 로그인 목록을 볼 수 있습니다. 

- **Azure Active Directory Premium 1** 버전에서는 여기에 더해 각 보고서에서 감지된 일부 기본 위험 이벤트를 살펴볼 수 있습니다. 

- **Azure Active Directory Premium 2** 버전은 모든 기본 위험 이벤트에 대한 가장 자세한 정보를 제공하며, 구성된 위험 수준에 자동으로 응답하는 보안 정책을 구성할 수도 있습니다.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Azure AD Free 및 Basic 버전의 위험한 로그인 보고서

Azure AD Free 및 Basic 버전에서는 사용자에 대해 감지된 위험한 로그인 목록이 제공됩니다. 각 레코드는 다음과 같은 특성을 포함합니다.

- **사용자** - 로그인 작업 중에 사용한 사용자의 이름
- **IP** - Azure Active Directory에 연결하는 데 사용한 장치의 IP 주소
- **위치** - Azure Active Directory에 연결하는 데 사용한 위치
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

- 감지된 [위험 이벤트 유형](concept-risk-events.md)에 대해 집계된 정보

- 보고서를 다운로드할 수 있는 옵션

![위험한 로그인](./media/concept-risky-sign-ins/456.png)

위험 이벤트를 선택하면 위험 이벤트에 대한 자세한 보고서 보기가 제공되고 다음과 같은 작업이 가능합니다.

- [사용자 위험 수정 정책](../identity-protection/howto-user-risk-policy.md)을 구성할 수 있는 옵션  

- 위험 이벤트에 대한 감지 타임라인 검토  

- 이 이벤트가 감지된 사용자 목록 검토

- 위험 이벤트를 수동으로 닫습니다. 

![위험한 로그인](./media/concept-risky-sign-ins/457.png)

사용자를 선택하면 사용자에 대한 자세한 보고서 보기가 제공되고 다음과 같은 작업이 가능합니다.

- 모든 로그인 보기 열기

- 사용자 암호 재설정

- 모든 이벤트 해제

- 사용자에 대해 보고된 위험 이벤트 조사. 

![위험한 로그인](./media/concept-risky-sign-ins/324.png)

위험 이벤트를 조사하려면 목록에서 위험 이벤트를 선택합니다.  
그러면 위험 이벤트에 대한 **세부 정보** 블레이드가 열립니다. **세부 정보** 블레이드에 위험 이벤트를 수동으로 닫거나 수동으로 닫은 위험 이벤트를 다시 활성화할 수 있는 옵션이 있습니다. 

![위험한 로그인](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>다음 단계

- [사용자 위험 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)
- [위험 수정 정책을 구성하는 방법](../identity-protection/howto-user-risk-policy.md)
- [위험 이벤트 유형](concept-risk-events.md)