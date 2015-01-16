<properties urlDisplayName="Azure Cross-Platform Command-Line Interface" pageTitle="Azure 플랫폼 간 명령줄 인터페이스" title="Azure 플랫폼 간 명령줄 인터페이스" metaKeywords="Azure 플랫폼 간 명령줄 인터페이스, Azure 명령줄, azure 명령줄, azure cli" description="Azure 서비스를 관리할 Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성" metaCanonical="http://www.windowsazure.com/ko-kr/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="need to identify contact" documentationCenter="" solutions="" authors="carolz" services="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="carolz" />

#Azure 플랫폼 간 명령줄 인터페이스 설치 및 구성

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/ko-kr/manage/install-and-configure-cli/" title="Cross-Platform CLI" class="current">플랫폼 간 CLI</a></div>

Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. xplat-cli는 웹 사이트, 가상 컴퓨터, 모바일 서비스, SQL 데이터베이스 및 Azure 플랫폼에서 제공하는 기타 서비스를 관리하는 기능과 같이 Azure 관리 포털에서 제공하는 기능과 동일한 기능을 대부분 제공합니다.

xplat-cli는 JavaScript로 작성되었으며 Node.js가 필요합니다. Node.js에 Azure SDK를 사용하여 구현되었으며 Apache 2.0 라이선스로 릴리스되었습니다. 프로젝트 리포지토리는 [https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/WindowsAzure/azure-sdk-tools-xplat)에 있습니다.

이 문서에서는 Azure 플랫폼 간 명령줄 인터페이스를 설치 및 구성하는 방법과 Azure 플랫폼에서 기본 작업을 수행하는 데 사용하는 방법을 설명합니다.

##문서 내용

