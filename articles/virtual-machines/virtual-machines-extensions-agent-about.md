<properties
 pageTitle="가상 컴퓨터 에이전트 및 확장 정보 | Microsoft Azure"
 description="에이전트 및 확장에 대한 개요와 에이전트 설치 방법을 제공합니다."
 services="virtual-machines"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="03/10/2015"
 ms.author="rasquill"/>
 
#가상 컴퓨터 에이전트 및 확장 정보
Azure 가상 컴퓨터 에이전트(VM 에이전트)는 Azure 가상 컴퓨터 확장(VM 확장)을 설치, 구성, 관리 및 실행하는 데 사용됩니다. VM 확장은 Microsoft 및 기타 타사가 제공하는 동적 기능을 구현합니다. 에이전트 및 확장은 주로 관리 포털을 통해 추가되지만 VM을 만들 때나 기존 VM에서 [Powershell](../install-configure-powershell.md) cmdlet 또는 [Azure CLI](xplat-install.md)를 사용하여 추가 및 구성할 수도 있습니다.

> [AZURE.NOTE]이 항목에서는 PowerShell 및 Azure CLI에 대해 설명하지만 리소스 관리자 배포 모델이 아니라 클래식 배포 모델에 사용되는 배포 호출을 참조합니다. 배포 모델에 대한 자세한 내용은 [Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)를 참조하세요.


VM 확장은 다음과 같은 작업에 도움이 될 수 있습니다.

-   보안 및 ID 기능 수정(예: 계정 값 재설정 및 맬웨어 방지 프로그램)
-   모니터링 및 진단 시작, 중지 또는 구성
-   연결 기능 재설정 또는 설치(예: RDP 및 SSH)
-   VM 진단, 모니터링 및 관리

그 밖에 다른 기능도 많으며 새로운 VM 확장 기능이 정기적으로 추가됩니다. 이 문서에서는 Windows 및 Linux용 Azure VM 에이전트와, 해당 에이전트의 VM 확장 기능 지원 방법에 대해 설명합니다. 기능 범주별 VM 확장 목록은 [Azure VM 확장 및 기능](virtual-machines-extensions-features.md)을 참조하세요.

##Windows 및 Linux 용 Azure VM 에이전트

Azure 가상 컴퓨터 에이전트(VM 에이전트)은 VM 에이전트가 설치된 경우 사용자 지정 VM 인스턴스와 이미지 갤러리에서 Azure 가상 컴퓨터의 인스턴스에 대한 VM 확장을 설치, 구성 및 제거하는 안전하고 가벼운 프로세스입니다. VM 에이전트는 Azure VM에 대해 안전한 로컬 제어 서비스 형태로 작동합니다. 에이전트가 로드하는 확장은 인스턴스를 사용한 생산성을 증대하기 위한 특정 기능을 제공합니다.

Windows VM용 에이전트와 Linux VM용 에이전트 등, 두 가지 Azure VM 에이전트가 있습니다. 기본적으로 VM 에이전트는 이미지 갤러리에서 VM을 만들 때 자동으로 설치되지만, 인스턴스를 만든 후에 VM 에이전트를 설치하거나 사용자 지정 VM 이미지에 설치한 다음 직접 업로드할 수 있습니다.

>[AZURE.IMPORTANT]이러한 VM 에이전트는 가상 컴퓨터 인스턴스의 보안 관리를 구현하는 매우 가벼운 서비스입니다. VM 에이전트를 원하지 않는 경우도 있을 수 있습니다. 이 경우에는 VM 에이전트가 설치되지 않는 VM을 만들어야 합니다. VM 에이전트를 물리적으로 제거할 수는 있지만 인스턴스에서 VM 확장의 동작은 정의되지 않은 상태입니다. 이 때문에 VM 에이전트를 설치 후 제거하는 것은 현재는 지원되지 않습니다.

VM 에이전트는 다음과 같은 상황에서 사용됩니다.

-   관리 포털에서 **Quick Create** 메서드를 사용하거나, 관리 포털에서 **Custom Create** 메서드를 사용하고 **VM 에이전트 설치** 확인란을 선택하여(아래 그림 참조) 가상 컴퓨터의 인스턴스를 만들 때. 자세한 내용은 [사용자 지정 가상 컴퓨터를 만드는 방법](virtual-machines-create-custom.md)을 참조하세요.

    ![VM 에이전트 확인란](./media/virtual-machines-extensions-agent-about/IC719409.png "VM 에이전트 확인란")

-   [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) 또는 [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) cmdlet을 사용하여 가상 컴퓨터의 인스턴스를 만들 때. **–DisableGuestAgent** 매개 변수를 [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) cmdlet에 추가하면 VM 에이전트 설치 없이 VM을 만들 수 있습니다.

-   Powershell 또는 REST 호출을 사용하여 기존 VM 인스턴스에 VM 에이전트(Windows 또는 Linux 버전)를 수동으로 다운로드 및 설치한 다음 **ProvisionGuestAgent** 값을 **true**로 설정할 수 있습니다. VM 에이전트를 수동으로 설치한 후 이 값을 설정하지 않으면 VM 에이전트의 추가가 제대로 검색되지 않습니다. 다음 코드 예제에서는 `$svc` 및 `$name` 인수가 이미 결정된 상황에서 PowerShell을 사용하여 이 작업을 수행하는 방법을 보여 줍니다.

        $vm = Get-AzureVM –serviceName $svc –Name $name
        $vm.VM.ProvisionGuestAgent = $TRUE
        Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

-   Azure에 업로드하기 전에 VM 에이전트가 설치된 VM 에이전트 만들기. Windows VM의 경우 [Windows VM 에이전트 .msi 파일](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다운로드하여 VM 에이전트를 설치합니다. Linux VM의 경우 <https://github.com/Azure/WALinuxAgent>에 있는 github 리포지토리에서 설치합니다. Linux에서 VM 에이전트를 설치하는 방법에 대한 자세한 내용은 [Azure Linux VM 에이전트 사용자 가이드](virtual-machines-linux-agent-user-guide.md)를 참조하세요.

>[AZURE.NOTE]PaaS에서는 VM 에이전트를 **GuestAgent**라고 하며 항상 웹 및 작업자 역할 VM에서 사용할 수 있습니다. 자세한 내용은 [Azure 역할 아키텍처](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx)를 참조하세요. 역할 VM에 대한 VM 에이전트는 이제 영구 가상 컴퓨터와 동일한 방식으로 클라우드 서비스 VM에 확장을 추가할 수 있습니다. 역할 VM에서의 VM 확장과 영구 VM 간의 가장 큰 차이는, 역할 VM에서는 확장이 먼저 클라우드 서비스에 추가된 다음 해당 클라우드 서비스 내 배포에 추가된다는 점입니다.

>사용 가능한 모든 역할 VM 확장을 나열하려면 [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet을 사용합니다.

##VM 확장 찾기, 추가, 업데이트 및 제거  

이러한 작업에 대한 자세한 내용은 [Azure VM 확장 추가, 찾기, 업데이트 및 제거](virtual-machines-extensions-install.md)를 참조하세요.

<!---HONumber=Sept15_HO2-->