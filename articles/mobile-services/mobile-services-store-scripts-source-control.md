<properties 
	pageTitle="소스 제어에 프로젝트 코드 저장 - Azure 모바일 서비스" 
	description="컴퓨터의 로컬 Git 리포지토리에 서버 스크립트 파일 및 모듈을 저장하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="ggailey777"/>

# 소스 제어에 프로젝트 코드 저장

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-store-code-source-control.md)
- [(Any | Javascript)](mobile-services-store-scripts-source-control.md)

이 항목에서는 Azure 모바일 서비스에서 제공하는 소스 제어를 사용하여 서버 스크립트를 저장하는 방법을 보여 줍니다. 스크립트 및 기타 JavaScript 백 엔드 코드 파일을 로컬 Git 리포지토리에서 프로덕션 모바일 서비스로 수준을 올릴 수 있습니다. 또한 여러 스크립트에서 필요할 수 있는 공유 코드를 정의하는 방법 및 package.json 파일을 사용하여 모바일 서비스에 Node.js 모듈을 추가하는 방법도 설명합니다.

이 자습서를 완료하려면 기존에 [모바일 서비스 시작] 또는 [기존 앱에 모바일 서비스 추가] 자습서를 완료하여 모바일 서비스를 만들었어야 합니다.

##<a name="enable-source-control"></a>모바일 서비스에서 소스 제어를 사용하도록 설정

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Git을 설치하고 로컬 리포지토리 만들기

1. 로컬 컴퓨터에 Git을 설치합니다. 

	Git를 설치하는 데 필요한 단계는 운영 체제마다 다릅니다. 운영 체제 특정 배포 및 설치 지침은 [Git 설치]를 참조하세요.

	> [AZURE.NOTE]
	> 일부 운영 체제에서는 Git의 명령줄과 GUI 버전을 둘 다 사용할 수 있습니다. 이 문서에서 제공하는 지침은 명령줄 버전을 사용합니다.

2. **GitBash**(Windows) 또는 **Bash**(Unix Shell)와 같은 명령줄을 엽니다. OS X 시스템에서는 **터미널** 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

3. 명령줄에서 스크립트를 저장할 디렉터리로 변경합니다. 예: `cd SourceControl`.

4. 다음 명령을 사용하여 새 Git 리포지토리의 로컬 복사본을 만들고 `<your_git_URL>`을 모바일 서비스용 Git 리포지토리의 URL로 바꿉니다.

		git clone <your_git_URL>

5. 메시지가 표시되면 모바일 서비스에서 원본 제어를 사용하도록 설정할 때 설정한 사용자 이름 및 암호를 입력합니다. 성공적으로 인증된 후에는 다음과 같은 일련의 응답이 표시됩니다.

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. `git clone` 명령을 실행한 디렉터리로 이동하여 다음 디렉터리 구조를 확인합니다.

	![4][4]

	이 경우에는 데이터 서비스의 로컬 리포지토리인 모바일 서비스의 이름을 사용하여 새 디렉터리가 만들어졌습니다.

7. .\\service\\table 하위 폴더를 열면 TodoItem 테이블에 대한 작업 권한이 JSON으로 표현된 TodoItem.json 파일이 포함된 것이 확인됩니다.

	이 테이블에 대해 서버 스크립트가 정의되어 있다면 주어진 테이블 작업에 대한 스크립트가 포함된 <code>TodoItem._&lt;operation&gt;_.js</code>라는 파일이 한 개 이상 있습니다. 스케줄러 및 사용자 지정 API 스크립트는 개별 이름을 사용하여 별도의 폴더에 유지됩니다. 자세한 내용은 [원본 제어]를 참조하세요.

지금까지 로컬 리포지토리를 만들었습니다. 이제 서버 스크립트를 변경하고 변경 내용을 다시 모바일 서비스에 밀어 넣을 수 있습니다.

##<a name="deploy-scripts"></a>업데이트된 스크립트 파일을 모바일 서비스에 배포

1. .\\service\\table 하위 폴더로 이동하고 todoitem.insert.js 파일이 아직 없는 경우 만듭니다.

2. 텍스트 편집기에서 새 todoitem.insert.js 파일을 열고 다음 코드를 붙여넣은 후 변경 내용을 저장합니다.

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	이 코드는 삽입된 항목을 로그에 씁니다. 이 파일에 아직 코드가 포함되어 있지 않은 경우 `console.log()` 호출과 같은 유효한 JavaScript 코드를 이 파일에 추가한 후 변경 내용을 저장하면 됩니다.

