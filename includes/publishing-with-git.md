# Azure 앱 서비스에서 GIT를 사용하여 연속 배포

[Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)는 BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial, TFS와 같은 소스 코드 제어 및 리포지토리 도구에서 웹 앱에 대한 지속적인 배포를 지원합니다. 이러한 도구를 사용하여 응용 프로그램의 콘텐츠 및 코드를 관리한 후 원할 때 쉽고 빠르게 Azure 웹 앱에 변경 내용을 적용할 수 있습니다.

이 문서에서는 Git를 사용하여 로컬 컴퓨터에서 웹 앱으로 바로 게시하는 방법(Azure에서는 이 게시 방법을 **로컬 Git**라고도 함)에 대해 배우게 됩니다. 또한 BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial과 같은 리포지토리 사이트에서 지속적으로 배포할 수 있도록 하는 방법에 대해서도 배우게 됩니다. TFS를 사용하여 지속적으로 배포하는 방법에 대한 내용은 [Visual Studio Online을 사용하여 Azure에 지속적으로 전송](영문)을 참조하십시오.

> [AZURE.NOTE]이 문서에 설명되어 있는 대부분의 Git 명령은 [Mac 및 Linux용 Azure 명령줄 도구](/develop/nodejs/how-to-guides/command-line-tools/)를 사용하여 웹 앱을 만드는 경우 자동으로 수행됩니다.

## <a id="Step1"></a>1단계: Git 설치

Git를 설치하는 데 필요한 단계는 운영 체제마다 다릅니다. 운영 체제 특정 배포 및 설치 지침은 [Git 설치]를 참조하십시오.

> [AZURE.NOTE]일부 운영 체제에서는 Git의 명령줄과 GUI 버전을 둘 다 사용할 수 있습니다. 이 문서에서 제공하는 지침은 명령줄 버전을 사용합니다.

## <a id="Step2"></a>2단계: 로컬 리포지토리 만들기

다음 작업을 수행하여 새 Git 리포지토리를 만드세요.

1. Git 리포지토리와 웹 앱 파일을 포함할 MyGitRepository라는 이름의 디렉터리를 만듭니다.

2. **GitBash**(Windows) 또는 **Bash**(Unix Shell)와 같은 명령줄 도구를 엽니다. OS X 시스템에서는 **터미널** 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

3. 명령줄에서 MyGitRepository 디렉터리로 변경합니다.

		cd MyGitRepository

4. 다음 명령을 사용하여 새 Git 리포지토리를 초기화합니다.

		git init

	이 명령은 **[경로]의 빈 Git 리포지토리가 초기화됨**과 같은 메시지를 반환합니다.

## <a id="Step3"></a>3단계: 웹 페이지 추가

웹 앱에서 다양한 프로그래밍 언어로 만들어진 응용 프로그램을 지원합니다. 이 예에서는 고정 .html 파일을 사용합니다.

1. 텍스트 편집기를 사용하여 Git 리포지토리(앞서 만든 MyGitRepository 디렉터리)의 루트에 이름이 **index.html**인 파일을 새로 만듭니다.

2. index.html 파일 내용으로 다음 텍스트를 추가하고 저장합니다.

		Hello Git!

3. 명령줄에서 Git 리포지토리의 루트에 있는지 확인합니다. 그런 후 다음 명령을 사용하여 리포지토리에 **index.html** 파일을 추가합니다.

		git add index.html 

	> [AZURE.NOTE]명령 다음에 -help 또는 --help를 입력하면 git 명령에 대한 도움말을 찾을 수 있습니다. 예를 들어 add 명령에 대한 매개 변수 옵션의 경우 명령줄 도움말은 'git add -help'를 입력하고, 자세한 도움말은 'git add --help'를 입력합니다.

4. 그리고 나서 다음 명령을 사용하여 리포지토리에 변경 내용을 커밋합니다.

		git commit -m "Adding index.html to the repository"

	다음과 비슷한 결과가 나타나야 합니다.

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

## <a id="Step4"></a>웹앱 리포지토리 사용

웹앱에서 Git 리포지토리를 사용할 수 있도록 하려면 다음 단계를 수행합니다.

1. [Azure Preview 포털]에 로그인합니다.

2. 웹 앱의 블레이드에서 **배포** 섹션까지 아래로 스크롤하고 **연속 배포 설정**을 클릭합니다. **소스 선택**을 클릭한 다음 **로컬 Git 리포지토리**를 클릭하고 **확인**을 클릭합니다.

	![로컬 Git 리포지토리](./media/publishing-with-git/azure1-local-git.png)

