
<properties
		pageTitle="VM 확장을 사용하여 Linux VM 모니터링 | Microsoft Azure"
		description="Linux 진단 확장을 사용하여 Azure Linux VM의 성능 및 진단 데이터를 모니터링하는 방법을 알아봅니다."
		services="virtual-machines"
		documentationCenter=""
  		authors="NingKuang"
		manager="timlt"
		editor=""
  		tags="azure-service-management"/>

<tags
		ms.service="virtual-machines"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="07/20/2015"
		ms.author="Ning"/>


# Linux 진단 확장을 사용하여 Linux VM의 성능 및 진단 데이터 모니터링

## 소개

Linux 진단 확장을 통해 사용자는 다음과 같은 기능으로 Microsoft Azure에서 실행하는 Linux VM을 모니터링할 수 있습니다.

- Linux VM의 시스템 성능, 진단 및 syslog 데이터를 수집하고 사용자의 저장소 테이블에 업로드합니다.
- 사용자가 수집 및 업로드된 데이터 메트릭을 사용자 지정할 수 있도록 설정합니다.
- 사용자가 지정된 저장소 테이블에 지정된 로그 파일을 업로드할 수 있도록 설정합니다.

2\.0 버전의 경우 데이터에 다음이 포함됩니다.

- 시스템, 보안 및 응용 프로그램 로그를 비롯한 모든 Linux Rsyslog 로깅
- 이 [문서](https://scx.codeplex.com/wikipage?title=xplatproviders")에 지정된 모든 시스템 데이터입니다.
- 사용자가 지정한 로그 파일

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


## 확장을 사용하도록 설정하는 방법
[Azure 포털](https://ms.portal.azure.com/#), Azure PowerShell 또는 Azure CLI 스크립트를 통해 확장을 사용하도록 설정할 수 있습니다.

Azure 포털에서 직접 시스템 및 성능 데이터를 보고 구성하려면 다음 [단계](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "Windows 블로그의 URL")를 따르세요.


이 문서에서는 Azure CLI 명령을 통해 확장을 사용하도록 설정하고 구성하는 과정을 중점적으로 다룹니다. 이 옵션을 통해 저장소 테이블에서 데이터를 직접 읽고 볼 수 있습니다.


## 필수 조건
- Microsoft Azure Linux 에이전트 버전 2.0.6 이상. 대부분의 Azure VM Linux 갤러리 이미지에는 2.0.6 이후 버전이 포함되어 있습니다. **WAAgent -version**을 실행하여 VM에 설치된 버전을 확인할 수 있습니다. VM 2.0.6보다 이전 버전을 실행하는 경우 다음 [지침](https://github.com/Azure/WALinuxAgent "지침")을 따라 이를 업데이트할 수 있습니다.
- [Azure CLI](./xplat-cli-install.md). [이 지침](./xplat-cli-install.md)에 따라 컴퓨터에 Azure CLI 환경을 설치합니다. Azure CLI가 설치되었으면 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트)에서 **azure** 명령을 사용하여 Azure CLI 명령에 액세스할 수 있습니다. 예를 들어 자세한 사용법을 확인하려면 **azure vm extension set --help**를 실행하고, Azure에 로그인하려면 **azure login**을 실행하고, Azure에 있는 모든 가상 컴퓨터를 나열하려면 **azure vm list**를 실행합니다.
- 데이터를 저장할 저장소 계정입니다. 데이터를 저장소에 업로드하려면 이전에 생성된 저장소 계정 이름과 선택키가 있어야 합니다.


## Azure CLI 명령을 사용하여 Linux 진단 확장을 사용하도록 설정

###  시나리오 1. 기본 데이터 집합으로 확장을 사용하도록 설정
버전 2.0 이후의 경우 수집되는 기본 데이터에는 다음이 포함됩니다.

- 모든 Rsyslog 정보(시스템, 보안 및 응용 프로그램 로그 포함)  
- 기본 시스템 데이터의 핵심 집합. 전체 데이터 집합은 이 [문서](https://scx.codeplex.com/wikipage?title=xplatproviders)에 설명되어 있습니다. 추가 데이터를 사용하도록 설정하려는 경우 시나리오 2와 3의 단계를 계속 진행하세요.

1단계. 다음과 같은 내용으로 PrivateConfig.json라는 파일을 만듭니다.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account"
	}

2단계. **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**을 실행합니다.


###   시나리오 2. 성능 모니터 메트릭 사용자 지정  
이 섹션은 성능 및 진단 데이터 테이블을 사용자가 지정하는 방법을 설명합니다.

1단계. 다음 예제에 표시되는 내용으로 PrivateConfig.json라는 파일을 만듭니다. 수집하려는 특정 데이터를 지정합니다.

지원되는 모든 공급자 및 변수에 대해서는 이 [문서](https://scx.codeplex.com/wikipage?title=xplatproviders)를 참조하세요. 스크립트에 더 많은 쿼리를 추가하여 여러 쿼리를 포함하고 여러 테이블에 저장할 수 있습니다.

기본적으로 Rsyslog 데이터는 항상 수집됩니다.

	{
     	"storageAccountName":"storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"perfCfg":[
           	{"query":"SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation","table":"LinuxMemory"
           	}
          ]
	}


2단계. **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**을 실행합니다.


###   시나리오 3. 고유한 로그 파일 업로드
이 섹션은 저장소 계정에 특정 로그 파일을 수집하고 업로드하는 방법을 설명합니다. 로그 파일의 경로 및 로그를 저장할 테이블 이름을 지정해야 합니다. 스크립트에 여러 파일/테이블 항목을 추가하여 여러 로그 파일을 포함할 수 있습니다.

1단계. 다음과 같은 내용으로 PrivateConfig.json라는 파일을 만듭니다.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"fileCfg":[
           	{"file":"/var/log/mysql.err",
             "table":"mysqlerr"
           	}
          ]
	}


2단계. **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**을 실행합니다.


###   시나리오 4. Linux 모니터 확장을 사용하지 않도록 설정
1단계. 다음과 같은 내용으로 PrivateConfig.json라는 파일을 만듭니다.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account",
     	“perfCfg”:[],
     	“enableSyslog”:”False”
	}


