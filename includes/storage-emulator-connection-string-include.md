---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622044"
---
에뮬레이터는 단일 고정 계정과 공유 키 인증에 대해 알려진 인증 키를 지원합니다. 에뮬레이터에서 사용할 수 있는 공유 키 자격 증명은 이 계정과 키뿐입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> 에뮬레이터에서 지원하는 인증 키는 클라이언트 인증 코드의 기능을 테스트할 때만 사용할 수 있습니다. 보안 기능은 제공하지 않습니다. 에뮬레이터에서 프로덕션 스토리지 계정과 키를 사용할 수 없습니다. 개발 계정을 프로덕션 데이터에 사용해서는 안 됩니다.
>
> 에뮬레이터는 HTTP를 통해서만 연결을 지원합니다. 그러나 프로덕션 Azure Storage 계정에서 리소스에 액세스하기 위한 프로토콜로 HTTPS를 사용하는 것이 좋습니다.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>바로 가기를 사용하여 에뮬레이터 계정에 연결

애플리케이션에서 에뮬레이터에 연결하는 가장 쉬운 방법은 바로 가기 `UseDevelopmentStorage=true`를 참조하는 애플리케이션의 구성 파일에서 연결 문자열을 구성하는 것입니다. 바로 가기는 모든 Azure Storage 서비스에 대한 계정 이름, 계정 키 및 에뮬레이터 엔드포인트를 지정하는 에뮬레이터의 전체 연결 문자열에 해당합니다.

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

다음 .NET 코드 조각에서는 연결 문자열을 사용하는 메서드에서 바로 가기를 사용하는 방법을 보여줍니다. 예를 들어 [BlobContainerClient(String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) 생성자는 연결 문자열을 사용합니다.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

코드 조각의 코드를 호출하기 전에 에뮬레이터가 실행되고 있는지 확인합니다.
