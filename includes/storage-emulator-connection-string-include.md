---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622044"
---
에뮬레이터는 단일 고정 계정과 공유 키 인증에 대 한 잘 알려진 인증 키를 지원 합니다. 이 계정과 키는 에뮬레이터에서 사용할 수 있는 유일한 공유 키 자격 증명입니다. 핵심 원리는 다음과 같습니다.

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> 에뮬레이터에서 지 원하는 인증 키는 클라이언트 인증 코드의 기능을 테스트 하는 용도로만 사용 됩니다. 보안 기능은 제공하지 않습니다. 에뮬레이터에서 프로덕션 저장소 계정 및 키를 사용할 수 없습니다. 개발 계정을 프로덕션 데이터에 사용해서는 안 됩니다.
>
> 에뮬레이터는 HTTP를 통해서만 연결을 지원 합니다. 그러나 프로덕션 Azure Storage 계정에서 리소스에 액세스하기 위한 프로토콜로 HTTPS를 사용하는 것이 좋습니다.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>바로 가기를 사용 하 여 에뮬레이터 계정에 연결

응용 프로그램에서 에뮬레이터에 연결 하는 가장 쉬운 방법은 바로 가기를 참조 하는 응용 프로그램의 구성 파일에서 연결 문자열을 구성 하는 것입니다 `UseDevelopmentStorage=true` . 바로 가기는 모든 Azure Storage 서비스에 대 한 계정 이름, 계정 키 및 에뮬레이터 끝점을 지정 하는 에뮬레이터의 전체 연결 문자열에 해당 합니다.

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

다음 .NET 코드 조각에서는 연결 문자열을 사용 하는 메서드의 바로 가기를 사용 하는 방법을 보여 줍니다. 예를 들어 [BlobContainerClient (string, string)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) 생성자는 연결 문자열을 사용 합니다.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

코드 조각의 코드를 호출 하기 전에 에뮬레이터가 실행 되 고 있는지 확인 합니다.
