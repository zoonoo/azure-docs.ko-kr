---
title: Azure 클래식 배포 리소스를 새 구독 또는 리소스 그룹 이동
description: Azure Resource Manager를 사용 하 여 새 리소스 그룹 또는 구독으로 클래식 배포 리소스를 이동 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4770f957b6b9eea75b50776a7491b1ca479e50e2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723507"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>클래식 배포 모델 리소스에 대 한 지침을 이동

클래식 모델을 통해 배포 된 리소스를 이동 하는 단계는 구독 내 또는 새 구독으로 리소스를 이동 하는 여부에 따라 다릅니다.

## <a name="move-in-the-same-subscription"></a>동일한 구독에서 이동

한 리소스 그룹에서 같은 구독 내 다른 리소스 그룹으로 리소스를 이동할 경우 다음 제한 사항이 적용됩니다.

* 가상 네트워크(클래식)는 이동할 수 없습니다.
* 가상 머신(클래식)은 클라우드 서비스로 이동해야 합니다.
* 클라우드 서비스는 이동에 모든 가상 머신이 포함된 경우에만 이동할 수 있습니다.
* 한 번에 하나의 클라우드 서비스만 이동할 수 있습니다.
* 한 번에 하나의 저장소 계정(클래식)만 이동할 수 있습니다.
* Storage 계정(클래식)은 가상 머신 또는 클라우드 서비스와 같은 작업으로 이동할 수 없습니다.

클래식 리소스를 동일한 구독 내에서 새 리소스 그룹을 이동 하려면 사용 합니다 [표준 이동 작업](../resource-group-move-resources.md) 포털, Azure PowerShell, Azure CLI 또는 REST API를 통해. Resource Manager 리소스 이동을 위해 사용하는 동일한 작업을 사용합니다.

## <a name="move-across-subscriptions"></a>구독 간 이동

새 구독으로 리소스 이동 시 다음 제한 사항이 적용됩니다.

* 구독의 모든 클래식 리소스는 동일한 작업에서 이동해야 합니다.
* 대상 구독은 다른 어떠한 클래식 리소스도 포함할 수 없습니다.
* 이동은 클래식 이동에 대한 별도의 REST API를 통해서만 요청할 수 있습니다. 클래식 리소스를 새 구독으로 이동할 경우 표준 Resource Manager 이동 명령은 작동하지 않습니다.

클래식 리소스를 새 구독으로 이동하려면 클래식 리소스와 관련된 REST 작업을 사용합니다. REST를 사용 하려면 다음 단계를 수행 합니다.

1. 원본 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     요청 본문에 다음을 포함합니다.

   ```json
   {
    "role": "source"
   }
   ```

     유효성 검사 작업에 대한 응답은 다음 형식입니다.

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. 대상 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     요청 본문에 다음을 포함합니다.

   ```json
   {
    "role": "target"
   }
   ```

     응답이 원본 구독 유효성 검사와 동일한 형식입니다.
1. 두 구독이 유효성 검사를 통과하면 다음 작업으로 한 구독에서 다른 구독으로 모든 클래식 리소스를 이동합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    요청 본문에 다음을 포함합니다.

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

이 작업은 몇 분 정도 실행될 수 있습니다.

## <a name="next-steps"></a>다음 단계

클래식 리소스를 이동 하는 데 문제가 있으면 문의 [지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)합니다.

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../resource-group-move-resources.md)을 참조하세요.
