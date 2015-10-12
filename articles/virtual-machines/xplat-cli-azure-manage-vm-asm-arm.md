<properties
	pageTitle="VM 작업에 대한 동등한 Azure CLI 명령 | Microsoft Azure"
	description="Azure 리소스 관리자 및 Azure 서비스 관리 모드에서 Azure VM을 만들고 관리하는 해당 Azure CLI 명령"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="08/28/2015"
	ms.author="danlep"/>


# Mac, Linux 및 Windows용 Azure CLI를 사용한 VM 작업에 해당하는 리소스 관리자 및 서비스 관리 명령
이 문서에서는 Azure 서비스 관리 및 Azure 리소스 관리자에서 Azure VM을 만들고 관리하는 해당 Microsoft Azure CLI(명령줄 인터페이스) 명령을 보여 줍니다. 이를 사용하여 명령 모드 간에 스크립트를 쉽게 마이그레이션할 수 있습니다.

* 아직 Azure CLI를 설치하고 구독에 연결하지 않은 경우 [Azure CLI 설치](../xplat-cli-install.md) 및 [Azure CLI에서 Azure 구독에 연결](../xplat-cli-connect.md)을 참조하세요. 리소스 관리자 모드 명령을 사용하려는 경우 로그인 메서드를 사용하여 연결해야 합니다.

* Azure CLI에서 리소스 관리자 모드로 시작하고 명령 모드를 전환하려면 [리소스 관리자에서 Azure 명령줄 인터페이스 사용](xplat-cli-azure-resource-manager.md)을 참조하세요. 기본적으로 CLI는 서비스 관리 모드로 시작됩니다. 리소스 관리자 모드를 변경하려면 `azure config mode arm`을(를) 실행합니다. 서비스 관리 모드로 돌아가려면 `azure config mode asm`을(를) 실행합니다.

* 온라인 명령 도움말 및 옵션을 보려면 `azure <command> <subcommand> --help` 또는 `azure help <command> <subcommand>`를 입력하세요.

## VM 작업
다음 표에서 서비스 관리 및 리소스 관리자에서 Azure CLI 명령으로 수행할 수 있는 일반적인 VM 작업을 비교합니다. 리소스 관리자 명령을 사용할 때는 대부분 기존 리소스 그룹의 이름을 전달해야 합니다.

> [AZURE.NOTE]이러한 예제에는 리소스 관리자에 템플릿 기반 작업이 포함되어 있지 않습니다. 자세한 내용은 [리소스 관리자에서 Azure 플랫폼 간 명령줄 인터페이스 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.

작업 | 서비스 관리 | 리소스 관리자
-------------- | ----------- | -------------------------
가장 기본적인 VM 만들기 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(`azure vm image list` 명령에서 `image-urn`을(를) 가져옵니다.)
Linux VM 만들기 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Windows VM 만들기 | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
VM 나열 | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
VM에 대한 정보 가져오기 | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
VM 시작 | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
VM 중지 | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
VM 할당 취소 | 사용할 수 없음 | `azure vm deallocate [options] <resource-group> <name>`
VM 다시 시작 | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
VM 삭제 | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
VM 캡처 | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
사용자 이미지에서 VM 만들기 | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
특수한 디스크에서 VM 만들기 | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
VM에 데이터 디스크 추가 | `azure  vm disk attach [options] <vm-name> <disk-image-name>` 또는 <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
VM에서 데이터 디스크 제거 | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
VM에 제네릭 확장 추가 | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
VM에 VM 액세스 확장 추가 | 사용할 수 없음 | `azure vm reset-access [options] <resource-group> <name>`
VM에 Docker 확장 추가 | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
VM에 Chef 확장 추가 | `azure  vm extension get-chef [options] <vm-name>` | 사용할 수 없음
VM 확장을 사용하지 않도록 설정 | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | 사용할 수 없음
VM 확장 제거 | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
VM 확장 나열 | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
VM 이미지 나열 | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]` 또는 <br/> `azure vm image list-publishers [options] <location>` 또는 <br/> `azure vm image list-offers [options] <location>` 또는 <br/> `azure vm image list-skus [options] <location>`
VM 이미지 표시 | `azure vm image show [options]` | 사용할 수 없음
VM 리소스 사용 | 사용할 수 없음 | `azure vm list-usage [options] <location>`
모든 사용 가능한 VM 크기 가져오기 | 사용할 수 없음 | `azure vm sizes [options]`


## 다음 단계

* Azure CLI를 사용하여 리소스 관리자 리소스에서 작업을 수행하는 방법에 대한 자세한 내용은 [리소스 관리자에서 Azure 명령줄 인터페이스 사용](xplat-cli-azure-resource-manager.md) 및 [Azure 명령줄 인터페이스를 사용하여 역할 기반 액세스 제어 관리](../role-based-access-control-xplat-cli-install.md)를 참조하세요.
* CLI 명령에 대한 다른 예는 [Azure 서비스 관리에서 Azure 명령줄 인터페이스 사용](../virtual-machines-command-line-tools.md) 및 [Azure 리소스 관리자에서 Azure CLI 사용](azure-cli-arm-commands.md)을 참조하세요.

<!---HONumber=Oct15_HO1-->