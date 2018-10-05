---
title: Azure AD의 ID 보안 점수란? - 미리 보기 | Microsoft Docs
description: ID 보안 점수를 사용하여 Azure AD 테넌트의 보안 상태를 개선하는 방법에 대해 알아봅니다.
services: active-directory
keywords: ID 보안 점수, Azure AD, 회사 리소스에 대한 보안 액세스
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a9bb22cae2e9fcd607a81d442f4fb51f12c43454
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224140"
---
# <a name="what-is-the-identity-secure-score-in-azure-ad---preview"></a>Azure AD의 ID 보안 점수란? - 미리 보기

Azure AD 테넌트는 얼마나 안전하나요? 이 질문에 답변하는 방법을 모르는 경우 이 문서를 읽고 ID 보안 점수가 ID 보안 상태를 모니터링하고 개선하는 데 어떻게 도움이 되는지 알아봅니다. 

## <a name="what-is-an-identity-secure-score"></a>ID 보안 점수란?

ID 보안 점수는 1에서 248 사이의 숫자로서, 사용자가 보안에 대한 Microsoft의 모범 사례 권장 사항과 얼마나 일치하는지를 나타냅니다.


![보안 점수](./media/identity-secure-score/01.png)



점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정

- ID 보안 개선 계획

- 개선 계획의 성공 여부 검토 


ID 보안 점수 대시보드에서 점수 및 관련 정보에 액세스할 수 있습니다. 이 대시보드에서 다음을 찾을 있습니다.

- 점수

    ![보안 점수](./media/identity-secure-score/02.png)

- 비교 그래프

    ![보안 점수](./media/identity-secure-score/03.png)

- 추세 그래프

    ![보안 점수](./media/identity-secure-score/04.png)

- ID 보안 모범 사례 목록입니다. 

    ![보안 점수](./media/identity-secure-score/05.png)


개선 작업을 따라 다음을 수행할 수 있습니다.

- 보안 상태 및 점수를 개선합니다.
 
- Microsoft의 ID 기능을 활용합니다. 



## <a name="how-do-i-get-my-secure-score"></a>내 보안 점수는 어떻게 가져오나요?

ID 보안 점수는 Azure AD의 모든 버전에서 사용할 수 있습니다.

점수에 액세스하려면 [Azure AD 개요 대시보드](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore)로 이동합니다.



## <a name="how-does-it-work"></a>작동 원리

Azure에서는 48시간마다 사용자 보안 구성을 살펴보고 사용자 설정과 권장 모범 사례를 비교합니다. 이 평가 결과에 따라 테넌트에 대한 새 점수가 계산됩니다. 즉, 사용자가 변경한 구성 내용이 점수에 반영될 때까지는 최대 48시간이 걸릴 수 있습니다. 

각 권장 사항은 Azure AD 구성에 따라 측정됩니다. 타사 제품을 사용하여 모범 사례 권장 사항을 사용하도록 설정하는 경우 개선 작업의 설정에서 이 권장 사항을 지정할 수 있습니다.

![보안 점수](./media/identity-secure-score/07.png)


또한 권장 사항이 사용자 환경에 적용되지 않는 경우 이를 무시하도록 설정하는 옵션도 있습니다. 무시된 권장 사항은 점수 계산에 포함되지 않습니다. 
 
![보안 점수](./media/identity-secure-score/06.png)



## <a name="how-does-it-help-me"></a>보안 점수가 어떻게 도움이 되나요?

보안 점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정

- ID 보안 개선 계획

- 개선 계획의 성공 여부 검토



## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="who-can-use-the-identity-secure-score"></a>ID 보안 점수는 누가 사용할 수 있나요?

ID 보안 점수는 다음과 같은 역할에 의해 사용될 수 있습니다.

- 글로벌 관리자
- 보안 관리자 
- 보안 읽기 권한자 

### <a name="what-does-not-scored-mean"></a>[점수가 매겨지지 않음]은 어떤 의미인가요?

[점수가 매겨지지 않음]으로 레이블이 지정된 작업은 조직에서 수행할 수 있지만 (아직!) 도구에 연결되지 않아 점수가 매겨지지 않는 작업입니다. 따라서 여전히 보안을 개선할 수는 있지만 해당 작업에 대한 크레딧을 지금 당장 받을 수는 없습니다.

### <a name="how-often-is-my-score-updated"></a>내 점수가 업데이트되는 빈도는?

점수는 하루에 한 번(약 오전 1시 PST) 계산됩니다. 측정된 작업을 변경하는 경우 점수는 다음날 자동으로 업데이트됩니다. 변경 내용이 점수에 반영되는 데 최대 48시간이 걸립니다.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>내 점수가 변경됐습니다. 변경 이유를 어떻게 알 수 있나요?

변경 내용을 확인하려면 [보안 점수 포털](https://securescore.microsoft.com/#!/score)의 점수 분석기 페이지에서 특정한 날의 데이터 요소를 클릭한 다음, 그 날의 완료된 작업 및 완료되지 않은 작업을 아래로 스크롤하여 확인합니다.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>보안 점수는 내가 보안을 위반할 위험도 측정하나요?

간단히 말해 아닙니다. 보안 점수는 위반할 가능성에 대한 측정을 절대값으로 표시하지 않습니다. 보안 점수는 보안을 위반할 위험을 상쇄할 수 있는 기능의 채택 정도를 표시합니다. 어떤 서비스도 위반하지 않는다는 것을 보장하지 못합니다. 보안 점수는 결코 이를 보장하는 것으로 해석해서는 안 됩니다.

### <a name="how-should-i-interpret-my-score"></a>내 점수는 어떻게 해석해야 하나요?

권장된 보안 기능을 구성하거나 보안 관련 작업(예: 보고서 읽기)을 수행하면 점수를 받습니다. 일부 작업은 사용자에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정하는 것처럼 부분 완료에 대해 점수가 매겨집니다. 보안 점수는 사용하는 Microsoft 보안 서비스를 직접적으로 상징합니다. 보안은 항상 유용성과 균형을 이루어야 합니다. 모든 보안 제어에는 사용자 영향 구성 요소가 있습니다. 사용자 영향이 낮은 제어는 사용자의 일상적인 작업에 거의 영향을 미치지 않습니다.

점수 기록을 확인하려면 [보안 점수 포털](https://securescore.microsoft.com/#!/score)의 점수 분석기 페이지로 이동합니다. 특정 날짜를 선택하여 해당 날짜에 사용하도록 설정된 제어 및 각 제어에 대해 획득한 점수를 확인합니다.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>ID 보안 점수는 Office 365 보안 점수와 어떤 관계가 있나요? 

[Office 365 보안 점수](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score)는 5개의 다른 점수의 집계로 마이그레이션되어야 합니다.

- ID

- Data

- 장치

- 인프라

- 앱

ID 보안 점수는 Office 365 보안 점수의 ID 부분을 나타냅니다. 즉, ID 보안 점수 및 Office 365의 보안 점수에 대한 권장 사항은 동일합니다. 


## <a name="next-steps"></a>다음 단계

Office 365 보안 점수에 대한 비디오를 참조하려는 경우 [여기](https://www.youtube.com/watch?v=jzfpDJ9Kg-A)를 클릭합니다.
 
