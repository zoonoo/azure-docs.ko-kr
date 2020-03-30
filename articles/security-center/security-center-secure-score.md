---
title: Azure 보안 센터의 보안 점수 | 마이크로 소프트 문서
description: " Azure 보안 센터의 보안 점수를 사용하여 보안 권장 사항의 우선 순위를 지정합니다. "
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
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415760"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Azure Security Center의 보안 점수 개선

> [!NOTE]
> 이 문서는 보안 점수의 이전 버전에 대 한 입니다. 이 보안 점수 환경은 UI에서 계속 사용할 수 있지만 시간이 지남에 따라 단계적으로 중단됩니다. 두 개의 보안 점수 환경은 더 원활한 전환을 가능하게 하기 위해 나란히 실행됩니다.
>
> 최신 보안 점수에 대한 자세한 내용은 [여기를](secure-score-security-controls.md)참조하십시오.
>

보안 혜택을 제공하는 서비스는 매우 많으므로 워크로드를 보호하고 강화하기 위해 먼저 수행할 단계를 아는 것은 종종 어려운 일입니다. 보안 점수는 보안 권장 사항을 검토하고 우선 순위를 지정하므로 먼저 수행할 권장 사항을 알 수 있습니다. 이를 통해 가장 심각한 보안 취약점을 찾아내 조사의 순위를 지정할 수 있습니다. 보안 점수는 워크로드 보안 상태를 평가하는 데 도움이 되는 도구입니다.

## <a name="secure-score-calculation"></a>보안 점수 계산

Security Center는 보안 권장 사항을 검토하고 고급 알고리즘을 적용하여 각 권장 사항의 중요도를 판별하는 보안 분석가의 작업을 모방합니다.
Azure Security Center는 활성 권장 사항을 지속적으로 검토하고 이를 기반으로 보안 점수를 계산하며, 권장 사항의 점수는 워크로드 보안에 가장 큰 영향을 미치는 심각도 및 보안 모범 사례에서 비롯됩니다.

보안 센터는 또한 **전반적인 보안 점수를**제공합니다. 

**전체 보안 점수는** 모든 추천 점수의 축적입니다. 선택한 항목에 따라 구독 또는 관리 그룹 전체에서 전체 보안 점수를 볼 수 있습니다. 점수는 선택되어 있는 구독과 이 구독에 대한 활성 권장 사항에 따라 달라집니다.

보안 점수에 가장 큰 영향을 미치는 권장 사항을 확인하려면 보안 센터 대시보드에서 가장 영향력 있는 권장 사항 3개를 보거나 **보안 점수 영향** 열을 사용하여 권장 사항 목록 블레이드의 권장 사항을 정렬할 수 있습니다.

전체 보안 점수를 보려면 다음 을 수행하십시오.

1. Azure 대시보드에서 **보안 센터를** 클릭한 다음 **보안 점수를**클릭합니다.

2. 상단에서 보안 점수 하이라이트를 볼 수 있습니다.
   - **전체 보안 점수는** 선택한 구독당 정책별 점수를 나타냅니다.
   - **카테고리별 보안 점수는** 가장 주의가 필요한 리소스를 보여줍니다.
   - **보안 점수 영향에 의한 주요 권장 사항은** 보안 점수를 구현하는 경우 가장 많이 향상되는 권장 사항 목록을 제공합니다.
 
   ![보안 점수](./media/security-center-secure-score/secure-score-dashboard.png)

3. 아래 표에서 각 구독및 각 구독의 전체 보안 점수를 볼 수 있습니다.

   > [!NOTE]
   > 각 구독의 보안 점수합계가 전체 보안 점수와 같지 는 않습니다. 보안 점수는 구독 전체의 보안 점수 합계가 아니라 권장 사항당 총 리소스와 정상 리소스 간의 비율을 기반으로 계산한 것입니다. 
   >
4. **권장 사항 보기를** 클릭하여 보안 점수를 개선하기 위해 수정할 수 있는 해당 구독에 대한 권장 사항을 확인합니다.
4. 권장 사항 목록에서 각 권장 사항에 대해 **보안 점수 영향을**나타내는 열이 있음을 확인할 수 있습니다. 이 숫자는 권장 사항을 따르는 경우 전체 보안 점수가 얼마나 향상되는지를 나타냅니다. 예를 들어 아래 화면에서 컨테이너 **보안 구성에서 취약점을 해결하면**보안 점수가 35포인트 증가합니다.

   ![보안 점수](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>개인 보안 점수

또한 개별 보안 점수를 보려면 개별 추천 블레이드에서 이러한 점수를 찾을 수 있습니다.  

**추천 보안 점수는** 정상 리소스와 총 리소스 간의 비율을 기반으로 계산된 것입니다. 정상 리소스 수가 총 리소스 수와 같으면 권장 사항의 최대 보안 점수가 50개입니다. 보안 점수를 최대 점수에 더 가깝게 얻으려면 권장 사항을 따라 비정상 리소스를 수정합니다.

**추천 영향을** 통해 추천 단계를 적용하면 보안 점수가 얼마나 향상되는지 알 수 있습니다. 예를 들어 보안 점수가 42이고 **추천 영향이** +3인 경우 권장 사항에 설명된 단계를 수행하면 점수가 45가 되도록 개선됩니다.

권장 사항은 수정 절차가 수행되지 않는 경우 워크로드가 노출되는 위협을 보여 줍니다.

![개별 권장 사항 보안 점수](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 보안 센터에서 **보안 점수를** 사용하여 보안 상태를 개선하는 방법을 보여 주어 도왔습니다. Security Center에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
