<properties urlDisplayName="Command Line Administration" pageTitle="명령줄에서 모바일 서비스 관리 - Azure 자습서" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# 명령줄 도구를 사용하여 모바일 서비스 자동화 

이 항목에서는 Azure 명령줄 도구를 사용하여 Azure 모바일 서비스 생성과 관리를 자동화하는 방법을 보여 줍니다. 명령줄 도구를 설치 및 시작하고 다음 모바일 서비스 작업을 수행하는 데 사용하는 방법을 보여 줍니다.

-	[새 모바일 서비스 만들기] 
-	[새 테이블 만들기]
-   [테이블 작업에 스크립트 등록][Register a new table script]
-   [테이블 나열]
- 	[기존 테이블 삭제]
-	[모바일 서비스 나열]
-   [기존 모바일 서비스 삭제]
 
단일 스크립트 또는 배치 파일에 결합될 경우 이러한 개별 명령은 모바일 서비스의 생성, 검증 및 삭제 프로세스를 자동화합니다. 

Azure 명령줄 도구를 사용하여 모바일 서비스를 관리하려면 Azure 모바일 서비스 기능을 사용할 수 있는 Azure 계정이 필요합니다.

+ 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/" target="_blank">Azure 무료 평가판</a>을 참조하세요.

+ 기존 계정이 있지만 Azure 모바일 서비스 미리 보기를 사용하도록 설정해야 하는 경우 <a href="http://azure.microsoft.com/ko-kr/documentation/articles/php-create-account/#enable" target="_blank">Azure 미리 보기 기능 사용</a>을 참조하세요.

이 항목에서는 Azure 명령줄 도구가 지원하는 몇 개의 일반적인 관리 작업에 대해 다룹니다. 자세한 내용은 [Azure 명령줄 도구 설명서][reference-docs]를 참조하세요.

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic. 

