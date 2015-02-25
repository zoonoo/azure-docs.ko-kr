<properties pageTitle="가상 컴퓨터의 가용성 집합을 구성하는 방법" description="Azure에서 VM에 대해 가용성 집합을 구성하는 단계에 대해 설명합니다." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="kathydav"/>

#가상 컴퓨터의 가용성 집합을 구성하는 방법#


가용성 집합을 사용하면 유지 관리 중과 같은 가동 중지 시간 동안에도 가상 컴퓨터를 계속 사용할 수 있습니다. 가용성 집합 내에 비슷하게 구성된 둘 이상의 가상 컴퓨터를 배치하면 가상 컴퓨터가 실행하는 서비스나 응용 프로그램의 가용성을 유지하는 데 필요한 중복성이 생성됩니다. 이 기능이 작동하는 방법에 대한 자세한 내용은 [가상 컴퓨터의 가용성 관리][]를 참조하세요. 

응용 프로그램을 항상 사용 가능한 상태로 유지하고 효과적으로 실행하려면 가용성 집합과 부하 분산 끝점을 함께 사용하는 것이 가장 좋습니다. 부하 분산 끝점에 대한 자세한 내용은 [Azure 인프라 서비스의 부하 분산][]을 참조하세요.

다음 두 옵션 중 하나를 사용하여 가상 컴퓨터를 가용성 집합에 배치할 수 있습니다.

- [옵션 1: 가상 컴퓨터와 가용성 집합을 동시에 만듭니다][]. 그런 다음 새 가상 컴퓨터를 만들 때 집합에 가상 컴퓨터를 추가합니다.
- [옵션 2: 기존 가상 컴퓨터를 가용성 집합에 추가합니다][].


>[AZURE.NOTE] 같은 가용성 집합에 배치할 가상 컴퓨터는 같은 클라우드 서비스에 속해야 합니다.   

## <a id="createset"> </a>옵션 1: 가상 컴퓨터와 가용성 집합을 동시에 만들기##

관리 포털 또는 Azure PowerShell cmdlet을 사용하여 이 작업을 수행할 수 있습니다. 

관리 포털을 사용하려면

