<properties
   pageTitle="Mac, Linux 및 Windows용 Azure CLI를 사용하여 Azure VM 관리"
   description="Mac, Linux 및 Windows용 Azure CLI를 사용하여 Azure VM을 생성, 관리 및 삭제하는 방법을 설명합니다."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="04/23/2015"
   ms.author="rasquill"/>

# Mac, Linux 및 Windows용 Azure CLI를 사용하여 가상 컴퓨터 관리

VM을 관리하기 위해 일상적으로 수행하는 많은 작업을 Azure CLI를 사용하여 자동화할 수 있습니다. 이 문서에서는 더 간단한 작업에 대한 예제 명령과 보다 복잡한 작업에 대한 명령을 보여 주는 문서에 대한 링크를 제공합니다.

>[AZURE.NOTE]아직 Azure CLI를 설치하고 구성하지 않은 경우 [여기](../xplat-cli-install.md)에서 지침을 확인할 수 있습니다. PowerShell의 동일한 작업에 대한 빠른 시작을 원하는 경우 [Azure PowerShell을 사용하여 VM 관리](virtual-machines-manage-vms-powershell.md)를 참조하세요.

## 예제 명령을 사용하는 방법
명령의 일부 텍스트는 환경에 적합한 텍스트로 바꿔야 합니다. < and > 기호는 바꿔야 하는 텍스트를 나타냅니다. 텍스트를 바꾸는 경우 기호는 제거하고 따옴표는 그대로 남겨 두세요.

> [AZURE.NOTE]콘솔 명령의 출력을 프로그래밍 방식으로 저장하고 조작하려는 경우 **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** 또는 작업에 적합한 언어 라이브러리 같은 JSON 구문 분석 도구를 사용할 수 있습니다.

## VM에 대한 정보 표시

자주 사용하게 될 기본 작업입니다. 이 작업을 사용하여 VM에 대한 정보를 가져오거나 VM에서 작업을 수행하거나 변수에 저장할 출력을 가져옵니다.

VM에 대한 정보를 가져오려면 이 명령을 실행하고 < and > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

     azure vm show -g <group name> -n <virtual machine name>

JSON 문서로 $vm 변수에 출력을 저장하려면 다음을 실행합니다.

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)
    
또는 stdout를 파일에 파이프할 수 있습니다.

## Linux 기반 가상 컴퓨터에 로그온

일반적으로 Linux 컴퓨터는 SSH를 통해 연결됩니다. 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-use-ssh-key.md)을 참조하세요 Azure 리소스 관리자 개요
## VM 중지

다음 명령을 실행합니다.

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]해당 클라우드 서비스의 마지막 VM인 경우 이 매개 변수를 사용하여 클라우드 서비스의 VIP(가상 IP)를 유지합니다. <br><br> StayProvisioned 매개 변수를 사용하는 경우 VM에 대한 요금이 청구됩니다.

## VM 시작

다음 명령을 실행합니다. Azure Resource Manager Overview azure vm start <group name> <virtual machine name>

## 데이터 디스크 연결

또한 새 디스크를 연결할지 데이터를 포함하는 디스크를 연결할지를 결정해야 합니다. 새 디스크의 경우 명령에서 .vhd 파일을 만들고 동일한 명령으로 디스크를 연결합니다.

새 디스크를 연결하려면 다음 명령을 실행합니다.

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb> 

기존 데이터 디스크를 연결하려면 다음 명령을 실행합니다.

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

## Linux VM 만들기

새 Linux 기반 VM을 만들려면 리소스 그룹 이름, 위치, 이미지 이름, VM 이름 및 백업 .vhd 이미지를 저장할 저장소 계정 등 여러 값을 직접 입력해야 합니다. 사용하려는 정보가 있는 경우 다음을 입력하면 Azure CLI에서 대화형 세션을 만들어 해당 값을 입력하라는 메시지를 표시할 수 있습니다.

    azure vm create
    
물론 이러한 값이 이미 있는 경우 `azure help vm create`를 입력하면 적절한 스위치를 찾아서 해당 값을 직접 전달할 수 있습니다.

## 다음 단계

**arm** 모드의 Azure CLI 사용에 대한 더 많은 예제는 [Azure 리소스 관리에 Mac, Linux 및 Windows용 Microsoft Azure CLI 사용](../xplat-cli-resource-manager.md)을 참조하세요. Azure 리소스 및 해당 개념에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.









<!--HONumber=52-->
 