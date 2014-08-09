<properties linkid="develop-mobile-tutorials-store-scripts-in-source-control" urlDisplayName="Store server scripts in source control" pageTitle="Store server scripts in source control - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="" editor="" />

원본 제어에 서버 스크립트 저장
==============================

이 항목에서는 Git 리포지토리에 서버 스크립트를 저장하도록 Azure 모바일 서비스에서 원본 제어를 처음으로 설정하는 방법을 보여 줍니다. 스크립트 및 기타 JavaScript 코드 파일을 로컬 리포지토리에서 프로덕션 모바일 서비스로 수준을 올릴 수 있습니다. 또한 여러 스크립트에서 필요할 수 있는 공유 코드를 정의하는 방법 및 Node.js 모듈을 업로드하는 방법도 설명합니다.

이 자습서에서는 다음 단계를 안내합니다.

1.  [모바일 서비스에서 원본 제어를 사용하도록 설정](#enable-source-control)합니다.
2.  [Git을 설치하고 로컬 리포지토리를 만듭니다](#clone-repo).
3.  [업데이트된 스크립트 파일을 모바일 서비스에 배포](#deploy-scripts)합니다.
4.  [스크립트에서 공유 코드 및 Node.js 모듈을 활용](#use-npm)합니다.

이 자습서를 완료하려면 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started) 또는 [데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet) 자습서를 완료하여 모바일 서비스를 만들어 둬야 합니다.

원본 제어 사용모바일 서비스에서 원본 제어 사용
----------------------------------------------

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

    ![](./media/mobile-services-store-scripts-source-control/mobile-services-selection.png)

2.  **대시보드** 탭을 클릭하고 **간략 상태** 아래에 있는 **소스 제어 설정**을 클릭한 후 **예**를 클릭하여 확인합니다.

    ![](./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png)

    > [WACOM.NOTE] 
    > 원본 제어는 미리 보기 기능입니다. 스크립트 파일이 모바일 서비스에 저장되어 있더라도 이 파일을 정기적으로 백업하는 것이 좋습니다.

3.  **사용자 이름**, **새 암호**를 제공하고 암호를 확인한 후 확인 단추를 클릭합니다.

    ![](./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png)

    모바일 서비스에서 Git 리포지토리가 만들어집니다. 방금 제공한 자격 증명을 기록해 둡니다. 이 자격 증명은 이 리포지토리에 액세스하는 데 사용합니다.

4.  구성 탭을 클릭하면 새 **소스 제어** 필드가 나타납니다.

    ![](./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png)

    Git 리포지토리의 URL이 표시됩니다. 이 URL을 사용하여 리포지토리를 로컬 컴퓨터에 복제합니다.

모바일 서비스에서 원본 제어를 사용하도록 설정했으므로 이제 Git을 사용하여 리포지토리를 로컬 컴퓨터에 복제합니다.

리포지토리 복제Git 설치 및 로컬 리포지토리 만들기
-------------------------------------------------

1.  로컬 컴퓨터에 Git을 설치합니다.

    Git를 설치하는 데 필요한 단계는 운영 체제마다 다릅니다. 운영 체제 특정 배포 및 설치 지침은 [Git 설치](http://git-scm.com/book/en/Getting-Started-Installing-Git)를 참조하십시오.

    > [WACOM.NOTE] 
    > 일부 운영 체제에서는 Git의 명령줄과 GUI 버전을 둘 다 사용할 수 있습니다. 이 문서에서 제공하는 지침은 명령줄 버전을 사용합니다.

2.  **GitBash**(Windows) 또는 **Bash**(Unix Shell)와 같은 명령줄을 엽니다. OS X 시스템에서는 **터미널** 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

3.  명령줄에서 스크립트를 저장할 디렉터리로 변경합니다. 예를 들어 `cd SourceControl`입니다.

4.  다음 명령을 사용하여 새 Git 리포지토리의 로컬 복사본을 만들고 `<your_git_URL>`을 모바일 서비스용 Git 리포지토리의 URL로 바꿉니다.

         git clone <your_git_URL>

5.  메시지가 표시되면 모바일 서비스에서 원본 제어를 사용하도록 설정할 때 설정한 사용자 이름 및 암호를 입력합니다. 성공적으로 인증된 후에는 다음과 같은 일련의 응답이 표시됩니다.

         remote: Counting objects: 8, done.
         remote: Compressing objects: 100% (4/4), done.
         remote: Total 8 (delta 1), reused 0 (delta 0)
         Unpacking objects: 100% (8/8), done.

6.  `git clone` 명령을 실행한 디렉터리로 이동하여 다음 디렉터리 구조를 확인합니다.

    ![4](./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png)

    이 경우에는 데이터 서비스의 로컬 리포지토리인 모바일 서비스의 이름을 사용하여 새 디렉터리가 만들어졌습니다.

7.  .\\service\\table 하위 폴더를 열면 TodoItem 테이블에 대한 작업 권한이 JSON으로 표현된 TodoItem.json 파일이 포함된 것이 확인됩니다.

    이 테이블에 대해 서버 스크립트가 정의되어 있다면 주어진 테이블 작업에 대한 스크립트가 포함된 `TodoItem._<operation>_.js`라는 파일이 한 개 이상 있습니다. 스케줄러 및 사용자 지정 API 스크립트는 개별 이름을 사용하여 별도의 폴더에 유지됩니다. 자세한 내용은 [원본 제어](http://msdn.microsoft.com/ko-kr/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643)를 참조하십시오.

지금까지 로컬 리포지토리를 만들었습니다. 이제 서버 스크립트를 변경하고 변경 내용을 다시 모바일 서비스에 밀어 넣을 수 있습니다.

스크립트 배포모바일 서비스에 업데이트된 스크립트 파일 배포
----------------------------------------------------------

1.  .\\service\\table 하위 폴더로 이동하고 todoitem.insert.js 파일이 아직 없는 경우 만듭니다.

2.  텍스트 편집기에서 새 todoitem.insert.js 파일을 열고 다음 코드를 붙여넣은 후 변경 내용을 저장합니다.

         function insert(item, user, request) {
             request.execute();
             console.log(item);
         }

    이 코드는 삽입된 항목을 로그에 씁니다. 이 파일에 아직 코드가 포함되어 있지 않은 경우 `console.log()` 호출과 같은 유효한 JavaScript 코드를 이 파일에 추가한 후 변경 내용을 저장하면 됩니다.

3.  Git 명령 프롬프트에서 새 스크립트 파일 추적을 시작하는 다음 명령을 입력합니다.

         $ git add .

4.  변경 내용을 커밋하는 다음 명령을 입력합니다.

         $ git commit -m "updated the insert script"

5.  원격 리포지토리에 변경 내용을 업로드하는 다음 명령을 입력합니다.

         $ git push origin master

    커밋이 모바일 서비스에 배포됨을 나타내는 일련의 명령이 표시됩니다.

6.  관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![](./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png)

7.  **스크립트**를 클릭한 후 **삽입** 작업을 선택합니다.

    ![](./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png)

    표시되는 삽입 작업 스크립트는 방금 리포지토리에 업로드한 JavaScript 코드와 같습니다.

공유 코드 및 모듈서버 스크립트에서 공유 코드 및 Node.js 모듈 활용
-----------------------------------------------------------------

모바일 서비스에서는 전체 핵심 Node.js 모듈에 액세스할 수 있으며, **require** 함수를 사용하여 코드에서 이 모듈을 사용할 수 있습니다. 모바일 서비스는 핵심 Node.js 패키지에 속하지 않는 Node.js 모듈도 사용할 수 있으며, 고유한 공유 코드를 Node.js 모듈로 정의할 수도 있습니다. 모듈 만들기에 대한 자세한 내용은 Node.js API 참조 설명서의 [모듈](http://nodejs.org/api/modules.html)(영문)을 참조하십시오.

다음으로, 원본 제어 및 NPM(Node.js 패키지 관리자)을 사용하여 [node-uuid](https://npmjs.org/package/node-uuid)(영문) Node.js 모듈을 모바일 서비스에 추가합니다. 그런 다음 이 모듈은 삽입된 항목에 대한 **uuid** 속성의 새 GUID 값을 생성하는 데 사용됩니다.

1.  아직 설치하지 않은 경우 [Node.js 웹 사이트](http://nodejs.org/)(영문)의 단계에 따라 로컬 컴퓨터에 Node.js를 설치합니다.

2.  로컬 Git 리포지토리의 `.\service` 폴더로 이동한 후 명령 프롬프트에서 다음 명령을 실행합니다.

         npm install node-uuid

    NPM은 현재 위치에 `node_modules` 디렉터리를 만들고 `\node-uuid` 하위 디렉터리에 [node-uuid](https://npmjs.org/package/node-uuid) 모듈을 설치합니다.

    **참고**

    디렉터리 계층 구조에 `node_modules`가 이미 있는 경우 NPM은 리포지토리에 새 `node_modules`를 만드는 대신 `\node-uuid` 하위 디렉터리를 만듭니다. 이 경우 기존 `node_modules` 디렉터리를 삭제합니다.

3.  이제 .\\service\\table 하위 폴더로 이동하여 todoitem.insert.js 파일을 열고 다음과 같이 수정합니다.

         function insert(item, user, request) {
             var uuid = require('node-uuid');
             item.uuid = uuid.v1();
             request.execute();
             console.log(item);
         }

    이 코드는 테이블에 uuid 열을 추가하고 고유한 GUID 식별자로 채웁니다.

4.  이전 섹션에서 한 것처럼 Git 명령 프롬프트에 다음 명령을 입력합니다.

         $ git add .
         $ git commit -m "added node-uuid module"
         $ git push origin master

    새 파일을 추가하고, 변경 내용을 커밋하며, 새로운 node-uuid 모듈 및 todoitem.insert.js 스크립트 변경 내용을 모바일 서비스에 밀어 넣습니다.

다음 단계
---------

이 자습서를 완료했으므로 이제 원본 제어에 스크립트를 지정하는 방법을 알게 되었습니다. 서버 스크립트 및 사용자 지정 API 작업에 대해 자세히 알아보십시오.

-   [모바일 서비스에서 서버 스크립트 작업](/en-us/develop/mobile/how-to-guides/work-with-server-scripts)
    <br/>서버 스크립트, 작업 스케줄러 및 사용자 지정 API 작업을 수행하는 방법을 보여 줍니다.

-   [풀 알림을 지원하는 사용자 지정 API 정의](/en-us/develop/mobile/tutorials/create-pull-notifications-dotnet)
    <br/>사용자 지정 API를 사용하여 Windows 스토어 앱에서 라이브 타일을 업데이트하는 정기 알림을 지원하는 방법을 알아봅니다.


<!-- Anchors. -->
[Enable source control in your mobile service]: #enable-source-control
[Install Git and create the local repository]: #clone-repo
[Deploy updated script files to your mobile service]: #deploy-scripts
[Leverage shared code and Node.js modules in your server scripts]: #use-npm

<!-- Images. -->
[0]: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
[1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
[2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
[3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[Source control]: http://msdn.microsoft.com/ko-kr/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Work with server scripts in Mobile Services]: /en-us/develop/mobile/how-to-guides/work-with-server-scripts
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[WindowsAzure.com]: http://www.windowsazure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[Define a custom API that supports pull notifications]: /en-us/develop/mobile/tutorials/create-pull-notifications-dotnet
[Node.js API Documentation: Modules]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid
