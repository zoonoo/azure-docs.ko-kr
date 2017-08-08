## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>VM 다시 부팅 이해 - 유지 관리 및 가동 중지
Azure의 가상 컴퓨터가 초래할 수 있는 세 가지 시나리오, 즉 계획되지 않은 하드웨어 유지 관리, 예기치 않은 가동 중지 및 계획된 유지 관리가 있습니다.

* **계획되지 않은 하드웨어 유지 관리 이벤트**는 Azure 플랫폼에서 물리적 컴퓨터와 관련된 하드웨어 또는 플랫폼 구성 요소가 실패할 것으로 예측할 때 발생합니다. 플랫폼에서 오류를 예측하는 경우 계획되지 않은 하드웨어 유지 관리 이벤트를 발급하여 해당 하드웨어에서 호스팅되는 가상 컴퓨터에 미치는 영향을 줄입니다. Azure에서는 실시간 마이그레이션 기술을 사용하여 오류가 발생한 하드웨어에서 정상적인 물리적 컴퓨터로 Virtual Machines를 마이그레이션합니다. 실시간 마이그레이션은 짧은 시간 동안 Virtual Machine을 일시 중지하는 VM 보존 작업입니다. 메모리, 열린 파일 및 네트워크 연결은 유지되지만 이벤트 전후에 성능이 저하될 수 있습니다. 실시간 마이그레이션을 사용할 수 없는 경우 아래에서 설명한 대로 VM에 예기치 않은 가동 중지가 발생합니다.


* **예기치 않은 가동 중지**는 어떤 방식으로든 가상 컴퓨터의 기반이 되는 하드웨어 또는 물리적 인프라에 오류가 발생한 경우에 드물게 발생합니다. 여기에는 로컬 네트워크 오류, 로컬 디스크 오류 또는 기타 랙 수준의 오류가 포함될 수도 있습니다. 이러한 오류가 감지되면 Azure 플랫폼에서 가상 컴퓨터를 정상적인 물리적 컴퓨터로 자동으로 마이그레이션(복구)합니다. 복구 과정 중에 가상 컴퓨터에서 가동 중지(재부팅)가 발생하고 경우에 따라 임시 드라이브가 손실됩니다. 연결된 OS 및 데이터 디스크는 항상 유지됩니다. 

* **계획된 유지 관리 이벤트**는 가상 컴퓨터가 실행되는 플랫폼 인프라의 전반적인 안정성, 성능 및 보안을 향상시키기 위해 Microsoft에서 기본 Azure 플랫폼에 적용하는 정기적인 업데이트입니다. 이러한 업데이트는 대부분 Virtual Machines 또는 Cloud Services에 영향을 주지 않고 수행됩니다([VM 보존 유지 관리](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/preserving-maintenance) 참조). Azure 플랫폼에서 가능한 모든 경우에 VM 보존 유지 관리를 사용하려고 시도하는 반면, 가상 컴퓨터를 다시 부팅해야 기본 인프라에 필요한 업데이트를 적용할 수 있는 경우가 드물게 있습니다. 이 경우 적절한 기간에 VM에 대한 유지 관리를 시작하여 유지 관리 - 재배포 작업이 포함된 Azure Planned Maintenance(Azure 계획된 유지 관리)를 수행할 수 있습니다. 자세한 내용은 [Virtual Machines에 대한 계획된 유지 관리](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/planned-maintenance/)를 참조하세요.


이러한 이벤트로 인한 가동 중지 시간의 영향을 줄이기 위해 가상 컴퓨터에 다음과 같은 고가용성 모범 사례를 권장합니다.

