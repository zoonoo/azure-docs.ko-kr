<properties
   pageTitle="Azure PowerShell | Microsoft Azure를사용하여 가상 컴퓨터 관리"
   description="가상 컴퓨터 관리에서의 작업 자동화에 사용할 수 있는 명령에 대해 알아봅니다."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="rothja"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/01/2016"
   ms.author="kasing"/>

# Azure PowerShell을 사용하여 가상 컴퓨터 관리

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Azure PowerShell cmdlet을 사용하여 매일 VM을 관리하기 위해 수행하는 많은 작업을 자동화할 수 있습니다. 이 문서에서는 더 간단한 작업에 대한 예제 명령과 보다 복잡한 작업에 대한 명령을 보여 주는 문서에 대한 링크를 제공합니다.

>[AZURE.NOTE] Azure PowerShell을 아직 설치 및 구성하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 문서에서 지침을 확인할 수 있습니다.

## 예제 명령을 사용하는 방법
명령의 일부 텍스트는 환경에 적합한 텍스트로 바꿔야 합니다. < 및 > 기호는 바꿔야 하는 텍스트를 나타냅니다. 텍스트를 바꾸는 경우 기호는 제거하고 따옴표는 그대로 남겨 두세요.

## VM 확인
자주 사용하게 될 기본 작업입니다. 이 작업을 사용하여 VM에 대한 정보를 가져오거나 VM에서 작업을 수행하거나 변수에 저장할 출력을 가져옵니다.

VM에 대한 정보를 가져오려면 이 명령을 실행하고 < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

출력을 $vm 변수에 저장하려면 다음을 실행합니다.

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Windows 기반 VM에 로그온

다음 명령을 실행합니다.

>[AZURE.NOTE] **Get-AzureVM** 명령 표시에서 가상 컴퓨터 및 클라우드 서비스 이름을 가져올 수 있습니다.
>
	$svcName = "<cloud service name>"
	$vmName = "<virtual machine name>"
	$localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile = $localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## VM 중지

다음 명령을 실행합니다.

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT] 해당 클라우드 서비스의 마지막 VM인 경우 이 매개 변수를 사용하여 클라우드 서비스의 VIP(가상 IP)를 유지합니다. <br><br> StayProvisioned 매개 변수를 사용하는 경우 VM에 대한 요금이 청구됩니다.

## VM 시작

다음 명령을 실행합니다.

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## 데이터 디스크 연결
이 작업에는 몇 단계가 필요합니다. 먼저, ****Add-AzureDataDisk**** cmdlet를 사용하여 $vm 개체에 디스크를 추가합니다. 그런 다음 **Update-AzureVM** cmdlet을 사용하여 VM의 구성을 업데이트합니다.

또한 새 디스크를 연결할지 데이터를 포함하는 디스크를 연결할지를 결정해야 합니다. 새 디스크의 경우 이 명령은 .vhd 파일을 만들고 디스크를 연결합니다.

새 디스크를 연결하려면 다음 명령을 실행합니다.

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

기존 데이터 디스크를 연결하려면 다음 명령을 실행합니다.

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Blob 저장소의 기존 .vhd 파일에서 데이터 디스크를 연결하려면 다음 명령을 실행합니다.

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## Windows 기반 VM 만들기

Azure에서 새 Windows 기반 가상 컴퓨터를 만들려면 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터를 만들고 미리 구성](virtual-machines-windows-classic-create-powershell.md)의 지침을 사용하세요. 이 항목에서는 미리 구성할 수 있는 Windows VM을 만드는 Azure PowerShell 명령 집합 만들기를 단계별로 안내합니다.

- Active Directory 도메인 구성원 자격을 통해 만들기
- 추가 디스크를 통해 만들기
- 기존 부하 분산 집합의 구성원으로 만들기
- 고정 IP 주소로 만들기

<!---HONumber=AcomDC_0706_2016-->
