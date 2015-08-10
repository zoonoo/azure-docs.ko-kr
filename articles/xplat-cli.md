<properties
	pageTitle="Mac, Linux 및 Windows용 Azure CLI"
	description="Mac, Linux 및 Windows용 Azure CLI의 설치 및 구성하여 Azure 서비스 관리"
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
	ms.date="03/10/2015"
	ms.author="rasquill"/>

# Azure CLI 설치 및 구성

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli.md)

Azure CLI는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. Azure CLI는 웹 사이트, 가상 컴퓨터, 모바일 서비스, SQL 데이터베이스 및 Azure 플랫폼에서 제공하는 기타 서비스를 관리하는 기능과 같이 Azure 관리 포털에서 제공하는 기능과 동일한 기능을 대부분 제공합니다.

Azure CLI는 JavaScript로 작성되므로 Node.js가 필요합니다. Node.js용 Azure SDK를 사용하여 구현되고, Apache 2.0 라이선스에 릴리스되었습니다. 프로젝트 리포지토리는 [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli)에 있습니다.

이 문서에서는 Mac, Linux 및 Windows용 Azure CLI를 설치 및 구성하는 방법과 Azure CLI를 사용하여 Azure 플랫폼에서 기본 작업을 수행하는 방법을 설명합니다.

<a id="install"></a>
## Azure CLI를 설치하는 방법

Azure CLI 설치 단계에 대해 자세히 알아보려면 [Azure CLI 설치](xplat-cli-install.md) 페이지를 읽어보세요.


<a id="configure"></a>
## Azure 구독에 연결하는 방법

Azure CLI에서 제공하는 일부 명령은 Azure 구독 없이도 작동하지만 대부분의 명령에 구독이 필요합니다. 구독과 함께 작동하도록 Azure CLI를 구성하는 방법을 알아보려면 [Azure CLI에서 Azure 구독에 연결](xplat-cli-connect.md)을 참조하세요.


<a id="use"></a>
## Azure CLI를 사용하는 방법

Azure CLI에는 `azure` 명령을 사용하여 액세스합니다. 사용 가능한 명령 목록을 보려면 매개 변수 없이 `azure` 명령을 사용합니다. 다음과 유사한 도움말 정보가 표시됩니다.

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Microsoft Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.10
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

위에 나열된 최상위 명령에는 Azure의 특정 영역 작업을 위한 명령이 포함되어 있습니다. 예를 들어 `azure account` 명령에는 이전에 사용한 `download` 및 `import` 설정과 같이 Azure 구독과 관련된 명령이 포함되어 있습니다. 사용할 수 있는 명령 및 옵션에 대한 자세한 내용은 [Mac, Linux 및 Windows용 Azure CLI 사용]을 참조하세요.

대부분의 명령은 `azure <command> <operation> [parameters]` 형식으로 지정되며 계정 구성 같은 서비스 또는 개체에 대한 작업을 수행합니다. 기타 명령은 하위 명령을 제공하며 `azure <command> <subcommand> <operation> [parameters]` 형식을 따릅니다. 다음은 계정 구성에 사용되는 예제 명령입니다.

* 가져온 구독을 보려면 다음을 사용합니다.

		azure account list

* 구독을 가져온 경우 다음을 사용하여 하나의 구독을 기본값으로 설정합니다.

		azure account set <subscription>

`--help` 또는 `-h` 매개 변수를 사용하여 특정 명령에 대한 도움말을 볼 수 있습니다. 또는 `azure help [command] [options]` 형식을 사용하여 동일한 정보를 반환할 수도 있습니다. 예를 들어 다음 명령은 모두 동일한 정보를 반환합니다.

	azure account set --help

	azure account set -h

	azure help account set

명령에 필요한 매개 변수가 궁금한 경우 `--help`, `-h` 또는 `azure help [command]`를 사용하여 도움말을 참조하세요.

### 구성 모드 설정

Azure CLI를 사용하면 데이터베이스 서버, 데이터베이스, 웹 사이트 등의 사용자 관리 엔터티인 개별 _리소스_에 대해 관리 작업을 수행할 수 있습니다. 이것이 Azure CLI에 대한 기본 작업 모드이며 **Azure 서비스 관리**라고 합니다. 그러나 여러 리소스로 구성된 복잡한 솔루션이 있는 경우 전체 솔루션을 하나의 단위로 관리하는 것이 유용합니다.

