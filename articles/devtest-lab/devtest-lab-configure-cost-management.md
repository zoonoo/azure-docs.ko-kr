---
title: "Azure DevTest Labs에서 월간 예상 랩 비용 추세 보기 | Microsoft 문서"
description: "Azure DevTest Labs의 월간 예상 비용 추세 차트에 대해 알아봅니다."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a95b496d16459e415e4ece50b230615bd1bd2169


---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Azure DevTest Labs에서 월간 예상 랩 비용 추세 보기
DevTest Labs의 비용 관리 기능은 랩의 비용을 추적하는 데 도움이 됩니다. 이 문서는 **월간 예상 비용 추세** 차트를 사용하여 이번 달의 현재 예상 비용 합계 뿐만 아니라 이번 달의 월말 추정 비용을 보는 방법을 보여줍니다. 이 문서에서는 Azure 포털의 월간 예상 비용 추세 차트를 보는 방법을 알아봅니다.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Monthly Estimated Cost Trend(월간 예상 비용 추세) 차트 보기
월간 예상 비용 추세 차트를 보려면 다음 단계를 수행합니다. 

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **추가 서비스**를 선택한 후 목록에서 **DevTest Labs**을 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.   
4. 랩의 블레이드에서 **비용 설정**을 선택합니다.
5. 랩의 **비용 설정** 블레이드에서 **랩 비용 추세**를 선택합니다.
6. 다음 스크릿샷은 비용 차트의 예를 보여줍니다. 
   
    ![비용 차트](./media/devtest-lab-configure-cost-management/graph.png)

**예상 비용** 값은 현재 월의 현재까지의 예상 비용입니다. **예측 비용** 은 이번 달 전체의 예상 비용으로, 이전 5일 동안의 랩 비용을 사용해서 계산합니다.

금액은 다음 정수로 올림됩니다. 예: 

* 5.01은 6으로 올림됩니다. 
* 5.50은 6으로 올림됩니다.
* 5.99는 6으로 올림됩니다.

차트에 언급되어 있듯이, 차트에 보이는 비용은 *종량제* 제안 요율을 사용하여 [예상된](https://azure.microsoft.com/offers/ms-azr-0003p/) 비용입니다.
또한 다음 항목은 비용 계산에 포함되지 *않습니다* .

* Azure DevTest Labs에서는 [Azure 청구 API](../billing-usage-rate-card-overview.md) 를 사용하여 랩 비용을 계산하며 CSP 또는 Dreamspark 구독을 지원하지 않으므로 이러한 구독 방식이 현재 지원되지 않습니다.
* 사용자에 대한 제안 요율. 현재는 Microsoft 또는 Microsoft 파트너와 협상한 제안 요율(구독 아래에 표시됨)을 사용할 수 없습니다. 종량제 요율을 사용합니다.
* 사용자 세금
* 사용자 할인
* 사용자의 청구 통화. 현재는, 랩 비용이 USD 통화로만 표시됩니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물
* [DevTest Labs에서 비용을 유지하기 하기 위한 두 가지 추가 항목](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [비용 임계값을 사용하는 이유는 무엇일까요?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>다음 단계
다음으로 수행해 볼 작업은 아래와 같습니다.

* [랩 정책 정의](devtest-lab-set-lab-policy.md) – 랩 및 해당 VM의 사용 방식을 관리하는 데 사용되는 다양한 정책을 설정하는 방법을 알아봅니다. 
* [사용자 지정 이미지 만들기](devtest-lab-create-template.md) - VM을 만들 때 사용자 지정 이미지 또는 마켓플레이스 이미지 중에서 기본 이미지를 지정할 수 있습니다. 이 문서에는 VHD 파일에서 사용자 지정 이미지를 만드는 방법이 나와 있습니다.
* [마켓플레이스 이미지 구성](devtest-lab-configure-marketplace-images.md) - DevTest Labs에서 Azure Marketplace 이미지에 기반하여 VM을 만들 수 있습니다. 이 문서에서는 랩에서 VM을 만들 때 사용할 수 있는 Azure 마켓플레이스 이미지(있는 경우)를 지정하는 방법을 보여 줍니다.
* [랩에서 VM 만들기](devtest-lab-add-vm-with-artifacts.md) - 기본 이미지(사용자 지정 또는 마켓플레이스 이미지)에서 VM을 만드는 방법 및 VM에서 아티팩트 작업 방법에 대해 설명합니다.




<!--HONumber=Nov16_HO3-->


