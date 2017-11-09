---
title: "Azure Site Recovery란? | Microsoft Docs"
description: "Azure Site Recovery 서비스의 개요를 제공하고 배포 시나리오를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b4b39cd23557093edaec97f7ef7a3e354f1ecd03
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="about-site-recovery"></a>Site Recovery란?

Azure Site Recovery 서비스를 시작합니다. 이 문서에서는 서비스에 대한 빠른 개요를 제공합니다.

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Azure Recovery Services를 사용한 비즈니스 연속성 및 재해 복구(BCDR)

조직으로서 계획되거나 계획되지 않은 정전이 발생한 경우 데이터 그리고 실행 중인 앱/워크로드를 안전하게 유지하는 방법을 알아야 합니다.

Azure Recovery Services는 BCDR 전략에 기여합니다.

- **Site Recovery 서비스**: Site Recovery는 사이트의 작동이 중단된 경우에도 VM 및 물리적 서버에서 실행 중인 앱을 계속 사용할 수 있도록 하여 비즈니스 연속성을 보장하는 데 도움이 됩니다. Site Recovery는 VM 및 물리적 서버에서 실행 중인 워크로드를 복제하여 기본 사이트를 사용할 수 없게 된 경우 보조 위치에서 계속 사용할 수 있도록 합니다. 사이트가 복구되어 다시 실행되면 워크로드를 기본 사이트로 복구합니다.
- **Backup 서비스**: 또한 [Azure Backup](https://docs.microsoft.com/azure/backup/) 서비스는 Azure에 데이터를 백업하여 계속 안전하고 복구 가능하도록 유지합니다.

Site Recovery는 다음을 위해 복제를 관리할 수 있습니다.

- Azure 지역 간에 Azure VM 복제
- 온-프레미스 가상 컴퓨터 및 물리적 서버를 Azure 또는 보조 사이트에 복제


## <a name="what-does-site-recovery-provide"></a>Site Recovery의 기능은 무엇입니까?

**기능** | **세부 정보**
--- | ---
**간단한 BCDR 솔루션 배포** | Site Recovery를 사용하면 Azure Portal의 단일 위치로부터 복제, 장애 조치 및 장애 복구를 설정 및 관리할 수 있습니다.
**Azure VM 복제** | BCDR 전략을 설정하여 Azure 지역 간에 Azure VM을 복제할 수 있습니다.
**온-프레미스 VM 오프사이트 복제** | 온-프레미스 VM 및 물리적 서버를 Azure 또는 보조 온-프레미스 위치에 복제할 수 있습니다. Azure에 복제하면 보조 데이터 센터를 유지하는 비용과 복잡성을 제거할 수 있습니다.
**워크로드 복제** | 지원되는 Azure VM, 온-프레미스 Hyper-V VM, VMware VM 및 Windows/Linux 물리적 서버에서 실행되는 모든 워크로드를 복제할 수 있습니다.
**데이터를 탄력적이고 안전하게 보관** | Site Recovery는 응용 프로그램 데이터를 차단하지 않고 복제를 오케스트레이션합니다. 복제된 데이터는 제공되는 복원력을 통해 Azure Storage에 저장됩니다. 장애 조치가 발생하는 경우 복제된 데이터를 기반으로 하여 Azure VM을 만듭니다.
**RTO 및 RPO 충족** | 조직 제한 내에서 복구 시간 목표(RTO) 및 복구 지점 목표(RPO)을 유지합니다. Site Recovery는 Azure VM 및 VMware VM에 대해 연속 복제를 제공하고 Hyper-V에 대해서는 짧은 빈도(30초)의 복제를 제공합니다. [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) 통합을 통합하여 복구 시간 목표(RTO)를 더 줄일 수 있습니다.
**장애 조치(Failover)를 통해 앱 일관성 유지** | 응용 프로그램 일치 스냅숏으로 복구 지점을 구성할 수 있습니다. 응용 프로그램 일치 스냅숏은 디스크 데이터, 메모리의 모든 데이터, 프로세스의 모든 트랜잭션을 캡처합니다.
**중단없는 테스트** | 진행 중인 복제에 영향을 주지 않고 재해 복구 훈련을 지원하는 테스트 장애 조치를 손쉽게 실행할 수 있습니다.
**유연한 장애 조치(failover) 실행** | 예상된 중단에 대해서는 계획된 데이터 무손실 장애 조치를 실행하고, 예기치 않은 재해에 대해서는 복제 빈도에 따라 데이터 손실을 최소화하는 계획되지 않은 장애 조치를 실행할 수 있습니다. 기본 사이트를 다시 사용할 수 있는 경우 손쉽게 이 사이트로 장애 복구할 수 있습니다.
**복구 계획 만들기** | 복구 계획으로 여러 VM에서 다중 계층 응용 프로그램의 장애 조치 및 복구를 사용자 지정하고 순서를 배열합니다. 계획 내에서 컴퓨터를 그룹화하고 스크립트와 수동 작업을 추가합니다. 복구 계획은 Azure Automation runbook과 통합될 수 있습니다.
**기존 BCDR 기술과 통합** | Site Recovery는 다른 BCDR 기술과 통합합니다. 예를 들어 Site Recovery를 사용하여 가용성 그룹의 장애 조치를 관리하는 AlwaysOn에 대한 SQL Server의 기본 지원을 비롯한 회사 워크로드의 SQL Server 백 엔드를 보호할 수 있습니다.
**자동화 라이브러리와 통합** | 다양한 Azure Automation 라이브러리는 Site Recovery를 다운로드하고 통합할 수 있는 프로덕션 준비된 응용 프로그램 특정 스크립트를 제공합니다.
**네트워크 설정 관리** | Site Recovery는 효율적인 네트워크 전환을 위한 IP 주소 예약, 부하 분산 장치 구성 또는 Azure Traffic Manager의 통합 등 간단한 응용 프로그램 네트워크 관리를 위해 Azure와 통합됩니다.


## <a name="what-can-i-replicate"></a>무엇을 복제할 수 있습니까?

**지원됨** | **세부 정보**
--- | ---
**무엇을 복제할 수 있습니까?** | Azure 지역 간 Azure VM 복제<br/><br/>  Azure에 온-프레미스 VMware VM, Hyper-V VM, 물리적 서버(Windows 및 Linux) 복제<br/><br/> VMM(Virtual Machine Manager)에 온-프레미스 VMware VM, Hyper-V VM, 물리적 서버 복제
**어떤 지역이 Site Recovery에 지원됩니까?** | [지원되는 지역](https://azure.microsoft.com/regions/services/) |
**복제된 컴퓨터에 필요한 운영 체제는 무엇입니까?** | [Azure VM 요구 사항](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)</br></br>[VMware VM 요구 사항](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Hyper-V VM의 경우 Azure 및 Hyper-V에서 지원하는 [게스트 OS](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)가 지원됩니다.<br/><br/> [물리적 서버 요구 사항](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**어떤 VMware 서버/호스트가 필요합니까?** | VMware VM은 [지원되는 vSphere 호스트/vCenter 서버](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)에 있을 수 있습니다.
**어떤 워크로드를 복제할 수 있습니까?** | 지원되는 복제 컴퓨터에서 실행되는 모든 워크로드를 복제할 수 있습니다. 또한 Site Recovery 팀이 [몇 가지 앱](site-recovery-workload.md#workload-summary)에 대해 앱별 테스팅을 수행하였습니다.



## <a name="next-steps"></a>다음 단계
* [워크로드 지원](site-recovery-workload.md)에 대해 자세히 알아봅니다.
* [지역 간 Azure VM 복제](azure-to-azure-quickstart.md)를 시작합니다. 
