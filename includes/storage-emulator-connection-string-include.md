---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070567"
---
Azurite은 단일 고정 계정 및 공유 키 인증에 대 한 잘 알려진 인증 키를 지원 합니다. Azurite와 함께 사용할 수 있는 유일한 공유 키 자격 증명은이 계정과 키입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Azurite에서 지 원하는 인증 키는 클라이언트 인증 코드의 기능을 테스트 하기 위한 목적 으로만 사용 됩니다. 보안 기능은 제공하지 않습니다. Azurite에서 프로덕션 저장소 계정 및 키를 사용할 수 없습니다. 개발 계정을 프로덕션 데이터에 사용해서는 안 됩니다.
> 
> Azurite는 HTTP를 통해서만 연결을 지원 합니다. 그러나 프로덕션 Azure Storage 계정에서 리소스에 액세스하기 위한 프로토콜로 HTTPS를 사용하는 것이 좋습니다.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>바로 가기를 사용하여 에뮬레이터 계정에 연결
응용 프로그램에서 Azurite에 연결 하는 가장 쉬운 방법은 바로 가기를 참조 하는 응용 프로그램의 구성 파일에서 연결 문자열을 구성 하는 것입니다 `UseDevelopmentStorage=true` . *app.config* 파일의 Azurite에 대 한 연결 문자열의 예는 다음과 같습니다. 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>잘 알려진 계정 이름 및 키를 사용하여 에뮬레이터 계정에 연결
에뮬레이터 계정 이름 및 키를 참조하는 연결 문자열을 만들려면 연결 문자열의 에뮬레이터에서 사용하려는 각각의 서비스에 엔드포인트를 지정해야 합니다. 이 작업은 연결 문자열이 프로덕션 스토리지 계정의 엔드포인트와 다른 에뮬레이터 엔드포인트를 참조하도록 하기 위해 반드시 필요합니다. 예를 들어 연결 문자열의 값은 다음과 같이 표시됩니다.

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

이 값은 위에 표시된 바로 가기 `UseDevelopmentStorage=true`와 동일합니다.
