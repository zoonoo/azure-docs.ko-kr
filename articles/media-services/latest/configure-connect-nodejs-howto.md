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
ms.openlocfilehash: 9628e46281e267bd1c1fd8277a3a975bc338c6dc
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096065"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Media Services v3 API에 연결-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 서비스 사용자 로그인 방법을 사용 하 여 Azure Media Services v3 node.js SDK에 연결 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Node.js](https://nodejs.org/en/download/)를 설치합니다.
- [Typescript](https://www.typescriptlang.org/download)를 설치 합니다.
- [Media Services 계정 만들기](./create-account-howto.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해야 합니다.

> [!IMPORTANT]
> Azure Media Services [명명 규칙](media-services-apis-overview.md#naming-conventions) 을 검토 하 여 엔터티에 대 한 중요 한 명명 제한 사항을 이해 합니다. 

## <a name="reference-documentation-for-azurearm-mediaservices"></a>에 대 한 참조 설명서 @Azure/arm-mediaservices
- [Node.jsAzure Media Services 모듈에 대 한 참조 설명서 ](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Azure의 Node.js에 대 한 추가 개발자 설명서
- [개발자 Node.js JavaScript & Azure](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [@azure/azure-sdk-for-jsGit 허브 리포지토리의 Media Services 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js 개발자를 위한 Azure 패키지 설명서](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="install-the-packages"></a>패키지 설치

1. 선호 하는 편집기를 사용 하 여 파일에 package.js를 만듭니다.
1. 파일을 열고 다음 코드를 붙여 넣습니다.

   최신 버전의 [JavaScript 용 AZUREMEDIASERVICES SDK](https://www.npmjs.com/package/@azure/arm-mediaservices)를 다운로드 해야 합니다.

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

다음 패키지를 지정 해야 합니다.

|패키지|Description|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK <br/>최신 Azure Media Services 패키지를 사용 하 고 있는지 확인 하려면 [npm 설치 @azure/arm-mediaservices ](https://www.npmjs.com/package/@azure/arm-mediaservices)를 확인 합니다.|
|`@azure/ms-rest-nodeauth` | 서비스 주체 또는 관리 Id를 사용 하는 AAD 인증에 필요|
|`@azure/storage-blob`|저장소 SDK. 자산에 파일을 업로드할 때 사용 됩니다.|
|`@azure/ms-rest-js`| 로그인 하는 데 사용 됩니다.|
|`@azure/storage-blob` | 인코딩에 대 한 Azure Media Services의 자산에 파일을 업로드 하 고 다운로드 하는 데 사용 됩니다.|
|`@azure/abort-controller`| 저장소 클라이언트와 함께 장기 실행 다운로드 작업 시간 제한에 사용 됩니다.|


다음 명령을 실행 하 여 최신 패키지를 사용 하 고 있는지 확인할 수 있습니다.

### <a name="install-azurearm-mediaservices"></a>@azure/arm-mediaservices 설치
```
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>@azure/ms-rest-nodeauth 설치

" @azure/ms-rest-nodeauth ": "^ 3.0.0"의 최소 버전을 설치 하세요.

```
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

## <a name="connect-to-nodejs-client-using-typescript"></a>TypeScript를 사용 하 여 Node.js 클라이언트에 연결

1. 원하는 편집기를 사용 하 여 TypeScript 파일을 만듭니다.
1. 파일을 열고 다음 코드를 붙여넣습니다.
1. Env 파일을 만들고 Azure Portal의 세부 정보를 입력 합니다. [액세스 api](./access-api-howto.md)를 참조 하세요.

### <a name="sample-env-file"></a>Sample env 파일
```
# copy the content of this file to a file named ".env". It should be stored at the root of the repo.
# The values can be obtained from the API Access page for your Media Services account in the portal.
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

## <a name="typescript---hello-world---list-assets"></a>Typescript-헬로 월드 목록 자산
이 샘플에서는 서비스 주체를 사용 하 여 Media Services 클라이언트에 연결 하 고 계정에 자산을 나열 하는 방법을 보여 줍니다. 새 계정을 사용 하는 경우 목록이 빈 상태로 돌아갑니다. 포털에서 몇 가지 자산을 업로드 하 여 결과를 볼 수 있습니다.

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

## <a name="run-the-sample-application-helloworld-listassets"></a>샘플 응용 프로그램을 실행 HelloWorld-ListAssets

Node.js 샘플에 대 한 리포지토리를 복제 합니다.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

디렉터리를 AMSv3Samples 폴더로 변경 합니다.
```bash
cd AMSv3Samples
```

packages.js에서 사용 되는 패키지를 설치 합니다.
```
npm install 
```

디렉터리를 HelloWorld-ListAssets 폴더로 변경 합니다.
```bash
cd HelloWorld-ListAssets
```

AMSv3Samples 폴더에서 Visual Studio Code를 시작 합니다. "Vscode" 폴더와 파일에 대 한 tsconfig.js있는 폴더에서을 시작 하려면이를 실행 해야 합니다.
```dotnetcli
cd ..
code .
```

HelloWorld-ListAssets의 폴더를 열고 Visual Studio Code 편집기에서 해당 파일을 엽니다.
Node.js 파일에 있는 동안 F5 키를 눌러 디버거를 시작 합니다. 계정에 자산이 이미 있는 경우 표시 되는 자산 목록이 표시 됩니다. 계정이 비어 있으면 빈 목록이 표시 됩니다.  포털에서 몇 가지 자산을 업로드 하 여 결과를 확인 합니다.

## <a name="more-samples"></a>다른 샘플

[리포지토리에서](https://github.com/Azure-Samples/media-services-v3-node-tutorials) 사용할 수 있는 샘플은 다음과 같습니다.

|프로젝트 이름|사용 사례|
|---|---|
|라이브/인덱스 ts| 기본 라이브 스트리밍 예제입니다. **경고**, live를 사용 하는 경우 모든 리소스가 정리 되었으며 포털에서 더 이상 청구 되지 않는지 확인 해야 합니다.|
|StreamFilesSample/인덱스| 로컬 파일을 업로드 하거나 원본 URL에서 인코딩하는 기본 예제입니다. 샘플에서는 저장소 SDK를 사용 하 여 콘텐츠를 다운로드 하는 방법을 보여 주고 플레이어에 게 스트리밍하는 방법을 보여 줍니다. |
|StreamFilesWithDRMSample/index ts| Widevine 및 PlayReady DRM을 사용 하 여 인코딩 및 스트리밍하는 방법을 보여 줍니다. |
|VideoIndexerSample/인덱스| 비디오 및 오디오 분석기의 미리 설정을 사용 하 여 비디오 또는 오디오 파일에서 메타 데이터 및 정보를 생성 하는 예제 |

## <a name="see-also"></a>추가 정보

- [Media Services 개념](concepts-overview.md)
- [npm 설치 @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [개발자 Node.js JavaScript & Azure](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [리포지토리의 소스 코드 Media Services @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>다음 단계

Media Services [Node.js 참조](/javascript/api/overview/azure/mediaservices/management) 설명서를 살펴보고 node.js와 함께 Media Services API를 사용하는 방법을 보여주는 [샘플](https://github.com/Azure-Samples/media-services-v3-node-tutorials)을 확인하세요.