* [Azure 플랫폼 간 명령줄 인터페이스를 설치하는 방법](#install)
* [Azure 구독에 연결하는 방법](#configure)
* [Azure 플랫폼 간 명령줄 인터페이스를 사용하는 방법](#use)
* [Azure 플랫폼 간 명령줄 인터페이스를 스크립팅하는 방법](#script)
* [추가 리소스](#additional-resources)

<h2><a id="install"></a>Azure 플랫폼 간 명령줄 인터페이스를 설치하는 방법</h2>

xplat-cli를 설치하는 방법에는 두 가지가 있습니다. Windows 및 OS X용 설치 관리자 패키지를 사용하거나 시스템에 Node.js가 설치된 경우 **npm** 명령을 사용합니다.

Linux 시스템의 경우 Node.js가 설치되어 있어야 하며 **npm**을 사용하여 아래 설명된 대로 xplat-cli를 설치하거나 소스에서 빌드해야 합니다. 소스는 [http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409)에서 사용할 수 있습니다. 소스에서 빌드하는 방법에 대한 자세한 내용은 보관 파일에 포함된 INSTALL 파일을 참조하세요.

xplat-cli가 설치된 후 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트)에서 **azure** 명령을 사용하여 xplat-cli 명령에 액세스할 수 있습니다.

###설치 관리자 사용

다음과 같은 설치 관리자 패키지를 사용할 수 있습니다.

* [Windows Installer][windows-installer]

* [OS X 설치 관리자][mac-installer]

###npm 사용

Node.js가 시스템에 설치된 경우 다음 명령을 사용하여 xplat-cli를 설치합니다.

	npm install azure-cli -g

>[WACOM.NOTE] __npm__ 명령을 성공적으로 실행하기 위해 `sudo`를 사용해야 할 수 있습니다.

xplat-cli 및 필수 종속성이 설치됩니다. 설치가 끝나면 다음과 유사하게 표시됩니다.

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

> [WACOM.NOTE] Node.js는 <a href="http://nodejs.org/">http://nodejs.org/</a>에서 설치할 수 있습니다.

<h2><a id="Configure"></a>Azure 구독에 연결하는 방법</h2>

xplat-cli에서 제공하는 일부 명령은 Azure 구독 없이도 작동하지만 대부분의 명령에 구독이 필요합니다. 구독과 함께 작동하도록 xplat-cli를 구성하기 위해 다음 중 하나를 수행할 수 있습니다.

* 게시 설정 파일을 다운로드 및 사용합니다.

또는

* 조직 계정을 사용하여 Azure에 로그인합니다. 로그인할 때 자격 증명을 인증하기 위해 Azure Active Directory가 사용됩니다.

요구에 적합한 인증 방법을 선택하려면 다음을 고려합니다.

*  로그인 방법은 구독에 대한 액세스 관리가 용이하지만 자격 증명이 시간 초과되고 다시 로그인해야 할 수 있으므로 자동화에 방해가 될 수 있습니다.

	> [WACOM.NOTE] 로그인 방법은 조직 계정에서만 작동합니다.  조직 계정은 조직에서 관리되고 조직 Azure Active Directory 테넌트에 정의된 사용자입니다. 현재 조직 계정이 없고 Microsoft 계정을 사용하여 Azure 구독에 로그인하는 경우 다음 단계에 따라 쉽게 만들 수 있습니다.
	> 
	> 1. [Azure 관리 포털][에 ]로그인한 후 **Active Directory**를 클릭합니다.
	> 
	> 2. 디렉터리가 없는 경우 **디렉터리 만들기**를 선택하고 요청된 정보를 제공합니다.
	> 
	> 3. 디렉터리를 선택하고 새 사용자를 추가합니다. 새 사용자는 조직 계정입니다.
	> 
	>     사용자를 만드는 동안 사용자의 메일 주소와 임시 암호가 제공됩니다. 이 정보는 다른 단계에서 사용되므로 저장해 둡니다.
	> 
	> 4. 관리 포털에서 **설정**을 선택한 후 **관리자**를 선택합니다. **추가**를 선택하고 새 사용자를 공동 관리자로 추가합니다. 그러면 조직 계정이 Azure 구독을 관리할 수 있습니다.
	> 
	> 5. 마지막으로, Azure 포털에서 로그아웃한 후 새 조직 계정을 사용하여 다시 로그인합니다. 이 계정으로 처음 로그인하는 경우 암호를 변경하라는 메시지가 표시됩니다.
	>
	>Microsoft Azure의 조직 계정에 대한 자세한 내용은 [조직으로 Microsoft Azure에 등록][signuporg]을 참조하세요.

*  게시 설정 파일 방법은 구독 및 인증서가 유효한 기간 동안 관리 작업을 수행할 수 있게 해 주는 인증서를 설치합니다. 이 방법을 통해 장기 실행 작업에 자동화를 쉽게 사용할 수 있습니다. 정보를 다운로드하고 가져온 후에는 다시 제공할 필요가 없습니다. 그러나 이 방법을 사용할 경우 인증서에 대한 액세스 권한이 있는 모든 사용자가 구독을 관리할 수 있으므로 구독에 대한 액세스를 관리하는 것이 어렵습니다.

인증 및 구독 관리에 대한 자세한 내용은 ["계정 기반 인증 및 인증서 기반 인증 간의 차이점"][authandsub]을 참조하세요.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][free-trial]을 참조하세요.

###로그인 방법 사용

조직 계정을 사용하여 로그인하려면 다음 명령을 사용합니다.

	azure login [username] [password]

> [WACOM.NOTE] 이러한 자격 증명으로 처음 로그인하는 경우 인증 토큰을 캐시할지 확인하는 메시지가 표시됩니다. 이 메시지는 아래에 설명된 `azure logout` 명령을 이전에 사용한 적이 있는 경우에도 표시됩니다. 자동화 시나리오에서 이 메시지를 표시하지 않으려면 `azure login` 명령에 `-q` 매개 변수를 사용합니다.
>
> 조직 계정으로 인증하는 경우 Azure 구독에 액세스하기 위한 정보가 `user` 디렉터리에 있는 `.azure` 디렉터리에 저장됩니다. 'user' 디렉터리는 운영 체제에서 보호됩니다. 그러나 추가 단계를 수행하여 'user' 디렉터리를 암호화하는 것이 좋습니다. 다음과 같은 방법으로 이 작업을 수행할 수 있습니다.

로그아웃하려면 다음 명령을 사용합니다.

	azure logout [username]

> [WACOM.NOTE] 계정과 연결된 구독이 Active Directory에서만 인증된 경우 로그아웃하면 구독 정보가 로컬 프로필에서 삭제됩니다. 그러나 구독에 대해 게시 설정 파일도 가져온 경우에는 로그아웃해도 Active Directory 관련 정보만 로컬 프로필에서 삭제됩니다.

> [WACOM.NOTE] 계정을 사용하여 인증하는 경우 다음 명령이 제대로 작동하지 않습니다.
> 
> * `azure vm`
> * `azure network`
> * `azure mobile`
> 
> 이러한 명령을 사용해야 하는 경우 다음 섹션에 설명된 대로 게시 설정 파일을 사용하여 Azure에 인증합니다.

###게시 설정 파일 방법 사용

계정에 대한 게시 설정을 다운로드하려면 다음 명령을 사용합니다.

	azure account download

기본 브라우저가 열리고 Azure 관리 포털에 로그인하라는 메시지가 표시됩니다. 로그인하면 `.publishsettings` 파일이 다운로드됩니다. 이 파일이 저장된 위치를 기록해 둡니다.

> [WACOM.NOTE] 계정이 여러 개의 Azure Active Directory 테넌트와 연결된 경우 게시 설정 파일을 다운로드하려는 Active Directory를 선택하라는 메시지가 표시될 수도 있습니다.
> 
> 다운로드 페이지를 사용하거나 Azure 관리 포털을 방문하여 선택한 후에는 선택한 Active Directory가 포털 및 다운로드 페이지에서 기본값으로 사용됩니다. 기본값이 설정되고 나면 다운로드 페이지의 맨 위에 '__선택 페이지로 돌아가려면 여기를 클릭하세요.__' 텍스트가 표시됩니다. 제공된 링크를 사용하여 선택 페이지로 돌아갑니다.

`[path to .publishsettings file]`을 해당 `.publishsettings` 파일의 경로로 바꿔서 다음 명령을 실행하여 `.publishsettings` 파일을 가져옵니다.

	azure account import [path to .publishsettings file]

> [WACOM.NOTE] 게시 설정을 가져오는 경우 Azure 구독에 액세스하기 위한 정보가 `user` 디렉터리에 있는 `.azure` 디렉터리에 저장됩니다. 'user' 디렉터리는 운영 체제에서 보호됩니다. 그러나 추가 단계를 수행하여 'user' 디렉터리를 암호화하는 것이 좋습니다. 다음과 같은 방법으로 이 작업을 수행할 수 있습니다.
>
> * Windows에서는 디렉터리 속성을 수정하거나 BitLocker를 사용합니다.
> * Mac에서는 디렉터리에 대해 FileVault를 켭니다.
> * Ubuntu에서는 암호화된 홈 디렉터리 기능을 사용합니다. 기타 Linux 배포에서도 동등한 기능을 제공합니다.

게시 설정을 가져온 후 `.publishsettings` 파일을 삭제해야 합니다. 더 이상 명령줄 도구에 필요하지 않으며 구독에 액세스하는 데 사용될 수 있어서 보안상 위험하기 때문입니다.

###여러 구독

Azure 구독이 여러 개 있는 경우 로그인하면 자격 증명과 연결된 모든 구독에 액세스할 수 있습니다. 게시 설정 파일을 사용하는 경우 `.publishsettings` 파일에 모든 구독 정보가 포함됩니다. 두 방법에서 모두, 하나의 구독이 xplat-cli에서 작업을 수행할 때 사용되는 기본 구독으로 선택됩니다. 구독 및 기본적으로 사용되는 구독을 볼 수 있지만 `azure account list` 명령을 사용해야 합니다. 이 명령은 다음과 유사한 정보를 반환합니다.

 info:    Executing command account list
 data:    Name              Id                                    Current
 data:    ----------------  ------------------------------------  -------
 data:    Azure-sub-1       ####################################  true
 data:    Azure-sub-2       ####################################  false

위 목록에서 **Current** 열은 현재 기본 구독을 Azure-sub-1로 표시합니다. 기본 구독을 변경하려면 `azure account set` 명령을 사용하고 기본값으로 사용할 구독을 지정합니다. 예를 들면 다음과 같습니다.

	azure account set Azure-sub-2

기본 구독이 Azure-sub-2로 변경됩니다. 

> [WACOM.NOTE] 기본 구독 변경은 즉시 적용되며 전체적으로 변경됩니다. 동일한 명령줄 인스턴스에서 실행되었든 또는 다른 인스턴스에서 실행되었든 관계없이 새 xplat-commands는 새 기본 구독을 사용합니다.

xplat-cli와 함께 기본값이 아닌 구독을 사용하지만 현재 기본값을 변경하지 않으려는 경우 `--subscription` 옵션을 사용하고 작업에 사용할 구독의 이름을 제공할 수 있습니다.

<h2><a id="use"></a>Azure 플랫폼 간 명령줄 인터페이스를 사용하는 방법</h2>

xplat-cli는 `azure` 명령을 사용하여 액세스합니다. 사용 가능한 명령 목록을 보려면 매개 변수 없이 `azure` 명령을 사용합니다. 다음과 유사한 도움말 정보가 표시됩니다.

 info:             _    _____   _ ___ ___
 info:            /_\  |_  / | | | _ \ __|
 info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
 info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
 info:       (_______ _ _)         _ ______ _)_ _
 info:              (______________ _ )   (___ _ _)
	info:
 info:    Microsoft Azure: Microsoft's Cloud Platform
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

위에 나열된 최상위 명령에는 Azure의 특정 영역 작업을 위한 명령이 포함되어 있습니다. 예를 들어 `azure account` 명령에는 이전에 사용한 download` 및 `import` 설정과 같이 Azure 구독과 관련된 명령이 포함되어 있습니다.

대부분의 명령은 `azure <command> <operation> [parameters]` 형식을 사용하며 계정 구성 등의 서비스나 개체에 대해 작업을 수행합니다. 다른 명령은 하위 명령을 제공하며 `azure <command> <subcommand> <operation> [parameters]` 형식을 따릅니다. 다음은 계정 구성에 사용되는 예제 명령입니다.

* 가져온 구독을 보려면 다음을 사용합니다.

		azure account list

* 구독을 가져온 경우 다음을 사용하여 하나의 구독을 기본값으로 설정합니다.

		azure account set <subscription>

`--help` 또는 `-h` 매개 변수를 사용하여 특정 명령에 대한 도움말을 볼 수 있습니다. 또는 `azure help [command] [options]` 형식을 사용하여 동일한 정보를 반환할 수도 있습니다. 예를 들어 다음 명령은 모두 동일한 정보를 반환합니다.

	azure account set --help

	azure account set -h

	azure help account set

명령에 필요한 매개 변수가 확실하지 않은 경우 `--help`, `-h` 또는 `azure help [command]`를 사용하여 도움말을 참조하세요.

###구성 모드 설정

xplat-cli를 사용하면 데이터베이스 서버, 데이터베이스, 웹 사이트 등의 사용자 관리 엔터티인 개별 _리소스_에 대해 관리 작업을 수행할 수 있습니다. 이것이 xplat-cli에 대한 기본 작업 모드이며 **Azure 서비스 관리**라고 합니다. 그러나 여러 리소스로 구성된 복잡한 솔루션이 있는 경우 전체 솔루션을 하나의 단위로 관리하는 것이 유용합니다.

하나의 논리적 단위 또는 _리소스 그룹_으로 리소스 그룹 관리를 지원하기 위해 Azure 리소스를 관리하는 새로운 방법으로 **리소스 관리자** Preview가 도입되었습니다. 

>[WACOM.NOTE] 리소스 관리자는 현재 Preview 상태이며, Azure 서비스 관리와 동일한 수준의 관리 기능을 제공하지 않습니다.

새 리소스 관리자를 지원하기 위해 xplat-cli에서 `azure config mode` 명령을 사용하여 이러한 관리 '모드'를 전환할 수 있습니다.

xplat-cli는 기본적으로 Azure 서비스 관리 모드로 설정되어 있습니다. 리소스 관리자 모드로 전환하려면 다음 명령을 사용합니다.

	azure config mode arm

Azure 서비스 관리 모드로 다시 변경하려면 다음 명령을 사용합니다.

	azure config mode asm 

>[WACOM.NOTE] 리소스 관리자 모드와 Azure 서비스 관리 모드는 함께 사용할 수 없습니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

xplat-cli를 사용하여 리소스 관리자 작업을 수행하는 방법에 대한 자세한 내용은 [리소스 관리자와 함께 Azure 플랫폼 간 명령줄 인터페이스 사용][xplatarm]을 참조하세요.

###Azure 서비스 관리 모드에서 서비스 작업

xplat-cli를 사용하여 Azure 서비스를 쉽게 관리할 수 있습니다. 이 예제에서는 xplat-cli를 사용하여 Azure 웹 사이트를 관리하는 방법을 알아보겠습니다.

1. 다음 명령을 사용하여 새 Azure 웹 사이트를 만듭니다. **mywebsite**를 고유한 이름으로 바꿉니다.

		azure site create mywebsite

	웹 사이트를 만들 지역을 지정하라는 메시지가 표시됩니다. 지리적으로 근처에 있는 지역을 선택합니다. 이 명령이 완료되면 http://mywebsite.azurewebsites.net에서 웹 사이트를 사용할 수 있습니다. **mywebsite**를 지정한 이름으로 바꿉니다.

	> [WACOM.NOTE] 프로젝트 소스 제어에 Git를 사용하는 경우 `--git` 매개 변수를 지정하여 Azure에 이 웹 사이트에 대한 Git 리포지토리를 만들 수 있습니다. 아직 없는 경우 명령이 실행된 디렉터리에서도 Git 리포지토리가 초기화됩니다. 또한 `git push azure master` 명령을 통해 Azure 웹 사이트에 배포를 푸시하는 데 사용될 수 있는 __azure__라는 Git remote가 만들어집니다.

	> [WACOM.NOTE] 'site'는 azure 명령이 아니라는 오류가 표시되는 경우 xplat-cli가 리소스 그룹 모드에 있을 가능성이 큽니다. 리소스 모드로 다시 변경하려면 `azure config mode asm` 명령을 사용합니다.

2. 다음 명령을 사용하여 구독에 대한 웹 사이트를 나열합니다.

		azure site list

	목록에 이전 단계에서 만든 사이트가 포함되어 있어야 합니다.

2. 다음 명령을 사용하여 웹 사이트를 중지합니다. **mywebsite**를 사이트 이름으로 바꿉니다.

		azure site stop mywebsite

	명령이 완료된 후 브라우저를 새로 고쳐 사이트가 중지되었는지 확인할 수 있습니다.

3. 다음 명령을 사용하여 웹 사이트를 시작합니다. **mywebsite**를 사이트 이름으로 바꿉니다.

		azure site start mywebsite

	명령이 완료된 후 브라우저를 새로 고쳐 사이트가 시작되었는지 확인할 수 있습니다.

4. 다음 명령을 사용하여 웹 사이트를 삭제합니다. **mywebsite**를 사이트 이름으로 바꿉니다.

		azure site delete mywebsite

	명령이 완료된 후 `azure site list` 명령을 사용하여 웹 사이트가 더 이상 존재하지 않는지 확인합니다.

<h2><a id="script"></a>Azure 플랫폼 간 명령줄 인터페이스를 스크립팅하는 방법</h2>

xplat-cli를 사용하여 수동으로 명령을 실행할 수 있지만 명령줄 인터프리터 및 시스템에서 사용 가능한 기타 명령줄 유틸리티의 기능을 활용하여 복잡한 자동화 워크플로를 만들 수도 있습니다. 예를 들어 다음 명령은 실행 중인 모든 Azure 웹 사이트를 중지합니다.

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

이 예제는 각 줄에서 'Running' 문자열을 검사하는 `grep` 명령에 웹 사이트 목록을 연결합니다. 그런 다음 일치하는 모든 줄이 `awk` 명령에 연결됩니다. 이 명령은 `azure site stop`을 호출하고 전달된 두 번째 열(실행 중인 사이트 이름)을 중지할 사이트 이름으로 사용합니다.

이 예제는 명령을 함께 연결하는 방법을 보여 주지만 명령줄 인터프리터에서 제공하는 스크립팅 기능을 사용하여 보다 정교한 스크립트를 만들 수도 있습니다. 명령줄 인터프리터마다 각기 다른 스크립팅 기능과 구문이 있습니다. Bash는 Linux, OS X 등의 UNIX 기반 시스템에 가장 널리 사용되는 명령줄 인터프리터입니다.

Bash의 스크립팅에 대한 자세한 내용은 [고급 Bash 스크립팅 가이드][advanced-bash]를 참조하세요.

OS X 또는 Linux 기반 시스템의 스크립팅에 대한 일반적인 내용은 [셸 스크립트][script]를 참조하세요.

배치 파일을 사용한 Windows 기반 시스템의 스크립팅에 대한 자세한 내용은 [명령줄 참조 A-Z][batch]를 참조하세요.

### 결과 이해

스크립트를 만들 때 명령 출력을 캡처하여 다른 명령으로 전달하거나 출력에 대해 특정 값 확인 등의 작업을 수행해야 하는 경우가 있습니다. xplat-cli는 STDOUT 및 STDERR로 출력을 생성합니다. 각 줄의 앞에는 정보 상태 메시지의 경우 `info:`, 서비스에 대해 반환된 데이터의 경우 `data:` 문자열이 추가됩니다. 그러나 `--verbose` 또는 `-v` 매개 변수를 사용하여 자세한 정보를 반환하도록 xplat-cli에 지시할 수 있습니다. 그러면 `verbose:` 문자열이 앞에 추가된 추가 정보가 반환됩니다.

예를 들어 다음은 `azure site list` 명령에서 반환된 출력입니다.

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

`verbose:` 정보는 JSON 형식 데이터처럼 표시됩니다. 기본적으로 JSON을 이해하는 유틸리티(예: [jsawk](https://github.com/micha/jsawk) 또는 [jq](http://stedolan.github.io/jq/))로 작업하는 경우 `--json` 매개 변수를 사용하여 JSON 형식으로 정보를 반환할 수 있습니다. 예를 들면 다음과 같습니다.

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

위 명령은 웹 사이트 목록을 JSON으로 검색한 후 jsawk를 사용하여 사이트 이름을 검색합니다. 마지막으로, xargs를 통해 사이트 이름을 매개 변수로 전달하여 각 사이트에 대해 사이트 삭제 명령을 실행합니다.

>[WACOM.NOTE] `--json` 매개 변수는 상태 또는 데이터 정보의 생성을 차단합니다(앞에 `info:` 및 `data:`가 붙은 문자열). 예를 들어 `azure site create`와 함께 `--json` 매개 변수를 사용할 경우 이 명령은 `info:` 이외의 데이터를 반환하지 않으므로 아무 출력도 반환되지 않습니다.

###오류 작업

xplat-cli는 오류 정보를 STDERR에 기록하지만 스크립트가 실행된 디렉터리의 **azure.err** 파일에 오류에 대한 추가 정보가 기록될 수도 있습니다. 이 파일에 정보가 로깅되는 경우 STDOUT에 다음이 기록됩니다.

 info:    Error information has been recorded to azure.err

###종료 상태

일부 xplat-cli 명령은 필수 매개 변수가 없을 경우 0이 아닌 종료 상태를 반환하지 않습니다. 대신 사용자 입력을 요청하는 메시지를 표시합니다. 예를 들어 `azure site create` 명령을 사용하여 새로운 웹 사이트를 만드는 경우 사이트 이름 또는 `--location` 매개 변수를 지정하지 않으면 해당 값을 제공하라는 메시지가 표시됩니다.

종료 상태에 의존하는 스크립트를 작성하는 경우 사용 중인 xplat-cli 명령이 사용자 입력을 요청하는 메시지를 표시하지 않는지 확인합니다.

<h2><a id="additional-resources"></a>추가 리소스</h2>

* xplat-cli에 대한 자세한 내용을 보거나, 리소스 코드를 다운로드하거나, 문제를 보고하거나, 프로젝트에 기여하려면 [Azure 플랫폼 간 명령줄 인터페이스에 대한 GitHub 리포지토리](https://github.com/WindowsAzure/azure-sdk-tools-xplat)(영문)를 방문하세요.

* xplat-cli 또는 Azure를 사용하는 데 문제가 있는 경우 [Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home)을 방문하세요.

* Azure에 대한 자세한 내용은 [http://azure.microsoft.com/](http://azure.microsoft.com)을 참조하세요.




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure 웹 사이트]: ../media/freetrial.png
[미리 보기 기능 선택]: ../media/antares-iaas-preview-02.png
[구독 선택]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/ko-kr/library/bb490890.aspx
[xplatarm]: /ko-kr/documentation/articles/xplat-cli-azure-resource-manager/
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/ko-kr/documentation/articles/sign-up-organization/
