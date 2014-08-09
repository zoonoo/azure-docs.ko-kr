
# 소스 제어에서 Azure 웹 사이트로 게시

Azure 웹 사이트는 BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial, TFS 등과 같은 소스 코드 제어 및 리포지토리 도구로부터 지속적인 배포를 지원합니다. 이러한 도구를 사용하여 웹 사이트의 콘텐츠 및 코드를 관리한 후 원할 때 쉽고 빠르게 사이트에 변경 내용을 적용할 수 있습니다.

이 문서에서는 Git를 사용하여 로컬 컴퓨터에서 Azure 웹 사이트로 바로 게시하는 방법(Azure에서는 이 게시 방법을 **Local Git**라고도 함)에 대해 배우게 됩니다. 또한 BitBucket, CodePlex, DropBox, GitHub 또는 Mercurial과 같은 리포지토리 웹 사이트에서 지속적으로 배포할 수 있도록 하는 방법에 대해서도 배우게 됩니다. TFS를 사용하여 지속적으로 배포하는 방법에 대한 내용은 [Visual Studio Online을 사용하여 Azure에 지속적으로 전송][1](영문)을 참조하십시오.

> [WACOM.NOTE] 이 문서에 설명되어 있는 많은 Git 명령은 [Mac 및 Linux용 Azure 명령줄 도구](/en-us/develop/nodejs/how-to-guides/command-line-tools/)를 사용하여 웹 사이트를 만드는 경우 자동으로 수행됩니다.

