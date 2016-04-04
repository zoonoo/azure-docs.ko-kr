


가용성 집합을 사용하면 유지 관리 중과 같은 가동 중지 시간 동안에도 가상 컴퓨터를 계속 사용할 수 있습니다. 가용성 집합 내에 비슷하게 구성된 둘 이상의 가상 컴퓨터를 배치하면 가상 컴퓨터가 실행하는 서비스나 응용 프로그램의 가용성을 유지하는 데 필요한 중복성이 생성됩니다. 이 기능이 작동하는 방식에 대한 자세한 내용은 [가상 컴퓨터의 가용성 관리][]를 참조하세요.

응용 프로그램을 항상 사용 가능한 상태로 유지하고 효과적으로 실행하려면 가용성 집합과 부하 분산 끝점을 함께 사용하는 것이 가장 좋습니다. 부하 분산 끝점에 대한 자세한 내용은 [Azure 인프라 서비스를 위한 부하 분산][]을 참조하세요.

클래식 배포 모델에서 다음 두 옵션 중 하나를 사용하여 가상 컴퓨터를 가용성 집합에 배치할 수 있습니다.

- [옵션 1: 가상 컴퓨터와 가용성 집합을 동시에 만듭니다.][] 그런 다음 새 가상 컴퓨터를 만들 때 집합에 가상 컴퓨터를 추가합니다.
- [옵션 2: 기존 가상 컴퓨터를 가용성 집합에 추가합니다.][]

>[AZURE.NOTE] 클래식 모델에서는 같은 가용성 집합에 배치할 가상 컴퓨터는 같은 클라우드 서비스에 속해야 합니다.

## <a id="createset"> </a>옵션 1: 동시에 가상 컴퓨터 및 가용성 집합 만들기##

Azure 클래식 포털 또는 Azure PowerShell 명령을 사용하여 다음을 수행할 수 있습니다.

Azure 클래식 포털을 사용하기

1. 아직 로그인하지 않은 경우, Azure 클래식 포털에 로그인합니다.

2. 명령 모음에서 **New**를 클릭합니다.

3. **Virtual Machine**을 클릭한 후 **From Gallery**를 클릭합니다.

4. 처음 두 화면에서 이미지, 사용자 이름 및 암호 등을 선택합니다. 자세한 내용은 [Windows를 실행하는 가상 컴퓨터 만들기][]를 참조하세요.

5. 세 번째 화면에서는 네트워킹, 저장소 및 가용성에 대한 리소스를 구성할 수 있습니다. 다음을 수행합니다.

	1. 적합한 클라우드 서비스를 선택합니다. 기존 가상 컴퓨터 클라우드 서비스에 이 새로운 가상 컴퓨터를 추가하지 않는 경우 옵션을 **새 클라우드 서비스 만들기**로 설정된 상태로 둡니다. 그런 다음 **클라우드 서비스 DNS 이름** 아래에 이름을 입력합니다. DNS 이름은 가상 컴퓨터에 연결하는 데 사용되는 URI의 일부가 됩니다. 클라우드 서비스는 통신 및 격리 그룹으로 작동합니다. 같은 클라우드 서비스의 모든 가상 컴퓨터는 서로 통신할 수 있고 부하 분산용으로 설정할 수 있으며 같은 가용성 집합에 배치할 수 있습니다.

	2. 가상 네트워크를 사용하려는 경우 **하위 지역/선호도 그룹/가상 네트워크**에서 가상 네트워크를 지정합니다. **중요**: 가상 컴퓨터에서 가상 네트워크를 사용하려는 경우 가상 컴퓨터를 만들 때 가상 컴퓨터를 가상 네트워크에 연결해야 합니다. 가상 컴퓨터를 만든 후에는 가상 컴퓨터를 가상 네트워크에 가입할 수 없습니다. 자세한 내용은 [가상 네트워크 개요(영문)][]를 참조하세요.

	3. 가용성 집합을 만듭니다. **가용성 집합**에서 옵션을 **가용성 집합 만들기**로 설정해 두고 집합의 이름을 입력합니다.

	4. 기본 끝점을 만들고 필요하면 끝점을 더 추가합니다. 나중에 끝점을 추가할 수도 있습니다.

	![새 가상 컴퓨터에 대한 가용성 집합 만들기](./media/virtual-machines-common-classic-configure-availability/VMavailabilityset.png)

