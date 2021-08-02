---
title: Elastic 문제 해결 - Azure 파트너 솔루션
description: 이 문서에서는 Azure와 Elastic 통합 문제 해결에 대한 정보를 제공합니다.
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4bc4bc9d9bb5890ed95bbb446f570d25e43d813
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952571"
---
# <a name="troubleshooting-elastic-integration-with-azure"></a>Azure와 Elastic 통합 문제 해결

이 문서에는 Elastic을 사용하는 솔루션 문제 해결에 대한 정보가 포함되어 있습니다.

## <a name="unable-to-create-an-elastic-resource"></a>Elastic 리소스를 만들 수 없음

Azure와의 Elastic 통합은 Azure 구독에 대한 *소유자* 액세스 권한이 있는 사용자만 설정할 수 있습니다. [적절한 액세스 권한이 있는지 확인합니다](../../role-based-access-control/check-access.md).

## <a name="logs-not-being-emitted-to-elastic"></a>Elastic으로 내보내지 않는 로그

[Azure Monitor 리소스 로그 범주](../../azure-monitor/essentials/resource-logs-categories.md)에 나열된 리소스만 Elastic에 로그를 내보냅니다. 리소스가 Elastic에 로그를 내보내고 있는지 확인하려면 리소스에 대한 [Azure 진단 설정](../../azure-monitor/essentials/diagnostic-settings.md)으로 이동합니다. 사용 가능한 진단 설정 옵션이 있는지 확인합니다.

:::image type="content" source="media/troubleshoot/check-diagnostic-setting.png" alt-text="진단 설정 확인:":::

## <a name="purchase-errors"></a>구매 오류

* 유효한 신용 카드가 Azure 구독에 연결되어 있지 않거나 결제 방법이 구독과 연결되어 있지 않기 때문에 구매가 실패합니다.

  다른 Azure 구독을 사용합니다. 또는 구독에 대한 신용 카드나 결제 방법을 추가하거나 업데이트합니다. 자세한 내용은 [크레딧 및 결제 방법 업데이트](../../cost-management-billing/manage/change-credit-card.md)를 참조하세요.

* EA 구독은 마켓플레이스 구매를 허용하지 않습니다.

  다른 구독을 사용하세요. 또는 마켓플레이스 구매에 EA 구독을 사용할 수 있는지 확인합니다. 자세한 내용은 [마켓플레이스 구매 사용](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)을 참조하세요.

## <a name="get-support"></a>지원 받기

Azure와 Elastic 통합에 대한 지원에 문의하려면 왼쪽 창에서 **새 지원 요청** 을 선택합니다. **Elastic 지원 티켓 열기** 를 선택합니다.

:::image type="content" source="media/troubleshoot/open-ticket.png" alt-text="지원 티켓 열기":::

Elastic 사이트에서 지원 요청을 엽니다.

:::image type="content" source="media/troubleshoot/elastic-support.png" alt-text="Elastic 지원 열기":::

## <a name="next-steps"></a>다음 단계

Elastic의 [인스턴스 관리](manage.md)에 대해 알아봅니다.