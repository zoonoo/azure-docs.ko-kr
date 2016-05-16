<properties 
   pageTitle="Linux 및 Mac용 기본 Azure CLI 명령 | Microsoft Azure"
   description="Linux 및 Mac에서 Azure Resource Manager 모드로 VM 관리를 시작할 수 있는 기본 Azure CLI 명령"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="RicksterCDN" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="04/11/2016"
   ms.author="rclaus" />

# Linux 및 Mac의 일반적 Azure CLI 명령

Resource Manager 명령 및 템플릿에서 Azure CLI와 리소스 그룹을 사용하여 Azure 리소스 및 워크로드를 배포하려면 Azure 계정이 필요합니다. 계정이 없는 경우 [여기에서 무료 Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.

아직 Azure CLI를 설치하고 구독에 연결하지 않은 경우 [Azure CLI 설치](../xplat-cli-install.md)를 참조하여 `azure config mode arm`으로 모드를 `arm`으로 설정하고 `azure login` 명령으로 Azure에 연결합니다.

## Azure CLI의 기본 Azure Resource Manager 명령

이 문서는 Azure CLI와 함께 사용하여 Azure 구독에서 ARM 리소스(기본적으로 VM)를 관리하고 상호 작용할 수 있는 기본 명령에 대해 설명합니다. 특정 명령줄 스위치 및 옵션에 대해 자세한 도움이 필요할 경우 `azure <command> <subcommand> --help` 또는 `azure help <command> <subcommand>`를 입력하여 온라인 명령 도움말 및 옵션을 사용할 수 있습니다.

> [AZURE.NOTE] 이러한 예제에는 템플릿 기반 작업이 포함되지 않으며 이 작업은 일반적으로 리소스 관리자의 VM 배포에 사용하는 것이 좋습니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure CLI 사용](../xplat-cli-azure-resource-manager.md) 및 [Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리](virtual-machines-linux-cli-deploy-templates.md)를 참조하세요.

작업 | 리소스 관리자
-------------- | ----------- | -------------------------
가장 기본적인 VM 만들기 | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(`azure vm image list` 명령에서 `image-urn`을 가져옵니다. 예제는 [이 문서](virtual-machines-linux-cli-ps-findimage.md)를 참조하세요.)
Linux VM 만들기 | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Windows VM 만들기 | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
VM 나열 | `azure  vm list [options]`
VM에 대한 정보 가져오기 | `azure  vm show [options] <resource_group> <name>`
VM 시작 | `azure vm start [options] <resource_group> <name>`
VM 중지 | `azure vm stop [options] <resource_group> <name>`
VM 할당 취소 | `azure vm deallocate [options] <resource-group> <name>`
VM 다시 시작 | `azure vm restart [options] <resource_group> <name>`
VM 삭제 | `azure vm delete [options] <resource_group> <name>`
VM 캡처 | `azure vm capture [options] <resource_group> <name>`
사용자 이미지에서 VM 만들기 | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
특수한 디스크에서 VM 만들기 | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
VM에 데이터 디스크 추가 | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
VM에서 데이터 디스크 제거 | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
VM에 제네릭 확장 추가 |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
VM에 VM 액세스 확장 추가 | `azure vm reset-access [options] <resource-group> <name>`
VM에 Docker 확장 추가 | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
VM 확장 제거 | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
VM 리소스 사용 | `azure vm list-usage [options] <location>`
모든 사용 가능한 VM 크기 가져오기 | `azure vm sizes [options]`


## 다음 단계

* 기본 VM 관리 이상의 CLI 명령에 대한 다른 예는 [Azure Resource Manager에서 Azure CLI 사용](azure-cli-arm-commands.md)을 참조하세요.

<!---HONumber=AcomDC_0504_2016-->