6. 네 번째 화면에서 설치할 확장을 클릭합니다. 확장은 맬웨어 방지 프로그램 실행, 암호 다시 설정 등 가상 컴퓨터를 보다 쉽게 관리할 수 있는 기능을 제공합니다. 자세한 내용은 [Azure VM 에이전트 및 VM 확장](virtual-machines-windows-classic-agents-and-extensions.md)을 참조하세요.

7.	화살표를 클릭하여 가상 컴퓨터와 가용성 집합을 만듭니다.

	새 가상 컴퓨터의 대시보드에서 **구성**을 클릭하면 가상 컴퓨터가 새 가용성 집합에 속함을 확인할 수 있습니다.

Azure PowerShell 명령을 사용하여 Azure 가상 컴퓨터를 만들고 새 또는 기존 가용성 집합에 추가하려면 다음을 참조하세요.


- [Azure PowerShell을 사용하여 Linux 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-linux-classic-createpowershell.md)
- [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-windows-classic-create-powershell.md)

## <a id="addmachine"> </a>옵션 2: 기존 가상 컴퓨터를 가용성 집합에 추가##

Azure 클래식 포털에서 기존 가용성 집합에 기존 가상 컴퓨터를 추가하거나 해당 새로 만들기를 추가할 수 있습니다. 동일한 가용성 집합의 가상 컴퓨터는 같은 클라우드 서비스에 속해야 합니다. 이 옵션에서도 단계는 거의 동일합니다. Azure PowerShell에서 기존 가용성 집합에 가상 컴퓨터를 추가할 수 있습니다.

1. 아직 로그인하지 않은 경우, Azure 클래식 포털에 로그인합니다.

2. 명령 모음에서 **가상 컴퓨터**를 클릭합니다.

3. 가상 컴퓨터 목록에서 집합에 추가할 가상 컴퓨터 이름을 선택합니다.

4. 가상 컴퓨터 이름 아래의 탭에서 **구성**을 클릭합니다.

5. 설정 섹션에서 **가용성 집합**을 찾은 후에 다음 중 하나를 수행합니다.

	A. **가용성 집합 만들기**를 선택하고 집합의 이름을 입력합니다.

	B. **가용성 집합 선택**을 선택하고 목록에서 집합을 선택합니다.

	![기존 가상 컴퓨터에 대한 가용성 집합 만들기](./media/virtual-machines-common-classic-configure-availability/VMavailabilityExistingVM.png)

6. **Save**를 클릭합니다.

Azure PowerShell 명령을 사용하려면 관리자 수준의 Azure PowerShell 세션을 열고 다음 명령을 실행합니다. 자리 표시자(예: &lt;VmCloudServiceName&gt;)의 경우 < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] 가용성 집합에 추가하는 작업을 완료하기 위해 가상 컴퓨터를 다시 시작해야 할 수 있습니다.

## 추가 리소스

[서비스 관리의 가상 컴퓨터 관련 문서]

<!-- LINKS -->
[옵션 1: 가상 컴퓨터와 가용성 집합을 동시에 만듭니다.]: #createset
[옵션 2: 기존 가상 컴퓨터를 가용성 집합에 추가합니다.]: #addmachine

[Azure 인프라 서비스를 위한 부하 분산]: virtual-machines-linux-load-balance.md
[가상 컴퓨터의 가용성 관리]: virtual-machines-linux-manage-availability.md

[Windows를 실행하는 가상 컴퓨터 만들기]: virtual-machines-windows-hero-tutorial.md
[가상 네트워크 개요(영문)]: virtual-networks-overview.md
[서비스 관리의 가상 컴퓨터 관련 문서]: https://azure.microsoft.com/documentation/articles/?tag=azure-service-management&service=virtual-machines

<!---HONumber=AcomDC_0323_2016-->