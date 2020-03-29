---
title: Azure 미디어 서비스 v3 API에 연결 - Node.js
description: 이 문서에서는 Node.js를 사용하여 미디어 서비스 v3 API에 연결하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896111"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>미디어 서비스 v3 API에 연결 - Node.js

이 문서에서는 서비스 주체 로그인 방법을 사용하여 Azure Media Services v3 node.js SDK에 연결하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Node.js를](https://nodejs.org/en/download/)설치합니다.
- [Media Services 계정 만들기](create-account-cli-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해야 합니다.

> [!IMPORTANT]
> [명명 규칙을 검토합니다.](media-services-apis-overview.md#naming-conventions)

## <a name="create-packagejson"></a>패키지 만들기.json

1. 즐겨 찾는 편집기에서 package.json 파일을 만듭니다.
1. 파일을 열고 다음 코드를 붙여 넣습니다.

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

다음 패키지를 지정해야 합니다.

|패키지|설명|
|---|---|
|`azure-arm-mediaservices`|Azure 미디어 서비스 SDK. <br/>최신 Azure 미디어 서비스 패키지를 사용 하려면 [NPM azure-arm-media services 설치를](https://www.npmjs.com/package/azure-arm-mediaservices/)확인합니다.|
|`azure-storage`|스토리지 SDK. 에셋에 파일을 업로드할 때 사용됩니다.|
|`ms-rest-azure`| 로그인하는 데 사용됩니다.|

다음 명령을 실행하여 최신 패키지를 사용하고 있는지 확인할 수 있습니다.

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Node.js 클라이언트에 연결

1. 즐겨 찾는 편집기에서 .js 파일을 만듭니다.
1. 파일을 열고 다음 코드를 붙여넣습니다.
1. "끝점 구성" 섹션의 값을 [액세스 API에서](access-api-cli-how-to.md)얻은 값으로 설정합니다.

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>앱 실행

명령 프롬프트를 엽니다. 샘플의 디렉토리를 찾아 다음 명령을 실행합니다.

```
npm install 
node index.js
```

## <a name="see-also"></a>참조

- [Media Services 개념](concepts-overview.md)
- [azure-arm-mediaservices NPM 설치](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>다음 단계

Media Services [Node.js 참조](/javascript/api/overview/azure/mediaservices/management) 설명서를 살펴보고 node.js와 함께 Media Services API를 사용하는 방법을 보여주는 [샘플](https://github.com/Azure-Samples/media-services-v3-node-tutorials)을 확인하세요.

