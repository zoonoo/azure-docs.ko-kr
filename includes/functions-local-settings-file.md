---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205721"
---
## <a name="local-settings-file"></a>로컬 설정 파일

local.settings.json 파일은 로컬 개발 도구에서 사용하는 앱 설정, 연결 문자열 및 설정을 저장합니다. local.settings.json 파일의 설정은 프로젝트를 로컬로 실행하는 경우에만 사용됩니다. 로컬 설정 파일에는 다음 구조가 있습니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

이러한 설정은 프로젝트를 로컬로 실행할 때 지원됩니다.

| 설정      | 설명                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 이 설정을 `true`으로 설정하면 모든 값이 로컬 컴퓨터 키로 암호화됩니다. `func settings` 명령과 함께 사용됩니다. 기본값은 `false`입니다. |
| **`Values`** | 프로젝트가 로컬로 실행될 때 사용되는 응용 프로그램 설정 및 연결 문자열의 배열입니다. 이러한 키 값(문자열 문자열) 쌍은 와 같은 [`AzureWebJobsStorage`]Azure의 함수 앱의 응용 프로그램 설정에 해당합니다. 많은 트리거 및 바인딩에는 `Connection` [Blob 저장소 트리거와](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)같은 연결 문자열 앱 설정을 참조하는 속성이 있습니다. 이러한 속성의 경우 배열에 정의된 `Values` 응용 프로그램 설정이 필요합니다. <br/>[`AzureWebJobsStorage`]은 HTTP 이외의 트리거에 필요한 앱 설정입니다. <br/>버전 2.x 이상 함수 런타임에는 핵심`FUNCTIONS_WORKER_RUNTIME`도구에 의해 프로젝트에 대해 생성되는 [] 설정이 필요합니다. <br/> Azure 저장소 [에뮬레이터를](../articles/storage/common/storage-use-emulator.md) 로컬로 설치하고 'Core `UseDevelopmentStorage=true`Tools'로 설정하면 [`AzureWebJobsStorage`] 에뮬레이터를 사용합니다. 에뮬레이터는 개발 중에 유용하지만 배포 하기 전에 실제 저장소 연결을 사용 하 여야 합니다.<br/> 값은 JSON 개체 나 배열이 아닌 문자열이어야합니다. 이름을 설정하면 콜론()`:`또는 이중 밑줄()을`__`포함할 수 없습니다. 이러한 문자는 런타임에 의해 예약됩니다.  |
| **`Host`** | 이 섹션의 설정은 프로젝트를 로컬로 실행할 때 Functions 호스트 프로세스를 사용자 지정합니다. 이러한 설정은 host.json 설정과 는 별개로 Azure에서 프로젝트를 실행할 때도 적용됩니다. |
| **`LocalHttpPort`** | 로컬 Functions 호스트(`func host start` 및 `func run`)를 실행할 때 사용되는 기본 포트를 설정합니다. `--port` 명령줄 옵션이 이 설정보다 우선합니다. |
| **`CORS`** | [CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)에 허용된 원본을 정의합니다. 원본은 공백 없이 쉼표로 구분된 목록으로 제공됩니다. 와일드카드 값(\*)이 지원되므로 모든 원본에서 요청할 수 있습니다. |
| **`CORSCredentials`** |  `true`로 설정하면 `withCredentials` 요청을 허용합니다. |
| **`ConnectionStrings`** | 컬렉션입니다. 함수 바인딩에서 사용하는 연결 문자열에는 이 컬렉션을 사용하지 마십시오. 이 컬렉션은 일반적으로 [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)와 같은 구성 `ConnectionStrings` 파일 의 섹션에서 연결 문자열을 받는 프레임워크에서만 사용됩니다. 이 개체의 연결 문자열은 공급자 유형이 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)인 환경에 추가됩니다. 이 컬렉션의 항목은 다른 앱 설정으로 Azure에 게시되지 않습니다. 함수 앱 설정의 컬렉션에 `Connection strings` 이러한 값을 명시적으로 추가해야 합니다. 함수 코드에서 를 [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) 만드는 경우 포털의 **응용 프로그램 설정에서** 다른 연결과 연결 문자열 값을 저장해야 합니다. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
