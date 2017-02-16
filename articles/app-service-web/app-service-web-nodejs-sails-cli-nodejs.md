---
title: "Azure App Service에 Sails.js 웹앱 배포 | Microsoft Docs"
description: "Azure 앱 서비스에서 Node.js 응용 프로그램을 배포하는 방법을 알아봅니다. 이 자습서에서는 Sails.js 웹앱을 배포하는 방법을 보여 줍니다."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 08b4cd3fe4a246bc62fb4a6cc596630968d7f9d7


---
# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Azure 앱 서비스에 Sails.js 웹앱을 배포합니다.
이 자습서에서는 Azure 앱 서비스에 Sails.js 앱을 배포하는 방법을 보여 줍니다. 프로세스를 통해 앱 서비스에서 실행할 Node.js 앱 구성 방법에 대한 일반적인 지식을 얻을 수 있습니다.

여기에서는 다음과 같은 유용한 기술을 배웁니다.

* App Service에서 실행되는 Sails.js 앱을 구성합니다.
* 명령줄에서 App Service에 앱을 배포합니다.
* 배포 문제를 해결하기 위해 stderr 및 stdout 로그를 읽습니다.
* 소스 제어 외부의 환경 변수를 저장합니다.
* 앱에서 Azure 환경 변수에 액세스합니다.
* 데이터베이스에 연결합니다(MongoDB).