1. 아직 로그인하지 않은 경우 [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. 명령 모음에서 **새로 만들기**를 클릭합니다.

3. **가상 컴퓨터**를 클릭한 후 **갤러리에서**를 클릭합니다.

4. 처음 두 화면에서 이미지, 사용자 이름, 암호 등을 선택합니다. 자세한 내용은 [Windows를 실행하는 가상 컴퓨터 만들기][]를 참조하세요.
 
5. 세 번째 화면에서는 네트워킹, 저장소 및 가용성에 대한 리소스를 구성할 수 있습니다. 다음을 수행합니다.
	 
	1. 클라우드 서비스로 적합한 항목을 선택합니다. 기존 VM 클라우드 서비스에 이 새 가상 컴퓨터를 추가하는 경우가 아니면 옵션을 **새 클라우드 서비스 만들기**로 설정해 둡니다. 그런 다음 **클라우드 서비스 DNS 이름** 아래에 이름을 입력합니다. DNS 이름은 가상 컴퓨터에 연결하는 데 사용되는 URI의 일부가 됩니다. 클라우드 서비스는 통신 및 격리 그룹으로 작동합니다. 같은 클라우드 서비스의 모든 가상 컴퓨터는 서로 통신할 수 있고 부하 분산용으로 설정할 수 있으며 같은 가용성 집합에 배치할 수 있습니다. 

	2. 가상 네트워크를 사용하려는 경우 **하위 지역/선호도 그룹/가상 네트워크**에서 가상 네트워크를 지정합니다. **중요**: 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 가상 컴퓨터를 만들 때 VM을 가상 네트워크에 가입시켜야 합니다. VM을 만든 후에는 가상 컴퓨터를 가상 네트워크에 가입할 수 없습니다. 자세한 내용은 [Azure 가상 네트워크 개요][]를 참조하세요. 
	
	3. 가용성 집합을 만듭니다. **가용성 집합**에서 옵션을 **가용성 집합 만들기**로 설정해 두고 집합의 이름을 입력합니다. 
	4. 기본 끝점을 만들고 필요하면 끝점을 더 추가합니다. 나중에 끝점을 추가할 수도 있습니다. 

	![Create an availabililty set for a new VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png) 

6. 네 번째 화면에서 설치할 확장을 선택합니다. 확장은 맬웨어 방지 프로그램 실행, 암호 다시 설정 등 가상 컴퓨터를 보다 쉽게 관리할 수 있는 기능을 제공합니다. 자세한 내용은 [Azure VM 에이전트 및 VM 확장](http://go.microsoft.com/fwlink/p/?LinkId=XXX)을 참조하세요.

7.	화살표를 클릭하여 가상 컴퓨터와 가용성 집합을 만듭니다.

	새 가상 컴퓨터의 대시보드에서 **구성**을 클릭하면 가상 컴퓨터가 새 가용성 집합에 속함을 확인할 수 있습니다.

Azure cmdlet을 사용하려면

1.	Azure PowerShell 세션을 열고 다음 예제와 같은 명령을 실행합니다. 이러한 예제에서는 가상 컴퓨터, 클라우드 서비스 및 가용성 집합을 만든다고 가정합니다.

2.	가상 컴퓨터를 만드는 데 사용할 이미지의 이름을 가져와 변수에 저장합니다. 다음 명령 예제는 인덱스 번호를 사용하여 이미지 개체의 ImageName 속성을 가져옵니다. 사용자가 원하는 이미지의 현재 인덱스 번호를 알고 있고 &lt;index_number&gt;에 해당 번호를 사용할 것으로 가정합니다. <br>

	`C:\PS> $image = (Get-AzureVMImage)[<index_number>].ImageName`

	>[AZURE.NOTE] 구독에 적용되는 모든 이미지 목록을 가져오려면 매개 변수 없이  `Get-AzureVMImage`를 실행합니다. 이렇게 하면 큰 목록이 반환될 수 있습니다. 이 목록을 간단히 줄이려면 이미지 패밀리 이름과 같은 속성을 사용합니다. 이 작업을 수행하여 특정 이미지를 찾는 방법을 보여 주는 팁 및 예제를 보려면 [Windows PowerShell을 사용하여 이미지 관리](http://msdn.microsoft.com/ko-kr/library/azure/dn790330.aspx)를 참조하세요.

3.	새 가상 컴퓨터의 구성을 지정한 다음 파이프라인을 사용하여 VM을 만드는 cmdlet에 구성 개체를 전달합니다. &lt;VmName&gt; 및 &lt;VmSize&gt;와 같은 자리 표시자는 원하는 값으로 바꿉니다.

	`C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM -ServiceName "<MySvc1>" `

## <a id="addmachine"> </a>옵션 2: 기존 가상 컴퓨터를 가용성 집합에 추가##

관리 포털에서 기존 가상 컴퓨터를 기존 가용성 집합에 추가하거나 가상 컴퓨터용으로 새 가용성 집합을 만들 수 있습니다. 가상 컴퓨터는 같은 클라우드 서비스에 속해야 합니다. 이 옵션에서도 단계는 거의 동일합니다. Azure PowerShell에서 기존 가용성 집합에 가상 컴퓨터를 추가할 수 있습니다. 

1. 아직 로그인하지 않은 경우 [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2. 탐색 모음에서 **가상 컴퓨터**를 클릭합니다.

3. 가상 컴퓨터 목록에서 집합에 추가할 가상 컴퓨터 중 하나를 선택합니다. 그런 다음 가상 컴퓨터의 행을 클릭하여 대시보드를 엽니다.

4. 가상 컴퓨터 이름 아래의 탭에서 **구성**을 클릭합니다. 

5. 설정 섹션에서 **가용성 집합**을 찾습니다. 다음 중 하나를 수행합니다.

	A. **가용성 집합 만들기**를 선택하고 집합의 이름을 입력합니다.

	B. **가용성 집합 선택**을 선택하고 목록에서 집합을 선택합니다.

	![Create an availabililty set for an existing VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png) 

6. **저장**을 클릭합니다.

Azure cmdlet을 사용하려면

Azure PowerShell 세션을 열고 다음 명령을 실행합니다. &lt;VmCloudServiceName&gt; 및 &lt;VmName&gt;와 같은 자리 표시자는 원하는 값으로 바꿉니다.

	C:\PS> Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<MyAvSet>" | Update-AzureVM

>[AZURE.NOTE] 가용성 집합에 추가하는 작업을 완료하기 위해 가상 컴퓨터가 다시 시작될 수 있습니다.


##추가 리소스
[Azure VM 구성 설정 정보]

[옵션 1: 가상 컴퓨터와 가용성 집합을 동시에 만듭니다]: #createset
[옵션 2: 기존 가상 컴퓨터를 가용성 집합에 추가합니다]: #addmachine

<!-- LINKS -->
[Azure 인프라 서비스의 부하 분산]: ../virtual-machines-load-balance
[가상 컴퓨터의 가용성 관리]: ../virtual-machines-manage-availability
[Windows를 실행하는 가상 컴퓨터 만들기]: ../virtual-machines-windows-tutorial
[Azure 가상 네트워크 개요]: http://go.microsoft.com/fwlink/p/?linkid=294063
[가상 네트워크의 선호도 그룹 정보]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
[클라우드 서비스에서 가상 컴퓨터를 연결하는 방법]: ../virtual-machines-connect-cloud-service
[Azure VM 구성 설정 정보]: http://msdn.microsoft.com/ko-kr/library/azure/dn763935.aspx



<!--HONumber=42-->
