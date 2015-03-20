<properties 
	pageTitle="Azure 플랫폼 간 명령줄 인터페이스" 
	description="Azure 서비스를 관리할 Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성" 
	editor="tysonn" 
	manager="timlt" 
	documentationCenter="" 
	authors="squillace" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/16/2015" 
	ms.author="rasquill"/>

# Azure 플랫폼간 명령줄 인터페이스 설치 및 구성

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Cross-Platform CLI" class="current">플랫폼간 CLI</a></div>

Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. xplat-cli는 웹 사이트, 가상 컴퓨터, 모바일 서비스, SQL 데이터베이스 및 Azure 플랫폼에서 제공하는 기타 서비스를 관리하는 기능과 같이 Azure 관리 포털에서 제공하는 기능과 동일한 기능을 대부분 제공합니다.

Xplat-cli는 JavaScript로 작성 됩니다 및 Node.js 필요 합니다. Azure SDK for Node.js를 사용 하 여 Apache 2.0 라이선스는 릴리스 및 구현 됩니다. 프로젝트 리포지토리는 [https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/WindowsAzure/azure-sdk-tools-xplat).

이 문서에서는 설치 하 고 Azure 플랫폼간 명령줄 인터페이스를 구성 하는 방법으로 사용 하 여 Azure 플랫폼을 사용 하는 기본 작업을 수행 하는 방법을 설명 합니다.

##문서 내용

