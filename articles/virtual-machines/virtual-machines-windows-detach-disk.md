<properties
	pageTitle="Windows VM에서 데이터 디스크 분리 | Microsoft Azure"
	description="Resource Manager 배포 모델을 사용하여 Azure의 가상 컴퓨터에서 데이터 디스크를 분리하는 방법을 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>



# Windows 가상 컴퓨터에서 데이터 디스크를 분리하는 방법


가상 컴퓨터에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 가상 컴퓨터에서 디스크가 제거되지만, 저장소에서는 제거되지 않습니다.

> [AZURE.WARNING] 디스크를 분리해도 자동으로 삭제되지 않습니다. 프리미엄 저장소를 구독하는 경우 디스크에 대한 저장소 요금이 계속 부과됩니다. 자세한 내용은 [프리미엄 저장소 사용 시 가격 책정 및 청구](../storage/storage-premium-storage.md#pricing-and-billing)를 참조하세요.

디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 가상 컴퓨터에 다시 연결할 수 있습니다.


## 포털을 사용하여 데이터 디스크 분리

1. 포털 허브에서 **가상 컴퓨터**를 선택합니다.

2. 분리할 데이터 디스크가 있는 가상 컴퓨터를 선택하고 **모든 설정**을 클릭합니다.

3. **설정** 블레이드에서 **디스크**를 선택합니다.

4. **디스크** 블레이드에서 분리하려는 데이터 디스크를 선택합니다.

5. 데이터 디스크에 대한 블레이드에서 **분리**를 클릭합니다.


	![분리 단추를 표시하는 스크린샷](./media/virtual-machines-windows-detach-disk/detach-disk.png)

디스크가 저장소에 유지되지만 더 이상 가상 컴퓨터에 연결되어 있지 않습니다.


## PowerShell을 사용하여 데이터 디스크 분리

이 예제에서 첫 번째 명령은 Get-AzureRmVM cmdlet을 사용하여 **RG11** 리소스 그룹에 가상 컴퓨터 **MyVM07**을 가져옵니다. 이 명령은 가상 컴퓨터를 **$VirtualMachine** 변수에 저장합니다.

두 번째 명령은 가상 컴퓨터에서 DataDisk3이라는 데이터 디스크를 제거합니다.

마지막 명령은 가상 컴퓨터의 상태를 업데이트하여 데이터 디스크 제거 프로세스를 완료합니다.

	$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
	Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
	Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


자세한 내용은 [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)를 참조하세요.

## 다음 단계

데이터 디스크를 다시 사용하려는 경우 [다른 VM에 연결](virtual-machines-windows-attach-disk-portal.md)하기만 하면 됩니다.

<!---HONumber=AcomDC_0928_2016-->