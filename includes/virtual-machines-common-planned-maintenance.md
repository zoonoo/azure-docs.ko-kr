

## <a name="memory-preserving-updates"></a>메모리 유지 업데이트
Microsoft Azure의 업데이트 클래스는 고객이 실행 중인 가상 컴퓨터에는 어떠한 영향도 주지 않습니다. 이러한 많은 업데이트는 실행 중인 인스턴스를 방해하지 않고 업데이트할 수 있는 구성 요소 또는 서비스에 대한 업데이트입니다. 이러한 업데이트 중 일부는 가상 컴퓨터를 완전히 다시 부팅하지 않고 적용할 수 있는 호스트 운영 체제의 플랫폼 인프라 업데이트입니다.

이러한 업데이트는 원위치 실시간 마이그레이션을 가능하게 하는 기술(“메모리 유지” 업데이트)를 통해 완수됩니다. 업데이트 시 기본 호스트 운영 체제가 필요한 업데이트 및 패치를 받는 동안 가상 컴퓨터는 "일시 중지" 상태로 들어가 RAM의 메모리를 유지합니다. 가상 컴퓨터는 일시 중지 후 30초 내에 다시 시작됩니다. 다시 시작된 후 가상 컴퓨터의 시계가 자동으로 동기화됩니다.

이 메커니즘을 사용하여 모든 업데이트를 배포할 수 있는 것은 아니지만 짧은 일시 중지 시간을 지정한 경우 이 방식으로 업데이트를 배포하면 가상 컴퓨터에 미치는 영향을 크게 줄일 수 있습니다.

다중 인스턴스 업데이트(가용성 집합의 가상 컴퓨터에 대한)는 한 번에 하나의 업데이트 도메인에 적용됩니다.  

## <a name="virtual-machine-configurations"></a>가상 컴퓨터 구성
가상 컴퓨터 구성에는 다중 인스턴스 구성 및 단일 인스턴스 구성이 있습니다. 다중 인스턴스 구성에서 유사한 가상 컴퓨터는 한 개의 가용성 집합에 배치됩니다.

다중 인스턴스 구성은 물리적 컴퓨터, 전원 및 네트워크에 대한 중복성을 제공하고 응용 프로그램의 가용성을 보장하기 위해 권장됩니다. 가용성 집합의 모든 가상 컴퓨터는 응용 프로그램에 동일한 목적을 충족시켜야 합니다.