Sails.js에 대한 실무 지식이 있어야 합니다. 이 자습서는 일반적으로 Sail.js의 실행과 관련된 문제를 해결하는 데 적합하지 않습니다.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](app-service-web-nodejs-sails-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0(미리 보기)](app-service-web-nodejs-sails.md) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="prerequisites"></a>필수 조건
* [Node.JS](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Azure CLI](../xplat-cli-install.md)
* Microsoft Azure 계정. 계정이 없는 경우 [무료 평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

> [!NOTE]
> Azure 계정 없이 [App Service를 체험](https://azure.microsoft.com/try/app-service/)할 수 있습니다. 시작 앱을 만들고 최대 한 시간 동안 해당 앱을 사용하여 재생합니다. -- 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="step-1-create-a-sailsjs-app-locally"></a>1단계: 로컬로 Sails.js 앱 만들기
먼저 다음 단계를 수행하여 개발 환경에서 기본 Sails.js 앱을 신속하게 만듭니다.

1. 선택한 명령줄 터미널을 열고 작업 디렉터리로 `CD` 합니다.
2. Sails.js 앱을 만들고 실행합니다.

        sails new <appname>
        cd <appname>
        sails lift

    기본 홈 페이지(http://localhost:1377)로 이동할 수 있는지 확인합니다.

## <a name="step-2-create-the-azure-app-resource"></a>2단계: Azure 앱 리소스 만들기
다음으로 Azure에서 App Service 리소스를 만듭니다. 나중에 Sails.js 앱을 이 리소스에 배포합니다.

1. 다음과 같이 Azure에 로그인합니다.
2. 동일한 터미널에서 ASM 모드로 변경하고 Azure에 로그인합니다.

        azure config mode asm
        azure login

    프롬프트를 따라 Azure 구독을 보유하고 있는 Microsoft 계정을 사용하여 브라우저에 로그인을 계속합니다.

3. App Service의 배포 사용자를 설정합니다. 나중에 자격 증명을 사용하여 코드를 배포합니다.
   
        azure site deployment user set --username <username> --pass <password>

3. 여전히 Sails.js 프로젝트의 루트 디렉터리에 있는지 확인합니다. 다음 명령을 사용하여 Azure에 고유한 앱 이름을 가진 앱 서비스 앱 리소스를 만듭니다. 웹앱의 URL은 http://&lt;appname>.azurewebsites.net입니다.

        azure site create --git <appname>

    프롬프트에 따라 배포할 Azure 지역을 선택합니다. 앱 서비스 앱 리소스를 만든 후:

   * Sails.js 앱이 Git-initialized입니다.
   * 로컬 Git-initialized 리포지토리가 Git remote로 "azure"라는 새 앱 서비스 앱에 연결되고
   * 루트 디렉터리에 iisnode.yml 파일이 만들어집니다. 이 파일을 사용하여 App Service가 Node.js 앱을 실행하는 데 사용할 [iisnode](https://github.com/tjanczuk/iisnode)를 구성할 수 있습니다.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>3단계: Sails.js 앱 구성 및 배포
 앱 서비스에서의 Sails.js 앱 작업은 세 가지 주요 단계로 구성됩니다.

* 앱 서비스에서 실행되도록 앱 구성
* 앱 서비스에 배포
* 배포 문제를 해결하기 위해 stderr 및 stdout 로그 읽기

다음 단계를 수행하세요.

1. 루트 디렉터리에서 새 iisnode.yml 파일을 열고 다음 두 줄을 추가합니다.

        loggingEnabled: true
        logDirectory: iisnode

    이제 Azure App Service가 Node.js 앱을 실행하는 데 사용하는 [iisnode](https://github.com/tjanczuk/iisnode) 서버에 대한 로깅이 활성화되었습니다. 
    이 작업 방법에 대한 자세한 내용은  [iisnode에서 stdout 및 stderr 로그 가져오기](app-service-web-nodejs-get-started.md#iisnodelog)를 참조하세요.
2. 프로덕션 환경을 구성하기 위해 config/env/production.js를 열고 `port` 및 `hookTimeout`을 설정합니다.

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    이러한 구성 설정에 대한 설명은  [Sails.js 설명서](http://sailsjs.org/documentation/reference/configuration/sails-config)에서 확인할 수 있습니다.

4. package.json에서 다음 `engines` 속성을 추가하여 Node.js를 원하는 버전으로 설정합니다.

        "engines": {
            "node": "6.9.1"
        },
5. 변경 내용을 저장하고 변경 내용을 테스트하여 로컬에서 계속 앱이 실행되는지 확인합니다. 이 작업을 수행하려면 `node_modules` 폴더를 삭제하고 다음을 실행합니다.

        npm install
        sails lift
6. 이제 git를 사용하여 앱을 Azure에 배포합니다.

        git add .
        git commit -m "<your commit message>"
        git push azure master
7. 마지막으로 브라우저에서 라이브 Azure 앱을 실행하면 됩니다.

        azure site browse

    이제 동일한 Sails.js 홈페이지가 나타납니다.

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>배포 문제 해결
Sails.js 응용 프로그램이 앱 서비스에서 어떤 이유로 실패하면 문제 해결을 위해 stderr 로그를 찾습니다.
자세한 내용은 [iisnode에서 stdout 및 stderr 로그 가져오기](app-service-web-nodejs-get-started.md#get-stdout-and-stderr-logs-from-iisnode)를 참조하세요.
성공적으로 시작된 경우 stdout 로그에 익숙한 메시지가 표시됩니다.

                   .-..-.
    
       Sails              <|    .-..-.
       v0.12.11            |\
                          /|.\
                         / || \
                       ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
       __---___--___---___--___---___--___
     ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

[config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) 파일에서 stdout 로그의 세분화 수준을 제어할 수 있습니다.

## <a name="connect-to-a-database-in-azure"></a>Azure의 데이터베이스에 연결
Azure 데이터베이스에 연결하려면 Azure에 Azure SQL Database, MySQL, MongoDB, Azure (Redis) Cache 등 원하는 데이터베이스를 만들고 해당하는 [데이터 저장소 어댑터](https://github.com/balderdashy/sails#compatibility) 를 사용하여 이 데이터베이스에 연결합니다. 이 섹션의 단계에서는 MongoDB 클라이언트 연결을 지원할 수 있는 [Azure DocumentDB](../documentdb/documentdb-protocol-mongodb.md) 데이터베이스를 사용하여 MongoDB에 연결하는 방법을 보여 줍니다.

1. [MongoDB 프로토콜 지원을 사용하는 DocumentDB 계정을 만듭니다](../documentdb/documentdb-create-mongodb-account.md).
2. [DocumentDB 컬렉션 및 데이터베이스를 만듭니다](../documentdb/documentdb-create-collection.md). 컬렉션의 이름은 중요하지 않지만 Sails.js에서 연결할 때 데이터베이스의 이름이 필요합니다.
3. [DocumentDB 데이터베이스에 대한 연결 정보를 찾습니다](../documentdb/documentdb-connect-mongodb-account.md#a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize).
2. 명령줄 터미널에서 MongoDB 어댑터를 설치합니다.

        npm install sails-mongo --save

3. config/connections.js를 열고 목록에 다음 연결 개체를 추가합니다.

        docDbMongo: {
            adapter: 'sails-mongo',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost,
            port: process.env.dbport,
            database: process.env.dbname,
            ssl: true
        },

    > [!NOTE] 
    > `ssl: true` 옵션은 [Azure DocumentDB에서 필요](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements)하기 때문에 중요합니다. 
    >
    >

4. 각 환경 변수(`process.env.*`)의 경우 App Service에서 설정해야 합니다. 이렇게 하려면 터미널에서 다음 명령을 실행합니다. DocumentDB 데이터베이스에 대한 연결 정보를 사용합니다.

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbport="<database port>"
        azure site appsetting add dbname="<database name>"

    Azure 앱 설정에 설정 내용을 적용하면 중요한 데이터의 소스를 제어할 수 없게 됩니다(Git). 다음으로, 동일한 연결 정보를 사용하도록 개발 환경을 구성합니다.
5. config/local.js를 열고 다음 연결 개체를 추가합니다.

        connections: {
            docDbMongo: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>",
                database: "<database name>",
                ssl: true
            },
        },

    이 구성은 config/connections.js 파일에서 로컬 환경에 대한 설정을 재정의합니다. 이 파일은 프로젝트에서 기본 .gitignore에 의해 제외되므로 Git에 저장되지 않습니다. 이제 Azure 웹앱 및 로컬 개발 환경 둘 다에서 DocumentDB(MongoDB) 데이터베이스에 연결할 수 있습니다.
6. config/env/production.js를 열어 프로덕션 환경을 구성하고 다음 `models` 개체를 추가합니다.

        models: {
            connection: 'docDbMongo',
            migrate: 'safe'
        },
7. config/env/development.js를 열어 개발 환경을 구성하고 다음 `models` 개체를 추가합니다.

        models: {
            connection: 'docDbMongo',
            migrate: 'alter'
        },

    `migrate: 'alter'`를 통해 데이터베이스 마이그레이션 기능을 사용하여 데이터베이스 컬렉션 또는 테이블을 쉽게 만들고 업데이트할 수 있습니다. 그러나 Sails.js에서는 프로덕션 환경에서 `migrate: 'alter'` 사용을 허용하지 않으므로 Azure(프로덕션) 환경에 `migrate: 'safe'`가 사용됩니다( [Sails.js 설명서](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings) 참조).
8. 터미널에서 평소처럼 Sails.js [청사진 AP](http://sailsjs.org/documentation/concepts/blueprints)I를 [생성](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate)한 다음 `sails lift`을(를) 실행하여 Sails.js 데이터베이스 마이그레이션을 통해 데이터베이스를 만듭니다. 예:

         sails generate api mywidget
         sails lift

    이 명령에 의해 생성된 `mywidget` 모델은 비어 있으나 이 모델을 사용하여 데이터베이스에 연결되어 있다는 사실을 나타낼 수 있습니다.
    `sails lift`를 실행하면 앱이 사용하는 모델에 대해 누락된 컬렉션 또는 테이블이 만들어집니다.
9. 브라우저에서 방금 만든 청사진 API에 액세스합니다. 예:

        http://localhost:1337/mywidget/create

    API가 만든 항목을 브라우저 창에 다시 반환합니다. 이것은 데이터베이스가 정상적으로 만들어졌다는 의미입니다.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. 이제 변경 내용을 Azure에 푸시하고, 앱으로 이동하여 계속 작동하는지 확인합니다.

         git add .
         git commit -m "<your commit message>"
         git push azure master
         azure site browse
11. Azure 웹앱의 청사진 API에 액세스합니다. 예:

         http://<appname>.azurewebsites.net/mywidget/create

     API가 다른 새 항목을 반환하는 경우 Azure 웹앱은 DocumentDB(MongoDB) 데이터베이스에 그 사실을 알립니다.

## <a name="more-resources"></a>추가 리소스
* [Azure 앱 서비스에서 Node.js 웹앱 시작](app-service-web-nodejs-get-started.md)
* [Azure 응용 프로그램에 Node.js 모듈 사용](../nodejs-use-node-modules-azure-apps.md)



<!--HONumber=Feb17_HO3-->


