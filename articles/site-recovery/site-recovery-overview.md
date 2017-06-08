---
title: "사이트 복구란? | Microsoft Docs"
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
ms.date: 03/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 276d7949e01b8936804514c5fe8bd26af438bd08
ms.contentlocale: ko-kr
ms.lasthandoff: 05/31/2017


---
# <a name="what-is-site-recovery"></a>사이트 복구란?

Azure Site Recovery 서비스를 시작합니다. 이 문서에서는 서비스에 대한 빠른 개요를 제공합니다.

중단은 자연 현상 및 작업 오류로 인해 발생합니다. 조직에서는 계획되거나 계획되지 않은 중단 중에 데이터를 안전하게 유지하고 앱을 사용 가능하게 유지하는 BCDR(비즈니스 연속성 및 재해 복구) 전략이 필요하며, 비즈니스를 가능한 한 빨리 정상 작업 상태로 복구해야 합니다.

Azure Recovery Services는 BCDR 전략에 기여합니다. [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/) 서비스를 사용하여 데이터를 안전하고 복구 가능하게 유지합니다. Site Recovery는 워크로드를 복제, 장애 조치 및 복구하여 장애가 발생하는 경우에도 계속 사용할 수 있습니다.

## <a name="what-does-site-recovery-provide"></a>Site Recovery의 기능은 무엇입니까?

- **클라우드에서 재해 복구** - Azure에서 Azure로 재해 복구 솔루션을 사용하여 Azure에서 실행되는 VM을 복제 및 보호할 수 있습니다. VM 및 물리적 서버에서 실행되는 워크로드를 보조 사이트가 아닌 Azure로 복제할 수 있습니다. 이렇게 하면 보조 데이터 센터를 유지하는 비용과 복잡성을 제거할 수 있습니다.
- **하이브리드 환경에 대한 유연한 복제** - Azure VM, 온-프레미스 Hyper-V VM, VMware VM 및 Windows/Linux 물리적 서버에서 지원되는 모든 워크로드를 복제할 수 있습니다.
- **마이그레이션** - Site Recovery를 사용하여 온-프레미스 및 AWS 인스턴스를 Azure VM으로 마이그레이션하거나 Azure 지역 간에 Azure VM을 마이그레이션할 수 있습니다.
- **간소화된 BCDR** - Azure Portal의 단일 위치에서 복제를 배포할 수 있습니다.  단일 및 다중 컴퓨터의 간단한 장애 조치 및 장애 복구를 실행할 수 있습니다.
- **복원력** - Site Recovery는 응용 프로그램 데이터를 차단하지 않고 복제와 장애 조치를 오케스트레이션합니다. 복제된 데이터는 Azure Storage가 제공하는 복원력을 통해 Azure Storage에 저장됩니다. 장애 조치가 발생하는 경우 복제된 데이터를 기반으로 하여 Azure VM을 만듭니다.
- **복제 성능** - Site Recovery는 Azure VM 및 VMware VM에 대해 연속 복제를 제공하고 Hyper-V에 대해서는 짧은 빈도(30초)의 복제를 제공합니다. Site Recovery의 자동화된 복구 프로세스 및 [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)와의 통합으로 RTO(복구 시간 목표)를 줄일 수 있습니다.
- **응용 프로그램 일관성** - 복구 지점에 대해 응용 프로그램에 일관된 스냅숏을 구성할 수 있습니다. 응용 프로그램 일관성 스냅숏은 디스크 데이터를 캡처할 뿐만 아니라 메모리의 모든 데이터와 프로세스의 모든 트랜잭션을 캡처합니다.
- **중단 없이 테스트** - 프로덕션 환경 및 진행 중인 복제에 영향을 주지 않고 재해 복구 훈련을 지원하는 테스트 장애 조치를 손쉽게 실행할 수 있습니다.
- **유연한 장애 조치 및 복구** - 예상된 중단에 대해서는 계획된 데이터 무손실 장애 조치를 실행하고, 예기치 않은 재해에 대해서는 복제 빈도에 따라 계획되지 않은 데이터 최소 손실 장애 조치를 실행할 수 있습니다. 기본 사이트를 다시 사용할 수 있는 경우 손쉽게 이 사이트로 장애 복구할 수 있습니다.
- **사용자 지정 복구 계획** - 복구 계획을 사용하면 여러 VM에 분산되어 있는 다중 계층 응용 프로그램의 장애 조치와 복구를 모델링하고 사용자 지정할 수 있습니다. 계획 내에서 그룹을 정렬하고 스크립트와 수동 작업을 추가합니다. 복구 계획은 Azure Automation runbook과 통합될 수 있습니다.
- **다중 계층 앱** - 다중 계층 앱의 순차화된 장애 조치 및 복구를 위한 복구 계획을 만들 수 있습니다. 복구 계획 내에서 다른 계층(예: 데이터베이스, 웹, 앱)의 컴퓨터를 그룹화하고, 각 그룹이 장애 조치하고 시작하는 방법을 사용자 지정할 수 있습니다.
* **기존 BCDR 기술과의 통합** - Site Recovery는 다른 BCDR 기술과 통합됩니다. 예를 들어 Site Recovery를 사용하여 가용성 그룹의 장애 조치를 관리하는 AlwaysOn에 대한 SQL Server의 기본 지원을 비롯한 회사 워크로드의 SQL Server 백 엔드를 보호할 수 있습니다.
* **자동화 라이브러리와의 통합** - 다양한 Azure Automation 라이브러리는 Site Recovery와 함께 다운로드하고 통합할 수 있는 프로덕션 지원 응용 프로그램별 스크립트를 제공합니다.
* **간단한 네트워크 관리** - Site Recovery 및 Azure의 고급 네트워크 관리는 효율적인 네트워크 전환을 위해 IP 주소 예약, 부하 분산 장치 구성 또는 Azure Traffic Manager의 통합을 포함하는 응용 프로그램 네트워크 요구 사항을 단순화합니다.


