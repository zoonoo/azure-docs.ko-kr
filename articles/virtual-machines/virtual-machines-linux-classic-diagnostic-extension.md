
<properties
		pageTitle="VM 확장을 사용하여 Linux VM 모니터링 | Microsoft Azure"
		description="Linux 진단 확장을 사용하여 Azure Linux VM의 성능 및 진단 데이터를 모니터링하는 방법을 알아봅니다."
		services="virtual-machines-linux"
		documentationCenter=""
  		authors="NingKuang"
		manager="timlt"
		editor=""
  		tags="azure-service-management"/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="12/15/2015"
		ms.author="Ning"/>


# Linux 진단 확장을 사용하여 Linux VM의 성능 및 진단 데이터 모니터링

## 소개

Linux 진단 확장을 통해 사용자는 Microsoft Azure에서 실행하는 Linux VM을 모니터링할 수 있습니다. 제공되는 기능은 다음과 같습니다.

- Linux VM에서 사용자의 저장소 테이블로 진단 및 syslog 정보를 포함하여 시스템 성능 정보를 수집하고 업로드합니다.
- 사용자가 수집 및 업로드된 데이터 메트릭을 사용자 지정할 수 있도록 설정합니다.
- 사용자가 지정된 저장소 테이블에 지정된 로그 파일을 업로드할 수 있도록 설정합니다.

2\.0 버전에는 다음 데이터가 포함됩니다.

