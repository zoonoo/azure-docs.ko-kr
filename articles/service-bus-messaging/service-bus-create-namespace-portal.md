---
title: "Azure 포털을 사용하여 Service Bus 네임스페이스 만들기 | Microsoft Docs"
description: "서비스 버스를 시작하려면 네임스페이스가 필요합니다. Azure 포털을 사용하여 만드는 방법은 다음과 같습니다."
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/30/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 0b1f6f7ec47e47f39407cdbfd5efef2a18944ecc
ms.openlocfilehash: 89610512c264cef4bd52728240ddc0a34091edea


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Azure Portal을 사용하여 Service Bus 네임스페이스 만들기
네임스페이스는 모든 메시징 구성 요소에 대한 공용 컨테이너입니다. 여러 큐 및 토픽은 단일 네임스페이스에 있을 수 있으며 네임스페이스는 종종 응용 프로그램 컨테이너로 사용됩니다. 현재 두 가지 다른 방법으로 서비스 버스 네임스페이스를 만들 수 있습니다.

1. Azure 포털(이 문서)
2. [Resource Manager 템플릿][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Azure Portal에서 네임스페이스 만들기
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

축하합니다. 이제 서비스 버스 메시징 네임스페이스를 만들었습니다.

## <a name="next-steps"></a>다음 단계
Azure Service Bus 메시징의 더 많은 고급 기능 중 일부를 보여 주는 [GitHub 샘플][github-samples]을 확인합니다.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Feb17_HO3-->


