---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114667"
---
저장소 에뮬레이터는 단일 고정 계정과 공유 키 인증에 대해 알려진 인증 키를 지원합니다. 저장소 에뮬레이터에서 사용할 수 있는 공유 키 자격 증명은 이 계정과 키뿐입니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> 저장소 에뮬레이터에서 지원하는 인증 키는 클라이언트 인증 코드의 기능을 테스트할 때만 사용할 수 있으며 보안 기능은 제공하지 않습니다. 프로덕션 저장소 계정과 키를 저장소 에뮬레이터에서 사용할 수는 없습니다. 개발 계정을 프로덕션 데이터에 사용해서는 안 됩니다.
> 
> 저장소 에뮬레이터는 HTTP를 통해서만 연결을 지원합니다. 그러나 프로덕션 Azure Storage 계정에서 리소스에 액세스하기 위한 프로토콜로 HTTPS를 사용하는 것이 좋습니다.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>바로 가기를 사용하여 에뮬레이터 계정에 연결
애플리케이션에서 스토리지 에뮬레이터에 연결하는 가장 쉬운 방법은 바로 가기를 참조하는 애플리케이션의 구성 파일에서 연결 문자열을 구성하는 것입니다.`UseDevelopmentStorage=true` 예를 들어 *app.config* 파일의 저장소 에뮬레이터에 대한 연결 문자열은 다음과 같습니다. 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>잘 알려진 계정 이름 및 키를 사용하여 에뮬레이터 계정에 연결
에뮬레이터 계정 이름 및 키를 참조하는 연결 문자열을 만들려면 연결 문자열의 에뮬레이터에서 사용하려는 각각의 서비스에 엔드포인트를 지정해야 합니다. 이 작업은 연결 문자열이 프로덕션 저장소 계정의 엔드포인트와 다른 에뮬레이터 엔드포인트를 참조하도록 하기 위해 반드시 필요합니다. 예를 들어 연결 문자열의 값은 다음과 같이 표시됩니다.

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

이 값은 위에 표시된 바로 가기 `UseDevelopmentStorage=true`와 동일합니다.

#### <a name="specify-an-http-proxy"></a>HTTP 프록시 지정
저장소 에뮬레이터에 대해 서비스를 테스트할 때 사용할 HTTP 프록시를 지정할 수도 있습니다. 이렇게 하면 저장소 서비스에 대한 작업을 디버깅하면서 HTTP 요청과 응답을 관찰하는 데 유용할 수 있습니다. 프록시를 지정하려면 `DevelopmentStorageProxyUri` 옵션을 연결 문자열에 추가하고 해당 값을 프록시 URI로 설정합니다. 예를 들어 저장소 에뮬레이터를 가리키고 HTTP 프록시를 구성하는 연결 문자열은 다음과 같습니다.

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