고가용성을 위해 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 [Windows 가상 컴퓨터의 가용성 관리](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux 가상 컴퓨터의 가용성 관리](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

이와 반대로 단일 인스턴스 구성은 가용성 집합에 배치되지 않은 독립 실행형 가상 컴퓨터에 사용됩니다. 이러한 가상 컴퓨터는 동일한 가용성 집합에 배포되는 둘 이상의 가상 컴퓨터를 요구하는 SLA(서비스 수준 약정)를 충족하지 못합니다.

SLA에 대한 자세한 내용은 [Service Level Agreements(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/)의 "클라우드 서비스 및 가상 컴퓨터" 섹션을 참조하세요.

## <a name="multi-instance-configuration-updates"></a>다중 인스턴스 구성 업데이트
계획된 유지 관리를 수행하는 동안 Azure 플랫폼은 다중 인스턴스 구성 내에 호스팅되는 가상 컴퓨터 집합을 우선 업데이트합니다. 업데이트로 인해 이러한 가상 컴퓨터가 다시 부팅되며 약 15분 동안 가동이 중지됩니다.

다중 인스턴스 구성 업데이트는 각 가상 컴퓨터가 가용성 집합의 다른 항목과 유사한 기능을 제공한다고 가정합니다. 이 설정에서 가상 컴퓨터는 프로세스 전체에서 가용성이 유지되는 방식으로 업데이트됩니다.

기본 Azure Platform에서는 가용성 집합에 포함된 각 가상 컴퓨터를 업데이트 도메인 및 장애 도메인에 할당합니다. 각각의 업데이트 도메인은 동일한 시간에 다시 부팅되는 가상 컴퓨터 그룹입니다. 각각의 장애 도메인은 공통된 전원 및 네트워크 스위치를 공유하는 가상 컴퓨터 그룹입니다.


업데이트 도메인 및 장애 도메인에 대한 자세한 내용은 [중복성을 위해 가용성 집합에 여러 가상 컴퓨터 구성](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)을 참조하세요.

업데이트를 통해 가용성을 유지하기 위해 Azure는 한 번에 하나의 도메인을 업데이트하는 업데이트 도메인을 통해 유지 관리를 수행합니다. 업데이트 도메인의 유지 관리는 도메인에서 각 가상 컴퓨터를 종료하고, 호스트 컴퓨터에 업데이트를 적용한 후, 가상 컴퓨터를 다시 시작하는 것으로 이루어집니다. 도메인에서 유지 관리가 완료되면 Azure는 다음 업데이트 도메인에서 프로세스를 반복하고 모두 업데이트가 될 때까지 각 도메인에서 계속 진행합니다.

다시 시작되는 업데이트 도메인의 순서는 계획된 유지 관리 중에 순차적으로 진행되지 않을 수 있지만 한 번에 하나의 업데이트 도메인만 다시 부팅됩니다. 현재 Azure는 다중 인스턴스 구성에서 가상 컴퓨터에 대해 계획된 유지 관리의 1주 사전 알림을 제공합니다.

가상 컴퓨터가 복원된 후 Windows 이벤트 뷰어에 표시될만한 내용의 예는 다음과 같습니다.

<!--Image reference-->
![][image2]


뷰어를 사용하여 가상 컴퓨터가 Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 다중 인스턴스 구성에서 구성되었는지 보고합니다. 예를 들어 Azure Portal을 사용한 경우 _가용성 집합_을 **가상 컴퓨터(클래식)** 찾아보기 대화 상자에 추가할 수 있습니다. 동일한 가용성 집합을 보고하는 가상 컴퓨터는 다중 인스턴스 구성의 일부입니다. 다음 예제에서 다중 인스턴스 구성은 가상 컴퓨터 SQLContoso01 및 SQLContoso02로 구성됩니다.

<!--Image reference-->
  ![Azure Portal의 가상 컴퓨터(클래식) 보기][image4]

## <a name="single-instance-configuration-updates"></a>단일 인스턴스 구성 업데이트
다중 인스턴스 구성 업데이트가 완료된 후 Azure는 단일 인스턴스 구성 업데이트를 수행합니다. 이러한 업데이트에서도 가용성 집합에서 실행되지 않는 가상 컴퓨터가 다시 부팅됩니다.

> [!NOTE]
> 가용성 집합에서 하나의 가상 컴퓨터 인스턴스만 실행하더라도 Azure Platform은 이를 다중 인스턴스 구성 업데이트로 취급합니다.
>

단일 인스턴스 구성의 유지 관리는 호스트 컴퓨터에서 실행 중인 각 가상 컴퓨터를 종료하고, 호스트 컴퓨터를 업데이트한 다음, 가상 컴퓨터 다시 시작하는 것으로 이루어집니다. 유지 관리에는 약 15분의 가동 중지 시간이 필요합니다. 계획된 유지 관리 이벤트는 단일 유지 관리 기간에 특정 지역의 모든 가상 컴퓨터에서 실행됩니다.


계획된 유지 관리 이벤트는 단일 인스턴트 구성에서 응용 프로그램의 가용성에 영향을 미칩니다. Azure는 단일 인스턴스 구성에서 가상 컴퓨터에 대해 계획된 유지 관리의 1주 사전 알림을 제공합니다.

## <a name="email-notification"></a>메일 알림
단일 인스턴스 및 다중 인스턴스 가상 컴퓨터 구성인 경우에만 Azure가 향후 계획된 유지 관리에 대한 전자 메일 경고를 1주 전에 전송합니다. 이 전자 메일은 구독 관리자 및 공동 관리자 전자 메일 계정으로 전송됩니다. 이러한 유형의 메일의 예는 아래와 같습니다.

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>지역 쌍

유지 관리를 실행할 때 Azure는 단일 지역의 해당 쌍이 되는 가상 컴퓨터 인스턴스만 업데이트합니다. 예를 들어 미국 중북부에 있는 가상 컴퓨터를 업데이트할 때 Azure는 미국 중남부의 가상 컴퓨터를 동시에 업데이트하지 않습니다. 이는 별도의 다른 시간에 예약하여 지역 간의 장애 조치(failover) 또는 부하 분산을 사용하도록 설정합니다. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다.

현재 지역 쌍은 다음 테이블을 참조하세요.

| 지역 1 | 지역 2 |
|:--- | ---:|
| 미국 동부 |미국 서부 |
| 미국 동부 2 |미국 중부 |
| 미국 중북부 |미국 중남부 |
| 미국 중서부 |미국 서부 2 |
| 캐나다 동부 |캐나다 중부 |
| 브라질 남부 |미국 중남부 |
| 미국 정부 아이오와 |미국 정부 버지니아 |
| 미국 국방부 동부 |미국 국방부 중부 |
| 북유럽 |서유럽 |
| 영국 서부 |영국 남부 |
| 독일 중부 |독일 북동부 |
| 동남아시아 |동아시아 |
| 오스트레일리아 남동부 |오스트레일리아 동부 |
| 인도 중부 |인도 남부 |
| 인도 서부 |인도 남부 |
| 일본 동부 |일본 서부 |
| 한국 중부 |한국 남부 |
| 중국 동부 |중국 북부 |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/windows/manage-availability.md#Understand-planned-versus-unplanned-maintenance/
