<properties
	pageTitle="Azure 클라우드 서비스에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업 | Microsoft Azure"
	description="Azure 클라우드 서비스에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업에 대해 알아봅니다."
	services="cloud-services"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="cloud-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Azure 클라우드 서비스에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업

Microsoft에서는 서비스가 필요할 때 서비스를 항상 사용할 수 있도록 하기 위해 많은 노력을 기울입니다. 다만 경우에 따라 계획되지 않은 서비스 중단이 발생하여 강제적으로 제어 영향을 벗어날 때가 있습니다.

Microsoft는 작동 시간 및 연결에 대한 약정으로 해당 서비스에 대한 서비스 수준 약정(SLA)을 제공합니다. 개별 Azure 서비스에 대한 SLA는 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)에서 찾을 수 있습니다.

Azure에는 항상 사용 가능한 응용 프로그램을 지원하는 많은 기본 제공 플랫폼 기능이 있습니다. 이러한 서비스에 대한 자세한 내용은 [Azure 응용 프로그램에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)을 참조하세요.

이 문서에서는 주요 자연 재해 또는 광범위한 서비스 중단으로 인해 전체 지역에 중단이 발생할 때의 실제 재해 복구 시나리오를 다룹니다. 이러한 경우는 드물게 발생하지만 전체 지역의 중단이 발생될 가능성에 대해 준비해야 합니다. 전체 지역에 서비스 중단이 발생하는 경우 데이터의 로컬 중복 복사본을 일시적으로 사용할 수 없게 됩니다. 지역에서 복제를 사용하는 경우 Azure 저장소 Blob 및 테이블의 추가 사본 3개는 다른 지역에 저장됩니다. 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해의 경우 Azure는 지역 복제된 지역에 모든 DNS 항목을 다시 매핑합니다.

>[AZURE.NOTE]이 프로세스는 사용자가 제어할 수 없으며 데이터 센터 전체 서비스 중단에 대해서만 발생합니다. 이 때문에 가장 높은 수준의 가용성을 달성하기 위해 다른 응용 프로그램별 백업 전략에 의존해야 합니다. 자세한 내용은 [재해 복구를 위한 데이터 전략](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR)에 관한 섹션을 참조하세요. 자체 장애 조치(failover)에 영향을 줄 수 있으려면 다른 지역에 데이터의 읽기 전용 복사본을 만드는 [RA-GRS(읽기 액세스 지역 중복 저장소)](../storage/storage-redundancy.md#read-access-geo-redundant-storage)를 참조하세요.

이러한 드문 경우를 처리할 수 있도록 Azure 가상 컴퓨터(VM) 응용 프로그램이 배포되는 전체 영역의 서비스가 중단될 경우에 대비해서 Azure 가상 컴퓨터(VM)에 대한 다음 지침을 제공합니다.

##옵션 1: 복구 대기
이 경우에 사용자의 조치가 필요하지 않습니다. Azure 팀은 서비스 가용성을 복원하기 위해 최선을 다하고 있습니다. 서비스의 현재 상태를 [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)에서 확인할 수 있습니다.

>[AZURE.NOTE]이 방법은 고객에게 Azure Site Recovery 기능이 없거나 다른 지역에 보조 배포가 있는 경우에 가장 좋은 옵션입니다.

배포된 클라우드 서비스에 즉시 액세스하려는 고객의 경우 아래 옵션을 사용할 수 있습니다.

>[AZURE.NOTE]이러한 옵션을 사용하면 일부 데이터가 손실될 수 있습니다.

##옵션 2: 새 지역에 클라우드 서비스 구성 다시 배포

원래 코드가 있는 경우 응용 프로그램 및 연결된 구성, 그와 관련된 리소스를 새 지역의 새 클라우드 서비스에 간단히 배포할 수 있습니다.

클라우드 서비스 응용 프로그램을 만들고 배포하는 방법에 대한 자세한 내용은 [클라우드 서비스를 만들고 배포하는 방법](./cloud-services-how-to-create-deploy-portal.md)을 참조하세요.

응용 프로그램 데이터 원본에 따라 응용 프로그램 데이터 원본에 대한 복구 절차를 확인해야 할 수 있습니다.
  * Azure 저장소 데이터 원본의 대해서는 [Azure 저장소 복제](../storage/storage-redundancy.md#read-access-geo-redundant-storage)를 참조하여 응용 프로그램에 대해 선택한 복제 모델에 따라 사용할 수 있는 옵션을 확인합니다.
  * SQL 데이터베이스 원본에 대해서는 [개요: SQL 데이터베이스를 사용한 클라우드 비즈니스 연속성 및 데이터베이스 재해 복구](../sql-database/sql-database-business-continuity.md)를 참조하여 응용 프로그램에 대해 선택한 복제 모델에 따라 사용할 수 있는 옵션을 확인합니다.

##옵션 3: Azure 트래픽 관리자를 통해 백업 배포 사용
이 옵션은 이미 지역별 재해 복구를 사용하여 응용 프로그램 솔루션을 디자인했다고 가정합니다. 이 옵션은 다른 지역에서 실행되고 트래픽 관리자 채널을 통해 연결된 보조 클라우드 서비스 응용 프로그램 배포가 이미 있는 경우에 사용할 수 있습니다. 이 경우, 보조 배포의 상태를 확인합니다. 정상이면 Azure 트래픽 관리자를 통해 트래픽을 리디렉션할 수 있습니다. 이 전략을 사용하면 Azure 트래픽 관리자에서 트래픽 라우팅 방법 및 장애 조치(failover) 순서 구성을 활용할 수 있습니다. 자세한 내용은 [트래픽 관리자 설정을 구성하는 방법](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings)을 참조하세요.

![Azure 트래픽 관리자를 사용하여 지역 간에 Azure 클라우드 서비스 분산](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##다음 단계

재해 복구 및 고가용성 전략을 구현하는 방법에 관해 자세히 알아보려면 [Azure 응용 프로그램에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)를 참조하세요.

클라우드 플랫폼의 기능에 대한 자세한 기술적 이해를 높이려면 [Azure 복원력 기술 지침](../resiliency/resiliency-technical-guidance.md)을 참조하세요.

지침이 명확하지 않거나 사용자를 대신해 Microsoft가 작업을 수행하도록 하고 싶은 경우 [고객 지원팀](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하세요.

<!---HONumber=AcomDC_0629_2016-->