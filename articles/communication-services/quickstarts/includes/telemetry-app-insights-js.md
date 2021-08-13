---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: orwatson
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: orwatson
ms.openlocfilehash: 8ad763ed5f0213569682599aaa0fc3cba4651939
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292402"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전.
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).
- [Azure Portal](../../../azure-monitor/app/create-new-resource.md)에서 Application Insights 리소스를 만듭니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-app"></a>새 Node.js 앱 만들기

ACS 라이브러리를 사용하는 앱이 이미 있고 JavaScript에 익숙한 경우 [추적 프로그램 설정](#setup-tracer)으로 건너뛸 수 있습니다.

시작하려면 클라이언트 라이브러리 중 하나를 사용하는 JS 앱이 필요합니다. [@azure/communication-identity](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/communication/communication-identity) 라이브러리를 사용하여 앱을 만듭니다. 아래 단계에 따라 샘플 앱을 설정합니다.

터미널 또는 명령 창을 열고, 새 디렉터리를 만들고, 해당 디렉터리로 이동합니다.

```console
mkdir export-js-telemetry && cd export-js-telemetry
```
`npm init -y`를 실행하여 기본 설정으로 **package.json** 을 만듭니다.

```console
npm init -y
```

### <a name="install-dependencies"></a>종속성 설치

`npm install` 명령을 사용하여 앱에서 사용할 종속성을 설치합니다.

```bash
npm install @azure/communication-identity @azure/monitor-opentelemetry-exporter @opentelemetry/node@^0.14.0 @opentelemetry/plugins-node-core --save
```

`--save` 옵션은 이전 단계에서 만든 **package.json** 파일에 라이브러리를 종속성으로 추가합니다.

특정 버전의 `@opentelemetry/node@^0.14.0`을 기억해야 합니다. 가이드를 작성한 시점에 이 버전은 `@azure/monitor-opentelemetry-exporter`와 완전히 호환된 패키지의 최신 버전이었습니다.

### <a name="add-main-app-file"></a>기본 앱 파일 추가

디렉터리 루트에 **token.js** 파일을 만듭니다. 파일에 다음 코드를 추가합니다.

```javascript
// token.js

const { CommunicationIdentityClient } = require("@azure/communication-identity");

async function main() {
  const ACS_CONNECTION_STRING = "<your-acs-connection-string>"
  const client = new CommunicationIdentityClient(ACS_CONNECTION_STRING);

  console.log("Creating user...")
  const user = await client.createUser();
  console.log(`User ${user.communicationUserId} was created successfully.\n`);

  console.log(`Issuing token for ${user.communicationUserId}...`);
  const { token } = await client.getToken(user, ["chat"]);
  console.log("Token issued successfully.\n")
  console.log(`${token}\n`);

  console.log(`Revoking token for ${user.communicationUserId}...`);
  await client.revokeTokens(user);
  console.log("Token revoked successfully.\n");

  console.log(`Deleting user ${user.communicationUserId}...`);
  await client.deleteUser(user);
  console.log("User deleted successfully.\n");
}

main().catch((error) => {
  console.log("Encountered an error:", error);
  process.exit(1);
});
```
## <a name="setup-tracer"></a>추적 프로그램 설정

디렉터리 루트에 **tracing.js** 파일을 만듭니다. 파일에 다음 코드를 추가합니다.

```javascript
// tracing.js

const azureSdkTracing = require("@azure/core-tracing");
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { NodeTracerProvider } = require("@opentelemetry/node");
const { BatchSpanProcessor } = require("@opentelemetry/tracing");

const AI_CONNECTION_STRING = "<your-application-insights-connection-string>";
const provider = new NodeTracerProvider();
const azExporter = new AzureMonitorTraceExporter({
  connectionString: AI_CONNECTION_STRING
});

provider.addSpanProcessor(
  new BatchSpanProcessor(azExporter, {
    bufferSize: 1000, // 1000 spans
    bufferTimeout: 5000 // 5 seconds
  })
);

provider.register();

const tracer = provider.getTracer("sample tracer");
azureSdkTracing.setTracer(tracer);

exports.default = tracer;
```

### <a name="add-tracer-to-main-app"></a>기본 앱에 추적 프로그램 추가

**token.js** 에 다음 업데이트를 적용합니다.

**tracing.js** 가 필요합니다.

```javascript
const { CommunicationIdentityClient } = require("@azure/communication-identity");
const tracer = require("./tracing.js");

async function main() {
```

루트 범위 만들기 및 데이터 내보내기

```javascript
  console.log("User deleted successfully.\n");
}

const rootSpan = tracer.startSpan("Root Identity Span");
tracer.withSpan(rootSpan, async function() {
  try {
    await main();
  } catch (error) {
    console.error("Error running sample:", error);
  } finally {
    // End the optional root span on completion
    rootSpan.end();
  }
}).then(function() {
  console.log("Awaiting batched span processor to export batched spans...");

  setTimeout(function() {
    console.log("Spans exported.");
  }, 6000);
});
```

## <a name="run-the-code"></a>코드 실행

자리 표시자 텍스트를 유효한 연결 문자열 값으로 바꾸어야 합니다.

node 명령을 사용하여 **token.js** 파일에 추가한 코드를 실행합니다.

```console
node token.js
```