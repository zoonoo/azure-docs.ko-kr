---
title: Vm에 대한 공통 PowerShell 명령 | Microsoft Docs
description: Microsoft Azure에서 Vm 만들기 및 관리를 시작하기 위한 공통 PowerShell 명령
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/07/2016
ms.author: davidmu

---
# Vm을 만들고 관리하기 위한 공통 PowerShell 명령
이 문서에서는 Azure 구독에서 가상 컴퓨터를 만들고 관리하는 데 사용할 수 있는 몇 가지 Azure PowerShell 명령을 다룹니다. 특정 명령줄 스위치 및 옵션에 대해 자세한 도움이 필요할 경우 **Get-Help** *명령*을 사용할 수 있습니다.

## Azure PowerShell을 사용하여 리소스 만들기
최신 버전의 Azure PowerShell을 설치하는 방법, 사용할 구독을 선택하는 방법, Azure 계정에 로그인하는 방법은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

| 작업 | 명령 |
| --- | --- |
| VM 구성 만들기 |$vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "vm\_name" -VMSize "vm\_size"<BR></BR><BR></BR>VM 구성은 VM에 대한 설정을 정의하거나 업데이트하는 데 사용 됩니다. 구성은 VM의 이름 및 [크기](virtual-machines-windows-sizes.md)를 사용하여 초기화합니다. |
| 구성 설정 추가 |$vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "computer\_name" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>[자격 증명](https://technet.microsoft.com/library/hh849815.aspx)을 포함한 운영 체제 설정은 New-AzureRmVMConfig를 사용하여 이전에 만든 구성 개체에 추가됩니다. |
| 네트워크 인터페이스 추가 |$vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM에는 가상 네트워크에서 통신할 [네트워크 인터페이스](virtual-machines-windows-ps-create.md)가 있어야 합니다. 또한 기존 네트워크 인터페이스 개체를 검색하는 데 [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx)를 사용할 수도 있습니다. |
| 플랫폼 이미지 지정 |$vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "publisher\_name" -Offer "publisher\_offer" -Skus "product\_sku" -Version "latest"<BR></BR><BR></BR>[이미지 정보 ](virtual-machines-windows-cli-ps-findimage.md)는 New-AzureRmVMConfig 사용하여 이전에 만든 구성 개체에 추가됩니다. 이 명령에서 반환되는 개체는 플랫폼 이미지를 사용하도록 OS 디스크를 설정할 때에만 사용됩니다. |
| 플랫폼 이미지를 사용하도록 OS 디스크 설정 |$vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "disk\_name" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>운영 체제 디스크의 이름과 [저장소](../storage/storage-powershell-guide-full.md)에서의 위치는 이전에 만든 구성 개체에 추가됩니다. |
| 일반화된 이미지를 사용하도록 OS 디스크 설정 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "disk\_name" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>운영 체제 디스크의 이름, 원본 이미지의 위치 및 [저장소](../storage/storage-powershell-guide-full.md)에서 디스크가 배치된 위치는 이전에 만든 구성 개체에 추가됩니다. |
| 특수 이미지를 사용하도록 OS 디스크 설정 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "name\_of\_disk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| VM 만들기 |[New-AzureRmVM]() -ResourceGroupName "resource\_group\_name" -Location "location\_name" -VM $vm<BR></BR><BR></BR>모든 리소스는 [리소스 그룹](../powershell-azure-resource-manager.md)에서 만듭니다. VM을 리소스 그룹과 동일한 [위치](https://msdn.microsoft.com/library/azure/dn495177.aspx)에 캐시를 만듭니다. 이 명령을 실행하기 전에, New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface, and Set-AzureRmVMOSDisk을 실행합니다. |
| 구독에서 Vm 나열 |[Get AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx) |
| 리소스 그룹에서 Vm 나열 |Get-AzureRmVM -ResourceGroupName "resource\_group\_name"<BR></BR><BR></BR>구독에서 리소스 그룹 목록을 가져오려면 [Get AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx)를 사용합니다. |
| VM에 대한 정보 가져오기 |Get-AzureRmVM -ResourceGroupName "resource\_group\_name" -Name "vm\_name" |
| VM 시작 |[Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name" |
| VM 중지 |[Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name" |
| 실행 중인 VM 다시 시작 |[Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name" |
| VM 삭제 |[Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name" |
| VM 일반화 |[Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "vm\_name" -Generalized<BR></BR><BR></BR>이 명령을 Save-AzureRmVMImage을 실행하기 전에 실행해야 합니다. |
| VM 캡처 |[Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "resource\_group\_name" -VMName "vm\_name" -DestinationContainerName "image\_container" -VHDNamePrefix "image\_name\_prefix" -Path "C:\\filepath\\filename.json"<BR></BR><BR></BR>가상 컴퓨터를 이미지를 만드는 데 사용하려면 [종료 및 일반화](virtual-machines-windows-capture-image.md)해야 합니다. 이 명령을 실행하기 전에 Set-AzureRmVm을 실행합니다. |
| VM 업데이트 |[Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "resource\_group\_name" -VM $vm<BR></BR><BR></BR>Get-AzureRmVM을 사용하 여 현재 VM 구성을 가져오고, VM 개체에서 구성 설정을 변경한 후, 이 명령을 실행합니다. |
| VM에 데이터 디스크 추가 |[Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "disk\_name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Get-AzureRmVM을 사용하여 VM 개체를 가져오기 합니다. LUN 번호 및 디스크 크기를 지정합니다. VM에 구성 변경을 적용하려면 Update-AzureRmVM을 실행합니다. 추가한 디스크가 초기화되지 않았습니다. 초기화하려면 [리소스 관리자 및 PowerShell을 사용하여 Azure 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 참조하세요. |
| VM에서 데이터 디스크 제거 |[Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "disk\_name"<BR></BR><BR></BR>VM 개체를 가져오려면 Get-AzureRmVM을 사용합니다. VM에 구성 변경을 적용하려면 Update-AzureRmVM을 실행합니다. |
| VM에 확장 추가 |[Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "resource\_group\_name" -Location "azure\_location" -VMName "vm\_name" -Name "extension\_name" -Publisher "publisher\_name" -Type "extension\_type" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>설치하려는 확장에 대한 해당 [구성 정보](virtual-machines-windows-extensions-configuration-samples.md)를 사용하여 이 명령을 실행합니다. |
| VM 확장 제거 |[Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "resource\_group\_name" -Name "extension\_name" -VMName "vm\_name" |

## 다음 단계
* [리소스 관리자 및 PowerShell을 사용하여 Windows VM 만들기](virtual-machines-windows-ps-create.md)에서 가상 컴퓨터 만들기 기본 단계를 참조합니다.

<!---HONumber=AcomDC_0629_2016-->