2단계. **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**을 실행합니다.


## 데이터 검토
성능 및 진단 데이터는 Azure 저장소 테이블에 저장됩니다. [이 문서](storage-ruby-how-to-use-table-storage.md)를 검토하여 Azure CLI 스크립트를 통해 저장소 테이블의 데이터에 액세스하는 방법을 알아봅니다.

또한 다음 UI 도구를 사용하여 데이터에 액세스할 수 있습니다.

1.	Visual Studio 서버 탐색기를 사용합니다. 저장소 계정으로 이동합니다. VM이 약 5분 정도 실행된 후에 "LinuxCpu", "LinuxDisk", "LinuxMemory" 및 "Linuxsyslog" 등 네 개의 기본 테이블을 참조해야 합니다. 데이터를 볼 테이블 이름을 두 번 클릭합니다.
2.	[Azure 저장소 탐색기](https://azurestorageexplorer.codeplex.com/ "Azure 저장소 탐색기")를 사용하여 데이터에 액세스합니다.

![이미지](./media/virtual-machines-linux-diagnostic-extension/no1.png)

시나리오 2 및 3에서 지정된 fileCfg 또는 perfCfg를 사용하도록 설정한 경우 이전의 도구를 사용하여 기본이 아닌 데이터를 볼 수 있습니다.



## 알려진 문제
- 버전 2.0의 경우 스크립팅을 통해 Rsyslog 정보 및 고객이 지정한 로그 파일에만 액세스할 수 있습니다.
- 버전 2.0의 경우 먼저 스크립트를 통해 Linux 진단 확장을 사용하도록 설정하면 Azure 포털에서 데이터를 볼 수 없습니다. 먼저 포털에서 확장을 사용하도록 설정하면 스크립트가 계속 작동합니다.

<!---HONumber=Oct15_HO3-->