+ (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

<h2><a name="install"></a>Azure 명령줄 도구 설치</h2>

다음 목록에는 운영 체제에 따른 명령줄 도구 설치 정보가 포함되어 있습니다.

* **Windows**: [Azure 명령줄 도구 설치 관리자를 ][windows-installer]를 다운로드합니다. 다운로드한 .msi 파일을 열고 메시지가 표시되면 설치 단계를 완료합니다.

* **Mac**: [Azure SDK 설치 관리자][mac-installer]를 다운로드합니다. 다운로드한 .pkg 파일을 열고 메시지가 표시되면 설치 단계를 완료합니다.

* **Linux**: 최신 버전의 [Node.js][nodejs-org](영문)를 설치한 후([패키지 관리자를 통해 Node.js 설치][install-node-linux](영문) 참조) 다음 명령을 실행합니다.

		npm install azure-cli -g

설치를 테스트하려면 명령 프롬프트에 'azure'를 입력합니다. 설치에 성공하면 사용 가능한 모든 'azure' 명령 목록이 표시됩니다.
<h2><a name="import-account"></a>게시 설정을 다운로드 및 가져오는 방법</h2>

시작하려면 먼저 게시 설정을 다운로드하고 가져와야 합니다. 그러면 Azure 서비스를 만들고 관리하기 위한 도구를 사용할 수 있습니다. 게시 설정을 다운로드하려면 'account download' 명령을 사용합니다.

		azure account download

기본 브라우저가 열리고 관리 포털에 로그인하라는 메시지가 표시됩니다. 로그인하면 '.publishsettings' 파일이 다운로드됩니다. 이 저장된 파일의 위치를 기록해 둡니다.

그런 다음 `<path-to-settings-file>`을 해당 `.publishsettings` 파일의 경로로 바꿔서 다음 명령을 실행하여 `.publishsettings` 파일을 가져옵니다.

		azure account import <path-to-settings-file>

account clear <code>명령을 사용하여</code> import <code>명령으로 저장된 모든 정보를</code> 제거할 수 있습니다.

		azure account clear

'account' 명령에 대한 옵션 목록을 보려면 '-help' 옵션을 사용합니다.

		azure account -help

게시 설정을 가져온 후 보안을 위해 '.publishsettings' 파일을 삭제해야 합니다. 자세한 내용은 [Mac 및 Linux에서 Azure 명령줄 도구를 설치하는 방법]을 참조하세요. 이제 명령줄이나 배치 파일에서 Azure 모바일 서비스를 만들고 관리할 준비가 되었습니다.  

<h2><a name="create-service"></a>모바일 서비스를 만드는 방법</h2>

명령줄 도구를 사용하여 새 모바일 서비스 인스턴스를 만들 수 있습니다. 모바일 서비스를 만드는 동안 새 서버에 SQL 데이터베이스 인스턴스도 만듭니다. 

다음 명령은 구독에 새 모바일 서비스 인스턴스를 만듭니다. 여기서 `<service-name>`은 새 모바일 서비스의 이름이고, `<server-admin>`은 새 서버의 로그인 이름이고, `<server-password>`는 새 로그인의 암호입니다.

		azure mobile create <service-name> <server-admin> <server-password>

지정한 모바일 서비스가 있을 경우 'mobile create' 명령이 실패합니다. 자동화 스크립트에서 모바일 서비스를 삭제한 후 다시 만들어야 합니다.

<h2><a name="list-services"></a>구독의 기존 모바일 서비스를 나열하는 방법</h2>

다음 명령은 Azure 구독의 모든 모바일 서비스 목록을 반환합니다.

		azure mobile list

이 명령은 각 모바일 서비스의 현재 상태와 URL도 표시합니다.

<h2><a name="delete-service"></a>기존 모바일 서비스를 삭제하는 방법</h2>

명령줄 도구를 사용하여 기존 모바일 서비스 및 관련된 SQL 데이터베이스와 서버를 삭제할 수 있습니다. 다음 명령은 모바일 서비스를 삭제합니다. 여기서 `<service-name>`은 삭제할 모바일 서비스의 이름입니다.

		azure mobile delete <service-name> -a -q

`-a` 및 `-q` 매개 변수가 포함되었으므로 이 명령은 프롬프트를 표시하지 않고 모바일 서비스에서 사용하는 SQL 데이터베이스와 서버도 삭제합니다.

<div class="dev-callout"><strong>참고</strong> 
   <p>-a 또는 -d와 함께 <code>-q</code> 매개 변수를 <code>지정하지 않을 경우</code> 실행이 <code>일시 중지되고</code>SQL 데이터베이스에 대한 삭제 옵션을 선택하라는 메시지가 표시됩니다. 데이터베이스나 <code>서버를 사용하는</code> 다른 서비스가 없는 경우 -a 매개 변수만 사용합니다. 그렇지 않으면 <code>-d</code> 매개 변수를 사용하여 삭제되는 모바일 서비스에 속하는 데이터만 삭제합니다.</p>
</div>

<h2><a name="create-table"></a>모바일 서비스에 테이블을 만드는 방법</h2>

다음 명령은 지정된 모바일 서비스에 테이블을 만듭니다. 여기서 `<service-name>`은 모바일 서비스의 이름이고 '<table-name>`은 만들 테이블의 이름입니다.

		azure mobile table create <service-name> <table-name>

기본 사용 권한이 설정된 새 테이블 'application'이 테이블 작업 `insert`, `read`, `update` 및 `delete`를 위해 만들어집니다. 

다음 명령은 public 'read' 권한이 설정되었지만 관리자에게만 'delete' 권한이 부여된 새 테이블을 만듭니다.

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

다음 표는 스크립트 사용 권한 값과 [Azure 관리 포털]의 사용 권한 값을 비교해서 보여 줍니다.

<table border="1" width="100%"><tr><th>스크립트 값</th><th>관리 포털 값</th></tr>
<tr><td><code>public</code></td><td>모든 사람</td></tr>
<tr><td><code>application</code> (기본값)</td><td>응용 프로그램 키가 있는 모든 사용자</td></tr>
<tr><td><code>user</code></td><td>인증된 사용자만</td></tr>
<tr><td><code>admin	</code></td><td>스크립트 및 관리자만</td></tr></table>

지정한 테이블이 이미 있을 경우 'mobile table create' 명령이 실패합니다. 자동화 스크립트에서 테이블을 삭제한 후 다시 만들어야 합니다.

<h2><a name="list-tables"></a>모바일 서비스의 기존 테이블을 나열하는 방법</h2>

다음 명령은 모바일 서비스의 모든 테이블 목록을 반환합니다. 여기서 `<service-name>`은 모바일 서비스의 이름입니다.

		azure mobile table list <service-name>

이 명령은 각 테이블의 인덱스 수와 현재 테이블에 있는 데이터 행 수도 표시합니다.

<h2><a name="delete-table"></a>모바일 서비스에서 기존 테이블을 삭제하는 방법</h2>

다음 명령은 모바일 서비스에서 테이블을 삭제합니다. 여기서 `<service-name>`은 모바일 서비스의 이름이고 '<table-name>`은 삭제할 테이블의 이름입니다.

		azure mobile table delete <service-name> <table-name> -q

자동화 스크립트에 '-q' 매개 변수를 사용하여 실행을 차단하는 확인 프롬프트를 표시하지 않고 테이블을 삭제합니다.

<h2><a name="register-script"></a>테이블 작업에 스크립트를 등록하는 방법</h2>

다음 명령은 테이블 작업에 함수를 업로드하고 등록합니다. 여기서 `<service-name>`은 모바일 서비스의 이름이고, `<table-name>`은 테이블의 이름이고, `<operation>`은 테이블 작업으로 `read`, `insert`, `update` 또는 `delete`일 수 있습니다.

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

이 작업은 로컬 컴퓨터에서 JavaScript(.js) 파일을 업로드합니다. 파일의 이름은 테이블 및 작업 이름으로부터 구성해야 하고, 명령이 실행되는 위치를 기준으로 `table` 하위 폴더에 위치해야 합니다. 예를 들어 다음 작업은 'TodoItems' 테이블에 속하는 새 'insert' 스크립트를 업로드하고 등록합니다.

		azure mobile script upload todolist table/todoitems.insert.js

또한 스크립트 파일의 함수 선언은 등록된 테이블 작업과 일치해야 합니다. 즉, 'insert' 스크립트의 경우 업로드된 스크립트에 다음 서명이 있는 함수가 포함되어 있습니다.

		function insert(item, user, request) {
		    ...
		} 

스크립트 등록에 대한 자세한 내용은 [모바일 서비스 서버 스크립트 참조]를 참조하세요.

<!--<h2><a name="test-service"></a>Test the new mobile service</h2>

When you are automating the creation of your mobile service, you can optionally use cURL or another command-line request generator to 

## <a name="nextsteps"> </a>Next Steps
Next steps here....
-->
<!-- Anchors. -->
[명령줄 도구 다운로드 및 설치]: #install
[게시 설정 다운로드 및 가져오기]: #import
[새 모바일 서비스 만들기]: #create-service
[마스터 키 얻기]: #get-master-key
[새 테이블 만들기]: #create-table
[새 테이블 스크립트 등록]: #register-script
[기존 테이블 삭제]: #delete-table
[기존 모바일 서비스 삭제]: #delete-service
[모바일 서비스 테스트]: #test-service
[모바일 서비스 나열]: #list-services
[테이블 나열]: #list-tables
[다음 단계]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Azure 관리 포털]: https://manage.windowsazure.com/
[nodejs-org](영문): http://nodejs.org/
[install-node-linux](영문): https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /ko-kr/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Mac 및 Linux에서 Azure 명령줄 도구를 설치하는 방법]: http://go.microsoft.com/fwlink/p/?LinkId=275795


<!--HONumber=35.1-->
