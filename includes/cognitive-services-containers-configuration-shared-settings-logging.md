---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 873fd8cbc211f098c93b8fb3fbe701e4a34d8487
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68320496"
---
`Logging` 설정은 컨테이너에 대한 ASP.NET Core 로깅 지원을 관리합니다. ASP.NET Core 애플리케이션에 사용하는 컨테이너에 대한 동일한 구성 설정 및 값을 사용할 수 있습니다. 

컨테이너에서 지원되는 로깅 공급자는 다음과 같습니다.

|공급자|목적|
|--|--|
|[콘솔](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` 로깅 공급자입니다. 이 로깅 공급자에 대한 모든 ASP.NET Core 구성 설정 및 기본값이 지원됩니다.|
|[디버그](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` 로깅 공급자입니다. 이 로깅 공급자에 대한 모든 ASP.NET Core 구성 설정 및 기본값이 지원됩니다.|
|[디스크](#disk-logging)|JSON 로깅 공급자입니다. 이 로깅 공급자는 출력 탑재에 로그 데이터를 씁니다.|

이 컨테이너 명령은 출력 탑재에 대한 JSON 형식의 로깅 정보를 저장합니다.

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

이 컨테이너 명령은 컨테이너가 실행되는 동안 접두사가 `dbug`인 디버깅 정보를 보여줍니다.

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>디스크 로깅

`Disk` 로깅 공급자는 다음과 같은 구성 설정을 지원합니다.

| 속성 | 데이터 형식 | Description |
|------|-----------|-------------|
| `Format` | String | 로그 파일에 대한 출력 형식입니다.<br/> **참고:** 로깅 공급자를 사용 설정하려면 이 값을 `json`으로 설정해야 합니다. 또한 컨테이너를 인스턴스화하는 동안 출력 탑재를 지정하지 않고 이 값이 지정된 경우에는 오류가 발생합니다. |
| `MaxFileSize` | 정수 | 로그 파일의 최대 크기(MB)입니다. 현재 로그 파일의 크기가 이 값을 충족하거나 초과할 경우 로깅 공급자가 새 로그 파일을 시작합니다. -1을 지정하면 로그 파일의 크기는 출력 탑재(있는 경우)에 대한 최대 파일 크기에 의해서만 제한됩니다. 기본값은 1입니다. |

ASP.NET Core 로깅 지원 구성에 대한 자세한 내용은 [설정 파일 구성](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)을 참조하세요.