* [중복성을 위해 가용성 집합에서 여러 가상 컴퓨터 구성]
* [가용성 집합의 VM에 Managed Disks 사용]
* [예약된 이벤트를 사용하여 VM에 영향을 미치는 이벤트에 대한 사전 예방적 대응](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-scheduled-events)
* [각 응용 프로그램 계층을 별도의 가용성 집합으로 구성]
* [가용성 집합과 부하 분산 장치 결합]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>중복성을 위해 가용성 집합에서 여러 가상 컴퓨터 구성
응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 컴퓨터를 가용성 집합으로 그룹화하는 것이 좋습니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 컴퓨터를 사용할 수 있고 99.95% Azure SLA가 충족되도록 합니다. 자세한 내용은 [Virtual Machines에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 참조하세요.

> [!IMPORTANT]
> 가용성 집합 안에 단일 인스턴스 가상 컴퓨터를 단독으로 두지 않도록 하십시오. 이러한 구성의 VM은 SLA 보증에 맞지 않으며 단일 VM이 [Azure Premium Storage](../articles/storage/storage-premium-storage.md)를 사용하는 경우를 제외하고 Azure 계획된 유지 관리 이벤트 중에 가동 중지하게 됩니다. 프리미엄 저장소를 사용하는 단일 VM의 경우 Azure SLA가 적용됩니다.

기본 Azure 플랫폼에서는 가용성 집합에 포함된 각각의 가상 컴퓨터를 **업데이트 도메인** 및 **장애 도메인**에 할당합니다. 특정 가용성 집합의 경우 기본적으로 사용자가 구성할 수 없는 다섯 개의 업데이트 도메인이 할당되어(그런 다음 최대 20개의 업데이트 도메인을 제공하도록 Resource Manager 배포를 늘릴 수 있음) 동시에 재부팅할 수 있는 가상 컴퓨터 및 기본 물리적 하드웨어 그룹을 나타냅니다. 단일 가용성 집합 내에 5개 이상의 가상 컴퓨터를 구성한 경우 6번째 가상 컴퓨터는 동일한 업데이트 도메인에 첫 번째 가상 컴퓨터로 배치되고, 7번째 가상 컴퓨터는 동일한 업데이트 도메인에 두 번째 가상 컴퓨터로 배치되는 식입니다. 재부팅되는 업데이트 도메인의 순서는 계획된 유지 보수 중 순차적으로 진행할 수 없으며 한 번에 하나의 업데이트 도메인만이 재부팅됩니다. 다시 부팅된 업데이트 도메인을 복구할 시간으로 30분이 제공되며 이 시간이 지나면 다른 업데이트 도메인에서 유지 관리가 시작됩니다.

장애 도메인은 공통 전원과 네트워크 스위치를 공유하는 가상 컴퓨터 그룹을 정의합니다. 기본적으로 가용성 집합 안에 구성된 가상 컴퓨터는 Resource Manager 배포의 경우 최대 3개의 장애 도메인(클래식의 경우 2개의 장애 도메인)으로 분리됩니다. 가상 컴퓨터를 가용성 집합에 배치한다고 해서 응용 프로그램이 운영 체제 또는 응용 프로그램 고유의 오류로부터 보호되는 것은 아닙니다. 잠재적인 물리적 하드웨어 오류, 네트워크 중단, 전력 차단의 영향이 제한될 뿐입니다.

<!--Image reference-->
   ![업데이트 도메인 및 장애 도메인 구성의 개념적 그림](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>가용성 집합에서 VM에 Managed Disks 사용
현재 관리되지 않는 디스크에서 VM을 사용하는 경우 [가용성 집합에서 VM을 변환하여 Managed Disks를 사용](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md)하는 것이 좋습니다.

[Managed Disks](../articles/storage/storage-managed-disks-overview.md)는 단일 실패 지점을 피할 만큼 가용성 집합의 VM 디스크가 서로 충분히 격리되도록 하여 가용성 집합에 더 나은 안정성을 제공합니다. 이 기능은 디스크를 다른 저장소 클러스터에 자동으로 배치하여 구현됩니다. 하드웨어 또는 소프트웨어 장애로 인해 저장소 클러스터에 장애가 발생하면 해당 스탬프에 디스크가 있는 VM 인스턴스만 실패합니다.

![Managed Disk FD](./media/virtual-machines-common-manage-availability/md-fd.png)

> [!IMPORTANT]
> 관리되는 가용성 집합에 대한 장애 도메인 수는 지역에 따라 다르며 - 지역마다 2개 또는 3개입니다. 다음 표는 지역별 수를 보여줍니다.

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

[관리되지 않는 디스크](../articles/storage/storage-about-disks-and-vhds-windows.md#types-of-disks)에서 VM을 사용하려는 경우 VM의 VHD(가상 하드 디스크)를 [페이지 Blob](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)로 저장하는 Storage 계정에 대한 아래의 유용한 모범 사례를 따릅니다.

1. **동일한 저장소 계정에서 VM과 모든 디스크(OS 및 데이터) 연결 유지**
2. 저장소 계정에 더 많은 VHD를 추가하기 전에 **Storage 계정에서 관리되지 않는 디스크의 수에 대한 [제한](../articles/storage/storage-scalability-targets.md) 검토**
3. **가용성 집합의 각 VM마다 별도의 저장소 계정을 사용합니다.** 동일한 가용성 집합에서 여러 VM을 사용하여 Storage 계정을 공유하지 않습니다. 위의 모범 사례를 따르면 여러 가용성 집합의 VM에서 저장소 계정을 공유할 수 있습니다.

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>각 응용 프로그램 계층을 별도의 가용성 집합으로 구성
가상 컴퓨터가 모두 거의 동일하고 응용 프로그램에 같은 목적으로 사용될 경우에는 응용 프로그램의 각 계층에 대해 가용성 집합을 구성하는 것이 좋습니다.  동일한 가용성 집합에 두 가지 계층을 배치하면 같은 응용 프로그램 계층에 있는 모든 가상 컴퓨터가 동시에 재부팅될 수 있습니다. 각 계층에 대해 최소 두 개의 가상 컴퓨터를 가용성 집합 안에 구성하면 각 계층에서 최소한 하나의 가상 컴퓨터는 사용할 수 있습니다.

예를 들어 IIS, Apache, Nginx를 실행하는 응용 프로그램의 프런트 엔드에 있는 모든 가상 컴퓨터를 단일 가용성 집합에 배치할 수 있습니다. 프런트 엔드 가상 컴퓨터만 같은 가용성 집합에 배치해야 합니다. 마찬가지로 데이터 계층 가상 컴퓨터만 자체의 가용성 집합(예: 복제된 SQL Server 가상 컴퓨터 또는 MySQL 가상 컴퓨터)에 배치해야 합니다.

<!--Image reference-->
   ![응용 프로그램 계층](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>가용성 집합과 부하 분산 장치 결합
[Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md)를 가용성 집합과 결합하여 응용 프로그램 복원력을 극대화하십시오. Azure 부하 분산 장치는 트래픽을 여러 가상 컴퓨터에 분산시킵니다. 표준 계층 가상 컴퓨터의 경우 Azure 부하 분산 장치가 포함되어 있습니다. 모든 가상 컴퓨터 계층에 Azure Load Balancer가 포함되어 있는 것은 아닙니다. 가상 컴퓨터 부하 분산에 대한 자세한 내용은 [가상 컴퓨터 부하 분산](../articles/virtual-machines/virtual-machines-linux-load-balance.md)을 참조하세요.

부하 분산 장치가 트래픽을 여러 가상 컴퓨터에 분산시키도록 구성되지 않은 경우에는 계획된 유지 관리 이벤트가 트래픽 처리 가상 컴퓨터에만 영향을 줌으로써 응용 프로그램 계층에 중단을 일으킬 수 있습니다. 같은 계층의 여러 가상 컴퓨터를 같은 부하 분산 장치와 가용성 집합 아래에 배치하면 언제든지 적어도 하나의 인스턴스에서는 트래픽을 계속 처리할 수 있습니다.


<!-- Link references -->
[중복성을 위해 가용성 집합에서 여러 가상 컴퓨터 구성]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[각 응용 프로그램 계층을 별도의 가용성 집합으로 구성]: #configure-each-application-tier-into-separate-availability-sets
[가용성 집합과 부하 분산 장치 결합]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[가용성 집합의 VM에 Managed Disks 사용]: #use-managed-disks-for-vms-in-an-availability-set
