---
title: 모니터링 하 고 Azure 무료 서비스 사용량 추적
description: Azure portal 및 사용량 CSV 파일에 대 한 무료 서비스 사용량을 확인 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491417"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Azure 무료 계정에 포함 된 무료 서비스 사용량 확인

서비스 한도 초과 하지 않는 한 Azure 체험 계정에 무료로 포함 된 서비스에 대 한 청구 되지 됩니다. 제한을 유지 하려면 모니터링 하 고 무료 서비스 사용량을 추적 하려면 Azure portal 또는 사용량 파일을 사용할 수 있습니다.

## <a name="check-usage-in-the-azure-portal"></a>Azure portal에서 사용량 확인

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

2.  왼쪽된 탐색 영역에서 선택 **모든 서비스**합니다.

3.  **구독**을 선택합니다.

4.  무료 계정을 등록할 때 만든 구독을 선택합니다.

    ![모든 구독을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  개요 섹션 구독에 대 한 필수 정보를 표시 합니다. 예를 들어, 구독 ID, 형식 및 구독 이름을 제공 합니다. 또한 무료 계정 크레딧이 만료 되 면 정보를 찾을 수 있습니다.

    ![구독의 중요 정보를 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  현재 및 예상 비용에 대 한 정보를 찾으려면 아래로 스크롤하십시오. 비용은 무료 계정 및 무료 서비스 한도 초과 하는 사용량에 포함 되지 않은 서비스 사용을 포함 합니다.

    ![구독 비용 정보를 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  개요 섹션의 마지막 부분에는 무료 서비스 사용량을 표시 하는 테이블을 있습니다.

    ![무료 서비스의 사용량을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    표에는 다음과 같은 열이 있습니다.

* **측정기 이름:** 사용 중인 측정기에 대한 측정 단위를 식별합니다. 서비스-측정기 매핑에 대한 자세한 내용은 [무료 서비스-측정기 매핑 이해](billing-understand-free-service-meter-mapping.md)를 참조하세요.
* **사용/한도:** 현재 월의 사용량 및 측정기의 제한입니다. 이 정보는 상태 표시줄에서 찾을 수 있습니다.
* **상태:** 측정기의 사용 상태입니다. 사용 패턴에 따라 할 수 있습니다 다음 상태 중 하나:
  * **사용 중이 아님:** 측정기를 사용하지 않았거나 측정기의 사용량이 청구 시스템에 연결되지 않았습니다.
  * **\<Date>에 초과됨:** \<Date>에 측정기 제한에 도달했습니다.
  * **초과할 가능성이 낮음:** 측정기의 제한을 초과할 가능성이 낮습니다.
  * **\<Date>에 초과:** \<Date>에 측정기 제한에 도달할 가능성이 높습니다.

## <a name="check-usage-with-the-usage-file"></a>사용 현황 파일을 사용 하 여 사용량을 확인 합니다.

사용량 파일에는 Azure 구독에 대해 자세히 설명합니다. Azure 계정 센터에서 월별 및 일별 사용 현황 파일을 다운로드할 수 있습니다. 사용량 파일을 다운로드하는 방법을 알아보고 필요한 액세스 권한을 이해하려면 [청구서 및 사용량 가져오기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요. 사용량 파일의 열에 대한 자세한 내용은 [사용량에서 용어 이해](billing-understand-your-usage.md)를 참조하세요.

사용량 파일에는 무료 및 유료 서비스에 대한 사용량 정보가 있습니다. 무료 서비스 측정기에서 측정기 이름 끝에는 **Free**가 표시됩니다. 무료 측정기, excel에서 파일 열기 및 필터를 찾을 수 합니다 **측정기 범주 열** 텍스트가 포함 된 셀에 대 한 **-무료** (사용 하 여 텍스트 필터 &rarr; 필터 포함).


![무료 서비스의 사용량을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계
- [구독 업그레이드](billing-upgrade-azure-subscription.md)