하나의 논리적 단위 또는 _리소스 그룹_으로 리소스 그룹 관리를 지원하기 위해 Azure 리소스를 관리하는 새로운 방법으로 **리소스 관리자** Preview가 도입되었습니다.

>[AZURE.NOTE]리소스 관리자는 현재 미리 보기 상태이며, Azure 서비스 관리와 동일한 수준의 관리 기능을 제공하지 않습니다.

새 리소스 관리자를 지원하기 위해 Azure CLI에서 `azure config mode` 명령을 사용하여 이러한 관리 '모드'를 전환할 수 있습니다.

Azure CLI는 기본적으로 Azure 서비스 관리 모드로 설정되어 있습니다. 리소스 관리자 모드로 전환하려면 다음 명령을 사용합니다.

	azure config mode arm

Azure 서비스 관리 모드로 다시 변경하려면 다음 명령을 사용합니다.

	azure config mode asm

>[AZURE.NOTE]리소스 관리자 모드 및 Azure 서비스 관리 모드는 상호 배타적입니다. 즉, 한 모드에서 만든 리소스는 다른 모드에서 관리할 수 없습니다.

Azure CLI를 사용하여 리소스 관리자 작업을 수행하는 방법에 대한 자세한 내용은 [리소스 관리자에서 Azure CLI 사용][cliarm]을 참조하세요.

### Azure 서비스 관리 모드에서 서비스 작업

Azure CLI를 사용하여 Azure 서비스를 쉽게 관리할 수 있습니다. 이 예제에서는 Azure CLI를 사용하여 Azure 웹 사이트를 관리하는 방법을 알아보겠습니다.

1. 다음 명령을 사용하여 새 Azure 웹 사이트를 만듭니다. **mywebsite**를 고유한 이름으로 바꿉니다.

		azure site create mywebsite

	웹 사이트를 만들 지역을 지정하라는 메시지가 표시됩니다. 지리적으로 근처에 있는 지역을 선택합니다. 이 명령이 완료되면 http://mywebsite.azurewebsites.net에서 웹 사이트를 사용할 수 있습니다. **mywebsite**를 지정한 이름으로 바꾸세요.

	> [AZURE.NOTE]프로젝트 소스 제어에 Git를 사용하는 경우 `--git` 매개 변수를 지정하여 Azure에 이 웹 사이트에 대한 Git 리포지토리를 만들 수 있습니다. 아직 없는 경우 명령이 실행된 디렉터리에서도 Git 리포지토리가 초기화됩니다. 또한 `git push azure master` 명령을 통해 Azure 웹 사이트에 배포를 푸시하는 데 사용될 수 있는 __azure__라는 Git remote가 만들어집니다.

	> [AZURE.NOTE]'site'는 azure 명령이 아니라는 오류가 표시되는 경우 Azure CLI가 리소스 그룹 모드에 있을 가능성이 큽니다. 리소스 모드로 다시 변경하려면 `azure config mode asm` 명령을 사용합니다.

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

<a id="script"></a>
## Mac, Linux 및 Windows용 Azure CLI를 스크립팅하는 방법

Azure CLI를 사용하여 수동으로 명령을 실행할 수 있지만 명령줄 인터프리터 및 시스템에서 사용 가능한 기타 명령줄 유틸리티의 기능을 활용하여 복잡한 자동화 워크플로를 만들 수도 있습니다. 예를 들어 다음 명령은 실행 중인 모든 Azure 웹 사이트를 중지합니다.

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

이 예제는 각 줄에서 'Running' 문자열을 검사하는 `grep` 명령에 웹 사이트 목록을 연결합니다. 그런 다음 일치하는 모든 줄이 `awk` 명령에 연결됩니다. 이 명령은 `azure site stop`을 호출하고 전달된 두 번째 열(실행 중인 사이트 이름)을 중지할 사이트 이름으로 사용합니다.

이 예제는 명령을 함께 연결하는 방법을 보여 주지만 명령줄 인터프리터에서 제공하는 스크립팅 기능을 사용하여 보다 정교한 스크립트를 만들 수도 있습니다. 명령줄 인터프리터마다 각기 다른 스크립팅 기능과 구문이 있습니다. Bash는 Linux, OS X 등의 UNIX 기반 시스템에 가장 널리 사용되는 명령줄 인터프리터입니다.

Bash의 스크립팅에 대한 자세한 내용은 [고급 Bash 스크립팅 가이드][advanced-bash](영문)를 참조하십시오.