## <a name="whats-supported"></a>지원되는 내용은 무엇입니까?

**지원됨** | **세부 정보**
--- | ---
**어떤 지역이 Site Recovery에 지원됩니까?** | [지원되는 지역](https://azure.microsoft.com/en-us/regions/services/) |
**무엇을 복제할 수 있습니까?** | Azure VM(미리 보기), 온-프레미스 VMware VM, Hyper-V VM, Windows 및 Linux 물리적 서버.
**복제된 컴퓨터에 필요한 운영 체제는 무엇입니까?** | Azure VM에 대해 [지원되는 운영 체제](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)<br></br>VMware VM용 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Hyper-V VM의 경우 Azure 및 Hyper-V에서 지원하는 [게스트 OS](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)가 지원됩니다.<br/><br/> 물리적 서버용 [운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**어떤 위치에 복제할 수 있습니까?** | Azure VM의 경우 다른 Azure 지역에 복제할 수 있습니다. <br></br> 온-프레미스 컴퓨터의 경우 Azure Storage 또는 보조 데이터 센터에 복제할 수 있습니다.<br/><br/> 

>[!NOTE]
>
> Hyper-V의 경우 System Center VMM 클라우드에서 관리되는 Hyper-V 호스트의 VM만 보조 데이터 센터로 복제할 수 있습니다.

**어떤 VMware 서버/호스트가 필요합니까?** | 복제하려는 VMware VM은 [지원되는 vSphere 호스트/vCenter 서버에서 관리할 수 있습니다.](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**어떤 워크로드를 복제할 수 있습니까?** | 지원되는 복제 컴퓨터에서 실행되는 모든 워크로드를 복제할 수 있습니다. 또한 Site Recovery 팀이 [몇 가지 앱](site-recovery-workload.md#workload-summary)에 대해 앱별 테스팅을 수행하였습니다.


## <a name="which-azure-portal"></a>어떤 Azure Portal입니까?

* Site Recovery는 [Azure Portal](https://portal.azure.com)에서 배포할 수 있습니다.
* Azure 클래식 포털에서 기존 서비스 관리 모델을 사용하여 Site Recovery를 관리할 수 있습니다.
- 클래식 포털은 기존 Site Recovery 배포를 유지하는 데에만 사용해야 합니다. 클래식 포털에서는 새 자격 증명 모음을 만들 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Virtual Machines 복제](site-recovery-azure-to-azure.md)에 대해 자세히 알아보세요.
* [워크로드 지원](site-recovery-workload.md)에 대해 자세히 알아보세요.
* [Site Recovery 아키텍처 및 구성 요소](site-recovery-components.md)에 대해 자세히 알아보세요.

