---
title: Azure Security Center의 보안 점수 | Microsoft Docs
description: " Azure Security Center에서 보안 점수를 사용하여 보안 권장 사항의 우선 순위를 지정하세요. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: fc521db9ad753c4162b65abfd2f9f23c318fa994
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131063"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Azure Security Center의 보안 점수 개선


보안 혜택을 제공하는 서비스는 매우 많으므로 워크로드를 보호하고 강화하기 위해 먼저 수행할 단계를 아는 것은 종종 어려운 일입니다. Azure Security Center 보안 점수는 보안 권장 사항을 검토하고 해당 권장 사항에 우선 순위를 지정하므로 우선 수행할 권장 사항을 알 수 있습니다. 조사 우선 순위를 지정할 수 있도록 가장 심각한 보안 취약성을 찾는 데 도움이 됩니다. 보안 점수는 보안 워크로드를 달성하기 위해 보안을 강화하는 데 도움이 되는 측정 도구입니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]


![보안 점수 대시보드](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>보안 점수 계산

Security Center는 보안 권장 사항을 검토하고 고급 알고리즘을 적용하여 각 권장 사항의 중요도를 판별하는 보안 분석가의 작업을 모방합니다.
Azure Security Center에서는 지속적으로 활성 권장 사항을 검토하고 이를 기반으로 보안 점수를 계산하며, 권장 사항의 점수는 워크로드 보안에 가장 많은 영향을 주는 심각도 및 보안 모범 사례에서 파생됩니다.

**보안 점수**는 정상 리소스와 총 리소스 간의 비율을 기반으로 하는 계산입니다. 정상 상태의 리소스 수가 총 리소스 수와 같은 경우 최대 보안 점수인 50을 받습니다. 최대 점수에 근접한 보안 점수를 받으려면 권장 사항을 수행하여 비정상 리소스를 수정합니다.

Security Center에서는 전체 보안 점수도 제공합니다. 

**전체 보안 점수**는 모든 권장 사항의 누적 점수입니다. 사용자가 선택하는 내용에 따라 구독 또는 관리 그룹의 전체 보안 점수를 볼 수 있습니다. 점수는 선택되어 있는 구독과 이 구독에 대한 활성 권장 사항에 따라 달라집니다.

 

보안 점수에 가장 많이 영향을 주는 권장 사항을 확인하기 위해 Security Center 대시보드에서 가장 영향력이 있는 최상위 3개의 권장 사항을 보거나 **보안 점수 영향** 열을 사용하여 권장 사항 목록 블레이드에서 권장 사항을 정렬할 수도 있습니다.


전체 보안 점수를 보려면

1. Azure 대시보드에서 **Security Center**를 클릭한 다음, **권장 사항**을 클릭합니다.
2. 맨 위에서는 선택한 구독에 대한 정책당 점수를 나타내는 보안 점수를 볼 수 있습니다. 
2. 권장 사항을 나열하는 아래의 표에서는 각 권장 사항에 대해 **보안 점수 영향**을 나타내는 열이 있는 것을 확인할 수 있습니다. 이 숫자는 권장 사항을 따르는 경우 전체 보안 점수가 향상되는 정도를 나타냅니다. 예를 들어, 아래 화면에서 **컨테이너 보안 구성에서 취약성 수정**을 수행하는 경우 보안 점수가 35포인트만큼 증가합니다.

   ![보안 점수](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>개별 보안 점수

또한 개별적인 보안 점수를 확인하기 위해 개별 권장 사항 블레이드 내에서 이러한 점수를 찾을 수 있습니다.  

**권장 사항 보안 점수**는 정상 리소스와 총 리소스 간의 비율을 기반으로 하는 계산입니다. 정상 상태의 리소스 수가 총 리소스 수와 같은 경우 권장 사항의 최대 보안 점수를 받습니다. 최대 점수에 근접한 보안 점수를 받으려면 수정 절차를 수행하여 비정상 리소스를 수정합니다.

**권장 사항 영향**을 통해 권장 사항 절차를 적용할 경우 보안 점수가 향상되는 정도를 알 수 있습니다. 예를 들어, 보안 점수가 42이고 **권장 사항 영향**이 +3인 경우, 권장 사항에 설명된 절차를 수행하면 보안 점수는 45로 향상됩니다.

권장 사항은 수정 절차가 수행되지 않는 경우 워크로드가 노출되는 위협을 보여 줍니다.

![개별 권장 사항 보안 점수](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center의 **보안 점수**를 사용하여 보안 태세를 개선하는 방법을 설명했습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.


