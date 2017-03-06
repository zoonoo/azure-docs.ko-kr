---
title: ".NET에서 Azure 릴레이 하이브리드 연결 시작 | Microsoft Docs"
description: "하이브리드 연결에 대한 C# 콘솔 응용 프로그램을 작성하는 방법"
services: service-bus-relay
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/03/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: d27016559ede5d810d7efcec2a3abc78334f0f0a
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-relay-hybrid-connections"></a>릴레이 하이브리드 연결 시작
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

이 자습서에서는 [Azure 릴레이 하이브리드 연결](relay-what-is-it.md#hybrid-connections)을 소개하고 메시지를 해당 수신기 응용 프로그램으로 보내는 클라이언트 응용 프로그램을 만드는 방법을 보여 줍니다. 

## <a name="what-will-be-accomplished"></a>수행될 작업
하이브리드 연결에는 클라이언트와 서버 구성 요소가 모두 필요하므로 이 자습서에서는 두 개의 콘솔 응용 프로그램을 만듭니다. 절차는 다음과 같습니다.

1. Azure 포털을 사용하여 릴레이 네임스페이스를 만듭니다.
2. Azure 포털을 사용하여 하이브리드 연결을 만듭니다.
3. 메시지를 수신하는 서버(수신기) 콘솔 응용 프로그램을 작성합니다.
4. 메시지를 보내는 클라이언트(발신자) 콘솔 응용 프로그램을 작성합니다.

## <a name="prerequisites"></a>필수 조건
1. [Visual Studio 2015 이상](http://www.visualstudio.com). 이 자습서의 예제에서는 Visual Studio 2015를 사용합니다.
2. Azure 구독.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Azure 포털을 사용하여 네임스페이스 만들기
릴레이 네임스페이스를 이미 만든 경우 [Azure Portal을 사용하여 하이브리드 연결 만들기](#2-create-a-hybrid-connection-using-the-azure-portal) 섹션으로 이동합니다.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Azure 포털을 사용하여 하이브리드 연결 만들기
하이브리드 연결을 이미 만든 경우 [서버 응용 프로그램 만들기](#3-create-a-server-application-listener) 섹션으로 이동합니다.

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. 서버 응용 프로그램(수신기) 만들기
릴레이에서 메시지를 수신하고 받으려면 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. 클라이언트 응용 프로그램(보낸 사람) 만들기
릴레이에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 작성합니다.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. 응용 프로그램 실행
1. 서버 응용 프로그램을 실행합니다.
2. 클라이언트 응용 프로그램을 실행하고 일부 텍스트를 입력합니다.
3. 서버 응용 프로그램 콘솔이 클라이언트 응용 프로그램에 입력된 텍스트를 출력하는지 확인합니다.

![응용 프로그램 실행](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

축하합니다. 종단 간 하이브리드 연결 응용 프로그램을 만들었습니다.

## <a name="next-steps"></a>다음 단계:
* [릴레이 FAQ](relay-faq.md)
* [네임스페이스 만들기](relay-create-namespace-portal.md)
* [Node 시작](relay-hybrid-connections-node-get-started.md)


