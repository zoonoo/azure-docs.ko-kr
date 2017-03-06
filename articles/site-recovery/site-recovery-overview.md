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
ms.date: 02/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 31c0d8a3b525d24861090adb0edf0351fae7467e
ms.openlocfilehash: de89ea7309736ac1636e7950d1cd7e68f9ec2f0e
ms.lasthandoff: 02/23/2017


---
# <a name="what-is-site-recovery"></a>사이트 복구란?

Azure Site Recovery 서비스를 시작합니다.

이 문서에서는 Azure Site Recovery 서비스의 개요와 추가 정보에 대한 링크를 제공합니다.

비즈니스 중단은 자연 현상 및 작업 오류로 인해 발생합니다. 조직에는 계획되거나 계획되지 않은 중단 중에 앱과 데이터를 안전하고 사용 가능하게 유지하고, 가능한 한 빨리 정상 작업 상태로 복구할 수 있는 BCDR(비즈니스 연속성 및 재해 복구) 전략이 필요합니다.

Azure Recovery Services는 BCDR 전략에 기여합니다. [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/)을 사용하여 데이터를 안전하고 복구 가능하게 유지합니다. Site Recovery를 통해 워크로드를 복제, 장애 조치 및 복구하여 장애가 발생할 때 계속 사용할 수 있습니다.

## <a name="benefits"></a>이점

Site Recovery가 BCDR 전략을 통해 도움이 되는 방법은 다음과 같습니다.

- **클라우드에서의 재해 복구** - VM 및 물리적 서버에서 실행되는 워크로드를 보조 사이트가 아닌 Azure로 복제할 수 있습니다. 이렇게 하면 보조 데이터 센터를 유지하는 비용과 복잡성을 제거할 수 있습니다.
- **하이브리드 환경에 대한 유연한 복제** - 지원되는 온-프레미스 Hyper-V VM, VMware VM 및 Windows/Linux 물리적 서버에서 실행되는 모든 워크로드를 복제할 수 있습니다.
- **마이그레이션** - Site Recovery를 사용하여 온-프레미스 AWS 인스턴스를 Azure VM으로 마이그레이션하거나 Azure 지역 간에 Azure VM을 마이그레이션할 수 있습니다.
- **간소화된 BCDR** - Azure Portal의 단일 위치에서 복제를 배포할 수 있습니다.  단일 및 다중 컴퓨터의 간단한 장애 조치 및 장애 복구를 실행할 수 있습니다.
- **복원력** - Site Recovery는 응용 프로그램 데이터를 차단하지 않고 복제와 장애 조치를 오케스트레이션합니다.
복제된 데이터는 제공되는 복원력을 통해 Azure Storage에 저장됩니다. 장애 조치가 발생하는 경우 복제된 데이터를 기반으로 하여 Azure VM을 만듭니다.
- **복제 성능** - Site Recovery는 Hyper-V에 대해 짧은 빈도(30초)의 복제를 제공하고, VMware에 대해서는 연속 복제를 제공합니다. RPO(복구 지점 목표) 임계값을 설정하여 데이터 복구 지점을 만드는 빈도를 제어하고, Site Recovery의 자동화된 복구 프로세스 및 [Azure Traffic Manager](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)와의 통합을 통해 RTO(복구 시간 목표)를 줄일 수 있습니다.
- **응용 프로그램 일관성** - 컴퓨터에서 응용 프로그램 일치 스냅숏을 사용하여 복제합니다. 응용 프로그램 일관성 스냅숏은 디스크 데이터를 캡처할 뿐만 아니라 메모리의 모든 데이터와 프로세스의 모든 트랜잭션을 캡처합니다.
- **중단 없이 테스트** - 프로덕션 환경에 영향을 주지 않고 재해 복구 훈련을 지원하는 테스트 장애 조치를 손쉽게 실행할 수 있습니다.
- **유연한 장애 조치 및 복구** - 예상된 중단에 대해서는 계획된 데이터 무손실 장애 조치를 실행하고, 예기치 않은 재해에 대해서는 복제 빈도에 따라 계획되지 않은 데이터 최소 손실 장애 조치를 실행할 수 있습니다. 기본 사이트를 다시 사용할 수 있는 경우 손쉽게 이 사이트로 장애 복구할 수 있습니다.
- **다양한 복구 계획 제공** - 복구 계획을 사용하면 여러 VM에 분산되어 있는 다중 계층 응용 프로그램의 장애 조치와 복구를 모델링하고 사용자 지정할 수 있습니다. 계획 내에서 그룹을 정렬하고 스크립트와 수동 작업을 추가합니다. 복구 계획은 Azure Automation runbook과 통합될 수 있습니다.
- **다중 계층 앱** - 다중 계층 앱의 순차화된 장애 조치 및 복구를 위한 복구 계획을 만들 수 있습니다. 복구 계획 내에서 다른 계층(예: 데이터베이스, 웹, 앱)의 컴퓨터를 그룹화하고, 각 그룹이 장애 조치하고 시작하는 방법을 사용자 지정할 수 있습니다.
* **기존 BCDR 기술과의 통합** - Site Recovery는 다른 BCDR 기술과 통합됩니다. 예를 들어 Site Recovery를 사용하여 가용성 그룹의 장애 조치를 관리하는 AlwaysOn에 대한 SQL Server의 기본 지원을 비롯한 회사 워크로드의 SQL Server 백 엔드를 보호할 수 있습니다.
* **자동화 라이브러리와의 통합** - 다양한 Azure Automation 라이브러리는 Site Recovery와 함께 다운로드하고 통합할 수 있는 프로덕션 지원 응용 프로그램별 스크립트를 제공합니다.
* **간단한 네트워크 관리** - Site Recovery 및 Azure의 고급 네트워크 관리는 효율적인 네트워크 전환을 위해 IP 주소 예약, 부하 분산 장치 구성 또는 Azure Traffic Manager의 통합을 포함하는 응용 프로그램 네트워크 요구 사항을 단순화합니다.

