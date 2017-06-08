---
title: "논리 앱에서 Azure 서비스 버스 커넥터 사용 방법 알아보기 | Microsoft Docs"
description: "Azure 앱 서비스로 논리 앱을 만듭니다. Azure 서비스 버스에 연결하여 메시지를 보내고 받습니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 동작을 수행할 수 있습니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e5d3301c90adf993b1cba35969dfe4dbeaeab499
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Azure 서비스 버스 커넥터 시작
Azure 서비스 버스에 연결하여 메시지를 보내고 받습니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 동작을 수행할 수 있습니다.

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../logic-apps/logic-apps-create-a-logic-app.md) 시작할 수 있습니다.

## <a name="connect-to-service-bus"></a>서비스 버스에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 연결을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>서비스 버스 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>서비스 버스 동작 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="view-the-swagger"></a>swagger 보기
[swagger 정보](/connectors/servicebus/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)


