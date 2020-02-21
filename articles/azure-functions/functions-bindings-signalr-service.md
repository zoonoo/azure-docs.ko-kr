---
title: Azure Functions SignalR Service 바인딩
description: Azure Functions에서 SignalR Service 바인딩을 사용하는 방법을 배웁니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523039"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions의 SignalR Service 바인딩

이 문서 집합에서는 Azure Functions의 SignalR 서비스 바인딩을 사용 하 여 [Azure SignalR service](https://azure.microsoft.com/services/signalr-service/) 에 연결 된 클라이언트에 실시간 메시지를 전송 하 고 전송 하는 방법을 설명 합니다. Azure Functions는 SignalR Service에 대한 입력 및 출력 바인딩을 지원합니다.

| 작업 | Type |
|---------|---------|
| 서비스 끝점 URL 및 액세스 토큰 반환 | [입력 바인딩](./functions-bindings-signalr-service-input.md) |
| SignalR Service 메시지 보내기 |[출력 바인딩](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>함수 2.x 이상

트리거와 바인딩을 사용 하려면 적절 한 패키지를 참조 해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용 되는 반면 확장 번들은 다른 모든 응용 프로그램 형식에 사용 됩니다.

| 언어                                        | 추가 방법 ...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치, 버전 3(sp3) | |
| C#스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure 도구 확장] 은 Visual Studio Code와 함께 사용 하는 것이 좋습니다. |
| C#스크립트 (온라인 전용 Azure Portal)         | 바인딩 추가                            | 함수 앱을 다시 게시 하지 않고 기존 바인딩 확장을 업데이트 하려면 [확장 업데이트]를 참조 하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./install-update-binding-extensions-manual.md
[Azure 도구 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

SignalR 서비스와 Azure Functions를 함께 구성 하 고 사용 하는 방법에 대 한 자세한 내용은 [Azure SignalR service를 사용 하 여 개발 및 구성 Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md)을 참조 하세요.

### <a name="annotations-library-java-only"></a>주석 라이브러리 (Java 전용)

Java 함수에서 SignalR 서비스 주석을 사용 하려면 *pom .xml* 파일에 *azure-SignalR* 아티팩트 (버전 1.0 이상)에 대 한 종속성을 추가 해야 합니다.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>다음 단계

- [서비스 끝점 URL 및 액세스 토큰 반환 (입력 바인딩)](./functions-bindings-signalr-service-input.md)
- [SignalR 서비스 메시지 보내기 (출력 바인딩)](./functions-bindings-signalr-service-output.md) 
