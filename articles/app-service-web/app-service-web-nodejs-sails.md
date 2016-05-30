<properties
	pageTitle="Azure 앱 서비스에 Sails.js 웹앱을 배포합니다."
	description="Azure 앱 서비스에서 Node.js 응용 프로그램을 배포하는 방법을 알아봅니다. 이 자습서에서는 Sails.js 웹앱을 배포하는 방법을 보여 줍니다."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="03/31/2016"
	ms.author="cephalin"/>

# Azure 앱 서비스에 Sails.js 웹앱을 배포합니다.

이 자습서에서는 Azure 앱 서비스에 Sails.js 앱을 배포하는 방법을 보여 줍니다. 프로세스를 통해 앱 서비스에서 실행할 Node.js 앱 구성 방법에 대한 일반적인 지식을 얻을 수 있습니다.

## 필수 조건

- Node.js. 설치 이진은 [여기](https://nodejs.org/)에 있습니다.
- Sails.js. 설치 지침은 [여기](http://sailsjs.org/get-started)를 참조하세요.
- Sails.js 작업 지식. 이 자습서는 일반적으로 Sail.js의 실행과 관련된 문제를 해결하는 데 적합하지 않습니다.
- Git. 설치 이진은 [여기](http://www.git-scm.com/downloads)에 있습니다.
- Azure CLI. 설치 지침은 [여기](../xplat-cli-install.md)를 참조하세요.
- Microsoft Azure 계정. 계정이 없는 경우 [무료 평가판을 등록](/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 동작 중인 Azure 앱 서비스를 확인하려면 [앱 서비스 체험](http://go.microsoft.com/fwlink/?LinkId=523751)으로 이동하세요. 여기서 신용 카드와 약정 없이 앱 서비스에서 수명이 짧은 스타터 앱을 즉시 만들 수 있습니다.

## 1단계: 개발 환경에서 Sails.js 앱 만들기

먼저 다음 단계를 수행하여 기본 Sails.js 앱을 신속하게 만듭니다.

1. 선택한 명령줄 터미널을 열고 작업 디렉터리로 `CD`합니다.

2. 새 Sails.js 앱을 만들고 실행합니다.

        sails new <appname>
        cd <appname>
        sails lift

    기본 홈 페이지(http://localhost:1377)로 이동할 수 있는지 확인합니다.

## 2단계: Azure에서 앱 서비스 앱 리소스 만들기

그런 다음 앱 서비스 앱 리소스를 만듭니다. 나중에 Sails.js 앱을 이 리소스에 배포합니다.

1. 동일한 터미널에서 다음과 같이 Azure에 로그인합니다.

        azure login

    프롬프트를 따라 Azure 구독을 보유하고 있는 Microsoft 계정을 사용하여 브라우저에서 계속 로그인합니다.

2. 여전히 Sails.js 프로젝트의 루트 디렉터리에 있는지 확인합니다. 다음 명령을 사용하여 Azure에 고유한 앱 이름을 가진 앱 서비스 앱 리소스를 만듭니다. 웹앱의 URL은 http://&lt;appname>.azurewebsites.net입니다.

        azure site create --git <appname>

    프롬프트에 따라 배포할 Azure 지역을 선택합니다. Azure 구독에 대한 Git/FTP 배포 자격 증명을 설정하지 않은 경우에도 배포 자격 증명을 만들라는 메시지가 표시됩니다.

    앱 서비스 앱 리소스를 만든 후:

    - Sails.js 앱이 Git-initialized입니다.
    - 로컬 Git-initialized 리포지토리가 Git remote로 "azure"라는 새 앱 서비스 앱에 연결되고
    - 루트 디렉터리에 iisnode.yml 파일이 만들어집니다. 이 파일을 사용하여 앱 서비스가 Node.js 앱을 실행하는 데 사용할 [iisnode](https://github.com/tjanczuk/iisnode)를 구성할 수 있습니다.

## 3단계: Sails.js 앱 구성 및 배포

 앱 서비스에서의 Sails.js 앱 작업은 세 가지 주요 단계로 구성됩니다.

 - 앱 서비스에서 실행되도록 앱 구성
 - 앱 서비스에 배포
 - 배포 문제를 해결하기 위해 stderr 및 stdout 로그 읽기

다음 단계를 수행하세요.

1. 루트 디렉터리에서 새 iisnode.yml 파일을 열고 다음 두 줄을 추가합니다.

        loggingEnabled: true
        logDirectory: iisnode

    이제 iisnode에 대해 로깅을 사용할 수 있습니다. 이 작업 방법에 대한 자세한 내용은 [iisnode에서 stdout 및 stderr 로그 가져오기](app-service-web-nodejs-sails.md#iisnodelog)를 참조하세요.

2. 프로덕션 환경을 구성하기 위해 config/env/production.js를 열고 `port` 및 `hookTimeout`을 설정합니다.

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    이러한 구성 설정에 대한 설명은 [Sails.js 설명서](http://sailsjs.org/documentation/reference/configuration/sails-config)에서 확인할 수 있습니다.

    그런 다음 [Grunt](https://www.npmjs.com/package/grunt)가 Azure의 네트워크 드라이브와 호환되는지 확인해야 합니다. 이 문서를 작성할 당시 Grunt는 현재 네트워크 드라이브를 지원하지 않는 오래된 [glob](https://www.npmjs.com/package/glob) 패키지(v3.1.21)를 사용하기 때문에 ["ENOTSUP: 소켓에서 지원되지 않는 작업입니다." 오류](https://github.com/isaacs/node-glob/issues/205)가 발생할 수 있습니다. 다음 단계는 Grunt가 [glob v5.0.14 이상](https://github.com/isaacs/node-glob/commit/bf3381e90e283624fbd652835e1aefa55d45e2c7)을 사용하도록 하는 방법을 보여 줍니다.

3. 앱이 만들어질 때 `npm install`이 이미 실행되었으므로 npm-shrinkwrap.json을 프로젝트 루트에 생성합니다.

        npm shrinkwrap

4. npm-shrinkwrap.json을 열고 `"grunt":`에 대한 json을 찾은 다음 원하는 glob 버전에 대한 종속성을 추가합니다. 완성된 json은 다음과 같아야 합니다.

        "grunt": {
            "version": "0.4.5",
            "from": "grunt@0.4.5",
            "resolved": "https://registry.npmjs.org/grunt/-/grunt-0.4.5.tgz",
            "dependencies": {
                "glob": {
                    "version": "5.0.14",
                    "from": "glob@5.0.14",
                    "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
                }
            }
        },

5. `"glob":`를 검색하여 glob에 대한 모든 참조를 찾습니다. v3.1.21 이하인 참조가 있으면 json을 변경합니다.

        "glob": {
            "version": "5.0.14",
            "from": "glob@5.0.14",
            "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
        }

6. 변경 내용을 저장하고 변경 내용을 테스트하여 로컬에서 계속 앱이 실행되는지 확인합니다.

        npm install
        sails lift

4. 이제 git를 사용하여 앱을 Azure에 배포합니다.

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. 마지막으로 브라우저에서 라이브 Azure 앱을 실행하면 됩니다.

        azure site browse

    이제 동일한 Sails.js 홈페이지가 나타납니다.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## 배포 문제 해결

Sails.js 응용 프로그램이 앱 서비스에서 어떤 이유로 실패하면 문제 해결을 위해 stderr 로그를 찾습니다. 자세한 내용은 [iisnode에서 stdout 및 stderr 로그 가져오기](app-service-web-nodejs-sails.md#iisnodelog)를 참조하세요. 성공적으로 시작된 경우 stdout 로그에 익숙한 메시지가 표시됩니다.

                .-..-.

    Sails              <|    .-..-.
    v0.12.1             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\a76e8111-663e-449d-956e-5c5deff2d304
    To shut down Sails, press <CTRL> + C at any time.

## Azure의 데이터베이스에 연결

Azure 데이터베이스에 연결하려면 Azure에 Azure SQL 데이터베이스, MySQL, MongoDB, Azure (Redis) Cache 등 원하는 데이터베이스를 만들고 해당하는 [데이터 저장소 어댑터](https://github.com/balderdashy/sails#compatibility)를 사용하여 이 데이터베이스에 연결합니다. 이 섹션의 단계는 Azure SQL 데이터베이스에 연결하는 방법을 보여 줍니다.

1. 새 SQL Server에 빈 Azure SQL 데이터베이스를 만들려면 [여기](../sql-database/sql-database-get-started.md) 자습서를 따릅니다. 기본 방화벽 설정으로 Azure 서비스(예: 앱 서비스)가 이 데이터베이스에 연결할 수 있습니다.

2. 명령줄 터미널에서 SQL Server 어댑터를 설치합니다.

        npm install sails-sqlserver --save

    package.json을 변경했으므로 npm-shrinkwrap.json을 다시 생성해야 합니다. 이 작업은 다음에 수행합니다.
    
3. node\_modules/ 디렉터리를 삭제합니다.

4. `npm shrinkwrap`을 실행합니다.

5. npm-shrinkwrap.json을 다시 열고 이전 섹션에서 수행한 것처럼 `glob` 패키지 버전을 업데이트합니다.

    이제 주 작업으로 돌아갑니다.
        
3. config/connections.js를 열고 다음 json을 어댑터 목록에 추가합니다.

        sqlserver: {
            adapter: 'sails-sqlserver',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.sqlserver, 
            database: process.env.dbname,
            options: {
                encrypt: true   // use this for Azure databases
            }
        },

4. 각 환경 변수(`process.env.*`)의 경우 앱 서비스에서 설정해야 합니다. 이렇게 하려면 터미널에서 다음 명령을 실행합니다.

        azure site appsetting add dbuser="<database server administrator>"
        azure site appsetting add dbpassword="<database server password>"
        azure site appsetting add sqlserver="<database server name>.database.windows.net"
        azure site appsetting add dbname="<database name>"
        
4. config/env/production.js를 열어 프로덕션 환경을 구성하고 `models` JSON 개체에서 `connection` 및 `migrate`을 설정합니다.

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

4. 터미널에서 일반적인 방식으로 Sails.js [청사진 API](http://sailsjs.org/documentation/concepts/blueprints)를 [생성](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate)합니다. 예:

         sails generate api mywidget
     
5. 모든 변경 내용을 저장하고 이 변경 내용을 Azure에 푸시하고, 앱으로 이동하여 여전히 작동하는지 확인합니다.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. 브라우저에서 방금 만든 청사진 API에 액세스합니다. 예:

        http://<appname>.azurewebsites.net/widget/create
    
    API는 만든 항목을 브라우저 창에 다시 반환합니다.
    
        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

## 추가 리소스

- [Azure 앱 서비스에서 Node.js 웹앱 시작](app-service-web-nodejs-get-started.md)
- [Azure 응용 프로그램에 Node.js 모듈 사용](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0518_2016-->