---
title: "Azure Portal에서 Service Bus 네임스페이스를 만드는 방법 | Microsoft Docs"
description: "Azure Portal을 사용하여 Service Bus 네임스페이스를 만듭니다."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: ffb134085c8555b22a317213622ca6c9490497d8
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---
# Azure Portal을 사용하여 Service Bus 네임스페이스 만들기
<a id="create-a-service-bus-namespace-using-the-azure-portal" class="xliff"></a>

네임스페이스는 모든 메시징 구성 요소에 대한 영역 컨테이너입니다. 여러 큐 및 토픽은 단일 네임스페이스 내에 있을 수 있으며 네임스페이스는 종종 응용 프로그램 컨테이너로 사용됩니다. 두 가지 다른 방법으로 Service Bus 네임스페이스를 만들 수 있습니다.

1. Azure Portal(이 문서)
2. [Resource Manager 템플릿][create-namespace-using-arm]

## Azure Portal에서 네임스페이스 만들기
<a id="create-a-namespace-in-the-azure-portal" class="xliff"></a>

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

축하합니다. 이제 Service Bus 메시징 네임스페이스를 만들었습니다.

## 다음 단계
<a id="next-steps" class="xliff"></a>

Azure Service Bus 메시징의 더 많은 고급 기능 중 일부를 보여 주는 [GitHub 샘플][github-samples]을 확인합니다.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

