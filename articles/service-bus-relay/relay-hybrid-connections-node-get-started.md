---
title: "Node에서 Azure 릴레이 하이브리드 연결 시작 | Microsoft Docs"
description: "Azure Relay Hybrid Connections에 대한 Node.js 콘솔 응용 프로그램 작성"
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.openlocfilehash: c3bfc45969f250059988129f532edd12dfe3dcfe
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>릴레이 하이브리드 연결 시작

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

이 자습서에서는 [Azure Relay Hybrid Connections](relay-what-is-it.md#hybrid-connections)를 소개하고 Node.js를 사용하여 메시지를 해당 수신기 응용 프로그램으로 보내는 클라이언트 응용 프로그램을 만드는 방법을 보여 줍니다. 

## <a name="what-will-be-accomplished"></a>수행될 작업

하이브리드 연결에는 클라이언트와 서버 구성 요소가 모두 필요하기 대문에 이 자습서에서는 두 개의 콘솔 응용 프로그램을 만듭니다. 단계는 다음과 같습니다.

1. Azure 포털을 사용하여 릴레이 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 하이브리드 연결을 만듭니다.
3. 메시지를 수신하는 서버 콘솔 응용 프로그램을 작성합니다.
4. 메시지를 보내는 클라이언트 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건

1. [Node.js](https://nodejs.org/en/).
2. Azure 구독.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Azure Portal을 사용하여 네임스페이스 만들기

릴레이 네임스페이스를 이미 만든 경우 [Azure Portal을 사용하여 하이브리드 연결 만들기](#2-create-a-hybrid-connection-using-the-azure-portal) 섹션으로 이동합니다.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Azure Portal을 사용하여 하이브리드 연결 만들기

하이브리드 연결을 이미 만든 경우 [서버 응용 프로그램 만들기](#3-create-a-server-application-listener) 섹션으로 이동합니다.

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. 서버 응용 프로그램(수신기) 만들기

릴레이에서 메시지를 수신하고 받으려면 Node.js 콘솔 응용 프로그램을 작성합니다.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. 클라이언트 응용 프로그램(보낸 사람) 만들기

릴레이에 메시지를 보내려면 Node.js 콘솔 응용 프로그램을 작성합니다.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. 응용 프로그램 실행

1. Node.js 명령 프롬프트 형식 `node listener.js`의 서버 응용 프로그램을 실행합니다.
2. Node.js 명령 프롬프트 형식 `node sender.js`의 클라이언트 응용 프로그램을 실행하고 일부 텍스트를 입력합니다.
3. 서버 응용 프로그램 콘솔이 클라이언트 응용 프로그램에 입력된 텍스트를 출력하는지 확인합니다.

![응용 프로그램 실행](./media/relay-hybrid-connections-node-get-started/running-applications.png)

축하합니다. Node.js를 사용하여 종단 간 하이브리드 연결 응용 프로그램을 만들었습니다.

## <a name="next-steps"></a>다음 단계:

* [릴레이 FAQ](relay-faq.md)
* [네임스페이스 만들기](relay-create-namespace-portal.md)
* [.NET 시작](relay-hybrid-connections-dotnet-get-started.md)
* [노드 시작](relay-hybrid-connections-node-get-started.md)

