# Mac 및 Linux에서 Azure 명령줄 도구를 사용하는 방법

이 가이드에서는 Mac 및 Linux에 Azure 명령줄 도구를 사용하여 Azure에 서비스를 만들고 관리하는 방법을 설명합니다. **도구 설치**, **게시 설정 가져오기**, **Azure 웹 사이트 만들기 및 관리**, **Azure 가상 컴퓨터 만들기 및 관리** 등의 시나리오를 다룹니다. 포괄적인 참조 설명서는 [Mac 및 Linux용 Azure 명령줄 도구 설명서][Mac 및 Linux용 Azure 명령줄 도구 설명서](영문)를 참조하세요.

## 목차

-   [Mac 및 Linux용 Azure 명령줄 도구 정의](#Overview)
-   [Mac 및 Linux에서 Azure 명령줄 도구를 설치하는 방법](#Download)
-   [Azure 계정을 만드는 방법](#CreateAccount)
-   [게시 설정을 다운로드 및 가져오는 방법](#Account)
-   [Azure 웹 사이트를 만들고 관리하는 방법](#WebSites)
-   [Azure 가상 컴퓨터를 만들고 관리하는 방법](#VMs)

## <span id="Overview"></span></a>Mac 및 Linux용 Azure 명령줄 도구 정의

Mac 및 Linux용 Azure 명령줄 도구는 Azure 서비스를 배포 및 관리하기 위한 명령줄 도구 집합입니다.

지원되는 작업은 다음과 같습니다.

-   게시 설정 가져오기
-   Azure 웹 사이트 만들기 및 관리
-   Azure 가상 컴퓨터 만들기 및 관리

지원되는 명령의 전체 목록을 보려면 도구를 설치한 후 명령줄에서 `azure -help`를 입력하거나 [참조 설명서][Mac 및 Linux용 Azure 명령줄 도구 설명서]를 참조하세요.

## <span id="Download"></span>Mac 및 Linux에서 Azure 명령줄 도구를 설치하는 방법</a>

다음 목록에는 운영 체제에 따른 명령줄 도구 설치 정보가 포함되어 있습니다.

-   **Mac**: [Azure SDK 설치 관리자][Azure SDK 설치 관리자]를 다운로드합니다. 다운로드한 .pkg 파일을 열고 메시지가 표시되면 설치 단계를 완료합니다.

-   **Linux**: 최신 버전의 [Node.js][Node.js]를 설치한 후([패키지 관리자를 통해 Node.js 설치][패키지 관리자를 통해 Node.js 설치](영문) 참조) 다음 명령을 실행합니다.

        npm install azure-cli -g

    **참고**: 이 명령은 상승된 권한으로 실행해야 할 수도 있습니다.

        sudo npm install azure-cli -g

-   **Windows**: Windows 설치 관리자(.msi 파일)를 실행합니다. 이 파일은 [Azure 명령줄 도구][Azure 명령줄 도구]에서 사용할 수 있습니다.

설치를 테스트하려면 명령 프롬프트에 `azure`를 입력합니다. 설치에 성공하면 사용 가능한 모든 `azure` 명령 목록이 표시됩니다.

## <span id="CreateAccount"></span></a>Azure 계정을 만드는 방법

Mac 및 Linux용 Azure 명령줄 도구를 사용하려면 Azure 계정이 필요합니다.

웹 브라우저를 열고 [][]<http://www.windowsazure.com></a>으로 이동한 후 오른쪽 위에 있는 **무료 체험**을 클릭합니다.

![Azure 웹 사이트][Azure 웹 사이트]

계정을 만들기 위한 지침을 따르세요.

## <span id="Account"></span></a>게시 설정을 다운로드 및 가져오는 방법

시작하려면 먼저 게시 설정을 다운로드하고 가져와야 합니다. 그러면 Azure 서비스를 만들고 관리하기 위한 도구를 사용할 수 있습니다. 게시 설정을 다운로드하려면 `account download` 명령을 사용합니다.

    azure account download

기본 브라우저가 열리고 관리 포털에 로그인하라는 메시지가 표시됩니다. 로그인하면 `.publishsettings` 파일이 다운로드됩니다. 이 파일이 저장된 위치를 기록해 둡니다.

다음 명령을 실행하여 `.publishsettings` 파일을 가져옵니다. `{path to .publishsettings file}`을 해당 `.publishsettings` 파일의 경로로 바꿉니다.

    azure account import {path to .publishsettings file}

`account clear` 명령을 사용하여 `import` 명령으로 저장된 모든 정보를 제거할 수 있습니다.

    azure account clear

`account` 명령에 대한 옵션 목록을 보려면 `-help` 옵션을 사용합니다.

    azure account -help

게시 설정을 가져온 후 보안을 위해 `.publishsettings` 파일을 삭제해야 합니다.

<div class="dev-callout"> 
<b>참고</b> 
<p>게시 설정을 가져오면 Azure 구독에 액세스하기 위한 자격 증명이 <code data-inline="1">user</code> 폴더 안에 저장됩니다. <code data-inline="1">user</code> 폴더는 운영 체제에 의해 보호됩니다. 그러나 추가 단계를 수행하여 <code data-inline="1">user</code> 폴더를 암호화하는 것이 좋습니다. 다음과 같은 방법으로 이 작업을 수행할 수 있습니다.</p>

<ul>
<li>Windows에서 폴더 속성을 수정하거나 BitLocker를 사용합니다.</li>
<li>Mac에서 폴더에 대해 FileVault를 켭니다.</li>
<li>Ubuntu에서는 암호화된 홈 디렉터리 기능을 사용합니다. 기타 Linux 배포에서도 동등한 기능을 제공합니다.</li>
</ul>

</div>

이제 Azure 웹 사이트와 Azure 가상 컴퓨터를 만들고 관리할 준비가 되었습니다.

## <span id="WebSites"></span></a>Azure 웹 사이트를 만들고 관리하는 방법

### 웹 사이트 만들기

Azure 웹 사이트를 만들려면 먼저 `MySite`라는 빈 디렉터리를 만들고 해당 디렉터리로 이동합니다.

다음 명령을 실행합니다.

    azure site create MySite --git

이 명령의 출력에는 새로 만든 웹 사이트의 기본 URL이 포함됩니다. `--git` 옵션을 통해 로컬 응용 프로그램 디렉터리와 웹 사이트의 데이터 센터 둘 다에 git 리포지토리를 만들면 git를 사용하여 웹 사이트에 게시할 수 있습니다. 로컬 폴더가 이미 git 리포지토리인 경우 이 명령은 기존 리포지토리에 새 원격을 추가하며 웹 사이트 데이터 센터의 리포지토리를 가리킵니다.

다음 옵션 중 하나와 함께 `azure site create` 명령을 실행할 수 있습니다.

-   `--location [location name]`. 이 옵션을 사용하면 웹 사이트가 만들어지는 데이터 센터의 위치를 지정할 수 있습니다(예: "미국 서부"). 이 옵션을 생략하면 위치를 선택하라는 메시지가 표시됩니다.
-   `--hostname [custom host name]`. 이 옵션을 사용하면 웹 사이트에 대한 사용자 지정 호스트 이름을 지정할 수 있습니다.

웹 사이트 디렉터리에 콘텐츠를 추가할 수 있습니다. 일반적인 git 흐름(`git add`, `git commit`)을 사용하여 콘텐츠를 커밋합니다. 다음 git 명령을 사용하여 웹 사이트 콘텐츠를 Azure에 밀어 넣습니다.

    git push azure master

### GitHub에서 게시 설정

GitHub 리포지토리에서 연속 게시를 설정하려면 사이트를 만들 때 `--GitHub` 옵션을 사용합니다.

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

GitHub 리포지토리의 로컬 클론이 있거나 GitHub 리포지토리에 대한 단일 원격 참조를 포함한 리포지토리가 있는 경우 이 명령은 GitHub 리포지토리의 코드를 사이트에 자동으로 게시합니다. 이후로 계속해서 GitHub 리포지토리에 적용된 모든 변경 내용이 사이트에 자동으로 게시됩니다.

GitHub에서 게시를 설정할 때 사용되는 기본 분기는 마스터 분기입니다. 다른 분기를 지정하려면 로컬 리포지토리에서 다음 명령을 실행합니다.

    azure site repository <branch name>

### 앱 설정 구성

앱 설정은 런타임에 응용 프로그램에서 사용할 수 있는 키-값 쌍입니다. Azure 웹 사이트에 대해 설정된 경우 앱 설정 값은 사이트의 Web.config 파일에 정의된 것과 동일한 키로 설정을 재정의합니다. Node.js 및 PHP 응용 프로그램의 경우 앱 설정을 환경 변수로 사용할 수 있습니다. 다음 예제에서는 키-값 쌍을 설정하는 방법을 보여 줍니다.

    azure site config add <key>=<value> 

모든 키/값 쌍의 목록을 보려면 다음을 사용합니다.

    azure site config list 

또는 키를 알고 있으며 값을 보려는 경우 다음을 사용할 수 있습니다.

    azure site config get <key> 

기존 키의 값을 변경하려면 먼저 기존 키를 지운 후 다시 추가해야 합니다. clear 명령은 다음과 같습니다.

    azure site config clear <key> 

### 사이트 나열 및 표시

웹 사이트를 나열하려면 다음 명령을 사용합니다.

    azure site list

사이트에 대한 자세한 정보를 가져오려면 `site show` 명령을 사용합니다. 다음 예제에서는 `MySite`에 대한 세부 정보를 보여 줍니다.

    azure site show MySite

### 사이트 중지, 시작 또는 다시 시작

사이트를 중지, 시작 또는 다시 시작하려면 `site stop`, `site start` 또는 `site restart` 명령을 사용합니다.

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### 사이트 삭제

마지막으로 `site delete` 명령을 사용하여 사이트를 삭제할 수 있습니다.

    azure site delete MySite

`site create`를 실행한 폴더 내부에서 위 명령을 실행하는 경우 사이트 이름 `MySite`를 마지막 매개 변수로 지정할 필요가 없습니다.

또한 `site` 명령에 대한 전체 목록을 보려면 `-help` 옵션을 사용합니다.

    azure site -help 

## <span id="VMs"></span></a>Azure 가상 컴퓨터를 만들고 관리하는 방법

Azure 가상 컴퓨터는 사용자가 제공하거나 이미지 갤러리에서 사용 가능한 가상 컴퓨터 이미지(.vhd 파일)에서 만들어집니다. 사용 가능한 이미지를 보려면 `vm image list` 명령을 사용합니다.

    azure vm image list

사용 가능한 이미지 중 하나에서 가상 컴퓨터를 프로비전 및 시작하려면 `vm create` 명령을 사용합니다. 다음 예제에서는 이미지 갤러리(CentOS 6.2)의 이미지에서 Linux 가상 컴퓨터(`myVM`)를 만드는 방법을 보여 줍니다. 가상 컴퓨터의 루트 사용자 이름과 암호는 각각 `myusername`, `Mypassw0rd`입니다. `--location` 매개 변수는 가상 컴퓨터가 만들어지는 데이터 센터를 지정합니다. `--location` 매개 변수를 생략하면 위치를 선택하라는 메시지가 표시됩니다.

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-ko-kr-30GB.vhd myusername --location "West US"

새로 만든 가상 컴퓨터에 대한 원격 연결을 사용하도록 설정하려면 `--ssh` 플래그(Linux) 또는 `--rdp` 플래그(Windows)를 `vm create`에 전달하는 것이 좋습니다.

사용자 지정 이미지에서 가상 컴퓨터를 프로비전하려는 경우 `vm image create` 명령을 사용하여 .vhd 파일에서 이미지를 만든 후 `vm create` 명령을 사용하여 가상 컴퓨터를 프로비전할 수 있습니다. 다음 예제에서는 로컬 .vhd 파일에서 Linux 이미지(`myImage`)를 만드는 방법을 보여 줍니다. `--location` 매개 변수는 이미지가 저장되는 데이터를 지정합니다.

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

로컬 .vhd에서 이미지를 만드는 대신 Azure Blob 저장소에 저장된 .vhd에서 이미지를 만들 수 있습니다. 이렇게 하려면 `blob-url` 매개 변수를 사용합니다.

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

이미지를 만든 후 `vm create`를 사용하여 이미지에서 가상 컴퓨터를 프로비전할 수 있습니다. 아래 명령은 위에서 만든 이미지(`myImage`)를 통해 `myVM`이라는 가상 컴퓨터를 만듭니다.

    azure vm create myVM myImage myusername --location "West US"

가상 컴퓨터를 프로비전한 후 가상 컴퓨터에 대한 원격 액세스를 허용하기 위해 끝점을 만들 수도 있습니다. 다음 예제에서는 `vm create endpoint` 명령을 사용하여 `myVM`에 외부 포트 22와 로컬 포트 22를 엽니다.

    azure vm endpoint create myVM 22 22

가상 컴퓨터에 대한 자세한 정보(IP 주소, DNS 이름 및 끝점 정보 포함)를 가져오려면 `vm show` 명령을 사용합니다.

    azure vm show myVM

가상 컴퓨터를 종료, 시작 또는 다시 시작하려면 다음 명령 중 하나를 사용합니다.

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

마지막으로, VM을 삭제하려면 `vm delete` 명령을 사용합니다.

    azure vm delete myVM

가상 컴퓨터를 만들고 관리하기 위한 명령의 전체 목록을 보려면 `-h` 옵션을 사용합니다.

    azure vm -h

<!-- LINKS -->
  [Mac 및 Linux용 Azure 명령줄 도구 설명서]: http://go.microsoft.com/fwlink/?LinkId=252246
  [Mac 및 Linux용 Azure 명령줄 도구 정의]: #Overview
  [Mac 및 Linux에서 Azure 명령줄 도구를 설치하는 방법]: #Download
  [Azure 계정을 만드는 방법]: #CreateAccount
  [게시 설정을 다운로드 및 가져오는 방법]: #Account
  [Azure 웹 사이트를 만들고 관리하는 방법]: #WebSites
  [Azure 가상 컴퓨터를 만들고 관리하는 방법]: #VMs
  [Azure SDK 설치 관리자]: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [패키지 관리자를 통해 Node.js 설치]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Azure 명령줄 도구]: http://go.microsoft.com/fwlink/?LinkID=275464
  []: http://www.windowsazure.com
  
<!-- IMAGES -->
  [Azure 웹 사이트]: ./media/crossplat-cmd-tools/freetrial.png
