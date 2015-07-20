<properties 
	pageTitle="Windows 가상 컴퓨터를 만드는 다양한 방법" 
	description="Windows 가상 컴퓨터를 만드는 다양한 방법을 나열하고 지침에 대한 링크를 제공합니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-windows" 
	ms.workload="infrastructure-services"
	ms.date="05/14/2015" 
	ms.author="kathydav"/>

# Windows 가상 컴퓨터를 만드는 다양한 방법

VM은 다른 사용자와 목적에 적합하기 때문에 Azure는 다른 방법으로 VM을 만들 수 있습니다. 즉, VM 및 만드는 방법에 대해 일부 선택 해야 함을 의미합니다. 이 문서에서는 이 선택 사항 및 지침에 대한 링크 요약을 제공합니다.

Azure 리소스 관리자 템플릿은 하나의 논리적 배포 단위로 다른 리소스 및 가상 컴퓨터를 만들고 관리하는 방법으로 최근에 도입되었습니다. 사용 가능한 경우, 이 방법에 대한 지침은 아래에 포함됩니다. Azure 리소스 관리자 및 리소스를 하나의 단위로 관리하는 방법에 대해 자세히 알려면 [개요][]를 참조하세요.

## 도구 선택

### GUI: Azure 포털 또는 미리 보기 포털 

Azure 포털의 그래픽 사용자 인터페이스는 특히 Azure로 시작한 경우 쉽게 가상 컴퓨터를 사용해 볼 수 있는 방법입니다. Azure 포털 또는 Azure Preview 포털을 사용하여 VM을 만듭니다.

[Windows를 실행하는 가상 컴퓨터 만들기][]

### 명령 셸: Azure CLI 또는 Azure PowerShell

명령 셸에서 작업을 원할 경우, Mac 및 Linux 사용자 또는 Azure PowerShell용 Azure 명령행 인터페이스(CLI) 간을 선택하며, Azure용 Windows PowerShell cmdlet 및 사용자 지정 콘솔을 포함합니다.

Azure CLI의 경우, [Mac, Linux 및 Windows용 Azure CLI를 사용한 VM 작업에 해당하는 리소스 관리자 및 서비스 관리 명령][]을 참조하세요. 템플릿을 사용하려면, [Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리][]를 참조하세요.

Azure PowerShell에 대해서는 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성][]을 참조하고, 템플릿을 사용하려면 [Azure 리소스 관리자 템플릿 및 PowerShell을 사용하여 가상 컴퓨터 배포 및 관리][]를 참조하세요.

### 개발 환경: Visual Studio

[Visual Studio에서 웹 사이트용 가상 컴퓨터 만들기][]

[계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스 배포][]

## 운영 체제 및 이미지 선택

실행하려는 운영 체제에 따라 이미지를 선택합니다. Azure 및 해당 파트너는 응용 프로그램 및 도구를 포함하는 여러 이미지를 제공합니다. 또는 사용자 고유의 이미지 중 하나를 사용합니다.

### Azure 이미지

이러한 지침은 Azure 이미지를 사용하여 네트워킹, 부하 분산 등에 대한 옵션으로 사용자 지정된 가상 컴퓨터를 만드는 방법을 보여줍니다. [Windows를 실행하는 사용자 지정 가상 컴퓨터를 만드는 방법][]을 참조하세요.

### 사용자 고유의 이미지 사용

해당 VM을 *캡처*하거나 자신의 이미지를 업로드하여 가상 하드 디스크(VHD)에 저장된 이미지를 기존 Azure 가상 컴퓨터를 기반으로 사용합니다.

- [Windows 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법][]
- [Windows Server VHD 만들기 및 Azure에 업로드][]

## 다음 단계

[가상 컴퓨터에 로그온][]

[데이터 디스크 연결][]

## 추가 리소스
[Azure VM 구성 설정 정보][]

[기본 구성 테스트 환경][]

[Azure 하이브리드 클라우드 테스트 환경][]

<!-- LINKS -->
[개요]: ../resource-group-overview.md

[Windows를 실행하는 가상 컴퓨터 만들기]: virtual-machines-windows-tutorial.md

[Mac, Linux 및 Windows용 Azure CLI를 사용한 VM 작업에 해당하는 리소스 관리자 및 서비스 관리 명령]: xplat-cli-azure-manage-vm-asm-arm.md
[Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Azure 리소스 관리자 템플릿 및 PowerShell을 사용하여 가상 컴퓨터 배포 및 관리]: virtual-machines-deploy-rmtemplates-powershell.md
[Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성]: virtual-machines-ps-create-preconfigure-windows-vms.md

[Windows를 실행하는 사용자 지정 가상 컴퓨터를 만드는 방법]: virtual-machines-windows-create-custom.md

[Windows 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법]: virtual-machines-capture-image-windows-server.md

[Windows Server VHD 만들기 및 Azure에 업로드]: virtual-machines-create-upload-vhd-windows-server.md


[Visual Studio에서 웹 사이트용 가상 컴퓨터 만들기]: virtual-machines-dotnet-create-visual-studio-powershell.md
[계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스 배포]: virtual-machines-arm-deployment.md

[가상 컴퓨터에 로그온]: virtual-machines-log-on-windows-server.md

[데이터 디스크 연결]: storage-windows-attach-disk.md

[Azure VM 구성 설정 정보]: http://msdn.microsoft.com/library/azure/dn763935.aspx

[기본 구성 테스트 환경]: virtual-machines-base-configuration-test-environment.md

[Azure 하이브리드 클라우드 테스트 환경]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=July15_HO2-->