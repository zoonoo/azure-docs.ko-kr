<properties
	pageTitle="Azure Site Recovery에서 VMM을 통한 Hyper-V 가상 컴퓨터 보호에 대한 네트워크 매핑 준비 | Microsoft Azure"
	description="온-프레미스 데이터 센터에서 Azure 또는 보조 사이트에 Hyper-V 가상 컴퓨터 복제에 대한 네트워크 매핑을 설정합니다."
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
	ms.date="07/06/2016"
	ms.author="raynew"/>


# Azure Site Recovery에서 VMM을 통한 Hyper-V 가상 컴퓨터 보호에 대한 네트워크 매핑 준비

Azure Site Recovery는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다.

이 문서에서는 네트워크 매핑을 설명하며 이는 사이트 복구를 사용하여 두 개의 온-프레미스 데이터 센터 또는 온-프레미스 데이터 센터와 Azure 간의 VMM 클라우드에 있는 Hyper-V 가상 컴퓨터를 복제하는 경우 최적으로 네트워크 설정을 구성하는 데 도움이 됩니다. VMM 클라우드없이 Hyper-V VM을 복제하거나 VMware VM 또는 물리적 서버를 복제하는 경우 이 문서는 관련이 없습니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## 개요

네트워크 매핑은 Hyper-V 복제본 또는 SAN 복제를 사용하여 Azure Site Recovery가 Azure 또는 보조 데이터 센터에 Hyper-V 가상 컴퓨터를 복제하도록 배포될 때 사용됩니다.

- **두 개의 온-프레미스 데이터 센터 간의 VMM 클라우드에서 Hyper-V 가상 컴퓨터 복제**—다음을 수행하도록 원본 VMM 서버의 VM 네트워크와 대상 VMM 서버의 VM 네트워크 사이의 네트워크 매핑을 매핑합니다.

	- **장애 조치 후 가상 컴퓨터 연결**—장애 조치 후 가상 컴퓨터가 적합한 네트워크에 연결되도록 합니다. 복제본 가상 컴퓨터는 원본 네트워크에 매핑되는 대상 네트워크에 연결됩니다.
	- **호스트 서버에 복제본 가상 컴퓨터 배치**—Hyper-V 호스트 서버에 복제본 가상 컴퓨터를 최적으로 배치합니다. 복제본 가상 컴퓨터는 매핑된 VM 네트워크에 액세스할 수 있는 호스트에 배치됩니다.
	- **네트워크 매핑 안 함** - 네트워크 매핑을 구성하지 않으면 장애 조치(Failover) 후 복제본 가상 컴퓨터가 VM 네트워크에 연결되지 않습니다.

- **Azure에 온-프레미스 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제**—네트워크 매핑은 원본 다음을 수행하도록 VMM 서버의 VM 네트워크와 대상 Azure 네트워크 간을 매핑합니다.
	- **장애 조치(Failover) 후 가상 컴퓨터 연결**—속해 있는 복구 계획에 관계없이 동일한 네트워크에서 장애 조치(Failover)되는 모든 컴퓨터가 서로 연결할 수 있습니다.
	- **네트워크 게이트웨이** - 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정된 경우 가상 컴퓨터가 다른 온-프레미스 가상 컴퓨터에 연결할 수 있습니다.
	- **네트워크 매핑 안 함** - 네트워크 매핑을 구성하지 않으면 동일한 복구 계획에서 장애 조치(Failover)되는 가상 컴퓨터만 Azure로의 장애 조치(Failover) 후에 서로 연결할 수 있습니다.


## 네트워크 매핑 예제

두 VMM 서버에서 또는 두 사이트가 동일한 서버를 통해 관리되는 경우 단일 VMM 서버의 VM 네트워크 간에 네트워크 매핑을 구성할 수 있습니다. 매핑이 올바르게 구성되었고 복제를 설정한 경우 기본 위치의 가상 컴퓨터가 네트워크에 연결되고 대상 위치의 복제본이 매핑된 네트워크에 연결됩니다.

네트워크가 VMM에 올바르게 설정된 경우, 네트워크 매핑 중에 대상 VM 네트워크를 선택하면 보호에 사용되는 대상 클라우드의 사용 가능한 대상 VM 네트워크와 함께 원본 VM 네트워크를 사용하는 VMM 원본 클라우드가 표시됩니다.

