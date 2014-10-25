<properties title="How to install and configure Symantec Endpoint Protection on an Azure VM" pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM" description="Describes installing and configuring Symantec Endpoint Protection on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Azure VM에서 Symantec Endpoint Protection을 설치하고 구성하는 방법

이 문서에서는 Windows Server가 실행되는 새 VM(가상 컴퓨터) 또는 기존 VM에서 Symantec Endpoint Protection 클라이언트를 설치하고 구성하는 방법을 보여 줍니다. 이 제품은 바이러스 및 스파이웨어 보호, 방화벽, 침입 방지와 같은 서비스를 포함하는 전체 클라이언트입니다.

이 클라이언트는 VM 에이전트를 사용하여 보안 확장 프로그램으로 설치됩니다. 새 가상 컴퓨터에서 끝점 클라이언트와 함께 이 에이전트를 설치합니다. 이 에이전트가 없는 기존 가상 컴퓨터에서는 에이전트를 먼저 다운로드하여 설치해야 합니다. 이 문서에서는 두 상황을 모두 다룹니다.

온-프레미스 솔루션에 대한 Symantec의 기존 구독이 있는 경우 이를 사용하여 Azure 가상 컴퓨터를 보호할 수 있습니다.<for an on-premises solution, you can use it to protect your azure virtual machines> 아직 고객이 아닌 경우에는 평가판 구독에 등록할 수 있습니다. 이 솔루션에 대한 자세한 내용은 [Microsoft Azure 플랫폼의 Symantec Endpoint Protection][Microsoft Azure 플랫폼의 Symantec Endpoint Protection](영문)을 참조하세요. 이 페이지에는 Symantec 고객을 위한 라이선스 정보 및 이 클라이언트를 설치하는 다른 방법에 대한 링크도 제공되어 있습니다.

## 새 가상 컴퓨터에 Symantec Endpoint Protection 설치

**갤러리에서** 옵션을 사용하여 가상 컴퓨터를 만들 경우 [Azure 관리 포털][Azure 관리 포털]을 사용하여 VM 에이전트 및 Symantec 보안 확장을 설치할 수 있습니다. 이 방법을 사용하면 단일 가상 컴퓨터를 만들 때 Symantec의 보호 기능을 쉽게 추가할 수 있습니다.

이 **갤러리에서** 옵션은 가상 컴퓨터 설치에 도움이 되는 마법사를 엽니다. 마법사 마지막 페이지에서 VM 에이전트 및 Symantec 보안 확장을 설치합니다.

일반적인 지침은 [Windows Server를 실행하는 가상 컴퓨터 만들기][Windows Server를 실행하는 가상 컴퓨터 만들기]를 참조하세요. 마법사 마지막 페이지가 되면 다음을 수행합니다.

1.  VM 에이전트에서 **VM 에이전트 설치**를 선택합니다.

2.  보안 확장에서 **Symantec Endpoint Protection**을 선택합니다.

    ![VM 에이전트 및 Endpoint Protection Client 설치][VM 에이전트 및 Endpoint Protection Client 설치]

3.  페이지 아래쪽에 있는 확인 표시를 클릭하여 가상 컴퓨터를 만듭니다.

## 기존 가상 컴퓨터에 Symantec Endpoint Protection 설치

기존 가상 컴퓨터에 Deep Security Agent를 설치하려면 다음이 필요합니다.

-   Azure PowerShell 모듈, 버전 0.8.2 이상. 지침 및 최신 버전으로 연결되는 링크를 보려면 [Azure PowerShell을 설치 및 구성하는 방법][Azure PowerShell을 설치 및 구성하는 방법]을 참조하세요.

-   VM 에이전트. 지침 및 다운로드 링크를 보려면 블로그 게시물 [VM 에이전트 및 확장 - 2부][VM 에이전트 및 확장 - 2부](영문)를 참조하세요.

Azure PowerShell 세션을 열고 다음 명령을 실행합니다. MyServiceName과 같은 자리 표시자를 자체 값으로 바꿉니다.

1.  클라우드 서비스 이름, 가상 컴퓨터 이름 및 VM을 가져온 후 다음 명령에서 사용될 수 있게 각 이름을 변수에 저장합니다.

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] 클라우드 서비스 및 VM 이름을 모르는 경우 Get-AzureVM을 실행하여 현재 구독의 모든 VM에 대한 해당 정보를 표시합니다.

2.  가상 컴퓨터에 Symantec Endpoint Protection Agent를 추가합니다. 최신 버전을 설치하려면 다음을 실행합니다.

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] 특정 버전을 설치하려면 다음 명령을 실행하여 사용 가능한 버전 목록을 표시합니다. `Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`.
    > 그런 후 Set-AzureVMExtension을 실행할 때 Version 매개 변수를 포함합니다.

3.  VM을 업데이트합니다. 그러면 Symantec Endpoint Protection Agent가 설치됩니다.

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## 추가 리소스

[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법][Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]

[확장 관리][확장 관리]

<!--Link references-->

  [Microsoft Azure 플랫폼의 Symantec Endpoint Protection]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [Azure 관리 포털]: http://manage.windowsazure.com
  [Windows Server를 실행하는 가상 컴퓨터 만들기]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [VM 에이전트 및 Endpoint Protection Client 설치]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [Azure PowerShell을 설치 및 구성하는 방법]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM 에이전트 및 확장 - 2부]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]: ../virtual-machines-log-on-windows-server/
  [확장 관리]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