## <a name="site-recovery-in-the-azure-portal"></a>Azure 포털에서 Site Recovery

* Site Recovery는 최신 [Azure Portal](https://portal.azure.com)과 [Azure 클래식 포털](https://manage.windowsazure.com/) 모두에서 배포할 수 있습니다.
* Azure 클래식 포털에서 기존 서비스 관리 모델을 사용하여 Site Recovery를 지원할 수 있습니다.
* Azure Portal에서는 클래식 모델 또는 최신 [Resource Manager 배포 모델](../azure-resource-manager/resource-manager-deployment-model.md)을 지원할 수 있습니다.
- 클래식 포털은 기존 Site Recovery 배포를 유지하는 데에만 사용해야 합니다. 클래식 포털에서는 새 자격 증명 모음을 만들 수 없습니다.

이 문서의 정보는 클래식 및 Azure 포털 배포 모두에 적용됩니다. 차이점에 대해서는 해당 위치에 지적해 두었습니다.

## <a name="whats-supported"></a>지원되는 내용

**지원** | **세부 정보**
--- | ---
**어떤 지역이 지원됩니까?** | [지원되는 지역](https://azure.microsoft.com/en-us/regions/services/) |
**무엇을 복제할 수 있습니까?** | 온-프레미스 VMware VM, Hyper-V VM, Windows 및 Linux 물리적 서버
**복제된 시스템에 지원되는 운영 체제는 무엇입니까?** | VMware VM용 [운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Hyper-V VM의 경우 Azure 및 Hyper-V에서 지원하는 모든 [게스트 OS](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)가 지원됩니다.<br/><br/> 물리적 서버용 [운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**어떤 위치에 복제할 수 있습니까?** | Azure 또는 보조 데이터 센터<br/><br/> System Center VMM 클라우드에서 관리되는 Hyper-V 호스트의 VM만 보조 데이터 센터에 복제할 수 있습니다.
**어떤 VMware 서버/호스트가 필요합니까?** | 복제하려는 VMware VM은 [지원되는 vSphere 호스트/vCenter 서버](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)에서 관리할 수 있습니다.
**어떤 워크로드를 복제할 수 있습니까?** | 지원되는 복제 컴퓨터에서 실행되는 모든 워크로드를 복제할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 자세한 내용은 [어떤 워크로드가 Site Recovery를 보호할 수 있습니까?](site-recovery-workload.md)
* Site Recovery 아키텍처에 대한 자세한 내용은 [Site Recovery 작동 방식](site-recovery-components.md)

