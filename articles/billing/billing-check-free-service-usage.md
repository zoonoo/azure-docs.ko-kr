---
title: Azure 무료 서비스 사용량 모니터링 및 추적
description: Azure Portal 및 사용량 CSV 파일에서 무료 서비스 사용량을 확인하는 방법에 대해 알아봅니다.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491417"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Azure 체험 계정에 포함된 무료 서비스 사용량 확인

서비스의 한도를 초과하지 않는 한, Azure 체험 계정에 무료로 포함된 서비스 요금은 청구되지 않습니다. 한도를 유지하기 위해 Azure Portal 또는 사용량 파일을 사용하여 무료 서비스 사용량을 모니터링하고 추적할 수 있습니다.

## <a name="check-usage-in-the-azure-portal"></a>Azure Portal에서 사용량 확인

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

2.  왼쪽 탐색 영역에서 **모든 서비스**를 선택합니다.

3.  **구독**을 선택합니다.

4.  무료 계정을 등록할 때 만든 구독을 선택합니다.

    ![모든 구독을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  개요 섹션에는 구독에 대한 필수 정보가 표시됩니다. 구독 ID, 제품 유형 및 구독 이름 등이 있습니다. 체험 계정 크레딧이 만료될 때 정보를 찾을 수도 있습니다.

    ![구독의 중요 정보를 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  현재 비용과 예상 비용에 대한 정보를 찾으려면 아래로 스크롤합니다. 비용에는 체험 계정에 포함되지 않은 서비스 사용량과 무료 서비스의 한도를 초과하는 사용량이 포함됩니다.

    ![구독 비용 정보를 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  개요 섹션의 마지막 부분에는 무료 서비스 사용량을 보여주는 표가 있습니다.

    ![무료 서비스의 사용량을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    표에는 다음과 같은 열이 있습니다.

* **측정기 이름:** 사용 중인 측정기에 대한 측정 단위를 식별합니다. 서비스-측정기 매핑에 대한 자세한 내용은 [무료 서비스-측정기 매핑 이해](billing-understand-free-service-meter-mapping.md)를 참조하세요.
* **사용/한도:** 현재 월의 사용량 및 측정기의 제한입니다. 이 정보는 상태 표시줄에서 찾을 수 있습니다.
* **상태:** 측정기의 사용 상태입니다. 사용 패턴에 따라, 다음 상태 중 하나일 수 있습니다.
  * **사용 중이 아님:** 측정기를 사용하지 않았거나 측정기의 사용량이 청구 시스템에 연결되지 않았습니다.
  * **\<Date>에 초과됨:** \<Date>에 측정기 제한에 도달했습니다.
  * **초과할 가능성이 낮음:** 측정기의 제한을 초과할 가능성이 낮습니다.
  * **\<Date>에 초과:** \<Date>에 측정기 제한에 도달할 가능성이 높습니다.

## <a name="check-usage-with-the-usage-file"></a>사용량 파일을 통해 사용량 확인

사용량 파일은 Azure 구독에 대한 자세한 정보를 제공합니다. Azure 계정 센터에서 월별 및 일일 사용량 파일을 다운로드할 수 있습니다. 사용량 파일을 다운로드하는 방법을 알아보고 필요한 액세스 권한을 이해하려면 [청구서 및 사용량 가져오기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요. 사용량 파일의 열에 대한 자세한 내용은 [사용량에서 용어 이해](billing-understand-your-usage.md)를 참조하세요.

사용량 파일에는 무료 및 유료 서비스에 대한 사용량 정보가 있습니다. 무료 서비스 측정기에서 측정기 이름 끝에는 **Free**가 표시됩니다. 무료 측정기를 찾으려면 Excel에서 해당 파일을 열고 **측정기 범주 열**에서 **- Free** 텍스트가 있는 셀을 필터링합니다(텍스트 필터 &rarr; 필터 포함 사용).


![무료 서비스의 사용량을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [구독 업그레이드](billing-upgrade-azure-subscription.md)
