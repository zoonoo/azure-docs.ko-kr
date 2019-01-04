---
title: Node.js에서 Azure Cache for Redis를 사용하는 방법에 대한 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 Node.js 및 node_redis와 함께 Azure Cache for Redis를 사용하는 방법을 알아봅니다.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 05/21/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 67983adc9588ff445c45d8718e1ed0fc9ef3fc41
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53018793"
---
# <a name="quickstart-how-to-use-azure-cache-for-redis-with-nodejs"></a>빠른 시작: Node.js에서 Azure Cache for Redis를 사용하는 방법



Azure Cache for Redis를 통해 Microsoft에서 관리하는 안전한 전용 Azure Cache for Redis에 액세스할 수 있습니다. Microsoft Azure 내의 모든 애플리케이션에서 캐시에 액세스할 수 있습니다.

이 항목에서는 Node.js를 사용하여 Azure Cache for Redis를 시작하는 방법을 보여 줍니다. 

이 빠른 시작의 단계를 완료하려면 아무 코드 편집기나 사용할 수 있습니다. 그러나 [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 뛰어난 옵션입니다.

![완료된 캐시 앱](./media/cache-nodejs-get-started/cache-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>필수 조건
[node_redis](https://github.com/mranney/node_redis) 설치:

    npm install redis

이 자습서에서는 [node_redis](https://github.com/mranney/node_redis)를 사용합니다. 다른 Node.js 클라이언트를 사용한 예는 [Node.js Redis 클라이언트](http://redis.io/clients#nodejs)에 나열된 Node.js 클라이언트의 개별 설명서를 참조하세요.


## <a name="create-a-cache"></a>캐시 만들기
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


**호스트 이름** 및 **기본** 액세스 키에 대한 환경 변수를 추가합니다. 코드에 직접 중요한 정보를 포함하는 대신 사용자 코드에서 이러한 변수를 사용합니다.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```


## <a name="connect-to-the-cache"></a>캐시에 연결

[node_redis](https://github.com/mranney/node_redis)의 최신 빌드는 SSL을 사용하여 Azure Cache for Redis에 연결을 지원합니다. 다음 예제에서는 6380 SSL 엔드포인트를 사용하여 Azure Cache for Redis에 연결하는 방법을 보여 줍니다. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

코드에서 각 작업에 대한 새 연결을 만들지 마십시오. 대신 가능한 한 연결을 다시 사용합니다. 

## <a name="create-a-new-nodejs-app"></a>새 Node.js 앱 만들기

*redistest.js*라는 새 스크립트 파일을 만듭니다.

다음 예제 JavaScript를 파일에 추가합니다. 이 코드는 캐시 호스트 이름 및 키 환경 변수를 사용하여 Azure Cache for Redis 인스턴스에 연결하는 방법을 보여 줍니다. 코드는 또한 캐시에 문자열 값을 저장하고 검색합니다. 또한 `PING` 및 `CLIENT LIST` 명령을 실행했습니다. [node_redis](https://github.com/mranney/node_redis) 클라이언트가 포함된 Redis 사용에 관한 더 많은 예는 [http://redis.js.org/](http://redis.js.org/)를 참조하세요.

```js
var redis = require("redis");
var bluebird = require("bluebird");

bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the SSL port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demostrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Node.js를 사용하여 스크립트를 실행합니다.

```
node redistest.js
```

아래 예제에서는 이전에 Azure Portal에서 Redis 콘솔을 사용하여 설정된 캐시된 값이 있는 `Message` 키를 볼 수 있습니다. 앱에서 캐시된 값을 업데이트했습니다. 또한 앱에서 `PING` 및 `CLIENT LIST` 명령을 실행했습니다.

![완료된 캐시 앱](./media/cache-nodejs-get-started/cache-app-complete.png)


## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 계속 진행하려는 경우 이 빠른 시작에서 만든 리소스를 그대로 두었다가 다시 사용할 수 있습니다.

또는, 빠른 시작 샘플 애플리케이션 사용을 마친 경우 이 빠른 시작에서 만든 Azure 리소스를 삭제하여 요금이 청구되는 것을 방지할 수 있습니다. 

> [!IMPORTANT]
> 리소스 그룹 삭제는 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내에 이 샘플을 호스트하기 위한 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.
>

[Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

**이름을 기준으로 필터링...** 텍스트 상자에 리소스 그룹의 이름을 입력합니다. 이 문서의 지침에서는 *TestResources*라는 리소스 그룹을 사용했습니다. 결과 목록의 리소스 그룹에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

![삭제](./media/cache-nodejs-get-started/cache-delete-resource-group.png)

리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인한 후 **삭제**를 클릭합니다.

잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.



## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Node.js 애플리케이션에서 Azure Cache for Redis를 사용하는 방법을 알아보았습니다. ASP.NET 웹앱에서 Azure Cache for Redis를 사용하려면 다음 빠른 시작으로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Cache for Redis를 사용하는 ASP.NET 웹앱 만들기](./cache-web-app-howto.md)



