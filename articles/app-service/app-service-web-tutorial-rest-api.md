---
title: "Azure App Service의 Node.js API 앱 | Microsoft Docs"
description: "Node.js RESTful API를 만들어 Azure App Service의 API 앱에 배포하는 방법에 대해 알아보세요."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/13/2017
ms.author: rachelap
ms.custom: mvc, devcenter
ms.openlocfilehash: 30c3df4ebc4417993170b994fc01bc82636dec3f
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2017
---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Node.js RESTful API를 빌드하여 Azure의 API 앱에 배포

이 빠른 시작에서는 Node.js [Express](http://expressjs.com/)로 작성된 REST API를 만들고 [Swagger](http://swagger.io/) 정의를 사용하여 Azure에 배포하는 방법을 보여 줍니다. 명령줄 도구를 사용하여 앱을 만들고, [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 리소스를 구성하고, Git를 사용하여 앱을 배포합니다.  완료하면 Azure에서 실행되는 작업 샘플 REST API를 갖습니다.

## <a name="prerequisites"></a>필수 조건

* [Git](https://git-scm.com/)
* [Node.js 및 NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="prepare-your-environment"></a>환경 준비

1. 터미널 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플을 복제합니다.

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. 샘플 코드를 포함하는 디렉터리로 변경합니다.

    ```bash
    cd app-service-api-node-contact-list
    ```

3. 로컬 컴퓨터에 [Swaggerize](https://www.npmjs.com/package/swaggerize-express)를 설치합니다. Swaggerize는 Swagger 정의에서 REST API에 대한 Node.js 코드를 생성하는 도구입니다.

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>Node.js 코드 생성 

자습서의 이 섹션에서는 Swagger 메타데이터를 먼저 만들고 API에 대한 서버 코드에 스캐폴딩(자동 생성)하는 데 사용할 API 개발 워크플로를 모델링합니다. 

디렉터리를 *시작* 폴더로 변경한 다음 `yo swaggerize`를 실행합니다. Swaggerize는 *api.json*으로 Swagger 정의에서 API에 대한 Node.js 프로젝트를 만듭니다.

```bash
cd start
yo swaggerize --apiPath api.json --framework express
```

Swaggerize가 프로젝트 이름을 물어보면 *ContactList*를 사용합니다.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>프로젝트 코드 사용자 지정

1. *lib* 폴더를 `yo swaggerize`에서 만든 *ContactList* 폴더로 복사한 다음 디렉터리를 *ContactList*로 변경합니다.

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. `jsonpath` 및 `swaggerize-ui` NPM 모듈을 설치합니다. 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. *handlers/contacts.js*의 코드를 다음 코드로 바꿉니다. 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    이 코드는 *lib/contactRepository.js*에서 제공하는 *lib/contacts.json*에 저장된 JSON 데이터를 사용합니다. 새 *contacts.js* 코드는 JSON 페이로드로 리포지토리의 모든 연락처를 반환합니다. 

4. **handlers/contacts/{id}.js** 파일의 코드를 다음 코드로 바꿉니다.

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    이 코드를 통해 지정된 ID로 연락처만 반환하도록 경로 변수를 사용할 수 있습니다.

5. **server.js**의 코드를 다음 코드로 바꿉니다.

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');
    var fs = require("fs");
    
    fs.existsSync = fs.existsSync || require('path').existsSync;

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    이 코드는 Azure App Service와 작동하도록 몇 가지 사소한 변경 사항을 만들고 API에 대한 대화형 웹 인터페이스를 노출합니다.

### <a name="test-the-api-locally"></a>로컬로 API 테스트

1. Node.js 앱 시작
    ```bash
    npm start
    ```
    
2. http://localhost:8000/contacts로 이동하여 전체 연락처 목록에 대한 JSON을 봅니다.
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. http://localhost:8000/contacts/2로 이동하여 두 개의 `id`로 연락처를 봅니다.
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. http://localhost:8000/docs에서 Swagger 웹 인터페이스를 사용하여 API를 테스트합니다.
   
    ![Swagger 웹 인터페이스](media/app-service-web-tutorial-rest-api/swagger-ui.png)

## <a id="createapiapp"></a> API App 만들기

이 섹션에서는 Azure CLI 2.0을 사용하여 Azure App Service에서 API를 호스트하는 리소스를 만듭니다. 

1.  [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

    ```azurecli-interactive
    az login
    ```

2. 여러 Azure 구독이 있는 경우 기본 구독을 원하는 구독으로 변경합니다.

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>Git로 API 배포

로컬 Git 리포지토리에서 Azure App Service로 커밋을 푸시하여 API 앱에 코드를 배포합니다.

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. *ContactList* 디렉터리에서 새 리포지토리를 초기화합니다. 

    ```bash
    git init .
    ```

3. Git에서 자습서의 이전 단계에서 npm으로 만든 *node_modules* 디렉터리를 제외합니다. 현재 디렉터리에 새 `.gitignore` 파일을 만들고 파일의 새 줄에 다음 텍스트를 추가합니다.

    ```
    node_modules/
    ```
    `node_modules` 폴더가 `git status`로 무시되고 있는지 확인합니다.

4. 리포지토리에 변경 내용을 커밋합니다.
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>Azure에서 API 테스트

1. http://app_name.azurewebsites.net/contacts로 브라우저를 엽니다. 자습서의 앞부분에서 요청을 로컬로 만들었을 때 반환된 동일한 JSON이 표시됩니다.

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. 브라우저에서 `http://app_name.azurewebsites.net/docs` 끝점으로 이동하여 Azure에서 실행되는 Swagger UI를 사용해 봅니다.

    ![Swagger Ii](media/app-service-web-tutorial-rest-api/swagger-azure-ui.png)

    이제 Azure Git 리포지토리에 커밋을 푸시하여 간단히 Azure에 샘플 API에 대한 업데이트를 배포할 수 있습니다.

## <a name="clean-up"></a>정리

이 빠른 시작에서 만든 리소스를 정리하려면 다음 Azure CLI 명령을 실행합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>다음 단계 
> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

