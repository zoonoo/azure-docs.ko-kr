---
title: Go를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs
description: Go를 사용하여 Event Hubs로 이벤트 전송 시작
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: f5e30a103b09613caee8e9912a89a5bc2d390f65
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458091"
---
# <a name="send-events-to-event-hubs-using-go"></a>Go를 사용하여 Event Hubs에 이벤트 보내기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 Go로 작성된 응용 프로그램에서 이벤트 허브로 이벤트를 전송하는 방법을 설명합니다. 

> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 로컬로 설치된 Go. 필요한 경우 [다음 지침](https://golang.org/doc/install)을 따릅니다.
* 기존 Event Hubs 네임스페이스 및 이벤트 허브 [이 문서의 지침](event-hubs-create.md)에 따라 이러한 엔터티를 만들 수 있습니다.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 네임스페이스 및 이벤트 허브 만들기
첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 응용 프로그램에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 진행하세요.

## <a name="install-go-package"></a>Go 패키지 설치

`go get` 또는 `dep` 명령을 사용하여 Event Hubs용 Go 패키지를 받습니다. 예: 

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>코드 파일에서 패키지 가져오기

Go 패키지를 가져오려면 다음 코드 예제를 사용합니다.

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>서비스 주체 만들기

[Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)의 지침에 따라 새 서비스 주체를 만듭니다. 제공된 자격 증명을 다른 이름으로 현재 환경에 저장합니다. Go용 Azure SDK 및 Event Hubs 패키지는 다음 변수 이름을 찾도록 미리 구성됩니다.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

이제 이러한 자격 증명을 사용하는 Event Hubs 클라이언트에 대한 권한 부여 공급자를 만듭니다.

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>Event Hubs 클라이언트 만들기

다음 코드는 새 Event Hubs 클라이언트를 만듭니다.

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>메시지 보내기

다음 코드 조각에서 (1) 터미널에서 대화식으로 메시지를 보내거나 (2) 프로그램 내에서 메시지를 보냅니다.

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>추가 항목

이벤트 허브의 파티션 ID 가져오기:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

응용 프로그램을 실행하여 이벤트 허브에 이벤트를 보냅니다. 

축하합니다! 이제 Event Hub에 메시지를 보냈습니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Go를 사용하여 이벤트 허브로 메시지를 전송했습니다. Go를 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 알아보려면 [이벤트 허브에서 이벤트 수신 - Go](event-hubs-go-get-started-receive-eph.md)를 참조하세요.

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
