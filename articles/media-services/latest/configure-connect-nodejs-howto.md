---
title: Azure Media Services v3 API에 연결-Node.js
description: 이 문서에서는 Node.js를 사용 하 여 Media Services v3 API에 연결 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: ab0113823bb5751828a71a9afd8c474091272e16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724628"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Media Services v3 API에 연결-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 서비스 사용자 로그인 방법을 사용 하 여 Azure Media Services v3 node.js SDK에 연결 하는 방법을 보여 줍니다. *미디어 서비스-v3-자습서* 샘플 리포지토리에서 파일을 사용 합니다. *HelloWorld-listassets* 샘플은 계정에 연결 하 여 자산을 나열 하는 코드를 포함 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Visual Studio Code 설치.
- [Node.js](https://nodejs.org/en/download/)를 설치합니다.
- [Typescript](https://www.typescriptlang.org/download)를 설치 합니다.
- [Media Services 계정 만들기](./create-account-howto.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해야 합니다.
- 애플리케이션에 대한 서비스 주체를 만듭니다. [액세스 api](./access-api-howto.md)를 참조 하세요.<br/>**Pro 팁!** 이 창을 열려 있는 상태로 유지 하거나 JSON 탭의 모든 항목을 메모장으로 복사 합니다. 
- 최신 버전의 [JavaScript 용 AZUREMEDIASERVICES SDK](https://www.npmjs.com/package/@azure/arm-mediaservices)를 다운로드 해야 합니다.

> [!IMPORTANT]
> Azure Media Services [명명 규칙](media-services-apis-overview.md#naming-conventions) 을 검토 하 여 엔터티에 대 한 중요 한 명명 제한 사항을 이해 합니다.

## <a name="clone-the-nodejs-samples-repo"></a>Node.JS 샘플 리포지토리를 복제 합니다.

Azure 샘플에서 일부 파일을 사용 하 게 됩니다. Node.JS 샘플 리포지토리를 복제 합니다.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>패키지 설치

### <a name="install-azurearm-mediaservices"></a>@azure/arm-mediaservices 설치

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>@azure/ms-rest-nodeauth 설치

" @azure/ms-rest-nodeauth ": "^ 3.0.0"의 최소 버전을 설치 하세요.

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

이 예에서는 파일에서 다음 패키지를 사용 합니다 `package.json` .

|패키지|Description|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK <br/>최신 Azure Media Services 패키지를 사용 하 고 있는지 확인 하려면 [npm 설치 @azure/arm-mediaservices ](https://www.npmjs.com/package/@azure/arm-mediaservices)를 확인 합니다.|
|`@azure/ms-rest-nodeauth` | 서비스 주체 또는 관리 Id를 사용 하는 AAD 인증에 필요|
|`@azure/storage-blob`|저장소 SDK. 자산에 파일을 업로드할 때 사용 됩니다.|
|`@azure/ms-rest-js`| 로그인 하는 데 사용 됩니다.|
|`@azure/storage-blob` | 인코딩에 대 한 Azure Media Services의 자산에 파일을 업로드 하 고 다운로드 하는 데 사용 됩니다.|
|`@azure/abort-controller`| 저장소 클라이언트와 함께 장기 실행 다운로드 작업 시간 제한에 사용 됩니다.|

### <a name="create-the-packagejson-file"></a>파일에 package.js를 만듭니다.

1. `package.json`즐겨 사용 하는 편집기를 사용 하 여 파일을 만듭니다.
1. 파일을 열고 다음 코드를 붙여 넣습니다.

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>TypeScript를 사용 하 여 Node.js 클라이언트에 연결



### <a name="sample-env-file"></a>Sample *env* 파일

이 파일의 내용을 *env* 라는 파일에 복사 합니다. 작업 리포지토리의 루트에 저장 되어야 합니다. 이러한 값은 포털에서 Media Services 계정에 대 한 API 액세스 페이지에서 가져온 값입니다.

*Env* 파일을 만들었으면 샘플 작업을 시작할 수 있습니다.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>샘플 응용 프로그램 *HelloWorld-listassets* 실행

1. 디렉터리를 *AMSv3Samples* 폴더로 변경 합니다.

```bash
cd AMSv3Samples
```

2. 파일 *의packages.js* 에 사용 되는 패키지를 설치 합니다.

```
npm install 
```

3. 디렉터리를 *HelloWorld-listassets* 폴더로 변경 합니다.

```bash
cd HelloWorld-ListAssets
```

4. AMSv3Samples 폴더에서 Visual Studio Code를 시작 합니다. "Vscode" 폴더와 파일에 대 한 tsconfig.js있는 폴더에서을 시작 하려면이를 실행 해야 합니다.

```dotnetcli
cd ..
code .
```

*HelloWorld-listassets* 의 폴더를 열고 Visual Studio Code 편집기 *에서 해당 파일을 엽니다* .

Node.js 파일에 있는 동안 F5 키를 눌러 디버거를 시작 *합니다.* 계정에 자산이 이미 있는 경우 표시 되는 자산 목록이 표시 됩니다. 계정이 비어 있으면 빈 목록이 표시 됩니다.  

표시 된 자산을 신속 하 게 확인 하려면 포털을 사용 하 여 몇 가지 비디오 파일을 업로드 합니다. 자산은 자동으로 하나씩 생성 되 고이 스크립트를 다시 실행 하면 해당 이름이 반환 됩니다.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>*HelloWorld-listassets* 샘플 자세히 보기

*HelloWorld-listassets* 샘플에서는 서비스 주체를 사용 하 여 Media Services 클라이언트에 연결 하 고 계정에 자산을 나열 하는 방법을 보여 줍니다. 수행 하는 작업에 대 한 자세한 설명은 코드의 설명을 참조 하세요.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>다른 샘플

[리포지토리에서](https://github.com/Azure-Samples/media-services-v3-node-tutorials) 사용할 수 있는 샘플은 다음과 같습니다.

|프로젝트 이름|사용 사례|
|---|---|
|라이브/인덱스 ts| 기본 라이브 스트리밍 예제입니다. **경고**, live를 사용 하는 경우 모든 리소스가 정리 되었으며 포털에서 더 이상 청구 되지 않는지 확인 해야 합니다.|
|StreamFilesSample/인덱스| 로컬 파일을 업로드 하거나 원본 URL에서 인코딩하는 기본 예제입니다. 샘플에서는 저장소 SDK를 사용 하 여 콘텐츠를 다운로드 하는 방법을 보여 주고 플레이어에 게 스트리밍하는 방법을 보여 줍니다. |
|StreamFilesWithDRMSample/index ts| Widevine 및 PlayReady DRM을 사용 하 여 인코딩 및 스트리밍하는 방법을 보여 줍니다. |
|VideoIndexerSample/인덱스| 비디오 및 오디오 분석기의 미리 설정을 사용 하 여 비디오 또는 오디오 파일에서 메타 데이터 및 정보를 생성 하는 예제 |

## <a name="see-also"></a>참고 항목

- [Node.jsAzure Media Services 모듈에 대 한 참조 설명서 ](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)
- [개발자 Node.js JavaScript & Azure](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [@azure/azure-sdk-for-jsGit 허브 리포지토리의 Media Services 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js 개발자를 위한 Azure 패키지 설명서](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)
- [Media Services 개념](concepts-overview.md)
- [npm 설치 @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [개발자 Node.js JavaScript & Azure](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [리포지토리의 소스 코드 Media Services @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>다음 단계

Media Services [Node.js 참조](/javascript/api/overview/azure/mediaservices/management) 설명서를 살펴보고 node.js와 함께 Media Services API를 사용하는 방법을 보여주는 [샘플](https://github.com/Azure-Samples/media-services-v3-node-tutorials)을 확인하세요.
