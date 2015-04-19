<properties 
	pageTitle="Azure PowerShell을 사용하여 Linux 기반 가상 컴퓨터 만들기 및 미리 구성" 
	description="Azure PowerShell을 사용하여 Azure에서 Linux 기반 가상 컴퓨터를 만들고 미리 구성하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="josephd"/>

# Azure PowerShell을 사용하여 Linux 기반 가상 컴퓨터 만들기 및 미리 구성

다음 단계에서는 구성 요소 접근 방식을 사용하여 Linux 기반 Azure 가상 컴퓨터를 만들고 미리 구성하는 Azure PowerShell 명령 집합을 사용자 지정하는 방법을 보여 줍니다. 이 프로세스를 사용하여 새 Linux 기반 가상 컴퓨터에 대한 명령 집합을 신속하게 만들고 기존 배포를 확장하거나, 사용자 지정 개발/테스트 또는 IT 전문가 환경을 신속하게 빌드하는 여러 명령 집합을 만들 수 있습니다.

다음 단계에서는 빈 칸 채우기 접근 방식에 따라 Azure PowerShell 명령 집합을 만듭니다. 이 접근 방식은 PowerShell을 처음 접하거나 성공적인 구성을 위해 지정할 값만 알기를 원하는 경우에 유용할 수 있습니다. 고급 PowerShell 사용자는 명령을 가져와 고유한 변수 값("$"로 시작하는 줄)을 대체할 수 있습니다.

Windows 기반 가상 컴퓨터에 대한 관련 항목은 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md)을 참조하세요.

## 1단계: Azure PowerShell 설치

[Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다(아직 설치하지 않은 경우). 그런 다음 Azure PowerShell 명령 프롬프트를 엽니다.

## 2단계: 구독 및 저장소 계정 설정

Azure PowerShell 명령 프롬프트에서 다음 명령을 실행하여 Azure 구독 및 저장소 계정을 설정합니다. < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr -Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

**Get-AzureSubscription** 명령의 출력에 표시된 SubscriptionName 속성에서 올바른 구독 이름을 가져올 수 있습니다. **Select-AzureSubscription** 명령을 실행한 후 **Get-AzureStorageAccount** 명령의 출력에 표시된 Label 속성에서 올바른 저장소 계정 이름을 가져올 수 있습니다. 또한 이러한 명령을 텍스트 파일에 저장하여 나중에 사용할 수 있습니다.

## 3단계: ImageFamily 확인

이제 만들려는 Azure 가상 컴퓨터에 해당하는 특정 이미지에 대한 ImageFamily 값을 확인해야 합니다. 다음 명령을 사용하여 사용 가능한 ImageFamily 값 목록을 가져올 수 있습니다.

	Get-AzureVMImage | select ImageFamily -Unique

다음은 Linux 기반 컴퓨터의 ImageFamily 값에 대한 몇 가지 예입니다.

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

선택한 텍스트 편집기의 새 인스턴스를 열고 ImageFamily 값을 대체하여 다음을 새 텍스트 파일에 복사합니다.
 
	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## 4단계: 명령 집합 작성

아래에서 해당 블록 집합을 새 텍스트 파일에 복사한 다음 변수 값을 입력하고 < 및 > 문자를 제거하여 나머지 명령 집합을 작성합니다. 최종 결과에 대한 아이디어는 이 문서의 끝에 있는 두 가지 [예제](#examples)를 참조하세요.

다음 두 명령 블록 중 하나를 선택하여 명령 집합을 시작합니다(필수).

옵션 1: 가상 컴퓨터 이름 및 크기를 지정합니다.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

옵션 2: 이름, 크기 및 가용성 집합 이름을 지정합니다.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

D 시리즈, DS 시리즈 또는 G 시리즈 가상 컴퓨터의 InstanceSize 값은 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](https://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요.

초기 Linux 사용자 이름 및 암호를 지정합니다(필수). 강력한 암호를 선택합니다. 암호의 강도를 확인하려면 [암호 검사기: 강력한 암호 사용](https://www.microsoft.com/security/pc-security/password-checker.aspx)을 참조하세요.

	$username="<user account name>"
	$pass="<user account password>"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

결과 명령 집합을 파일로 저장하려면 안전한 위치에 저장하여 계정 이름과 암호를 보호해야 합니다.

선택적으로 구독에 이미 배포된 SSH 키 쌍 집합을 지정합니다.

	$vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-use-ssh-key.md)을 참조하세요.

선택적으로 구독에 이미 배포된 SSH 공개 키 쌍 목록을 지정합니다.

	$vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Linux 기반 가상 컴퓨터에 대한 추가 사전 구성 옵션은 [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx)에서 **Linux** 매개 변수 집합에 대한 구문을 참조하세요.

선택적으로 가상 컴퓨터에 특정 IP 주소(고정 DIP라고 함)를 할당합니다.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

특정 IP 주소를 사용할 수 있는지 확인할 수 있습니다.

	Test-AzureStaticVNetIP -VNetName <VNet name> -IPAddress <IP address>

선택적으로 Azure 가상 네트워크의 특정 서브넷에 가상 컴퓨터를 할당합니다.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

선택적으로 가상 컴퓨터에 단일 데이터 디스크를 추가합니다. 

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

선택적으로 외부 트래픽에 대한 기존 부하 분산된 집합에 가상 컴퓨터를 추가합니다.

	$prot="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, udp>"
	$probeport=<TCP or UDP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

마지막으로 다음 명령 블록 중 하나를 선택하여 가상 컴퓨터 만들기 프로세스를 시작합니다(필수).

옵션 1: 새 클라우드 서비스에서 가상 컴퓨터를 만듭니다. 

	New-AzureVM -Location "<An Azure location, such as US West>" -VMs $vm1

다음을 사용하여 Azure 위치 목록을 가져올 수 있습니다.

	Get-AzureLocation | Select DisplayName

옵션 2: 기존 클라우드 서비스에서 가상 컴퓨터를 만듭니다. 

	New-AzureVM -ServiceName "<short name of the cloud service>" -VMs $vm1

클라우드 서비스의 짧은 이름은 Azure 관리 포털의 클라우드 서비스 목록에 표시된 이름 또는 Azure 미리 보기 포털의 리소스 그룹 목록에 표시된 이름입니다. 

옵션 3: 기존 클라우드 서비스 및 가상 네트워크에서 가상 컴퓨터를 만듭니다.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## 5단계: 명령 집합 실행

텍스트 편집기에서 작성한 Azure PowerShell 명령 집합(4단계의 여러 명령 블록으로 구성)을 검토합니다. 필요한 모든 변수를 지정하고 해당 변수에 올바른 값이 있는지 확인합니다. 또한 < 및 > 문자를 모두 제거했는지 확인합니다.

클립보드에 명령 집합을 복사한 다음 열려 있는 Azure PowerShell 명령 프롬프트를 마우스 오른쪽 버튼으로 클릭합니다. 그러면 명령 집합이 일련의 PowerShell 명령으로 실행되고 Azure 가상 컴퓨터가 만들어집니다. 잘못된 구독, 저장소 계정, 클라우드 서비스, 가용성 집합, 가상 네트워크 또는 서브넷에서 가상 컴퓨터를 만든 경우 가상 컴퓨터를 삭제하고 명령 블록 구문을 수정한 다음 수정된 명령 집합을 실행합니다. 

가상 컴퓨터를 만든 후 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)을 참조하세요. 

이 가상 컴퓨터 또는 이와 유사한 가상 컴퓨터를 다시 만들려는 경우 다음과 같이 할 수 있습니다. 

- 이 명령 집합을 텍스트 파일 또는 PowerShell 스크립트 파일(*.ps1)로 저장
- Azure 관리 포털의 **자동화** 섹션에서 이 명령을 Azure 자동화 Runbook으로 저장 

## <a id="examples"></a>예제

다음은 위 단계를 사용하여 Azure에서 Linux 기반 가상 컴퓨터를 만드는 Azure PowerShell 명령 집합을 작성하는 두 가지 예제입니다.

### 예제 1

다음과 같은 MySQL Server용 Linux 가상 컴퓨터를 처음 만드는 데 사용할 수 있는 PowerShell 명령 집합이 필요합니다.

- Ubuntu Server 12.10 이미지를 사용함
- 이름이 AZMYSQL1임 
- 500GB의 추가 데이터 디스크가 있음
- 고정 IP 주소가 192.168.244.4임
- AZDatacenter 가상 네트워크의 BackEnd 서브넷에 있음
- Azure-TailspinToys 클라우드 서비스에 있음

다음은 이 가상 컴퓨터를 만드는 해당 Azure PowerShell 명령 집합입니다. 각 블록 사이의 빈 줄은 가독성을 위해 추가된 것입니다.

	$family="Ubuntu Server 12.10"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="AZMYSQL1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin397A"
	$pass="3A#q291{Y"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=500
	$disklabel="MySQLData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### 예제 2

다음과 같은 Apache 서버용 Linux 가상 컴퓨터를 만드는 데 사용할 수 있는 PowerShell 명령 집합이 필요합니다.

- SUSE Linux Enterprise Server 12 이미지를 사용함
- 이름이 LOB1임
- 50GB의 추가 데이터 디스크가 있음 
- 표준 웹 트래픽에 대한 LOBServers 부하 분산 장치 집합의 멤버임
- AZDatacenter 가상 네트워크의 FrontEnd 서브넷에 있음
- Azure-TailspinToys 클라우드 서비스에 있음

다음은 이 가상 컴퓨터를 만드는 해당 Azure PowerShell 명령 집합입니다.

	$family="SUSE Linux Enterprise Server 12"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vmname="LOB1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$username="Admin261Z"
	$pass="9Z2:3Wqp1~"
	$vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $username -Password $pass

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=50
	$disklabel="LOBApp"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$prot="tcp"
	$localport=80
	$pubport=80
	$endpointname="LOB1"
	$lbsetname="LOBServers"
	$probeprotocol="tcp"
	$probeport=80
	$probepath="/"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM -ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## 추가 리소스

[가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure 가상 컴퓨터 FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Azure 가상 컴퓨터 개요](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)

[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-how-to-log-on.md)

[Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md)


<!--HONumber=47-->
