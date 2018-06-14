---
title: Node.js 버전 지정
description: Azure 웹 사이트 및 클라우드 서비스에서 사용하는 Node.js의 버전을 지정하는 방법에 대해 알아봅니다.
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: a20179c72b227deb14df442bea7b80cf31728aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
ms.locfileid: "23036848"
---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Azure 응용 프로그램에서 Node.js 버전 지정
Node.js 응용 프로그램을 호스트하는 경우 응용 프로그램에서 특정 버전의 Node.js를 사용하는지 확인하는 것이 좋습니다. Azure에 호스트되는 응용 프로그램에 대해 이 작업을 수행하는 여러 가지 방법이 있습니다.

## <a name="default-versions"></a>기본 버전
Azure에서 제공하는 Node.js 버전은 지속적으로 업데이트됩니다. 별도로 지정하지 않는 한, `WEBSITE_NODE_DEFAULT_VERSION` 환경 변수에 지정된 기본 버전이 사용됩니다. 기본값을 재정의하려면, 이 문서의 다음 섹션에 나오는 단계를 수행합니다.

> [!NOTE]
> Azure 클라우드 서비스(웹 또는 작업자 역할)에서 응용 프로그램을 호스트하며 응용 프로그램을 처음 배포하는 것이면 Azure는 Azure에서 사용 가능한 기본 버전 중 하나와 일치할 경우 개발 환경에 설치한 것과 동일한 버전의 Node.js를 사용하려고 합니다.
>
>

## <a name="versioning-with-packagejson"></a>package.json을 사용하여 버전 관리
**package.json** 파일에 다음을 추가하여 사용할 Node.js의 버전을 지정할 수 있습니다.

    "engines":{"node":version}

여기서 *version* 은 사용할 특정 버전 번호입니다. 다음과 같이 버전에 보다 복잡한 조건을 지정할 수 있습니다.

    "engines":{"node": "0.6.22 || 0.8.x"}

0.6.22는 호스팅 환경에서 사용 가능한 버전 중 하나가 아니므로 사용 가능한 0.8 시리즈의 가장 높은 버전인 0.8.4가 대신 사용됩니다.

## <a name="versioning-websites-with-app-settings"></a>앱 설정을 사용하여 웹 사이트 버전 관리
웹 사이트에서 응용 프로그램을 호스트하는 경우 환경 변수 **WEBSITE_NODE_DEFAULT_VERSION**을 원하는 버전으로 설정할 수 있습니다.

## <a name="versioning-cloud-services-with-powershell"></a>PowerShell을 사용하여 클라우드 서비스 버전 관리
클라우드 서비스에서 응용 프로그램을 호스트하고 Azure PowerShell을 사용하여 응용 프로그램을 배포하는 경우 **Set-AzureServiceProjectRole** PowerShell cmdlet을 사용하여 기본 Node.js 버전을 재정의할 수 있습니다. 예:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

위의 문에서 매개 변수는 대/소문자를 구분합니다.  역할의 **package.json**에서 **engines** 속성을 검사하여 올바른 버전의 Node.js가 선택되었는지 확인할 수 있습니다.

**Get-AzureServiceProjectRoleRuntime** 을 사용하여 클라우드 서비스로 호스트된 응용 프로그램에 사용 가능한 Node.js 버전 목록을 검색할 수도 있습니다.  프로젝트에서 사용하는 Node.js 버전이 목록에 있는지 항상 확인합니다.

## <a name="using-a-custom-version-with-azure-websites"></a>Azure 웹 사이트에서 사용자 지정 버전 사용
Azure는 Node.js의 기본 버전을 여러 개 제공하지만 기본적으로 제공되지 않는 버전을 사용할 수도 있습니다. 응용 프로그램이 Azure 웹 사이트로 호스트된 경우 **iisnode.yml** 파일을 사용하면 됩니다. 다음 단계에서는 Azure 웹 사이트에서 사용자 지정 버전의 Node.Js를 사용하는 프로세스를 단계별로 안내합니다.

1. 새 디렉터리를 만든 후 디렉터리 내에 **server.js** 파일을 만듭니다. **server.js** 파일에는 다음이 포함되어야 합니다.

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    웹 사이트를 검색할 때 사용되는 Node.js 버전이 표시됩니다.
2. 새로운 웹 사이트를 만들고 사이트 이름을 기록해 둡니다. 예를 들어 다음 코드는 [Azure 명령줄 도구] 를 사용하여 **mywebsite**라는 새 Azure 웹 사이트를 만든 후 웹 사이트에 Git 리포지토리를 사용하도록 설정합니다.

        azure site create mywebsite --git
3. **server.js** 파일이 포함된 디렉터리의 자식으로 **bin** 디렉터리를 새로 만듭니다.
4. 응용 프로그램과 함께 사용할 특정 버전의 **node.exe** (Windows 버전)를 다운로드합니다. 예를 들어 다음 코드는 **curl** 을 사용하여 버전 0.8.1을 다운로드합니다.

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    이전에 만든 **bin** 폴더에 **node.exe** 파일을 저장합니다.
5. **server.js** 파일과 동일한 디렉터리에 **iisnode.yml** 파일을 만든 후 **iisnode.yml** 파일에 다음 콘텐츠를 추가합니다.

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    이 경로는 Azure 웹 사이트에 응용 프로그램을 게시한 후 프로젝트 내의 **node.exe** 파일이 있는 위치입니다.
6. 응용 프로그램을 게시합니다. 예를 들어 이전에 --git 매개 변수를 사용하여 새로운 웹 사이트를 만들었으므로 다음 명령은 로컬 Git 리포지토리에 응용 프로그램 파일을 추가한 후 웹 사이트 리포지토리에 푸시합니다.

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    응용 프로그램이 게시된 후 브라우저에서 웹 사이트를 엽니다. "Hello from Azure running node version: v0.8.1"이라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계
지금까지 응용 프로그램에서 사용되는 Node.js 버전을 지정하는 방법을 배웠습니다. 이제 [모듈 작업] 방법, [Node.js 웹 사이트 빌드 및 배포](app-service/app-service-web-get-started-nodejs.md) 방법, [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법]을 알아보겠습니다.

자세한 내용은 [Node.js 개발자 센터](https://azure.microsoft.com/develop/nodejs/)를 참조하세요.

[Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법]:cli-install-nodejs.md
[Azure 명령줄 도구]:cli-install-nodejs.md
[모듈 작업]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: app-service/app-service-web-get-started-nodejs.md