다음 단계가 작업에 포함되어 있습니다.
* [Git 설치](#Step1)
* [로컬 리포지토리 만들기](#Step2)
* [웹 페이지 추가](#Step3)
* [웹 사이트 리포지토리 사용](#Step4)
* [프로젝트 배포](#Step5)
  * [Azure에 로컬 파일 푸시(로컬 Git)](#Step6)
  * [BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial과 같은 리포지토리 웹 사이트에서
    파일 배포](#Step7)
* [문제 해결](#Step8)

<h2><a id="Step2" ></a>Git 설치</h2>


Git를 설치하는 데 필요한 단계는 운영 체제마다 다릅니다. 운영 체제 특정 배포 및 설치 지침은 [Git 설치][2]를
참조하십시오.

> [WACOM.NOTE] 일부 운영 체제에서는 Git의 명령줄과 GUI 버전을 둘 다 사용할 수 있습니다. 이 문서에서 제공하는 지침은 명령줄 버전을 사용합니다.

<h2><a id="Step2" ></a>로컬 리포지토리 만들기</h2>


다음 작업을 수행하여 새 Git 리포지토리를 만드십시오.

1.  Git 리포지토리와 웹 사이트 파일을 포함할 MyGitRepository라는 이름의 디렉터리를 만듭니다.

2.  **GitBash**(Windows) 또는 **Bash**(Unix Shell)와 같은 명령줄을 엽니다. OS X 시스템에서는 **터미널** 응용 프로그램을 통해 명령줄에 액세스할 수 있습니다.

3.  명령줄에서 MyGitRepository 디렉터리로 변경합니다.
    
         cd MyGitRepository

4.  다음 명령을 사용하여 새 Git 리포지토리를 초기화합니다.
    
         git init
    
    이 명령은 **[경로]의 빈 Git 리포지토리가 초기화됨**과 같은 메시지를 반환합니다.

<h2><a id="Step3" ></a>웹 페이지 추가</h2>


Azure 웹 사이트는 다양한 프로그래밍 언어로 만들어진 응용 프로그램을 지원합니다. 이 예에서는 고정 .html 파일을 사용합니다. 다른 프로그래밍 언어로 Azure에 웹 사이트를 게시하는 방법에 대한 내용은 [Azure 개발자 센터][3](영문)를 참조하십시오.

1.  텍스트 편집기를 사용하여 Git 리포지토리(앞서 만든 MyGitRepository 디렉터리)의 루트에 이름이 **index.html**인 파일을 새로 만듭니다.

2.  index.html 파일 내용으로 다음 텍스트를 추가하고 저장합니다.
    
         Hello Git!

3.  명령줄에서 Git 리포지토리의 루트에 있는지 확인합니다. 그런 후 다음 명령을 사용하여 리포지토리에 **index.html** 파일을 추가합니다.
    
         git add index.html


    
    > [WACOM.NOTE] 명령 다음에 -help 또는 --help를 입력하면 git 명령에 대한 도움말을 찾을 수 있습니다. 예를 들어 add 명령의 매개 변수 옵션의 경우 명령줄 도움말은 'git add -help'를 입력하거나 자세한 도움말은 'git add --help'를 입력합니다.

4.  그리고 나서 다음 명령을 사용하여 리포지토리에 변경 내용을 커밋합니다.
    
         git commit -m "Adding index.html to the repository"
    
    다음과 비슷한 결과가 나타나야 합니다.
    
         [master (root-commit) 369a79c] Adding index.html to the repository
          1 file changed, 1 insertion(+)
          create mode 100644 index.html

<h2><a id="Step4" ></a>웹 사이트 리포지토리 사용</h2>


Azure 포털을 사용하여 웹 사이트에서 Git 리포지토리를 사용할 수 있도록 하려면 다음 단계를 수행하십시오.

1.  [Azure 포털][4]에 로그인합니다.

2.  페이지의 왼쪽에서 **Web Sites**를 선택한 후 리포지토리를 사용하도록 하려는 웹 사이트를 선택합니다.
    
    ![선택한 웹 사이트를 나타내는
    이미지](./media/publishing-with-git/git-select-website.png)

3.  **DASHBOARD** 탭을 선택합니다.

4.  **quick glance** 섹션에서 **Set up deployment from source control**을 선택합니다. 다음 **SET UP DEPLOYMENT** 대화 상자가 나타납니다.
    
    ![git-WhereIsYourSourceCode](./media/publishing-with-git/git-WhereIsYourSourceCode.png)

5.  **Local Git**를 선택한 후 **Next** 화살표를 클릭합니다.

6.  잠시 후에 리포지토리가 준비되었다는 메시지가 나타나야 합니다.
    
    ![git-instructions](./media/publishing-with-git/git-instructions.png)

<h2><a id="Step5" ></a>프로젝트 배포</h2>


<h3><a id="Step6" ></a>Azure에 로컬 파일 푸시(로컬 Git)</h3>


이때 포털에 로컬 리포지토리 초기화 및 파일 추가에 대한 지침이 표시됩니다. 이 항목의 이전 단계에서 이미 이 작업을 수행했습니다. 하지만 배포 자격 증명을 설정하지 않았으면 지침에서 3단계를 수행해야 합니다. 이는 **Reset your deployment credentials**라는 링크를 따르도록 지시합니다.

로컬 Git를 사용하여 웹 사이트를 Azure에 게시하려면 다음 단계를 사용하십시오.

1.  앞서 만든 index.html 파일을 포함하는 로컬 Git 리포지토리의 루트에 있는지 명령줄을 사용하여 확인합니다.

2.  포털에서 반환한 지침의 3단계에 나열된 git remote add 명령을 복사합니다. 다음 명령과 유사합니다.
    
         git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git


   
    > [WACOM.NOTE] **remote** 명령은 명명된 참조를 원격 리포지토리에 추가합니다. 이 경우 Azure 웹 사이트 리포지토리용으로 'azure'라는 이름의 참조를 만듭니다.

3.  명령줄에서 다음을 사용하여 로컬 리포지토리에서 'azure' 원격으로 현재 리포지토리 내용을 푸시합니다.
    
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


   
    > [WACOM.NOTE] Azure 웹 사이트용으로 만들어진 리포지토리는 대상인 리포지토리의 **마스터** 분기에 대한 푸시 요청이 필요하며, 이는 웹 사이트의 콘텐츠로 사용됩니다.

4.  포털에서 포털 아래쪽의 **BROWSE** 링크를 클릭하여 **index.html**이 배포되었는지 확인합니다. 'Hello Git!'가 포함된 페이지가 나타납니다.
    
    !['Hello Git!'가 포함된 웹
    페이지](./media/publishing-with-git/git-hello-git.png)

5.  텍스트 편집기를 사용하여 'Yay!'를 포함하도록 **index.html** 파일을 변경한 후 저장합니다.

6.  명령줄에서 다음 명령을 사용하여 변경 내용을 추가(**add**)하고 커밋(**commit**)한 다음 원격 리포지토리에 그 변경 내용을 푸시합니다(**push**).
    
         git add index.html
         git commit -m "Celebration"
         git push azure master
    
    일단 **push** 명령이 완료되면 브라우저를 새로 고치고(제대로 새로 고치기 위해 Ctrl+F5를 눌러야 할 수도 있음) 그리고 나면 페이지 콘텐츠에 최신 커밋 변경 내용이 반영됩니다.
    
    !['Yay!'가 포함된 웹 페이지](./media/publishing-with-git/git-yay.png)

<h3><a id="Step7" ></a>BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial과 같은 리포지토리 웹 사이트에서 파일 배포</h3>


로컬 Git를 사용하여 Azure에 로컬 파일을 푸시하면 업데이트를 로컬 프로젝트에서 사용자의 Azure 웹 사이트로 수동으로 푸시할 수 있으며, BitBucket, CodePlex, Dropbox, GitHub 또는 Mercurial에서 배포하면 Azure가 프로젝트에서 최신 업데이트를 가져오는 지속적인 배포 프로세스가 수행됩니다.

두 가지 방법 모두 수행하면 프로젝트가 Azure 웹 사이트에 배포되지만, 한 프로젝트에 여러 명이 함께 작업하는 경우에 누가 최근에 업데이트했든 상관없이 항상 최신 버전이 게시되도록 하려는 경우 지속적인 배포 방법이 유용합니다. 또한 지속적인 배포는 위에 언급한 도구 중 하나를 응용 프로그램의 중앙 리포지토리로 사용하는 경우에 유용합니다.

GitHub, CodePlex 또는 BitBucket에서 파일을 배포하려면 로컬 프로젝트를 이러한 서비스 중 하나에 게시해야 합니다. 이러한 서비스에 프로젝트를 게시하는 방법에 대한 자세한 내용은 [Create a Repo (GitHub)][5], [Using Git with CodePlex][6], [Create a Repo (BitBucket)][7], [Using Dropbox to Share Git Repositories][8] 또는 [Quick Start - Mercurial][9]을 참조하십시오.

1.  먼저 지속적으로 배포하는 데 사용될 선택한 리포지토리에 웹 사이트 파일을 넣습니다.

2.  웹 사이트의 Azure 포털에서 **DASHBOARD** 탭을 선택합니다. **quick glance** 섹션에서 **Set up deployment from source control**을 선택합니다. **Where is your source code?**를 묻는 **Set Up Deployment** 대화 상자가 나타납니다.

3.  지속적인 배포에 사용할 소스 제어 방법을 선택합니다.

4.  메시지가 나타나면 선택한 서비스에 대한 자격 증명을 입력합니다.

5.  계정에 액세스하도록 Azure에 권한을 부여하면 리포지토리 목록이 나타납니다.
    
    ![git-ChooseARepositoryToDeploy](./media/publishing-with-git/git-ChooseARepositoryToDeploy.png)

6.  Azure 웹 사이트에 연결할 리포지토리를 선택합니다. 계속하려면 확인 표시를 클릭합니다.


   
    > [WACOM.NOTE] GitHub 또는 BitBucket에서 지속적으로 배포할 수 있도록 하면 공용 및 개인 프로젝트가 둘 다 표시됩니다.

7.  Azure는 선택한 리포지토리와 연결하고 마스터 분기에서 파일을 가져옵니다. 이 프로세스를 완료하면 **Deployments** 페이지의 **deployment history**에 다음과 같은 **Active Deployment** 메시지가 표시됩니다.
    
    ![git-githubdeployed](./media/publishing-with-git/git-GitHubDeployed.png)

8.  이때 선택한 리포지토리에서 Azure 웹 사이트로 프로젝트가 배포됩니다. 해당 사이트가 활성화되어 있는지 확인하려면 포털 아래쪽의 **Browse** 링크를 클릭하십시오. 브라우저가 웹 사이트로 이동해야 합니다.

9.  지속적으로 배포되고 있는지 확인하려면 프로젝트를 변경한 다음 이 웹 사이트에 연결된 리포지토리에 업데이트를 푸시하십시오. 리포지토리에 푸시한 직후 변경 내용이 반영되도록 웹 사이트가 업데이트되어야 합니다. 웹 사이트의 **Deployments** 페이지에서 업데이트를 가져왔는지 확인할 수 있습니다.
    
    ![git-GitHubDeployed-Updated](./media/publishing-with-git/git-GitHubDeployed-Updated.png)

<h4>지속적인 배포 작업</h4>


사용자 사이트의 **Configure** 탭의 **deployments** 섹션에서 찾은 **DEPLOYMENT TRIGGER URL**을 제공하여 지속적인 배포가 이루어집니다.

![git-DeploymentTrigger](./media/publishing-with-git/git-DeploymentTrigger.png)

리포지토리가 업데이트되면 POST 요청이 이 URL로 보내져 리포지토리가 업데이트되었다고 Azure 웹 사이트에 알립니다. 이때 Azure에서 업데이트를 가져와 웹 사이트에 배포합니다.

<h4>사용할 분기 지정</h4>


지속적인 배포를 사용하도록 설정하면 기본값은 리포지토리의 **마스터** 분기가 됩니다. 다른 분기를 사용하려면 다음 단계를 수행하십시오.

1.  포털에서 웹 사이트를 선택한 후 **CONFIGURE**를 선택합니다.

2.  페이지의 **deployments** 섹션에서 **BRANCH TO DEPLOY** 필드에 사용할 분기를 입력한 후 Enter 키를 누릅니다. 마지막으로 **SAVE**를 클릭합니다.
    
    새 분기의 변경 내용을 기반으로 Azure가 즉시 업데이트를 시작해야 합니다.

<h4>지속적 배포 사용 안 함</h4>


Azure **Dashboard**에서 지속적인 배포를 사용하지 않도록 설정할 수 있습니다. **quick glance** 섹션에서 사용 중인 리포지토리와 연결을 끊도록 옵션을 선택하십시오.

![git-DisconnectFromGitHub](./media/publishing-with-git/git-DisconnectFromGitHub.png)

확인 메시지에서 **Yes**를 누른 후 다른 소스로부터 게시를 설정하려면 **quick glance**로 돌아가 **Set up deployment from source control**을 클릭하십시오.

<h2><a id="Step8" ></a>문제 해결</h2>


다음은 Git를 사용하여 Azure 웹 사이트에 게시할 때 주로 발생하는 오류나 문제입니다.

* * *

**증상**: 'hostname' 호스트를 확인할 수 없음

**원인**: 이 오류는 'azure' 원격을 만들 때 입력한 주소 정보가 올바르지 않을 경우에 발생할 수 있습니다.

**해결 방법**: `git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드십시오.

* * *

**증상**: 공통 참조 없음 및 지정하지 않음; 아무것도 안 함. '마스터'처럼 분기를 지정해야 할 수도 있습니다.

**원인**: 이 오류는 git push 작업을 수행할 때 분기를 지정하지 않은 경우 및 Git에서 사용하는 push.default 값을 설정하지 않은 경우에 발생할 수 있습니다.

**해결 방법**: 마스터 분기를 지정하고 push 작업을 다시 수행하십시오. 예:

    git push azure master

* * *

**증상**: src refspec [branchname]이 전혀 일치하지 않음

**원인**: 이 오류는 'azure' 원격의 마스터가 아닌 다른 분기에 푸시하려는 경우 발생할 수 있습니다.

**해결 방법**: 마스터 분기를 지정하고 push 작업을 다시 수행하십시오. 예:

    git push azure master

* * *

**증상**: 오류 - 변경 내용이 원격 리포지토리에 커밋되었으나 웹 사이트가 업데이트되지 않음

**원인**: 이 오류는 추가 필수 모듈을 지정하는 package.json 파일이 포함된 Node.js 응용 프로그램을 배포하는 경우에 발생할 수 있습니다.

**해결 방법**: 'npm ERR!'가 포함된 추가 메시지가 이 오류 이전에 기록되어야 오류 원인에 대한 추가 내용을 제공할 수 있습니다. 다음은 이 오류 및 해당 'npm ERR!' 메시지의 알려진 원인입니다.

* **잘못된 package.json 파일**: npm ERR! 종속성을 읽을 수 없음

* **Windows용 바이너리 배포가 없는 네이티브 모듈**:
  
  * npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1
    
    또는
  
  * npm ERR! [modulename@version] preinstall: \`make \|\| gmake\`

## 추가 리소스

* [Azure용 PowerShell 사용 방법][10]
* [Mac 및 Linux에서 Azure 명령줄 도구를 사용하는
  방법](/en-us/develop/nodejs/how-to-guides/command-line-tools/)
* [Git 설명서][11]



[1]: http://www.windowsazure.com/ko-kr/develop/net/common-tasks/publishing-with-tfs/
[2]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[3]: http://www.windowsazure.com/ko-kr/develop/overview/
[4]: http://manage.windowsazure.com
[5]: https://help.github.com/articles/create-a-repo
[6]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[7]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[8]: https://gist.github.com/trey/2722927
[9]: http://mercurial.selenic.com/wiki/QuickStart
[10]: http://www.windowsazure.com/ko-kr/develop/nodejs/how-to-guides/powershell-cmdlets/
[11]: http://git-scm.com/documentation