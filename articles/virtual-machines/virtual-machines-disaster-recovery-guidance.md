<properties
	pageTitle="Azure 가상 컴퓨터에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업 | Microsoft Azure"
	description="Azure 가상 컴퓨터에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업을 알아본다."
	services="virtual-machines"
	documentationCenter=""
	authors="kmouss"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="virtual-machines"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Azure 가상 컴퓨터에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업

Microsoft에서는 서비스가 필요할 때 서비스를 항상 사용할 수 있도록 하기 위해 많은 노력을 기울입니다. 다만 경우에 따라 계획되지 않은 서비스 중단이 발생하여 강제적으로 제어 영향을 벗어날 때가 있습니다.

Microsoft는 작동 시간 및 연결에 대한 약정으로 해당 서비스에 대한 서비스 수준 약정(SLA)을 제공합니다. 개별 Azure 서비스에 대한 SLA는 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)에서 찾을 수 있습니다.

Azure에는 항상 사용 가능한 응용 프로그램을 지원하는 많은 기본 제공 플랫폼 기능이 있습니다. 이러한 서비스에 대한 자세한 내용은 [Azure 응용 프로그램에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)을 참조하세요.

이 문서에서는 주요 자연 재해 또는 광범위한 서비스 중단으로 인해 전체 지역에 중단이 발생할 때의 실제 재해 복구 시나리오를 다룹니다. 이러한 경우는 드물게 발생하지만 전체 지역의 중단이 발생될 가능성에 대해 준비해야 합니다. 전체 지역에 서비스 중단이 발생하는 경우 데이터의 로컬 중복 복사본을 일시적으로 사용할 수 없게 됩니다. 지역에서 복제를 사용하는 경우 Azure 저장소 Blob 및 테이블의 추가 사본 3개는 다른 지역에 저장됩니다. 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해의 경우 Azure는 지역 복제된 지역에 모든 DNS 항목을 다시 매핑합니다.

>[AZURE.NOTE]이 프로세스는 사용자가 제어할 수 없으며 지역 전체 서비스 중단에 대해서만 발생합니다. 이 때문에 가장 높은 수준의 가용성을 달성하기 위해 다른 응용 프로그램별 백업 전략에 의존해야 합니다. 자세한 내용은 [재해 복구를 위한 데이터 전략](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery)의 섹션을 참조하세요.

이러한 드문 경우를 처리할 수 있도록 Azure 가상 컴퓨터 응용 프로그램이 배포되는 전체 영역의 서비스가 중단될 경우에 대비해서 Azure 가상 컴퓨터에 대한 다음 지침을 제공합니다.

##옵션 1: 복구 대기
이 경우에 사용자의 조치가 필요하지 않습니다. 서비스 가용성을 복원하기 위해 열심히 노력 중입니다. 서비스의 현재 상태를 [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)에서 확인할 수 있습니다.

>[AZURE.NOTE]이 옵션은 가동 중단 이전에 ASR(Azure Site Recovery), 가상 컴퓨터 백업, 읽기 액세스 지역 중복 저장소 또는 지역 중복 저장소를 설치하지 않은 경우 최선의 옵션입니다. VM VHD(가상 하드 드라이브)가 저장되는 저장소 계정으로 지역 중복 저장소 또는 읽기 액세스 지역 중복 저장소를 설치한 경우 기본 이미지 VHD 복구를 볼 수 있으며 새 VM 프로비전을 시도합니다. 이 옵션은 Azure VM 백업 또는 Azure Site Recovery가 사용되지 않는 한 데이터 동기화를 보장할 수 없기 때문에 권장되는 옵션이 아닙니다. 따라서, 이 옵션은 작동한다는 보장이 없습니다.

가상 컴퓨터에 대한 즉각적인 액세스를 원하는 고객의 경우 다음 두 가지 옵션을 사용할 수 있습니다.

>[AZURE.NOTE]다음 옵션은 모두 일부 데이터가 손실될 가능성이 있습니다.

##옵션 2: 백업에서 VM 복원
VM 백업을 구성한 고객의 경우 백업 및 복구 지점에서 VM을 복원할 수 있습니다.

Azure 백업에서 새 VM을 복원하려면, [Azure에서 가상 컴퓨터 복원](../backup/backup-azure-restore-vms.md)을 참조하세요.

Azure 가상 컴퓨터 백업 인프라를 계획하려면 [Azure에서 VM 백업 인프라 계획](../backup/backup-azure-vms-introduction.md)을 참조하세요.

##옵션 3: Azure Site Recovery를 사용하여 장애 조치(failover) 시작
영향을 받는 Azure 가상 컴퓨터와 작동하도록 Azure Site Recovery를 구성한 경우 해당 복제본에서 VM을 복원할 수 있습니다. 이러한 복제본은 Azure 또는 온-프레미스에도 있을 수 있습니다. 이 경우에 기존 복제본에서 새 VM을 만들 수 있습니다. Azure Site Recovery 복제본에서 VM을 복원하려면, [Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS 가상 컴퓨터 마이그레이션](../site-recovery/site-recovery-migrate-azure-to-azure.md)을 참조하세요.

>[AZURE.NOTE]Azure 가상 컴퓨터 운영 체제와 데이터 디스크가 보조 VHD로 복제되더라도 지역 중복 저장소 또는 읽기 액세스 지역 중복 저장소 계정에 있는 경우 각 VHD는 독립적으로 복제 됩니다. 이 수준의 복제는 복제된 VHD들의 일관성을 보장하지 않습니다. 이러한 서로 종속성을 갖는 데이터 디스크를 사용하는 데이터베이스 및/또는 응용 프로그램은 모든 VHD가 하나의 스냅숏으로 복제되는 것을 보장하지 않습니다. 또한 지역 중복 저장소 또는 읽기 액세스 지역 중복 저장소에서 VHD 복제본이 VM을 부팅하는 응용 프로그램 일치 스냅숏을 생성하지 않을 수도 있습니다.

##다음 단계
재해 복구 및 고가용성 전략을 구현하는 방법에 관해 자세히 알아보려면 [Azure 응용 프로그램에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)을 참조하세요.

클라우드 플랫폼의 기능에 대한 자세한 기술적 이해를 높이려면 [Azure 복원력 기술 지침](../resiliency/resiliency-technical-guidance.md)을 참조하세요.

VM을 백업하는 방법을 알아보려면 [Azure 가상 컴퓨터 백업](../backup/backup-azure-vms.md)을 참조하세요.

Azure 사이트 복구를 사용하여 VMware 및 Hyper-V Vm에서 실행되는 실제 (및 가상) Windows 및 Linux 컴퓨터 보호를 오케이스트레이션 및 자동화하는 방법을 알아보려면 [Azure 사이트 복구](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)를 참조하세요.

지침이 명확하지 않거나 사용자를 대신해 Microsoft가 작업을 수행하도록 하고 싶은 경우 [고객 지원팀](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하세요.

<!---HONumber=AcomDC_0824_2016-->