이 메커니즘을 설명하는 예는 다음과 같습니다. 뉴욕과 시카고 두 위치에 있는 조직을 보겠습니다.

**위치** | **VMM 서버** | **VM 네트워크** | **다음으로 매핑**
---|---|---|---
뉴욕 | VMM-뉴욕| VMNetwork1-뉴욕 | VMNetwork1-시카고로 매핑
 | | VMNetwork2-뉴욕 | 매핑되지 않음
시카코 | VMM-시카고| VMNetwork1-시카고 | VMNetwork1-뉴욕으로 매핑
 | | VMNetwork1-시카고 | 매핑되지 않음

이 예제에서:

- VMNetwork1-뉴욕에 연결된 모든 가상 컴퓨터에 대한 복제 가상 컴퓨터를 만들면 VMNetwork1-시카고에 연결됩니다.
- VMNetwork2-뉴욕 또는 VMNetwork2-시카고에 대한 복제 가상 컴퓨터를 만들면 어떤 네트워크에도 연결되지 않습니다.

VMM 클라우드가 예제 조직 및 클라우드와 연결된 논리 네트워크에서 설정된 방식은 다음과 같습니다.

### 클라우드 보호 설정

**보호된 클라우드** | **클라우드 보호** | **논리 네트워크(뉴욕)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>해당 없음</p><p></p> | <p>LogicalNetwork1-뉴욕</p><p>LogicalNetwork1-시카고</p>
SilverCloud2 | <p>해당 없음</p><p></p> | <p>LogicalNetwork1-뉴욕</p><p>LogicalNetwork1-시카고</p>

### 논리 및 VM 네트워크 설정

**위치** | **논리 네트워크** | **연결된 VM 네트워크**
---|---|---
뉴욕 | LogicalNetwork1-뉴욕 | VMNetwork1-뉴욕
시카코 | LogicalNetwork1-시카고 | VMNetwork1-시카고
 | LogicalNetwork2Chicago | VMNetwork2-시카고

### 대상 네트워크

이러한 설정에 따라 대상 VM 네트워크를 선택하면 다음 표에 사용 가능한 선택 항목이 보입니다.

**선택** | **보호된 클라우드** | **클라우드 보호** | **사용 가능한 대상 네트워크**
---|---|---|---
VMNetwork1-시카고 | SilverCloud1 | SilverCloud2 | 사용 가능
 | GoldCloud1 | GoldCloud2 | 사용 가능
VMNetwork2-시카고 | SilverCloud1 | SilverCloud2 | 사용할 수 없음
 | GoldCloud1 | GoldCloud2 | 사용 가능



## 다중 서브넷

대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제본 가상 컴퓨터가 장애 조치(Failover) 후에 대상 서브넷에 연결됩니다. 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.


### 장애 복구

장애 복구(역방향 복제)의 경우 수행되는 작업을 보려면 다음 설정을 사용하여 VMNetwork1-뉴욕이 VMNetwork1-시카고에 매핑되어 있다고 가정해 보겠습니다.


**가상 컴퓨터** | **VM 네트워크에 연결**
---|---
VM1 | VMNetwork1-네트워크
VM2(VM1의 복제) | VMNetwork1-시카고

이러한 설정을 사용하여 몇 가지 가능한 시나리오에서 발생하는 결과를 검토해 보겠습니다.

**시나리오** | **결과**
---|---
장애 조치(Failover) 후 VM-2의 네트워크 속성이 변경되지 않음 | VM-1에 원본 네트워크에 연결된 상태로 유지됩니다.
장애 조치(Failover) 후 VM-2의 네트워크 속성이 변경되고 연결이 끊김 | VM-1의 연결이 끊김
장애 조치(Failover) 후 VM-2의 네트워크 속성이 변경되고 VMNetwork2-시카고에 연결됨 | VMNetwork2-시카고가 매핑되지 않는 경우 VM-1의 연결이 끊김
VMNetwork1-시카고의 네트워크 매핑이 변경됨 | VM-1이 현재 VMNetwork1-시카고에 매핑된 네트워크에 연결됨


## 다음 단계

네트워크 매핑을 보다 이해했으므로 [Azure Site Recovery 배포를 시작](site-recovery-best-practices.md)합니다.

<!---HONumber=AcomDC_0706_2016-->