* [Azure 플랫폼간 명령줄 인터페이스를 설치 하는 방법](#install)
* [Azure 구독에 연결 하는 방법](#configure)
* [Azure 플랫폼간 명령줄 인터페이스를 사용 하는 방법(#use)
* [Azure 플랫폼간 명령줄 인터페이스를 스크립팅 하는 방법(#script)
* [추가 리소스(#additional-resources)

## <a id="install">Azure 플랫폼간 명령줄 인터페이스를 설치 하는 방법</a>

두 가지 방법으로 xplat-cli;를 설치 하려면 Windows 및 OS X 용 설치 관리자 패키지를 사용 하 여 Node.js가 시스템에 설치 된 경우 또는 **npm** 명령 합니다. Xplat-cli를 설치한 후에 사용할 수는 **azure** 명령을 명령줄 인터페이스 (Bash, 터미널, 명령 프롬프트) xplat-cli 명령은 액세스할 수 있습니다.

###설치 관리자 사용

다음 설치 관리자 패키지를 사용할 수 있습니다.

* [Windows installer][windows 설치 관리자]

* [OS X 설치 관리자][mac installer]

###Npm을 사용 하 여

Node.js가 시스템에 이미 설치 하는 경우 xplat-cli를 설치 하려면 다음 명령을 사용 합니다.

	npm install azure-cli -g

>[AZURE.NOTE] 사용 하도록 할 수도 `sudo` 는 __npm__ 명령을 성공적으로 수행 합니다.

Node.js를 설치 하지 않은 경우 먼저 설치 해야 야 합니다. Linux 시스템의 경우 Node.js가 설치 되어 있어야 하 고 사용 하거나 **npm** 를 아래 설명된대로 xplat-cli를 설치 하거나 소스에서 빌드해야 합니다. 소스에서 제공 됩니다. [http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409). 소스에서 빌드하는 방법에 대한 자세한 내용은 보관 파일에 포함된 INSTALL 파일을 참조하십시오.

####사용 하는 분포에서 node.js 및 npm 설치 [dpkg](http://en.wikipedia.org/wiki/Dpkg) 패키지 관리 
이러한 배포판의 가장 일반적인 중 하나를 사용 하는 [고급 패키징 도구 (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 또는 기타 도구에 따라는 `.deb` 패키지 형식입니다. 예로 Debian, Ubuntu 있지만 많은 키워드가 있습니다. 

대부분의 이러한 배포판의 더 최근 설치 해야할 **nodejs 레거시** 가져오기 위해는 제대로 configued **npm** azure cli를 설치 하는 도구입니다. 다음 코드를 설치 하는 명령을 보여줍니다 **npm** Ubuntu 14.04에 올바르게 합니다.
	
	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

일부 Ubuntu 12.04와 같은 이전 배포를 설치 하는 node.js의 현재 바이너리 배포가 필요 합니다. 다음 코드를 설치 하 고 사용 하 여 수행 하는 방법을 보여줍니다 **curl**. 

>[AZURE.NOTE] 여기에 명령의 찾을 Joyent 설치 지침에서 가져온 [여기](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). 그러나 사용 하는 경우 **sudo** 항상 주의 해야 설치 하는 하 고 정확 하 게 어떤 두어야를 통해이 실행 하기 전에 수행 하의 유효성을 검사 하는 스크립트를 확인 하려면 파이프 대상으로 **sudo**.
	
	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

####사용 하는 분포에서 node.js 및 npm 설치 [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager) 패키지 관리

RPM 기반 배포판의 node.js를 설치 EPEL 리포지토리를 사용 하도록 설정 해야 합니다. 다음 코드에서는 CentOS 7에서 설치를 위한 모범 사례를 보여줍니다. (처음에는 줄 아래에서 '-' (하이픈 문자)는 중요 한!)

	su -     
	yum update [enter]
	yum upgrade -y [enter] 
	yum install epel-release [enter]
	yum install nodejs [enter] 
	yum install npm [enter] 
	npm install -g azure-cli  [enter]


Xplat-cli를 설치한 후에 사용할 수는 **azure** 명령을 명령줄 인터페이스 (Bash, 터미널, 명령 프롬프트) xplat-cli 명령은 액세스할 수 있습니다. 설치가 끝나면 다음과 유사하게 표시됩니다.

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

> [AZURE.NOTE] Node.js 및 npm 수에 설치 되어 Windows에서 <a href="http://nodejs.org/">http://nodejs.org/</a>.

## <a id="configure">Azure 구독에 연결 하는 방법</a>

xplat-cli에서 제공하는 일부 명령은 Azure 구독 없이도 작동하지만 대부분의 명령에 구독이 필요합니다. 구독과 함께 작동하도록 xplat-cli를 구성하기 위해 다음 중 하나를 수행할 수 있습니다.

* 다운로드 하 여 게시 설정 파일을 사용 합니다.

또는

* 조직 계정을 사용하여 Azure에 로그인합니다. 로그인할 때 자격 증명을 인증하기 위해 Azure Active Directory가 사용됩니다.

를 사용자의 요구에 적합 한 인증 방법을 선택할 수 있도록 하려면 다음을 고려 합니다.

*  로그인 방법은 구독에 대 한 액세스 관리를 쉽게 만들 수 있지만 자격 증명 시간 초과 하 고 다시 로그인 해야 자동화에 방해가 될 수 있습니다.

	> [AZURE.NOTE] 로그인 방법이 조직 계정 에서만 작동합니다.  조직 계정에는 조직에서 관리 되 고 조직 Azure Active Directory 테 넌 트에 정의 하는 사용자입니다. 현재 조직 계정이 없고 Microsoft 계정을 사용하여 Azure 구독에 로그인하는 경우 다음 단계에 따라 쉽게 만들 수 있습니다.
	> 
	> 1. 로그인 하는 [Azure 관리 포털][포털]를 클릭 하 고 **Active Directory**.
	> 
	> 2. 디렉터리가 없는 경우 선택 **디렉터리 만들기** 요청 된 정보를 제공 합니다.
	> 
	> 3. 디렉터리를 선택하고 새 사용자를 추가합니다. 새 사용자는 조직 계정입니다.
	> 
	>     사용자를 만드는 동안 사용자의 메일 주소와 임시 암호가 제공됩니다. 이 정보는 다른 단계에서 사용되므로 저장해 두십시오.
	> 
	> 4. 관리 포털에서 선택 **설정을** 선택한 다음 **관리자**. 선택 **추가**, 새 사용자를 공동 관리자로 추가 합니다. 그러면 조직 계정이 Azure 구독을 관리할 수 있습니다.
	> 
	> 5. 마지막으로, Azure 포털에서 로그아웃한 후 새 조직 계정을 사용하여 다시 로그인합니다. 이 계정으로 처음 로그인하는 경우 암호를 변경하라는 메시지가 표시됩니다.
	>
	>조직 계정 Microsoft Azure에 대 한 자세한 내용은 참조 [조직으로 Microsoft Azure에 등록][signuporg].

*  게시 설정 파일 방법은 구독 및 인증서가 유효한 기간 동안 관리 작업을 수행할 수 있게 해 주는 인증서를 설치합니다. 이 메서드를 사용 하면 장기 실행 작업에 대 한 자동화를 사용 하기 쉽습니다. 다운로드 하 고 정보를 가져온 후에 다시 제공할 필요가 없습니다. 그러나 이 방법을 사용할 경우 인증서에 대한 액세스 권한이 있는 모든 사용자가 구독을 관리할 수 있으므로 구독에 대한 액세스를 관리하는 것이 어렵습니다.

인증 및 구독 관리에 대 한 자세한 내용은 참조 ["란 계정 기반 인증 및 인증서 기반 인증의 차이점"][authandsub].

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 참조 [Azure 무료 평가판][무료 평가판].

###로그인 방법 사용

조직 계정을 사용 하 여 로그인 하려면 다음 명령을 사용 합니다.

	azure login -u username -p password

대화형으로 (예를들어 경우 암호를 화면에 마스크)을 입력 하 여 로그인 하려는 경우에 다음 명령을 사용 합니다.

	azure login

자격 증명을 묻는 메시지가 및 사용자 암호 문자 마스크 됩니다.

> [AZURE.NOTE] 처음으로 이러한 자격 증명을 사용 하 여 로그인 한 경우 인증 토큰을 캐시 하려면 확인 하 라는 프롬프트를 받게 됩니다. 이 프롬프트는 이전에 사용 되는 경우에 발생 합니다는 `azure logout` 명령 아래에서 설명 합니다. 사용 하 여 자동화 된 시나리오에 대 한이 프롬프트를 무시 하는 '-q' 매개 변수는 `azure login` 명령 합니다.
>
> Azure 구독에 액세스 하는 것에 대 한 정보에 저장 됩니다 조직 계정에 인증할 때는 `.azure` 디렉터리에 있는 프로그램 `user` 디렉터리입니다. 프로그램 `user` 디렉터리; 운영 체제에 의해 보호 됩니다 그러나 것이 좋습니다 암호화 하려면 추가 단계를 수행 하 여 `user` 디렉터리입니다. 따라서 다음과 같은 방법으로 수행할 수 있습니다.

로그 아웃, 다음 명령을 사용 합니다.

	azure logout -u <username>

> [AZURE.NOTE] 계정에 연결 된 구독 에서만 Active Directory와 인증을 하는 경우 로그 아웃 로컬 프로필에서 구독 정보를 삭제 합니다. 그러나 구독에 대해 게시 설정 파일도 가져온 경우에는 로그아웃해도 Active Directory 관련 정보만 로컬 프로필에서 삭제됩니다.

> [AZURE.NOTE] 계정을 사용하여 인증하는 경우 다음 명령이 제대로 작동하지 않습니다.
> 
> * `azure vm`
> * `azure network`
> * `azure mobile`
> 
> 이러한 명령을 사용 해야하는 경우 게시 설정 파일을 사용 하 여 다음 섹션에서 설명한 대로 Azure에 인증 합니다.

### 게시 설정 파일 방법 사용

사용자 계정에 대 한 게시 설정을 다운로드 하려면 다음 명령을 사용 합니다.

	azure account download

기본 브라우저가 열리고 Azure 관리 포털에 로그인하라는 메시지가 표시됩니다. 로그인 한 `.publishsettings` 파일이 다운로드 됩니다. 이 파일을 저장 된 위치 메모해 둡니다.

> [AZURE.NOTE] 계정이 여러 개의 Azure Active Directory 테넌트와 연결된 경우 게시 설정 파일을 다운로드하려는 Active Directory를 선택하라는 메시지가 표시될 수도 있습니다.
> 
> 다운로드 페이지를 사용하거나 Azure 관리 포털을 방문하여 선택한 후에는 선택한 Active Directory가 포털 및 다운로드 페이지에서 기본값으로 사용됩니다. 기본값이 설정되고 나면 다운로드 페이지의 맨 위에 '__선택 페이지로 돌아가려면 여기를 클릭하세요.__' 텍스트가 표시됩니다. 제공된 링크를 사용하여 선택 페이지로 돌아갑니다.

다음으로 가져오기는 `.publishsettings` 다음 명령을 실행 하 여 파일 대체 '[.publishsettings 파일에 대 한 경로]'에 대 한 경로와 프로그램 `.publishsettings` 파일:

	azure account import [path to .publishsettings file]

> [AZURE.NOTE] 가져올 때 게시 설정, Azure 구독에 액세스 하기 위한 정보에 저장 됩니다는 `.azure` 디렉터리에 있는 프로그램 `user` 디렉터리입니다. 프로그램 `user` 디렉터리; 운영 체제에 의해 보호 됩니다 그러나 것이 좋습니다 암호화 하려면 추가 단계를 수행 하 여 `user` 디렉터리입니다. 따라서 다음과 같은 방법으로 수행할 수 있습니다.
>
> * Windows에서는 디렉터리 속성을 수정하거나 BitLocker를 사용합니다.
> * Mac에서는 디렉터리에 대해 FileVault를 켭니다.
> * Ubuntu에서는 암호화 된 홈 디렉터리 기능을 사용 합니다. 기타 Linux 배포 에서도 동등한 기능을 제공 합니다.

가져온 후 프로그램 게시 설정을 삭제 해야 합니다.는 `.publishsettings` 파일을 더이상 명령줄 도구에 필요 하 고 구독에 액세스 하는데 사용할 수 있는 것 처럼 보안 위험이 없습니다.

###여러 구독

Azure 구독이 여러 개 있는 경우 로그인하면 자격 증명과 연결된 모든 구독에 액세스할 수 있습니다. 게시 설정 파일을 사용 하는 경우는 `.publishsettings` 파일에 대 한 모든 구독에 대 한 정보가 포함 됩니다. 위의 방법으로 하나의 구독만 xplat-cli 작업을 수행할 때 사용 되는 기본 구독으로 선택 됩니다. 어떤 것 뿐만 기본값이 있지만 사용 하 여 구독을 볼 수 있습니다는 `azure account list` 명령 합니다. 이 명령은 다음과 유사한 정보를 반환합니다.

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

위의 목록에는 **현재** 열은 현재 기본 구독으로 Azure-sub-1을 나타냅니다. 기본 구독을 변경 하려면 사용 된 `azure account set` 명령을 실행 하 고 기본 하고자 하는 구독을 지정 합니다. 예를 들면 다음과 같습니다.

	azure account set Azure-sub-2

이렇게 하면 기본 구독 Azure-sub-2로 변경 됩니다. 

> [AZURE.NOTE] 기본 구독 변경은 즉시 적용되며 전체적으로 변경됩니다. 동일한 명령줄 인스턴스에서 실행되었든 또는 다른 인스턴스에서 실행되었든 관계없이 새 xplat-commands는 새 기본 구독을 사용합니다.

xplat-cli와 함께 기본값이 아닌 구독을 사용하지만 현재 기본값을 변경하지 않으려는 경우 `--subscription` 옵션을 사용하고 작업에 사용할 구독의 이름을 제공할 수 있습니다.

<h2><a id="use"></a>Azure 플랫폼간 명령줄 인터페이스를 사용 하는 방법</h2>

Xplat-cli를 사용 하 여 액세스 하는 `azure` 명령 합니다. 사용 가능한 명령 목록을 보려면,는 `azure` 매개 변수 없이 명령 합니다. 다음과 유사한 도움말 정보가 표시됩니다.

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Windows Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.0
	help:
	help:    Display help for a given command
	help:      help [options] [command]
	help:
	help:    Opens the portal in a browser
	help:      portal [options]
	help:
	help:    Commands:
	help:      account        Commands to manage your account information and publish settings
	help:      config         Commands to manage your local settings
	help:      hdinsight      Commands to manage your HDInsight accounts
	help:      mobile         Commands to manage your Mobile Services
	help:      network        Commands to manage your Networks
	help:      sb             Commands to manage your Service Bus configuration
	help:      service        Commands to manage your Cloud Services
	help:      site           Commands to manage your Web Sites
	help:      sql            Commands to manage your SQL Server accounts
	help:      storage        Commands to manage your Storage objects
	help:      vm             Commands to manage your Virtual Machines
	help:
	help:    Options:
	help:      -h, --help     output usage information
	help:      -v, --version  output the application version

위에 나열된 최상위 명령에는 Azure의 특정 영역 작업을 위한 명령이 포함되어 있습니다. 예는 `azure account` 명령에는 같은 Azure 구독과 관련 된 명령이 포함 된는 `download` 및 `import` 이전에 사용 되는 설정입니다.

대부분의 명령으로 서식이 지정 되어 ' azure < 명령 >< 작업 > [매개 변수]' 서비스 또는 계정 구성 등과 같은 개체에 대해 연산을 수행 합니다. 다른 명령은 하위 명령을 제공 하며 형식을 따릅니다 ' azure < 명령 >< 하위 명령 >< 작업 > [매개 변수]'. 다음은 계정 구성에 사용되는 예제 명령입니다.

* 가져온 구독을 보려면 다음을 사용 합니다.

		azure account list

* 구독을 가져온 경우 기본값으로 하나를 설정 하려면 다음을 사용 합니다.

		azure account set <subscription>

`--help` 또는 `-h` 매개 변수를 사용하여 특정 명령에 대한 도움말을 볼 수 있습니다. 또는 ' azure 도움말 [명령][옵션]' 동일한 정보를 반환할 형식을 사용할 수도 있습니다. 예를 들어 다음 명령은 모두 동일한 정보를 반환합니다.

	azure account set --help

	azure account set -h

	azure help account set

사용 하 여 도움말을 참조 하십시오 명령에 필요한 매개 변수에 대 한 확실 하지 않은, '-도움말 ', '-h' 또는 ' azure 도움말 [명령]'.

###구성 모드 설정

xplat-cli를 사용하면 데이터베이스 서버, 데이터베이스, 웹 사이트 등의 사용자 관리 엔터티인 개별 _리소스_에 대해 관리 작업을 수행할 수 있습니다. Xplat-cli에 대 한 작업의 기본 모드 이며 라고 **Azure 서비스 관리**. 그러나 여러 리소스로 구성된 복잡한 솔루션이 있는 경우 전체 솔루션을 하나의 단위로 관리하는 것이 유용합니다.

리소스 그룹을 단일 논리 단위 또는 group_ (_r)로 관리를 지원 하기 위해 미리 보기를 도입 되었습니다는 **리소스 관리자** Azure 리소스를 관리 하는 새로운 방법으로 합니다. 

>[AZURE.NOTE] 리소스 관리자는 현재 Preview 상태이며, Azure 서비스 관리와 동일한 수준의 관리 기능을 제공하지 않습니다.

새 리소스 관리자를 지원 하기 위해 xplat-cli 이러한 관리 간을 전환할 수 있습니다 'modes' 를 사용 하 여 `azure config mode` 명령 합니다.

xplat-cli는 기본적으로 Azure 서비스 관리 모드로 설정되어 있습니다. 리소스 관리자 모드로 전환하려면 다음 명령을 사용합니다.

	azure config mode arm

다시 Azure 서비스 관리 모드를 변경 하려면 다음 명령을 사용 합니다.

	azure config mode asm 

>[AZURE.NOTE] 리소스 관리자 모드와 Azure 서비스 관리 모드는 함께 사용할 수 없습니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

Xplat-cli를 사용 하 여 리소스 관리자와 작업에 대 한 자세한 내용은 참조 [리소스 관리자와 Azure 플랫폼간 명령줄 인터페이스를 사용 하 여][xplatarm].

###Azure 서비스 관리 모드에서 서비스 작업

xplat-cli를 사용하여 Azure 서비스를 쉽게 관리할 수 있습니다. 이 예제에서는 xplat-cli를 사용하여 Azure 웹 사이트를 관리하는 방법을 알아보겠습니다.

1. 다음 명령을 사용하여 새 Azure 웹 사이트를 만듭니다. 대체 **mywebsite** 고유한 이름을 사용 합니다.

		azure site create mywebsite

	웹 사이트를 만들 지역을 지정하라는 메시지가 표시됩니다. 지리적으로 근처에 있는 지역을 선택합니다. 이 명령이 완료 되 면 웹사이트 http://mywebsite.azurewebsites.net에서 사용할 수 있습니다 (대체 **mywebsite** 지정한 이름을 가진.)

	> [AZURE.NOTE] 지정할 수는 경우 Git를 사용 하면 소스 제어 프로젝트에 대 한는 '-git' 매개 변수를이 웹사이트에 대 한 Azure에서 Git 리포지토리를 만듭니다. 또한 있는 명령이 이미 존재 하지 않는 경우 실행 된 디렉터리에 Git 리포지토리가 초기화 됩니다. 사용 하 여 Azure 웹사이트에 배포를 푸시 사용할 수 있는 __azure__ 라는 Git 원격 만들어집니다는 `git push azure master` 명령 합니다.

	> [AZURE.NOTE] 오류가 발생 하는 경우는 'site' azure 없는 명령, xplat-cli가 리소스 그룹 모드에 있을 가능성이 큽니다. 리소스 모드로 다시 변경 하려면 사용 된 `azure config mode asm` 명령 합니다.

2. 구독에 대 한 웹사이트 목록에 다음 명령을 사용 합니다.

		azure site list

	목록에는 이전 단계에서 만든 사이트가 포함 되어야 합니다.

2. 다음 명령을 사용하여 웹 사이트를 중지합니다. 대체 **mywebsite** 을 사이트 이름입니다.

		azure site stop mywebsite

	명령이 완료 된 후 사이트의이 중지 되었음을 확인 하려면 브라우저를 새로 고칠 수 있습니다.

3. 다음 명령을 사용하여 웹 사이트를 시작합니다. 대체 **mywebsite** 을 사이트 이름입니다.

		azure site start mywebsite

	명령이 완료 된 후 사이트 시작 되었는지 확인 하려면 브라우저를 새로 고칠 수 있습니다.

4. 다음 명령을 사용하여 웹 사이트를 삭제합니다. 대체 **mywebsite** 을 사이트 이름입니다.

		azure site delete mywebsite

	사용 하 여 명령이 완료 된 후의 `azure site list` 명령을 사용 하는 웹사이트 더이상 존재 하지 않는지 확인 합니다.

<h2><a id="script"></a>Azure 플랫폼간 명령줄 인터페이스를 스크립팅 하는 방법</h2>

xplat-cli를 사용하여 수동으로 명령을 실행할 수 있지만 명령줄 인터프리터 및 시스템에서 사용 가능한 기타 명령줄 유틸리티의 기능을 활용하여 복잡한 자동화 워크플로를 만들 수도 있습니다. 예를 들어 다음 명령은 실행 중인 모든 Azure 웹 사이트를 중지합니다.

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

이 예제에는 웹사이트의 목록이 파이프는 `grep` 문자열에 대 한 각 줄을 검사 하는 명령 'Running'. 일치 하는 모든 줄에 파이프 됩니다는 `awk` 명령, 되는 호출 `azure site stop` 사이트 이름으로 (실행 중인 사이트 이름)에 전달 하 고 두번째 열을 사용 하 여 중지 합니다.

이 예제는 명령을 함께 연결하는 방법을 보여 주지만 명령줄 인터프리터에서 제공하는 스크립팅 기능을 사용하여 보다 정교한 스크립트를 만들 수도 있습니다. 명령줄 인터프리터 마다 서로 다른 스크립팅 기능과 구문이 있는 합니다. Bash는 Linux, OS X 등의 UNIX 기반 시스템에 가장 널리 사용되는 명령줄 인터프리터입니다.

Bash의 스크립팅에 대 한 자세한 내용은 참조는 [고급 Bash 스크립팅 가이드][고급 bash].

OS X 또는 Linux 기반 시스템의 스크립팅에 대 한 일반적인 정보를 참조 하십시오. [셸 스크립트][스크립트].

배치 파일을 사용 하 여 Windows 기반 시스템의 스크립팅에 대 한 자세한 내용은 [명령줄 참조 ㄱ-ㅎ][일괄 처리].

### 결과 이해

스크립트를 만들 때 명령 출력을 캡처하여 다른 명령으로 전달하거나 출력에 대해 특정 값 확인 등의 작업을 수행해야 하는 경우가 있습니다. Xplat-cli는 STDOUT 및 STDERR로 출력을 생성합니다. 각 줄은 문자열을 접두사로 `info:` for informational status messages, or `data:` for data returned about a service; however, you can instruct the xplat-cli to return more verbose information by using the `--verbose` 또는 '-v' 매개 변수입니다. 이 문자열이 앞에 추가 정보를 반환 합니다. `verbose:`.

For example, the following output is returned from the `azure site list`명령입니다.

	info:    Executing command site list
	+ Enumerating sites
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

'--verbose' 또는 '-v' 매개 변수를 지정하면 다음과 유사한 정보가 반환됩니다.

	info:    Executing command site list
	verbose: Subscription ####################################
	verbose: Enumerating sites
	verbose: [
	verbose:     {
	verbose:         ComputeMode: 'Shared',
	verbose:         HostNameSslStates: {
	verbose:             HostNameSslState: [
	verbose:                 {
	verbose:                     IpBasedSslResult: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     SslState: 'Disabled',
	verbose:                     ToUpdateIpBasedSsl: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     VirtualIP: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Thumbprint: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     ToUpdate: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Name: 'myawesomesite.azurewebsites.net'
	verbose:                 },
	...
	verbose:     }
	verbose: ]
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

이때는 `verbose:` information appears to be JSON formatted data. You can use the `--json` 매개 변수를 고유 하 게와 같은 JSON을 이해 하는 유틸리티를 사용 하는 경우 JSON 형식으로 정보를 반환 [jsawk](https://github.com/micha/jsawk) 또는 [jq](http://stedolan.github.io/jq/). 예를 들면 다음과 같습니다.

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

위 명령은 JSON으로 웹사이트의 목록을 검색 한 후 jsawk를 사용 하 여 사이트 이름을 검색 하 고 사용 하 여 xargs 사이트를 실행 하는를 통해 사이트 이름을 매개 변수로 전달 하는 각 사이트에 대 한 명령을 삭제 하는 마지막으로 합니다.

>[AZURE.NOTE] '-json' 상태 또는 데이터 정보를 생성을 차단 하는 매개 변수 (문자열 접두사로 `info:` and `data:`). For example, if the `--json` 매개 변수는 함께 사용 되는 `azure site create`,이 명령은 데이터를 이외의 반환 하지 않는 반환 됩니다. 출력이 없음 ' 정보:'.

###오류 작업

Xplat-cli 않습니다 오류 정보를 STDERR를 기록 하는 동안 오류에 대 한 추가 정보가 기록 될 수도 있습니다 하는 **azure.err** 에서 스크립트가 디렉터리에 파일을 실행 합니다. 이 파일에 정보가 로깅되는 경우 STDOUT에 다음이 기록됩니다.

	info:    Error information has been recorded to azure.err

###종료 상태

일부 xplat-cli 명령은 필수 매개 변수가 없을 경우 0이 아닌 종료 상태를 반환하지 않습니다. 대신, 사용자 입력 하 라는 메시지가 나타납니다. 예를 사용 하는 경우는 `azure site create` 사이트 이름이 없는 경우 새 웹사이트를 만들기 위한 명령 또는 '-위치 ' 이러한 값을 제공 하 라는 메시지가 표시 될 매개 변수를 지정 합니다.

종료 상태에 의존 하는 스크립트를 작성 하는 경우에 사용 하는 xplat-cli 명령은 사용자 입력에 대해 묻지 않음 확인 하십시오.

<h2><a id="additional-resources"></a>추가 리소스</h2>

* Xplat-cli를 대 한 자세한 내용은 소스 코드, 문제를 보고, 다운로드 또는 프로젝트에 기여를 방문는 [GitHub 리포지토리 Azure 플랫폼간 명령줄 인터페이스에 대 한](https://github.com/WindowsAzure/azure-sdk-tools-xplat).

* Xplat-cli 또는 Azure를 사용 하 여 문제를 발생 하는 경우 참조는 [Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Azure에 대 한 자세한 내용은 참조 [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure 웹사이트]: ../media/freetrial.png
[미리 보기 기능을 선택 합니다.]: ../media/antares-iaas-preview-02.png
[구독 선택]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[xplatarm]: /documentation/articles/xplat-cli-azure-resource-manager/
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/

<!--HONumber=47-->
