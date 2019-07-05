---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455159"
---
## <a name="local-settings-file"></a>로컬 설정 파일

Local.settings.json 파일에는 앱 설정, 연결 문자열 및 로컬 개발 도구에서 사용 되는 설정을 저장 합니다. Local.settings.json 파일에서 설정은 로컬로 실행 하는 경우에 사용 됩니다. 로컬 설정 파일에는 다음과 같은 구조에 있습니다.

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

로컬로 실행 하는 경우 다음 설정이 지원 됩니다.

| 설정      | 설명                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 로 설정 하면 `true`, 모든 값은 로컬 컴퓨터 키를 사용 하 여 암호화 됩니다. `func settings` 명령과 함께 사용됩니다. 기본값은 `false`입니다. |
| **`Values`** | 응용 프로그램 설정 및 로컬로 실행할 때 사용 하는 연결 문자열의 배열입니다. 이러한 키-값 (문자열) 쌍과 같은 Azure에서 함수 앱의 응용 프로그램 설정에 해당 [ `AzureWebJobsStorage` ]합니다. 많은 트리거와 바인딩에 같은 연결 문자열 앱 설정 참조 속성이 `Connection` 에 대 한 합니다 [Blob storage 트리거](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration)합니다. 이러한 속성에 정의 된 응용 프로그램 설정 해야 합니다 `Values` 배열입니다. <br/>[`AzureWebJobsStorage`] HTTP 이외의 트리거에 대 한 필수 앱 설정입니다. <br/>버전의 Functions 런타임 2.x 필요 합니다 [`FUNCTIONS_WORKER_RUNTIME`] 핵심 도구 프로젝트에 대해 생성 되는 설정입니다. <br/> 경우는 [Azure storage 에뮬레이터](../articles/storage/common/storage-use-emulator.md) 로컬로 설치를 설정할 수 있습니다 [ `AzureWebJobsStorage` ] 를 `UseDevelopmentStorage=true` 핵심 도구 에뮬레이터를 사용 합니다. 개발 중에 유용하지만 배포 전에 실제 저장소 연결을 테스트해야 합니다.<br/> 값은 문자열 및 없습니다 JSON 개체 또는 배열 이어야 합니다. 설정 이름은 콜론을 포함할 수 없습니다 (`:`) 또는 이중 밑줄 (`__`); 런타임에서 예약 된 합니다.  |
| **`Host`** | 이 섹션의 설정은 로컬에서 실행할 때 Functions 호스트 프로세스를 사용자 지정합니다. 이러한 작업은 Azure에서 실행 중인 경우에 적용 하는 host.json 설정을 별개입니다. |
| **`LocalHttpPort`** | 로컬 Functions 호스트(`func host start` 및 `func run`)를 실행할 때 사용되는 기본 포트를 설정합니다. `--port` 명령줄 옵션이 이 값보다 우선합니다. |
| **`CORS`** | [CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)에 허용된 원본을 정의합니다. 원본은 공백 없이 쉼표로 구분된 목록으로 제공됩니다. 와일드카드 값(\*)이 지원되므로 모든 원본에서 요청할 수 있습니다. |
| **`CORSCredentials`** |  허용 하려면 true로 설정 `withCredentials` 요청 합니다. |
| **`ConnectionStrings`** | 함수 바인딩에서 사용하는 연결 문자열에 대해 이 컬렉션을 사용하지 마십시오. 이 컬렉션은 일반적으로 연결 문자열에서 가져오기 프레임 워크만 사용 합니다 `ConnectionStrings` 섹션의 구성 파일을 [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). 이 개체의 연결 문자열은 공급자 유형이 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)인 환경에 추가됩니다. 이 컬렉션의 항목은 다른 앱 설정을 사용하여 Azure에 게시되지 않습니다. 이러한 값을 명시적으로 추가 해야 합니다 `Connection strings` 함수 앱 설정의 컬렉션입니다. 만들려는 경우는 [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) 함수 코드에서 연결 문자열 값을 저장 해야 **응용 프로그램 설정** 다른 연결을 사용 하 여 포털에 있습니다. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