4. Azure에서 리포지토리를 처음 설정하는 경우 로그인 자격 증명을 만들어야 합니다. 이를 사용하여 Azure 리포지토리에 로그인하고 로컬 Git 리포지토리에서 변경 내용을 푸시합니다. 웹앱의 블레이드에서 **설정> 배포 자격 증명 설정**을 클릭한 다음 배포 사용자 이름 및 암호를 구성합니다. 완료되면 **확인**을 클릭합니다.

	![](./media/publishing-with-git/azure2-credentials.png)

## <a id="Step5"></a>프로젝트 배포

* [Azure에 로컬 파일 푸시(로컬 Git)](#Step6)
* [BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial과 같은 리포지토리 웹 사이트에서 파일 배포](#Step7)
* [BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial에서 Visual Studio 솔루션 배포](#Step75)

로컬 Git를 사용하여 웹앱을 Azure에 게시하려면 다음 단계를 사용합니다.

1. 웹 앱의 블레이드의 배포 섹션에서 **찾을 수 없는 배포**를 클릭합니다.

	![](./media/publishing-with-git/azure3-repo-details.png)

	**Git URL**은 로컬 리포지토리로(부터) 배포할 원격 참조입니다. 다음 단계에서 이 URL을 사용합니다.

1. 앞서 만든 index.html 파일을 포함하는 로컬 Git 리포지토리의 루트에 있는지 명령줄을 사용하여 확인합니다.

2. `git remote`을(를) 사용하여 1단계의 **Git URL**에 나열된 원격 참조를 추가합니다. 명령은 다음과 유사합니다.

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE]**remote** 명령은 명명된 참조를 원격 리포지토리에 추가합니다. 이 경우 웹 앱의 리포지토리용으로 'azure'라는 이름의 참조를 만듭니다.

1. 명령줄에서 다음을 사용하여 로컬 리포지토리에서 'azure' 원격으로 현재 리포지토리 내용을 푸시합니다.

		git push azure master

	포털에서 배포 자격 증명을 다시 설정할 때 이전에 만든 암호를 입력하라는 메시지가 나타납니다. 암호를 입력합니다(Gitbash는 암호를 입력할 때 콘솔에 별표를 표시하지 않음). 다음과 비슷한 결과가 나타나야 합니다.

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [AZURE.NOTE]웹 앱용으로 만들어진 리포지토리는 대상인 리포지토리의 <strong>마스터</strong> 분기에 대한 푸시 요청이 필요하며, 이는 웹 앱의 콘텐츠로 사용됩니다.

2. Azure 포털에서 웹 앱의 블레이드로 돌아갑니다. **찾을 수 없는 배포**는 최신 푸시의 로그 항목을 포함한 **활성 배포**로 변경되어야 합니다.

	![](./media/publishing-with-git/azure4-deployed.png)

2. 웹앱 블레이드 위쪽에 있는 **URL** 아래에서 링크를 클릭하여 **index.html**이 배포되었는지 확인합니다. 'Hello Git!'가 포함된 페이지가 나타납니다.

	!['Hello Git!'가 포함된 웹 페이지][hello-git]

3. 텍스트 편집기를 사용하여 'Yay!'를 포함하도록 **index.html** 파일을 변경한 후 저장합니다.

4. 명령줄에서 다음 명령을 사용하여 변경 내용을 추가(**add**)하고 커밋(**commit**)한 다음 원격 리포지토리에 그 변경 내용을 푸시합니다(**push**).

		git add index.html
		git commit -m "Celebration"
		git push azure master

	일단 **push** 명령이 완료되면 브라우저를 새로 고치고(제대로 새로 고치기 위해 Ctrl+F5를 눌러야 할 수도 있음) 그리고 나면 페이지 콘텐츠에 최신 커밋 변경 내용이 반영됩니다.

### <a id="Step7"></a>BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial과 같은 리포지토리 사이트에서 파일 배포

로컬 Git를 사용하여 Azure에 로컬 파일을 푸시하면 업데이트를 로컬 프로젝트에서 Azure의 웹 앱으로 수동으로 푸시할 수 있으며, BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial에서 배포하면 Azure가 프로젝트에서 최신 업데이트를 가져오는 지속적인 배포 프로세스가 수행됩니다.

두 가지 방법 모두 수행하면 프로젝트가 웹 앱에 배포되지만, 한 프로젝트에 여러 명이 함께 작업하는 경우에 누가 최근에 업데이트했든 상관없이 항상 최신 버전이 게시되도록 하려는 경우 지속적인 배포 방법이 유용합니다. 또한 지속적인 배포는 위에 언급한 도구 중 하나를 응용 프로그램의 중앙 리포지토리로 사용하는 경우에 유용합니다.

GitHub, CodePlex 또는 BitBucket에서 파일을 배포하려면 로컬 프로젝트를 이러한 서비스 중 하나에 게시해야 합니다. 이러한 서비스에 프로젝트를 게시하는 방법에 대한 자세한 내용은 [Create a Repo (GitHub)], [Using Git with CodePlex], [Create a Repo (BitBucket)], [Using Dropbox to Share Git Repositories] 또는 [Quick Start - Mercurial]을 참조하십시오.

1. 먼저 지속적으로 배포하는 데 사용될 선택한 리포지토리에 웹 앱 파일을 넣습니다.

2. 포털의 웹 앱의 블레이드에서 **배포** 섹션까지 아래로 스크롤하고 **연속 배포 설정**을 클릭합니다. **소스 선택**을 클릭한 다음 예를 들어 **GitHub**를 클릭합니다.

	![](./media/publishing-with-git/azure6-setup-github.png)
	
2. **연속 배포** 블레이드에서 **권한 부여**를 클릭한 다음 **권한 부여**를 클릭합니다. Azure 포털은 권한 부여 프로세스를 완료하려면 리포지토리 사이트로 리디렉션합니다.

4. 완료되면 Azure 포털로 돌아가서 **권한 부여** 블레이드에서 **확인**을 클릭합니다.

5. **연속 배포** 블레이드에서, 배포할 조직, 프로젝트 및 지점을 선택합니다. 완료되면 **확인**을 클릭합니다.
  
	![](./media/publishing-with-git/azure7-setup-github-configure.png)

	> [AZURE.NOTE]GitHub 또는 BitBucket에서 지속적으로 배포할 수 있도록 하면 공용 및 개인 프로젝트가 둘 다 표시됩니다.

Azure는 선택한 리포지토리와 연결하고 지정한 분기에서 파일을 가져옵니다. 이 프로세스를 완료한 후 웹 앱 블레이드의 **배포** 섹션은 배치가 성공했음을 나타내는 **활성 배포** 메시지를 표시합니다.

7. 이때 선택한 리포지토리에서 웹 앱으로 프로젝트가 배포됩니다. 웹앱 상태가 활성인지를 확인하려면 포털 위쪽에 있는 **URL**을 클릭합니다. 브라우저가 웹 앱으로 이동해야 합니다.

8. 사용자가 선택한 리포지토리에서 지속적으로 배포되는지 확인하려면, 리포지토리에 대한 변경을 푸시합니다. 리포지토리에 푸시한 직후 변경 내용이 반영되도록 웹 앱을 업데이트해야 합니다. 웹앱의 **배포** 블레이드에서 업데이트를 가져왔는지 확인할 수 있습니다.

### <a id="Step75"></a>BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial에서 Visual Studio 솔루션 배포

Azure 앱 서비스에서 웹 앱에 Visual Studio 솔루션을 푸시하는 것은 간단한 index.html 파일을 푸시하는 것만큼 쉽습니다. 웹 앱 배포 프로세스는 NuGet 종속성 복원 및 응용 프로그램 이진 파일 생성 등의 모든 세부 정보를 간소화합니다. Git 리포지토리에서 코드를 유지 관리하는 소스 제어 모범 사례만 따르면 나머지는 Azure 웹 사이트 배포에서 처리할 수 있습니다.

솔루션과 리포지토리를 다음과 같이 구성한 경우 Visual Studio 솔루션을 Azure 웹앱에 푸시하는 단계는 [이전 섹션](#Step7)과 동일합니다.

-	리포지토리 루트에서 `.gitignore` 파일을 추가한 후 리포지토리에서 제외할 모든 파일 및 폴더(예: `Obj`, `Bin` 및 `packages` 폴더)를 지정합니다(서식 정보는 [gitignore 설명서](http://git-scm.com/docs/gitignore) 참조). 예:

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[AZURE.NOTE]GitHub를 사용하는 경우 리포지토리를 만들 때 Visual Studio 관련 .gitignore 파일을 선택적으로 생성할 수 있습니다. 여기에는 모든 공통 임시 파일, 빌드 결과 등이 포함됩니다. 그런 다음 특정 요구에 맞게 사용자 지정할 수 있습니다.

-	전체 솔루션의 디렉터리 트리를 리포지토리에 추가하고 .sln 파일을 리포지토리 루트에 둡니다.

-	Visual Studio 솔루션에서 [NuGet 패키지 복원을 사용하도록 설정](http://docs.nuget.org/Consume/Package-Restore)하여 Visual Studio에서 누락된 패키지를 자동으로 복원하도록 합니다.

설명된 대로 리포지토리를 설정하고, 온라인 Git 리포지토리 중 하나에서 연속 게시에 대해 Azure에서 웹 앱을 구성한 후에는 Visual Studio에서 ASP.NET 응용 프로그램을 로컬로 개발하고 온라인 Git 리포지토리에 변경 내용을 푸시하여 코드를 지속적으로 배포할 수 있습니다.

## 지속적 배포 사용 안 함

**배포** 블레이드에서 지속적인 배포를 사용하지 않도록 설정할 수 있습니다. 웹앱 블레이드의 **배포** 섹션에서 **활성 배포**를 클릭합니다. 그런 다음 **연결 끊기**를 클릭합니다.

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)

확인 메시지에서 **예**를 누른 후, 다른 소스로부터 게시를 설정하려면 웹 앱의 블레이드로 돌아가 **연속 배포 설정**을 클릭하세요.

## <a id="Step8"></a>문제 해결

다음은 Git를 사용하여 Azure에서 웹 앱을 게시할 때 주로 발생 하는 문제 또는 오류입니다.

****

**증상**: '[siteURL]'에 액세스할 수 없음: [scmAddress]에 연결하지 못했습니다.

**원인**: 이 오류는 웹 앱이 가동 및 실행되지 않는 경우에 발생할 수 있습니다.

**해결 방법**: Azure 포털에서 웹앱을 시작합니다. 웹 앱이 실행되지 않으면 Git 배포가 작동하지 않습니다.


****

**증상**: 'hostname' 호스트를 확인할 수 없음

**원인**: 이 오류는 'azure' 원격을 만들 때 입력한 주소 정보가 올바르지 않을 경우에 발생할 수 있습니다.

**해결 방법**: `git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드세요.

****

**증상**: 공통 참조 없음 및 지정하지 않음; 아무것도 안 함. '마스터'와 같은 분기를 지정해야 할 수도 있습니다.

**원인**: 이 오류는 git push 작업을 수행할 때 분기를 지정하지 않은 경우 및 Git에서 사용하는 push.default 값을 설정하지 않은 경우에 발생할 수 있습니다.

**해결 방법**: 마스터 분기를 지정하고 push 작업을 다시 수행하세요. 예:

	git push azure master

****

**증상**: src refspec [branchname]이 전혀 일치하지 않음

**원인**: 이 오류는 'azure' 원격의 마스터가 아닌 다른 분기에 푸시하려는 경우 발생할 수 있습니다.

**해결 방법**: 마스터 분기를 지정하고 push 작업을 다시 수행하세요. 예:

	git push azure master

****

**증상**: 오류 - 변경 내용이 원격 리포지토리에 커밋되었으나 웹앱이 업데이트되지 않음

**원인**: 이 오류는 추가 필수 모듈을 지정하는 package.json 파일이 포함된 Node.js 응용 프로그램을 배포하는 경우에 발생할 수 있습니다.

**해결 방법**: 'npm ERR!'가 포함된 추가 메시지가 이 오류 이전에 기록되어야 오류 원인에 대한 추가 내용을 제공할 수 있습니다. 다음은 이 오류 및 해당 'npm ERR!' 메시지의 알려진 원인입니다.

* **잘못된 package.json 파일**: npm ERR! 종속성을 읽을 수 없음

* **Windows용 바이너리 배포가 없는 네이티브 모듈**:

	* npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1

		OR

	* npm ERR! [modulename@version] preinstall: `make || gmake`


## 추가 리소스

* [Azure용 PowerShell 사용 방법]
* [Mac 및 Linux용 Azure 명령줄 도구를 사용하는 방법]
* [Git 설명서]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[Azure Preview 포털]: https://portal.azure.com
[Git website]: http://git-scm.com
[Git 설치]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure용 PowerShell 사용 방법]: ../articles/install-configure-powershell.md
[Mac 및 Linux용 Azure 명령줄 도구를 사용하는 방법]: ../articles/xplat-cli.md
[Git 설명서]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Create a Repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Create a Repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Using Dropbox to Share Git Repositories]: https://gist.github.com/trey/2722927
[Visual Studio Online을 사용하여 Azure에 지속적으로 전송]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!---HONumber=July15_HO4-->