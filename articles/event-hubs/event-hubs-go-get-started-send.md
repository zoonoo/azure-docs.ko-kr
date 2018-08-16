---
title: Go를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs
description: Go를 사용하여 Event Hubs로 이벤트 전송 시작
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005600"
---
# <a name="send-events-to-event-hubs-using-go"></a>Go를 사용하여 Event Hubs에 이벤트 보내기

Azure Event Hubs는 초당 수백만 개의 이벤트를 처리할 수 있는 확장성이 뛰어난 이벤트 관리 시스템으로, 연결된 장치와 기타 시스템에서 생성되는 대량의 데이터를 처리 및 분석할 수 있습니다. 이벤트 허브에 이벤트가 수집되면 In Process 처리기를 사용하여 또는 다른 분석 시스템으로 전달하여 이벤트를 받아서 처리할 수 있습니다.

Event Hubs에 대한 자세한 내용은 [Event Hubs 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 Go로 작성된 응용 프로그램에서 이벤트 허브로 이벤트를 전송하는 방법을 설명합니다. 이벤트를 수신하려면 [해당 수신 문서](event-hubs-go-get-started-receive-eph.md)에 설명된 대로 **Go eph**(이벤트 프로세서 호스트) 패키지를 사용하세요.

이 자습서의 코드는 [이 GitHub 샘플](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)에서 가져온 것으로, 이 코드를 검사하면 가져온 명령문 및 변수 선언을 포함하여 온전하게 작동하는 응용 프로그램을 볼 수 있습니다.

다른 예는 [Event Hubs 패키지 리포지토리](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples)에 제공됩니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 로컬로 설치된 Go. 필요한 경우 [다음 지침](https://golang.org/doc/install)을 따릅니다.
* 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][]을 만듭니다.
* 기존 Event Hubs 네임스페이스 및 이벤트 허브 [이 문서의 지침](event-hubs-create.md)에 따라 이러한 엔터티를 만들 수 있습니다.

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

## <a name="next-steps"></a>다음 단계

다음 페이지를 방문하여 Event Hubs에 대해 자세히 알아보세요.

* [EventProcessorHost를 사용하여 이벤트 수신](event-hubs-go-get-started-receive-eph.md)
* [Event Hubs 개요][Event Hubs overview]
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[무료 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