- 시스템, 보안 및 응용 프로그램 로그를 비롯한 모든 Linux Rsyslog 로그.
- [System Center 플랫폼 간 솔루션 사이트](https://scx.codeplex.com/wikipage?title=xplatproviders)에 지정된 모든 시스템 데이터.
- 사용자가 지정한 로그 파일.

이 확장은 클래식 및 리소스 관리자 배포 모델 둘 다에서 작동합니다.


## 확장 사용
[Azure 포털](https://portal.azure.com/#), Azure PowerShell 또는 Azure CLI 스크립트를 통해 확장을 사용하도록 설정할 수 있습니다.

Azure 포털에서 직접 시스템 및 성능 데이터를 보고 구성하려면 다음 [단계](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "Windows 블로그에 대한 URL"/)를 따르세요.


이 문서는 Azure CLI 명령을 사용하여 확장을 사용하도록 설정하고 구성하는 방법에 중점을 두고 있습니다. 이렇게 하면 저장소 테이블에서 직접 데이터를 읽고 볼 수 있습니다.

여기서 설명된 구성 방법은 Azure 포털에서 작동하지 않습니다. Azure 포털에서 직접 시스템 및 성능 데이터를 보고 구성하려면 Azure 포털을 통해 확장할 수 있어야 합니다.


## 필수 조건
- **Azure Linux 에이전트 버전 2.0.6 이상**. 대부분의 Azure VM Linux 갤러리 이미지에는 2.0.6 이후 버전이 포함되어 있습니다. **WAAgent -version**을 실행하여 VM에 설치된 버전을 확인할 수 있습니다. VM 2.0.6보다 이전 버전을 실행하는 경우 다음 [GitHub의 이러한 지침](https://github.com/Azure/WALinuxAgent "지침")을 따라 이를 업데이트할 수 있습니다.

- **Azure CLI**. [CLI 설치 지침](../xplat-cli-install.md)을 따라 컴퓨터에 Azure CLI 환경을 설정합니다. Azure CLI가 설치되었으면 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트)에서 **azure** 명령을 사용하여 Azure CLI 명령에 액세스할 수 있습니다. 예:
	- 자세한 도움말 정보는 **azure vm extension set –help**를 실행합니다.
	- Azure에 로그인하려면 **azure login**을 실행합니다.
	- Azure에 있는 모든 가상 컴퓨터를 나열하려면 **azure vm list**를 실행합니다.
- 데이터를 저장할 저장소 계정입니다. 데이터를 저장소에 업로드하려면 이전에 생성된 저장소 계정 이름과 선택키가 있어야 합니다.


## Azure CLI 명령을 사용하여 Linux 진단 확장을 사용하도록 설정

### 시나리오 1. 기본 데이터 집합으로 확장을 사용하도록 설정
버전 2.0 이후의 경우 수집되는 기본 데이터에는 다음이 포함됩니다.

- 모든 Rsyslog 정보(시스템, 보안 및 응용 프로그램 로그 포함)  
- 기본 시스템 데이터의 핵심 집합입니다. 전체 데이터 집합은 [System Center 플랫폼 간 솔루션 사이트](https://scx.codeplex.com/wikipage?title=xplatproviders)에 설명되어 있습니다. 추가 데이터를 사용하도록 설정하려는 경우 시나리오 2와 3의 단계를 계속 진행하세요.

1단계. 다음과 같은 내용으로 PrivateConfig.json라는 파일을 만듭니다.

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

2단계. **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**을 실행합니다.


###   시나리오 2. 성능 모니터 메트릭 사용자 지정  
이 섹션은 성능 및 진단 데이터 테이블을 사용자가 지정하는 방법을 설명합니다.

1단계. 시나리오 1에서 설명한 내용으로 PrivateConfig.json이라는 파일을 만듭니다. 또한 PublicConfig.json이라는 파일도 만듭니다. 수집하려는 특정 데이터를 지정합니다.

지원되는 모든 공급자 및 변수에 대해서는 [System Center 플랫폼 간 솔루션 사이트](https://scx.codeplex.com/wikipage?title=xplatproviders)를 참조하세요. 스크립트에 더 많은 쿼리를 추가하여 여러 쿼리를 포함하고 여러 테이블에 저장할 수 있습니다.

기본적으로 Rsyslog 데이터는 항상 수집됩니다.

    {
      	"perfCfg":
      	[
      	    {
      	        "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
      	        "table" : "LinuxMemory"
      	    }
      	]
    }


2단계. **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**을 실행합니다.


###   시나리오 3. 고유한 로그 파일 업로드
이 섹션은 저장소 계정에 특정 로그 파일을 수집하고 업로드하는 방법을 설명합니다. 로그 파일의 경로와 로그를 저장하고자 하는 테이블 이름을 모두 지정해야 합니다. 스크립트에 여러 파일/테이블 항목을 추가하여 여러 로그 파일을 만들 수 있습니다.

1단계. 시나리오 1에서 설명한 내용으로 PrivateConfig.json이라는 파일을 만듭니다. 그런 다음 다음과 같은 내용으로 PublicConfig.json이라는 다른 파일을 만듭니다.

    {
        "fileCfg" :
        [
            {
                "file" : "/var/log/mysql.err",
                "table" : "mysqlerr"
             }
        ]
    }


2단계. **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**을 실행합니다.


###   시나리오 4. 모든 로그 수집에서 확장 중지
이 섹션에서는 로그 수집에서 확장을 중지하는 방법을 설명합니다. 이러한 재구성에도 모니터링 에이전트 프로세스는 계속 실행됩니다. 모니터링 에이전트 프로세스는 완전히 중지하려는 경우 확장을 사용하지 않도록 설정합니다. 확장을 사용하지 않도록 설정하는 명령은 **azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'**입니다.

1단계. 시나리오 1에서 설명한 내용으로 PrivateConfig.json이라는 파일을 만듭니다. 다음과 같은 내용으로 PublicConfig.json이라는 다른 파일을 만듭니다.

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


2단계. **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json**을 실행합니다.


## 데이터 검토
성능 및 진단 데이터는 Azure 저장소 테이블에 저장됩니다. Azure CLI 스크립트를 사용하여 저장소 테이블의 데이터에 액세스 하는 방법을 알아보려면 [Ruby에서 Azure 테이블 저장소를 사용하는 방법](../storage/storage-ruby-how-to-use-table-storage.md)을 검토하세요.

또한 다음 UI 도구를 사용하여 데이터에 액세스할 수 있습니다.

1. Visual Studio 서버 탐색기. 저장소 계정으로 이동합니다. VM을 약 5분 정도 실행하고 나면 "LinuxCpu", "LinuxDisk", "LinuxMemory" 및 "Linuxsyslog" 등 네 개의 기본 테이블이 나타납니다. 데이터를 볼 테이블 이름을 두 번 클릭합니다.

2. [Azure 저장소 탐색기](https://azurestorageexplorer.codeplex.com/ "Azure 저장소 탐색기").

![이미지](./media/virtual-machines-linux-classic-diagnostic-extension/no1.png)

(시나리오 2와 3에 설명된 대로) FileCfg 또는 perfCfg를 사용하도록 설정한다면, 기본이 아닌 데이터를 Visual Studio 서버 탐색기 및 Azure 저장소 탐색기를 사용하여 볼 수 있습니다.

## 알려진 문제
- Linux 진단 확장 버전 2.0의 경우 Rsyslog 정보 및 고객이 지정한 로그 파일은 스크립팅을 통해서만 액세스할 수 있습니다.

<!---HONumber=AcomDC_0615_2016-->