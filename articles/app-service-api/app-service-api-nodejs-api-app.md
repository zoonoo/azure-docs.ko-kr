<properties
	pageTitle="Azure 앱 서비스에서 Node.js API 앱 빌드 및 배포"
	description="Node.js API 앱 패키지를 만들고 Azure 앱 서비스에 배포하는 방법을 알아봅니다."
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="mohisri"
	editor="tdykstra "/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="02/25/2015"
	ms.author="bradygaster"/>

# Azure 앱 서비스에서 Node.js API 앱 빌드 및 배포

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 필수 조건
1. 개발 컴퓨터에서 실행되는 [Node.js](http://nodejs.org)(이 샘플에서는 Node.js 버전 4.2.2가 설치된 것으로 가정함)
1. [GitHub](https://github.com/) 계정
1. Microsoft Azure [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)
1. 로컬 개발 워크스테이션에 설치된 Git

## 설치 지침
아래 명령은 Node.js 명령줄을 사용하여 수행해야 합니다. Swaggerize Yo 생성기를 사용하면 Swagger JSON 파일에 정의된 HTTP 요청을 처리하는 데 필요한 기준선 Node.js 코드를 스캐폴드할 수 있습니다.
 
1. **yo** 및 **generator-swaggerize** NPM 모듈을 전역적으로 설치합니다.

        npm install -g yo
	    npm install -g generator-swaggerize
		
1. [샘플 코드가 포함된 GitHub 리포지토리](https://github.com/Azure-Samples/app-service-api-node-contact-list)를 복제합니다.

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
				
1. 소스 코드에 포함된 **api.json** 파일에 따라 API를 스캐폴드하는 명령을 실행합니다. **api.json** 파일은 다음 단계에서 "yo swaggerize" 명령을 사용하여 스캐폴드할 실제 API를 나타내는 Swagger 파일입니다.

        yo swaggerize
        
    **참고:** API.json은 API 앱 미리 보기 기간부터 *apiapp.json* 파일과 다릅니다.

1. Swaggerize는 **api.json**에 포함된 Swagger 메타데이터의 처리기 및 구성을 스캐폴드합니다. 스캐폴딩 프로세스 동안 GitHub 사용자 이름 및 전자 메일 주소 등을 묻는 다양한 질문이 나타납니다. 이 정보는 응용 프로그램의 폴더에 **package.json** 파일을 생성하는 데 사용됩니다. 스캐폴딩 프로세스 중에 나타나는 모든 질문 중에서 **express**를 선택해야 하는 질문이 가장 중요합니다. 이 샘플에서는 express 뷰 엔진을 사용하여 나중에 Azure에서(또는 로컬로) API 앱를 실행할 때 Swagger 도움말 페이지를 생성하기 때문입니다.

	![Swaggerize 명령줄](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
1. 스캐폴드된 코드가 들어 있는 폴더(이 예제의 경우 *ContactList* 하위 폴더)로 이동합니다. 그런 다음 **jsonpath** NPM 모듈을 설치합니다.

        npm install --save jsonpath
        
    명령줄 환경에 설치 결과가 표시됩니다.

    ![Jsonpath 설치](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. **swaggerize ui** NPM 모듈을 설치합니다.

        npm install --save swaggerize-ui
        
    명령줄 환경에 설치 결과가 표시됩니다.

    ![Swaggerize Ui 설치](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

1. **start** 폴더의 **lib** 폴더를 scaffolder에서 만든 **ContactList** 폴더에 복사합니다.

1. **handlers/contacts.js** 파일의 코드를 아래 코드로 바꿉니다. 이 코드는 **lib/contactRepository.js**에서 제공하는 **lib/contacts.json** 파일에 저장된 JSON 데이터를 사용합니다. 아래의 새 contacts.js 코드는 이 코드를 사용하여 모든 연락처를 가져오는 HTTP 요청에 응답합니다.

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. **handlers/contacts/{id}.js** 파일의 코드를 아래 코드로 바꿉니다. 아래 코드는 **lib/contactRepository.js**를 사용하여 HTTP 요청에서 요청된 연락처를 가져오고 이를 JSON 페이로드로 반환합니다.

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. **server.js**의 코드를 아래 코드로 바꿉니다. 적용된 변경 내용을 볼 수 있도록 server.js 파일에 적용된 변경 내용이 주석으로 강조 표시됩니다.

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth change
            app.setHost(undefined); // sixth and final change
        });

1. Node.js 명령줄 실행 파일을 사용하여 서버를 활성화합니다.

        node server.js

    이 명령을 실행하면 Node.js HTTP 서버가 시작되고 API가 제공되기 시작합니다.

1. **http://localhost:8000/contacts**로 이동하면 연락처 목록의 JSON 출력이 표시됩니다(또는 사용 중인 브라우저에 따라 다운로드할지 묻는 메시지가 표시됨).

    ![모든 연락처 Api 호출](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. **http://localhost:8000/contacts/2**로 이동하면 해당 id 값이 나타내는 연락처가 표시됩니다.

    ![특정 연락처 Api 호출](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Swagger JSON 데이터는 **/swagger** 끝점을 통해 제공됩니다.

    ![연락처 Swagger Json](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. Swagger UI는 **/docs** 끝점을 통해 제공됩니다. Swagger UI에서 리치 HTML 클라이언트 기능을 사용하여 API를 테스트할 수 있습니다.

    ![Swagger Ui](media/app-service-api-nodejs-api-app/swagger-ui.png)

## Azure 포털에서 새 API 앱 만들기
이 섹션에서는 Azure에서 비어 있는 새 API 앱을 만드는 과정을 안내합니다. 그런 다음 코드 변경 내용을 지속적으로 전달할 수 있도록 이 앱을 Git 리포지토리에 연결합니다.

소스 코드를 복제한 GitHub 리포지토리는 배포를 위해 코드를 푸시할 리포지토리와 다릅니다. 샘플 GitHub 리포지토리에는 "Start" 상태의 코드가 포함되어 있으며, 이제 "end" 상태의 코드를 스캐폴드했으므로 이 코드를 API 앱과 연결된 Git 리포지토리에 푸시하기만 하면 됩니다. 먼저 Azure 포털을 사용하여 API 앱을 만든 후 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 

1. 새 API 앱을 만듭니다.

    ![새 Api 앱 포털](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

1. 새 API 앱을 기존 리소스 그룹 및/또는 앱 서비스 계획에 추가하거나, 아래 스크린샷에 표시된 것처럼 새 리소스 그룹 및 앱 서비스 계획을 만들 수 있습니다.

    ![Api 앱 만들기 블레이드](media/app-service-api-nodejs-api-app/api-app-creation-blade.png)

1. 포털에서 API 앱을 만든 후에는 아래와 같이 API 앱에 대한 설정이 포함된 블레이드로 이동합니다.

    ![포털 탐색 설정](media/app-service-api-nodejs-api-app/portal-nav-to-settings.png)

1. 설정 메뉴에서 **배포 자격 증명** 탐색 항목을 클릭합니다. 블레이드가 열리면 Node.js 코드를 API 앱에 게시하는 데 사용할 사용자 이름 및 암호를 추가합니다. 그런 다음 **배포 자격 증명 설정** 블레이드에서 **저장** 단추를 클릭합니다.

    ![배포 자격 증명](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. 배포 자격 증명을 설정한 후에는 앱 서비스와 연결된 Git 리포지토리를 만들 수 있습니다. 이 저장소에 코드를 푸시할 때마다 Azure 앱 서비스에서 변경 내용을 선택하여 API 앱 인스턴스에 직접 배포합니다. 사이트와 연결된 Git 리포지토리를 만들려면 아래와 같이 설정 메뉴 블레이드에서 **연속 배포** 메뉴 항목을 클릭합니다. 그런 다음 **원본 선택** 블레이드에서 **로컬 Git 리포지토리** 옵션을 선택합니다. 확인 단추를 클릭하면 Git 리포지토리가 만들어집니다.

    ![Git 리포지토리 만들기](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Git 리포지토리를 만들어지면 블레이드가 변경되고 활성 배포가 표시됩니다. 이 리포지토리는 새 리포지토리이므로 목록에 활성 배포가 없어야 합니다.

    ![활성 배포 없음](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. 마지막 단계로, 포털에서 Git 리포지토리 URL을 복사합니다. 이렇게 하려면 새 API 앱의 블레이드로 이동하여 **필수 패키지** 섹션을 확인합니다. 필수 패키지 섹션에 **Git 복제 URL**이 표시되어야 합니다. 그 옆에는 URL을 클립보드에 복사하는 아이콘이 있습니다. 이 아이콘을 클릭하여 URL을 복사(URL 위에 마우스를 놓으면 단추가 표시됨)하거나, 전체 URL을 선택하여 클립보드에 복사합니다.

    ![포털에서 Git Url 가져오기](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **참고**: Git 복제 URL은 다음 단계에서 임의 위치에 임시로 저장하는 데 필요합니다.

이제 백업 Git 리포지토리가 있는 새 API 앱을 만들었으므로 코드를 리포지토리에 푸시하고 Azure의 연속 배포 기능을 사용하여 변경 내용을 자동으로 배포할 수 있습니다.

## Azure에 API 앱 코드 배포
Azure 앱 서비스에서 기본 제공하는 지속적인 업데이트 기능을 사용하여 앱 서비스와 연결된 Git 리포지토리에 코드를 간편하게 커밋할 수 있습니다. 그러면 Azure에서 소스 코드를 선택하여 API 앱에 배포합니다.

1. GitHub에서 복제한 기본 리포지토리(getting-started 코드 포함) 외부에 있어야 하는 새 로컬 Git 리포지토리를 만들 것이므로 swaggerize scaffolder에서 만든 **src/end/ContactList** 폴더를 바탕 화면이나 다른 폴더에 복사합니다. 

1. Node.js 명령줄 환경을 사용하여 새 폴더로 이동합니다. 여기에서 다음 명령을 실행하여 새 로컬 Git 리포지토리를 만듭니다.

        git init

    이 명령은 로컬 Git 리포지토리를 만들고 새 리포지토리가 초기화되었음을 확인하는 메시지를 표시합니다.

    ![새 로컬 Git 리포지토리](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Node.js 명령줄 환경을 사용하여 로컬 리포지토리에 Git remote를 추가하는 다음 명령을 실행합니다. 방금 만들어 Azure에서 실행 중인 API 앱에 연결한 리포지토리가 원격 리포지토리가 됩니다.

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **참고**: 위에서 "YOUR\_GIT\_CLONE\_URL\_HERE" 문자열을 이전에 복사한 사용자 고유의 Git 복제 URL로 바꿀 수 있습니다.

1. 다음으로, Node.js 명령줄 환경에서 아래의 두 명령을 실행합니다.

        git add .
        git commit -m "initial revision"

    이 두 명령을 완료하면 명령줄 창에 아래 스크린샷과 같은 코드가 표시됩니다.

    ![Git 커밋 출력](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. API 앱에 대한 배포를 트리거하는 코드를 Azure에 푸시하기 위해 Node.js 명령줄에서 다음 명령을 실행합니다. 암호를 묻는 메시지가 나타나면 이전에 Azure 포털에서 배포 자격 증명을 만들 때 사용한 암호를 사용합니다.

        git push azure master

1. API 앱의 **연속 배포** 블레이드로 다시 이동하면 배포가 진행 중인 것을 볼 수 있습니다.

    ![배포 진행](media/app-service-api-nodejs-api-app/deployment-happening.png)

    이와 동시에 Node.js 명령줄에 진행 중인 배포의 상태가 반영됩니다.

    ![Node Js 배포 진행](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

1. 배포가 완료되면 **연속 배포** 블레이드에 코드 변경 내용이 API 앱에 성공적으로 배포되었음이 반영됩니다. API 앱 블레이드의 **필수 패키지** 섹션에서 **URL**을 복사합니다.

    ![배포 완료](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Postman 또는 Fiddler(또는 사용 중인 웹 브라우저)와 같은 REST API 클라이언트를 사용하여 연락처 API 호출 URL을 제공합니다. 이는 API 앱의 **/contacts** 끝점이어야 합니다.

    **참고:** URL은 http://myapiapp.azurewebsites.net/contacts와 유사합니다.

    이 끝점으로 GET 요청을 실행하면 API 앱의 JSON 출력이 표시됩니다.

    ![Postman 연결 Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

## 다음 단계

이제 Node.js를 사용하여 첫 번째 API 앱을 만들고 배포했습니다. API 앱 시작 시리즈의 다음 자습서에서는 [CORS를 사용하여 JavaScript 클라이언트에서 API 앱을 사용](app-service-api-cors-consume-javascript.md)하는 방법을 보여 줍니다.

이 샘플을 작성하려면 데이터베이스 또는 API 앱 인스턴스의 디스크에 데이터를 저장하는 코드를 처리기에 추가할 수 있습니다. 연속 배포를 연결했으므로 코드를 변경하고 Git 리포지토리에 푸시하는 간단한 방법으로 API 앱의 기능을 변경하고 확장할 수 있습니다.

<!---HONumber=AcomDC_0420_2016-->