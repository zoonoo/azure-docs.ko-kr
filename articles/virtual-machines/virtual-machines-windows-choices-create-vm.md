<properties
	pageTitle="Windows VM을 만드는 다양한 방법 | Microsoft Azure"
	description="리소스 관리자로 Windows 가상 컴퓨터를 만드는 다양한 방법을 나열합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="10/22/2015"
	ms.author="cynthn"/>

# 리소스 관리자로 Windows 가상 컴퓨터를 만드는 다양한 방법

가상 컴퓨터는 다양한 사용자와 목적에 맞게 조절 가능하기 때문에 Azure에서는 여러 방법으로 가상 컴퓨터를 만들 수 있습니다. 즉 가상 컴퓨터를 만드는 방법과 그에 대한 몇 가지 선택이 필요합니다. 이 문서에서는 이 선택 사항 및 지침에 대한 링크 요약을 제공합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

Azure 리소스 관리자 템플릿은 하나의 논리적 배포 단위로 다른 리소스 및 가상 컴퓨터를 만들고 관리하는 방법으로 최근에 도입되었습니다. 사용 가능한 경우, 이 방법에 대한 지침은 아래에 포함됩니다. Azure 리소스 관리자 및 리소스를 하나의 단위로 관리하는 방법에 대해 자세히 알려면 [개요][]를 참조하세요.

## 도구 선택

### GUI: Azure Preview 포털

Azure 포털의 그래픽 사용자 인터페이스는 특히 Azure로 시작한 경우 쉽게 가상 컴퓨터를 사용해 볼 수 있는 방법입니다. Azure Preview 포털을 사용하여 가상 컴퓨터 만들기:

[Windows를 실행하는 가상 컴퓨터 만들기][]

### 명령 셸: Azure CLI 또는 Azure PowerShell

명령 셸에서 작업을 원할 경우, Mac 및 Linux 사용자 또는 Azure PowerShell용 Azure 명령행 인터페이스(CLI) 간을 선택하며, cmdlet 및 사용자 지정 콘솔을 포함합니다.

Azure CLI의 경우 다음 내용 참조:

- [Mac, Linux 및 Windows용 Azure CLI를 사용한 가상 컴퓨터 작업에 해당하는 리소스 관리자 및 서비스 관리 명령][]
- [Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리][]

Azure PowerShell의 경우 다음 내용 참조:

- [리소스 관리자 및 PowerShell을 사용하여 Windows VM 만들기][]
- [리소스 관리자 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 미리 구성][]
- [Azure 리소스 관리자 템플릿 및 PowerShell을 사용하여 가상 컴퓨터 배포 및 관리][]
- [리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기][]

### 개발 환경: Visual Studio

[계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스 배포][]

## 운영 체제 및 이미지 선택

실행하려는 운영 체제에 따라 이미지를 선택합니다. Azure 및 해당 파트너는 응용 프로그램 및 도구를 포함하는 여러 이미지를 제공합니다. 또는 사용자 고유의 이미지 중 하나를 사용합니다. [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택][]의 정보를 사용하여 응용 프로그램에 필요한 플랫폼 이미지를 찾습니다.

<!-- LINKS -->
[개요]: ../resource-group-overview.md

[Windows를 실행하는 가상 컴퓨터 만들기]: virtual-machines-windows-tutorial.md

[Mac, Linux 및 Windows용 Azure CLI를 사용한 가상 컴퓨터 작업에 해당하는 리소스 관리자 및 서비스 관리 명령]: xplat-cli-azure-manage-vm-asm-arm.md
[Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리]: virtual-machines-deploy-rmtemplates-azure-cli.md
[리소스 관리자 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 미리 구성]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Azure 리소스 관리자 템플릿 및 PowerShell을 사용하여 가상 컴퓨터 배포 및 관리]: virtual-machines-deploy-rmtemplates-powershell.md
[리소스 관리자 및 PowerShell을 사용하여 Windows VM 만들기]: virtual-machines-create-windows-powershell-resource-manager.md
[리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기]: virtual-machines-create-windows-powershell-resource-manager-template-simple.md


[Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택]: resource-groups-vm-searching.md
[계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스 배포]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Nov15_HO1-->