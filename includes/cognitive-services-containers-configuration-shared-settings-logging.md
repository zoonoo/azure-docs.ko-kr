---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: c8235fa65a3be91956ffad731d47488a852e42a2
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977354"
---
`Logging` 설정은 컨테이너에 대한 ASP.NET Core 로깅 지원을 관리합니다. ASP.NET Core 애플리케이션에 사용하는 컨테이너에 대한 동일한 구성 설정 및 값을 사용할 수 있습니다. 

컨테이너에서 지원되는 로깅 공급자는 다음과 같습니다.

|공급자|목적|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` 로깅 공급자입니다. 이 로깅 공급자에 대한 모든 ASP.NET Core 구성 설정 및 기본값이 지원됩니다.|
|[디버그](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` 로깅 공급자입니다. 이 로깅 공급자에 대한 모든 ASP.NET Core 구성 설정 및 기본값이 지원됩니다.|
|[디스크](#disk-logging)|JSON 로깅 공급자입니다. 이 로깅 공급자는 출력 탑재에 로그 데이터를 씁니다.|

### <a name="disk-logging"></a>디스크 로깅

`Disk` 로깅 공급자는 다음과 같은 구성 설정을 지원합니다.  

| 이름 | 데이터 형식 | 설명 |
|------|-----------|-------------|
| `Format` | 문자열 | 로그 파일에 대한 출력 형식입니다.<br/> **참고:** 이 값은 `json`으로 설정해야 로깅 공급자를 사용할 수 있습니다. 또한 컨테이너를 인스턴스화하는 동안 출력 탑재를 지정하지 않고 이 값이 지정된 경우에는 오류가 발생합니다. |
| `MaxFileSize` | 정수  | 로그 파일의 최대 크기(MB)입니다. 현재 로그 파일의 크기가 이 값을 충족하거나 초과할 경우 로깅 공급자가 새 로그 파일을 시작합니다. -1을 지정하면 로그 파일의 크기는 출력 탑재(있는 경우)에 대한 최대 파일 크기에 의해서만 제한됩니다. 기본값은 1입니다. |

ASP.NET Core 로깅 지원 구성에 대한 자세한 내용은 [설정 파일 구성](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)을 참조하세요.