OS X 또는 Linux 기반 시스템의 스크립팅에 대한 일반적인 내용은 [셸 스크립트][script]를 참조하십시오.

배치 파일을 사용한 Windows 기반 시스템의 스크립팅에 대한 자세한 내용은 [명령줄 참조 A-Z][batch](영문)를 참조하십시오.

### 결과 이해

스크립트를 만들 때 명령 출력을 캡처하여 다른 명령으로 전달하거나 출력에 대해 특정 값 확인 등의 작업을 수행해야 하는 경우가 있습니다. Azure CLI는 STDOUT 및 STDERR로 출력을 생성합니다. 각 줄의 앞에는 정보 상태 메시지의 경우 `info:`, 서비스에 대해 반환된 데이터의 경우 `data:` 문자열이 추가됩니다. 그러나 `--verbose` 또는 `-v` 매개 변수를 사용하여 자세한 정보를 반환하도록 Azure CLI에 지시할 수 있습니다. 그러면 `verbose:` 문자열이 앞에 추가된 추가 정보가 반환됩니다.

예를 들어 다음은 `azure site list` 명령에서 반환된 출력입니다.

	info:    Executing command site list
	+ Enumerating sites
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

`--verbose` 또는 `-v` 매개 변수를 지정하면 다음과 유사한 정보가 반환됩니다.

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

`verbose:` 정보는 JSON 형식 데이터처럼 표시됩니다. 기본적으로 JSON을 이해하는 유틸리티(예: [jsawk](https://github.com/micha/jsawk) 또는 [jq](http://stedolan.github.io/jq/))로 작업하는 경우 `--json` 매개 변수를 사용하여 JSON 형식으로 정보를 반환할 수 있습니다. 예:

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q

위 명령은 웹 사이트 목록을 JSON으로 검색한 후 jsawk를 사용하여 사이트 이름을 검색합니다. 마지막으로, xargs를 통해 사이트 이름을 매개 변수로 전달하여 각 사이트에 대해 사이트 삭제 명령을 실행합니다.

>[AZURE.NOTE]`--json` 매개 변수는 상태 또는 데이터 정보(앞에 `info:` 및 `data:`가 추가된 문자열)의 생성을 차단합니다. 예를 들어 `azure site create`와 함께 `--json` 매개 변수를 사용할 경우 이 명령은 `info:` 이외의 데이터를 반환하지 않으므로 아무 출력도 반환되지 않습니다.

### 오류 작업

Azure CLI는 오류 정보를 STDERR에 기록하지만 스크립트가 실행된 디렉터리의 **azure.err** 파일에 오류에 대한 추가 정보가 기록될 수도 있습니다. 이 파일에 정보가 로깅되는 경우 STDOUT에 다음이 기록됩니다.

	info:    Error information has been recorded to azure.err

### 종료 상태

일부 Azure CLI 명령은 필수 매개 변수가 없을 경우 0이 아닌 종료 상태를 반환하지 않습니다. 대신 사용자 입력을 요청하는 메시지가 표시됩니다. 예를 들어 `azure site create` 명령을 사용하여 새로운 웹 사이트를 만드는 경우 사이트 이름 또는 `--location` 매개 변수를 지정하지 않으면 해당 값을 제공하라는 메시지가 표시됩니다.

종료 상태에 의존하는 스크립트를 작성하는 경우 사용 중인 Azure CLI 명령이 사용자 입력을 요청하는 메시지를 표시하지 않는지 확인합니다.

<a id="additional-resources"></a>

## 추가 리소스

* [서비스 관리에서 Azure CLI 사용][Using the Azure CLI]

* [리소스 관리에서 Azure CLI 사용][cliarm]

* Azure CLI에 대한 자세한 내용을 보거나, 소스 코드를 다운로드하거나, 문제를 보고하거나, 프로젝트에 기여하려면 [Azure CLI에 대한 GitHub 리포지토리](https://github.com/azure/azure-xplat-cli)를 방문하세요.

* Azure CLI 또는 Azure를 사용하는 데 문제가 있는 경우 [Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home)을 방문하세요.

* Azure에 대한 자세한 내용은 [http://azure.microsoft.com/](http://azure.microsoft.com)을 참조하세요.




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure Web Site]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[cliarm]: xplat-cli-azure-resource-manager.md
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[Using the Azure CLI]: virtual-machines-command-line-tools.md

<!---HONumber=July15_HO5-->