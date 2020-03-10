---
title: Azure Security Center의 보안 점수 | Microsoft Docs
description: " Azure Security Center 보안 점수를 사용 하 여 보안 권장 사항의 우선 순위를 지정 합니다. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 15a94a9724fac29d36f6bb88ee4810b3bc7ca607
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394659"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Azure Security Center의 보안 점수 향상

> [!NOTE]
> 미리 보기에서는 향상 된 보안 점수를 사용할 수 있습니다. 보안이 강화 된 보안 점수는 궁극적으로 기존 보안 점수를 대체 하지만,이를 병렬로 실행 하 여 쉽게 전환할 수 있습니다.
>
> 향상 된 보안 점수의 이점에 대 한 자세한 내용은 [여기](secure-score-security-controls.md)를 참조 하세요.
>
> 미리 보기에 참여 하려면 Azure Portal을 열고 Azure Security Center를 시작 하 고 보안 점수를 선택 합니다. 여기에서 새로운 보안 점수 경험을 제공 하는 페이지 맨 위에 배너가 표시 됩니다. 또는 [여기](https://aka.ms/ascnewscore)를 클릭 하십시오.

보안 혜택을 제공하는 서비스는 매우 많으므로 워크로드를 보호하고 강화하기 위해 먼저 수행할 단계를 아는 것은 종종 어려운 일입니다. 보안 점수는 보안 권장 사항을 검토 하 고 우선 순위를 매기 므로 먼저 수행할 권장 사항을 알고 있습니다. 이를 통해 가장 심각한 보안 취약점을 찾아내 조사의 순위를 지정할 수 있습니다. 보안 점수는 워크 로드 보안 상태를 평가 하는 데 도움이 되는 도구입니다.

## <a name="secure-score-calculation"></a>보안 점수 계산

Security Center는 보안 권장 사항을 검토하고 고급 알고리즘을 적용하여 각 권장 사항의 중요도를 판별하는 보안 분석가의 작업을 모방합니다.
Azure Security center는 활성 권장 사항을 지속적으로 검토 하 고 이러한 권장 사항을 기반으로 하 여 보안 점수를 계산 합니다. 권장 사항의 점수는 가장 많은 워크 로드 보안에 영향을 주는 심각도 및 보안 모범 사례에서 파생 됩니다.

또한 Security Center는 **전체 보안 점수**를 제공 합니다. 

**전반적인 보안 점수** 는 모든 추천 점수를 누적 한 것입니다. 선택한 항목에 따라 구독 또는 관리 그룹에서 전체 보안 점수를 볼 수 있습니다. 점수는 선택되어 있는 구독과 이 구독에 대한 활성 권장 사항에 따라 달라집니다.

보안 점수에 영향을 주는 권장 사항을 확인 하려면 Security Center 대시보드에서 가장 자주 사용 하는 세 가지 주요 권장 사항을 확인 하거나 **보안 점수 영향** 열을 사용 하 여 권장 구성 목록 블레이드에서 권장 사항을 정렬할 수 있습니다.

전체 보안 점수를 보려면 다음을 수행 합니다.

1. Azure 대시보드에서 **Security Center** 를 클릭 한 다음 **보안 점수**를 클릭 합니다.

2. 위쪽에서 보안 점수 하이라이트를 볼 수 있습니다.
   - **전반적인 보안 점수** 는 선택한 구독 당 정책 당 점수를 나타냅니다.
   - **범주별로 보안 점수** 를 표시 하 여 가장 많은 주의가 필요한 리소스를 표시 합니다.
   - **보안 점수 영향과 관련 된 최고 권장 사항은** 보안 점수를 구현 하는 경우 가장 높은 점수를 높일 수 있는 권장 사항 목록을 제공 합니다.
 
   ![보안 점수](./media/security-center-secure-score/secure-score-dashboard.png)

3. 아래 표에서 각 구독과 각 구독에 대 한 전체 보안 점수를 확인할 수 있습니다.

   > [!NOTE]
   > 각 구독에 대 한 보안 점수의 합계는 전체 보안 점수와 동일 하지 않습니다. 보안 점수는 구독 전체에서 보안 점수가 합산 되는 것이 아니라 정상 리소스와 권장 구성 당 총 리소스 간의 비율을 기반으로 하는 계산입니다. 
   >
4. **권장 사항 보기** 를 클릭 하 여 보안 점수를 개선 하기 위해 수정할 수 있는 구독에 대 한 권장 사항을 확인 합니다.
4. 권장 사항 목록에서 각 권장 사항에 대해 **보안 점수 영향**을 나타내는 열이 있음을 알 수 있습니다. 권장 사항을 따르는 경우이 숫자는 전반적인 보안 점수를 개선 하는 정도를 나타냅니다. 예를 들어 아래 화면에서 **컨테이너 보안 구성의 취약성**을 수정 하는 경우 보안 점수가 35 포인트로 증가 합니다.

   ![보안 점수](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>개별 보안 점수

또한 개별 보안 점수를 보려면 개별 권장 사항 블레이드 내에서 찾을 수 있습니다.  

**권장 보안 점수** 는 정상 리소스와 총 리소스 간의 비율을 기반으로 하는 계산입니다. 정상 리소스의 수가 총 리소스 수와 같으면 50 권장 구성의 최대 보안 점수를 얻을 수 있습니다. 최대 점수에 더 가까운 보안 점수를 얻으려면 권장 사항에 따라 비정상 리소스를 수정 하세요.

권장 **사항에 영향** 을 주어 권장 단계를 적용 하면 보안 점수를 얼마나 향상 시킬 수 있는지 알 수 있습니다. 예를 들어 보안 점수가 42이 고 **권장 사항** 에 대 한 영향이 + 3 인 경우 권장 사항에 설명 된 단계를 수행 하면 점수가 45이 됩니다.

권장 사항은 수정 절차가 수행되지 않는 경우 워크로드가 노출되는 위협을 보여 줍니다.

![개별 권장 사항 보안 점수](./media/security-center-secure-score/indiv-recommendation-secure-score.png)




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center 보안 **점수** 를 사용 하 여 보안 상태를 개선 하는 방법을 살펴보았습니다. Security Center에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [AZURE SECURITY CENTER FAQ](faq-general.md)--서비스 및 보안 점수에 대 한 질문과 대답을 찾습니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [보안 점수-고급](secure-score-security-controls.md)--현재 미리 보기에서 향상 된 보안 점수의 이점에 대해 알아봅니다.