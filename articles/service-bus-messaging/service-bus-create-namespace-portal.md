---
title: Azure Portal에서 Service Bus 네임스페이스를 만드는 방법 | Microsoft Docs
description: Azure Portal을 사용하여 Service Bus 네임스페이스를 만듭니다.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c456fa392c3ba07ad26e300eff07a89fa849b354
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856318"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Azure Portal을 사용하여 Service Bus 네임스페이스 만들기

네임스페이스는 모든 메시징 구성 요소에 대한 영역 컨테이너입니다. 여러 큐 및 토픽은 단일 네임스페이스 내에 있을 수 있으며 네임스페이스는 종종 애플리케이션 컨테이너로 사용됩니다. 두 가지 방법으로 Service Bus 네임스페이스를 만들 수 있습니다.

1. Azure Portal(이 문서)
2. [Resource Manager 템플릿][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Azure Portal에서 네임스페이스 만들기

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

축하합니다! 이제 Service Bus 메시징 네임스페이스를 만들었습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징의 더 많은 고급 기능 중 일부를 보여주는 Service Bus [GitHub 샘플][github-samples]을 확인합니다.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
