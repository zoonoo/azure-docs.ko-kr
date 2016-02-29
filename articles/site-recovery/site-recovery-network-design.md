<properties
	pageTitle="VMM 및 사이트 복구에 대한 배포 고려 사항 | Microsoft Azure"
	description="이 문서에서는 Azure Site Recovery를 사용하여 VMM 통합을 위한 실제 디자인 고려 사항을 알아봅니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  VMM 및 사이트 복구에 대한 배포 고려 사항

Azure Site Recovery 서비스는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터는 Azure 또는 보조 온-프레미스 데이터 센터로 복제할 수 있습니다. 빠른 개요를 알아보려면 [Azure Site Recovery란?](site-recovery-overview.md)을 확인하세요.


## 개요

이 문서는 BCDR 솔루션 및 System Center VMM 및 Azure 사이트 복구를 포함하는 인프라를 설계 및 구현하는 데 도움을 줍니다.

BCDR 전략의 목적은 비즈니스 응용 프로그램이 계속 실행되도록 하고, 실패한 작업 및 서비스를 복원하여 조직이 신속하게 정상 작업을 재개할 수 있도록 하는 것입니다. 재해 복구 전략 개발이 어려운 이유는 예상치 못한 이벤트를 예측해야 하는 본질적인 어려움과 광범위한 오류에 대해 적절한 보호를 구현하는 데 드는 높은 비용 때문입니다. 사이트 복구를 사용하면 처음에 주 데이터를 복사(복제)한 다음 주기적으로 복제본을 새로 고침으로써 보호를 구현하고 주 데이터 센터에서 보조 데이터 센터(또는 Azure)로 장애 조치(failover)할 수 있습니다.

BCDR 계획에서 가능한 빨리(낮은 RTO) 최소한의 데이터 손실(낮은 RPO)로 조직의 데이터를 온라인으로 다시 가져올 수 있도록 RTO(복구 시간 목표)와 RPO(복구 지점 목표)를 정의하는 것이 중요합니다. 조직의 네트워크 디자인은 RTO 및 RPO 목표에 문제가 될 수 있으므로 디자인 계획을 잘 세워야 이러한 문제를 방지할 수 있습니다.


VMM 클라우드의 Hyper-V VM을 복제하도록 사이트 복구를 배포하는 경우 사이트 복구 복제 및 장애 조치 전략으로 VMM을 통합하는 방법을 고려해야 합니다.

## 독립 실행형 VMM 서버 통합

이 토폴로지에서는 VMM 서버를 주 사이트의 가상 컴퓨터에 배포하고 이 가상 컴퓨터를 Site Recovery 및 Hyper-V 복제본을 사용하여 보조 사이트로 복제합니다. VM은 하나만 인스턴스화되어야 하기 때문에 동일한 가상 컴퓨터에 VMM 서버와 이 서버를 지원하는 SQL Server를 설치해서 가동 중지 시간을 줄이는 것을 고려해 볼 수 있습니다. VMM 서비스가 원격 SQL Server를 사용하는 경우 VMM 서버를 복구하기 전에 SQL Server 인스턴스를 복구해야 합니다.

Hyper-V 복제본을 사용하여 VM에 단일 VMM을 배포하려면:

1. SQL Server가 설치되어 있는 VM에 VMM을 설치합니다.
2. 관리할 호스트를 이 VMM 서버의 클라우드에 추가합니다.
3. Azure 포털에 로그인한 다음 보호하도록 클라우드를 구성합니다.
4. VMM 서버로 보호해야 할 모든 VM에 대해 복제를 사용하도록 설정합니다.
5. Hyper-V 관리 콘솔로 이동하고 Hyper-V 복제본을 선택한 다음 VMM VM에서 복제를 사용하도록 설정합니다.
6. Hyper-V 복제 설정이 ASR에 의해 재정의되지 않도록 VMM VM이 ASR 서비스로 보호되는 클라우드에 추가되지 않도록 합니다.

재해가 발생할 경우 다음과 같이 작업을 복구할 수 있습니다.

1. Hyper-V 관리자를 사용하여 복제본 VMM VM 을 복구 사이트에 장애 조치(failover)합니다.
2. VMM VM이 복구된 후 사용자는 보조 사이트에서 Hyper-V 복구 관리자에 로그인할 수 있습니다.
3. 계획되지 않은 장애 조치(failover)가 완료된 후 사용자는 주 사이트의 모든 리소스에 액세스할 수 있습니다.
4. 작업을 장애 조치(failover)하려면 먼저 VMM VM을 수동으로 보조 사이트에 장애 조치(failover)해야 합니다.


### VMM 클러스터 통합


[클러스터에 VMM을 배포](https://technet.microsoft.com/library/gg610675.aspx)하면 하드웨어 장애 조치(failover)에 대해 고가용성 및 보호를 제공합니다. Site Recovery를 사용하여 VMM 클러스터를 배포하는 경우 다음 사항에 유의합니다.

- 지리적으로 떨어져 있는 사이트에서는 VMM 서버가 늘어난(stretched) 클러스터에 배포되어야 합니다.
- VMM에서 사용하는 SQL Server 데이터베이스는 보조 사이트에서 복제를 통해 SQL Server AlwaysOn 가용성 그룹과 함께 보호되어야 합니다.
- 재해가 VMM 서버에서 발생하는 경우 그에 해당하는 SQL Server가 자동으로 복구 사이트에 장애 조치(failover)됩니다. 그런 다음 Site Recovery를 사용하여 작업을 장애 조치(failover)할 수 있습니다.

	![늘어난 VMM 클러스터](./media/site-recovery-network-design/network-design1.png)


## 다음 단계

Site Recovery가 원본 및 대상 네트워크를 매핑하는 방법을 [알아봅니다.](site-recovery-network-mapping.md)

<!---HONumber=AcomDC_0218_2016-->