3. Git 명령 프롬프트에서 새 스크립트 파일 추적을 시작하는 다음 명령을 입력합니다.

		$ git add .
	

4. 변경 내용을 커밋하는 다음 명령을 입력합니다.

		$ git commit -m "updated the insert script"

5. 원격 리포지토리에 변경 내용을 업로드하는 다음 명령을 입력합니다.

		$ git push origin master
	
	커밋이 모바일 서비스에 배포됨을 나타내는 일련의 명령이 표시됩니다.

6. 관리 포털로 돌아가서 **데이터** 탭, **TodoItem** 테이블을 차례로 클릭하고 **스크립트**를 클릭하고 나서 **삽입**을 선택합니다.
7. 
	표시되는 삽입 작업 스크립트는 방금 리포지토리에 업로드한 JavaScript 코드와 같습니다.

##<a name="use-npm"></a>서버 스크립트에서 공유 코드 및 Node.js 모듈 활용

모바일 서비스에서는 전체 핵심 Node.js 모듈에 액세스할 수 있으며, **require** 함수를 사용하여 코드에서 이 모듈을 사용할 수 있습니다. 모바일 서비스는 핵심 Node.js 패키지에 속하지 않는 Node.js 모듈도 사용할 수 있으며, 고유한 공유 코드를 Node.js 모듈로 정의할 수도 있습니다. 모듈 만들기에 대한 자세한 내용은 Node.js API 참조 설명서의 [모듈](영문)을 참조하세요.

모바일 서비스에 Node.js 모듈을 추가하려면 서비스의 package.json 파일에 대한 참조를 추가하는 것이 좋습니다. 그런 다음 package.json 파일을 업데이트하여 [node-uuid] Node.js 모듈을 모바일 서비스에 추가합니다. 업데이트를 Azure로 푸시하면 모바일 서비스가 다시 시작되고 모듈이 설치됩니다. 그런 다음 이 모듈은 삽입된 항목에 대한 **uuid** 속성의 새 GUID 값을 생성하는 데 사용됩니다.

2. 로컬 Git 리포지토리의 `.\service` 폴더로 이동한 후 텍스트 편집기에서 package.json 파일을 열고 **dependencies** 개체에 다음 필드를 추가합니다.

		"node-uuid": "~1.4.3"

	>[AZURE.NOTE]package.json 파일에 이 업데이트를 적용하면 커밋이 푸시된 후 모바일 서비스가 다시 시작됩니다.

4. 이제 .\\service\\table 하위 폴더로 이동하여 todoitem.insert.js 파일을 열고 다음과 같이 수정합니다.

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(item);
		}

	이 코드는 테이블에 uuid 열을 추가하고 고유한 GUID 식별자로 채웁니다.

5. 이전 섹션에서 한 것처럼 Git 명령 프롬프트에 다음 명령을 입력합니다.

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	새 파일을 추가하고, 변경 내용을 커밋하며, 새로운 node-uuid 모듈 및 todoitem.insert.js 스크립트 변경 내용을 모바일 서비스에 밀어 넣습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서를 완료했으므로 이제 원본 제어에 스크립트를 지정하는 방법을 알게 되었습니다. 서버 스크립트 및 사용자 지정 API 작업에 대해 자세히 알아보세요.

+ [모바일 서비스에서 서버 스크립트 작업]
	<br/>서버 스크립트, 작업 스케줄러 및 사용자 지정 API 작업을 수행하는 방법을 보여 줍니다.

+ [클라이언트에서 사용자 지정 API 호출] 
	<br/> 클라이언트에서 호출할 수 있는 사용자 지정 API를 만드는 방법을 보여줍니다.

<!-- Anchors. -->
[Enable source control in your mobile service]: #enable-source-control
[Install Git and create the local repository]: #clone-repo
[Deploy updated script files to your mobile service]: #deploy-scripts
[Leverage shared code and Node.js modules in your server scripts]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[원본 제어]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Git 설치]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[모바일 서비스 시작]: mobile-services-ios-get-started.md
[기존 앱에 모바일 서비스 추가]: mobile-services-ios-get-started-data.md
[모바일 서비스에서 서버 스크립트 작업]: mobile-services-how-to-use-server-scripts.md
[Azure 관리 포털]: https://manage.windowsazure.com/
[클라이언트에서 사용자 지정 API 호출]: mobile-services-ios-call-custom-api.md
[모듈]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid
 

<!-----HONumber=July15_HO4-->