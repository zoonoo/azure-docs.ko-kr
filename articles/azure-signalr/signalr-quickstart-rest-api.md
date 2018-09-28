---
title: 빠른 시작 - Azure SignalR Service REST API | Microsoft Docs
description: Azure SignalR Service REST API를 사용하기 위한 빠른 시작입니다.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972762"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>빠른 시작: 콘솔 앱에서 실시간 메시지 브로드캐스트

Azure SignalR Service는 브로드캐스트와 같은 서버-클라이언트 통신 시나리오를 지원하는 [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)를 제공합니다. REST API를 호출할 수 있는 모든 프로그래밍 언어를 선택할 수 있습니다. 연결된 모든 클라이언트, 이름별 특정 클라이언트 또는 클라이언트 그룹에 메시지를 게시할 수 있습니다.

이 빠른 시작에서는 명령줄 앱에서 C#으로 작성된 연결된 클라이언트 앱에 메시지를 보내는 방법을 알아보겠습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작은 macOS, Windows 또는 Linux에서 실행할 수 있습니다.
* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* 선택한 텍스트 편집기 또는 코드 편집기


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 <https://portal.azure.com/>에서 Azure Portal에 로그인합니다.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

서비스가 배포되는 동안 코드 준비로 전환하겠습니다. [GitHub의 샘플 앱](https://github.com/aspnet/AzureSignalR-samples.git)을 복제하고, SignalR Service 연결 문자열을 설정하고, 응용 프로그램을 로컬로 실행합니다.

1. Git 터미널 창을 엽니다. 샘플 프로젝트를 복제할 폴더로 변경합니다.

1. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

이 샘플은 Azure SignalR Service 사용을 보여 주는 콘솔 앱입니다. 다음 두 가지 모드를 제공합니다.

- 서버 모드: 단순 명령을 사용하여 Azure SignalR Service REST API를 호출합니다.
- 클라이언트 모드: Azure SignalR Service에 연결하고 서버로부터 메시지를 받습니다.

또한 액세스 토큰을 생성하여 Azure SignalR Service에 인증하는 방법을 확인할 수 있습니다.

### <a name="build-the-executable-file"></a>실행 파일 빌드

macOS osx.10.13-x64를 예제로 사용합니다. 다른 플랫폼에서 빌드하는 방법에 대한 [참조](https://docs.microsoft.com/dotnet/core/rid-catalog)를 확인할 수 있습니다.
```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>클라이언트 시작

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>서버 시작

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>게시하지 않고 샘플 실행

아래 명령을 실행하여 서버 또는 클라이언트를 시작할 수도 있습니다.

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>user-secrets를 사용하여 연결 문자열 지정

샘플의 루트 디렉터리에서 `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"`을 실행할 수 있습니다. 그 뒤에는 `-c "<ConnectionString>"` 옵션이 더 이상 필요하지 않습니다.

## <a name="usage"></a>사용 현황

서버가 시작된 후에 명령을 사용하여 메시지 보내기

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

각기 다른 클라이언트 이름으로 여러 클라이언트를 시작할 수 있습니다.


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
