---
title: Azure SignalR Service Serverless 빠른 시작 - Java
description: Azure SignalR Service와 Azure Functions를 사용하여 대화방을 만들기 위한 빠른 시작입니다.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 9e4e64b99a69e523547bae04146c7460d08bc1df
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261176"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-java"></a>빠른 시작: Java를 사용하여 Azure Functions와 SignalR Service로 대화방 만들기

Azure SignalR Service를 사용하면 애플리케이션에 실시간 기능을 쉽게 추가할 수 있습니다. Azure Functions는 인프라를 관리하지 않고 코드를 실행할 수 있는 서버리스 플랫폼입니다. 이 빠른 시작에서는 SignalR Serivces와 Functions를 사용하여 서버리스, 실시간 대화 애플리케이션을 빌드하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작은 macOS, Windows 또는 Linux에서 실행할 수 있습니다.

[Visual Studio Code](https://code.visualstudio.com/)와 같은 코드 편집기가 설치되어 있는지 확인합니다.

Azure Function 앱을 로컬로 실행하려면 [Azure Functions Core Tools(v2)](https://github.com/Azure/azure-functions-core-tools#installing)를 설치하세요.

> [!NOTE]
> Java에서 SignalR Service 바인딩을 사용하려면 Azure Functions Core Tools 버전 2.4.419 이상(호스트 버전 2.0.12332)을 사용하고 있는지 확인합니다.

확장을 설치하려면 현재 Azure Functions Core Tools에는 [.NET Core SDK](https://www.microsoft.com/net/download)가 설치되어 있어야 합니다. 그러나 JavaScript Azure Function 앱을 빌드하는 데는 .NET에 대한 지식이 필요하지 않습니다.

Java 통해 함수 앱을 개발하려면 다음을 설치해야 합니다.

* [Java Developer Kit](https://www.azul.com/downloads/zulu/) 버전 8
* [Apache Maven](https://maven.apache.org) 버전 3.0 이상

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 <https://portal.azure.com/>에서 Azure Portal에 로그인합니다.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Azure Function 앱을 구성하고 실행합니다.

1. Azure Portal이 열리는 브라우저에서, 포털의 맨 위에 있는 검색 상자에서 해당 이름을 검색하여 이전에 배포한 SignalR Service 인스턴스를 성공적으로 만들었는지 확인합니다. 인스턴스를 선택하여 엽니다.

    ![SignalR Service 인스턴스를 검색합니다.](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. SignalR Service 인스턴스의 연결 문자열을 보려면 **키**를 선택합니다.

1. 기본 연결 문자열을 선택하여 복사합니다.

    ![SignalR Service 만들기](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. 코드 편집기에서 복제된 리포지토리의 *src/chat/java* 폴더를 엽니다.

1. *local.settings.sample.json*의 이름을 *local.settings.json*으로 바꿉니다.

1. **local.settings.json** 에서 연결 문자열을 **AzureSignalRConnectionString** 설정 값에 붙여넣습니다. 파일을 저장합니다.

1. 함수가 포함된 주 파일은 *src/chat/java/src/main/java/com/function/Functions.java*에 있습니다.

    - **negotiate** - *SignalRConnectionInfo* 입력 바인딩을 사용하여 올바른 연결 정보를 생성하고 리턴합니다.
    - **sendMessage** - 요청 본문에서 대화 메시지를 수신하고 *SignalR* 출력 바인딩을 사용하여 모든 연결된 클라이언트 애플리케이션으로 메시지를 브로드캐스트합니다.

1. 터미널에서 *src/chat/java* 폴더에 있는지 확인합니다. Function App을 빌드합니다.

    ```bash
    mvn clean package
    ```

1. Function App을 로컬로 실행합니다.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Maven을 사용하여 실시간 서버리스 애플리케이션을 빌드하고 실행했습니다. 다음 과정으로, Java Azure Functions를 처음부터 새로 만드는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Java 및 Maven을 사용하여 첫 번째 함수 만들기](../azure-functions/functions-create-first-java-maven.md)