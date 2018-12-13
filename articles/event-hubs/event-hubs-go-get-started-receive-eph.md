---
title: Go를 사용하여 Azure Event Hubs에서 이벤트 수신 | Microsoft Docs
description: Go를 사용하여 Event Hubs에서 이벤트 수신 시작
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 0a8cb349be0ffefecb07bc705391684dc57f1b96
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239494"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Go를 사용하여 Event Hubs에서 이벤트 수신

Azure Event Hubs는 초당 수백만 개의 이벤트를 처리할 수 있는 확장성이 뛰어난 이벤트 관리 시스템으로, 연결된 디바이스와 기타 시스템에서 생성되는 대량의 데이터를 처리 및 분석할 수 있습니다. 이벤트 허브에 이벤트가 수집되면 In Process 처리기를 사용하여 또는 다른 분석 시스템으로 전달하여 이벤트를 받아서 처리할 수 있습니다.

Event Hubs에 대한 자세한 내용은 [Event Hubs 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 Go 응용 프로그램의 이벤트 허브에서 이벤트를 수신하는 방법을 설명합니다. 이벤트를 전송하는 방법을 알아보려면 [해당 보내기 문서](event-hubs-go-get-started-send.md)를 참조하세요.

이 자습서의 코드는 [이 GitHub 샘플](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)에서 가져온 것으로, 이 코드를 검사하면 가져온 명령문 및 변수 선언을 포함하여 온전하게 작동하는 응용 프로그램을 볼 수 있습니다.

다른 예는 [Event Hubs 패키지 리포지토리](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples)에 제공됩니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 로컬로 설치된 Go. 필요한 경우 [다음 지침](https://golang.org/doc/install)을 따릅니다.
* 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][]을 만듭니다.
* 메시지를 수신하려면 대상 이벤트 허브에 메시지가 있어야 합니다. [보내기 자습서](event-hubs-go-get-started-send.md)에서 메시지를 보내는 방법을 알아보세요.
* 기존 이벤트 허브(다음 섹션 참조).
* 기존 저장소 계정 및 컨테이너(다다음 섹션 참조).

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

이 자습서에서는 기존 Event Hubs 네임스페이스 및 이벤트 허브를 시작합니다. [이 문서의 지침](event-hubs-create.md)에 따라 이러한 엔터티를 만들 수 있습니다.

### <a name="create-a-storage-account-and-container"></a>Storage 계정 및 컨테이너 만들기

이벤트 스트림의 파티션 및 검사점 임대와 같은 상태는 Azure Storage 컨테이너를 사용하여 수신기 간에 공유됩니다. Go SDK를 사용하여 저장소 계정 및 컨테이너를 만들 수 있지만, [Azure 저장소 계정 정보](../storage/common/storage-create-storage-account.md)의 지침에 따라 새로 만들 수도 있습니다.

Go SDK를 사용하여 Storage 아티팩트를 만드는 샘플은 [Go 샘플 리포지토리](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) 및 이 자습서에 해당하는 샘플에서 사용할 수 있습니다.

## <a name="receive-messages"></a>메시지 받기

메시지를 받으려면 `go get` 또는 `dep` 명령을 사용하여 Event Hubs용 Go 패키지를 받으세요.

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

## <a name="import-packages-in-your-code-file"></a>코드 파일에서 패키지 가져오기

Go 패키지를 가져오려면 다음 코드 예제를 사용합니다.

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

## <a name="create-service-principal"></a>서비스 주체 만들기

[Azure CLI 2.0을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)의 지침에 따라 새 서비스 주체를 만듭니다. 제공된 자격 증명을 사용자 환경에 다음 이름으로 저장합니다. Go용 Azure SDK 및 Event Hubs 패키지는 이러한 변수 이름을 찾도록 미리 구성되어 있습니다.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

다음으로, 이제 이러한 자격 증명을 사용하는 Event Hubs 클라이언트에 대한 권한 부여 공급자를 만듭니다.

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="get-metadata-struct"></a>메타데이터 구조체 가져오기

Azure Go SDK를 사용하여 Azure 환경에 대한 메타데이터로 구조체를 가져옵니다. 이후 작업에서 이 구조체를 사용하여 올바른 엔드포인트를 찾을 것입니다.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>자격 증명 도우미 만들기 

이전 AAD(Azure Active Directory) 자격 증명을 사용하여 Storage에 대한 SAS(공유 액세스 서명) 자격 증명을 만드는 자격 증명 도우미를 만듭니다. 마지막 매개 변수는 이전에 사용한 것과 동일한 환경 변수를 이 구문에 사용하라고 지시합니다.

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

## <a name="create-checkpointer-and-leaser"></a>Checkpointer 및 Leaser 만들기 

다른 수신기가 올바른 오프셋에서 읽기를 시작할 수 있도록 파티션을 특정 수신기에게 임대하는 일을 담당하는 **Checkpointer** 및 메시지 스트림에 대한 검사점을 작성하는 일을 담당하는 **Leaser**를 만듭니다.

현재는 동일한 Storage 컨테이너를 사용하여 임대와 검사점을 관리하는 단일 **StorageLeaserCheckpointer**가 제공됩니다. **StorageLeaserCheckpointer**는 컨테이너에 올바르게 액세스하려면 저장소 계정 및 컨테이너 이름 외에도 이전 단계에서 만든 자격 증명과 Azure 환경 구조체가 필요합니다.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

## <a name="construct-event-processor-host"></a>구문 이벤트 프로세서 호스트

이제 다음과 같이 EventProcessorHost를 작성하는 데 필요한 방법이 제공됩니다. 앞에서 설명했듯이, 동일한 **StorageLeaserCheckpointer**가 Leaser 및 Checkpointer로 사용됩니다.

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

## <a name="create-handler"></a>처리기 만들기 

이제 처리기를 만들어 이벤트 프로세서 호스트에 등록합니다. 호스트가 시작되면 이 처리기와 지정된 기타 처리기가 들어오는 메시지에 적용됩니다.

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

## <a name="receive-messages"></a>메시지 받기

모든 설정이 끝나면 `Start(context)` 명령으로 이벤트 프로세서 호스트를 시작하여 영구적으로 실행할 수도 있고, `StartNonBlocking(context)` 명령으로 메시지를 사용할 수 있는 만큼만 실행할 수도 있습니다.

이 자습서는 다음과 같이 시작 및 실행됩니다. `StartNonBlocking` 사용 예제는 GitHub 샘플을 참조하세요.

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>메모

이 자습서에서는 **EventProcessorHost**의 단일 인스턴스를 사용합니다. 처리량과 안정성을 높이려면 여러 시스템에서 여러 **EventProcessorHost** 인스턴스를 실행해야 합니다. Leaser 시스템은 하나의 수신기만 연결하고, 지정된 시간에 지정된 파티션에서 메시지를 수신합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 이벤트 허브에서 메시지를 받는 Go 응용 프로그램을 만들었습니다. Go를 사용하여 이벤트 허브에 이벤트를 보내는 방법을 알아보려면 [이벤트 허브에서 이벤트 보내기 - Go](event-hubs-go-get-started-send.md)를 참조하세요.

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[체험 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
