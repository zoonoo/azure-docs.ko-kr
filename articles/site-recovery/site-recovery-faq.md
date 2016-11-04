---
title: 'Azure Site Recovery: 질문과 대답(FAQ) | Microsoft Docs'
description: 이 문서에서는 Azure Site Recovery에 대한 일반적인 질문에 대답합니다.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: get-started-article
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/12/2016
ms.author: raynew

---
# Azure Site Recovery: FAQ(질문과 대답)
## 이 문서의 내용
이 문서는 Azure Site Recovery에 대한 질문과 대답을 제공합니다. 이 문서를 읽은 후 질문이 있다면 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 게시해 주세요.

## 일반
### Site Recovery의 기능은 무엇입니까?
Site Recovery는 온-프레미스 가상 컴퓨터 및 물리적 서버에서 Azure 또는 보조 데이터 센터로의 복제 작업을 오케스트레이션 및 자동화하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. [자세히 알아봅니다](site-recovery-overview.md).

### Site Recovery로 무엇을 보호할 수 있습니까?
* **VMware 가상 컴퓨터**: Site Recovery는 Hyper-V VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다.
* **물리적 서버**: Site Recovery는 Windows 또는 Linux를 실행하는 물리적 서버를 보호할 수 있습니다.
* **VMware 가상 컴퓨터**: Site Recovery는 VMware VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다.

### Site Recovery는 Azure Resource Manager 모델을 지원합니까?
Azure 클래식 포털의 Site Recovery 외에도 Resource Manager에 대한 지원을 통해 Azure 포털에서 Site Recovery를 사용할 수 있습니다. 대부분의 배포 시나리오에서 Azure 포털의 Site Recovery는 간소화된 배포 경험을 제공하며 VM 및 물리적 서버를 클래식 저장소 또는 Resource Manager 저장소에 복제할 수 있습니다. 지원되는 배포는 다음과 같습니다.

* [Azure 포털에서 VMware VM 또는 물리적 서버를 Azure로 복제](site-recovery-vmware-to-azure.md)
* [Azure 포털에서 VMM 클라우드의 Hyper-V VM을 Azure로 복제](site-recovery-vmm-to-azure.md)
* [Azure 포털에서 (VMM 없이) Hyper-V VM을 Azure로 복제](site-recovery-hyper-v-site-to-azure.md)
* [Azure 포털에서 VMM 클라우드의 Hyper-V VM을 보조 사이트로 복제](site-recovery-vmm-to-vmm.md)

### Hyper-V에서 Site Recovery를 통해 복제를 오케스트레이션하기 위해 필요한 것은 무엇입니까?
Hyper-V 호스트 서버에서 필요한 사항은 배포 시나리오에 따라 달라집니다. Hyper-V 필수 구성 요소를 확인해 보세요.

* [Azure로 Hyper-V VM 복제(VMM 없이)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
* [Azure로 Hyper-V VM 복제(VMM 사용)](site-recovery-vmm-to-azure.md#before-you-start)
* [보조 데이터 센터에 Hyper-V VM 복제](site-recovery-vmm-to-vmm.md#before-you-start)
* 보조 데이터 센터에 복제하는 경우 [Hyper-V VM에 대해 지원되는 게스트 운영 체제](https://technet.microsoft.com/library/mt126277.aspx)에 대해 알아보세요.
* Azure에 복제하는 경우에는 [Azure에서 지원되는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 모든 게스트 운영 체제를 Site Recovery에서도 지원합니다.

### Hyper-V가 클라이언트 운영 체제에서 실행 중인 경우 VM을 보호할 수 있습니까?
아니요, VM은 지원되는 Windows 서버 컴퓨터를 실행하는 Hyper-V 호스트 서버에 위치해야 합니다. 클라이언트 컴퓨터를 보호해야 하는 경우 물리적 컴퓨터를 [Azure](site-recovery-vmware-to-azure.md) 또는 [보조 데이터 센터](site-recovery-vmware-to-vmware.md)로 복제할 수 있습니다.

### Site Recovery로 어떤 워크로드를 보호할 수 있습니까?
Site Recovery를 사용하여 지원되는 VM 또는 물리적 서버에서 실행되는 대부분의 워크로드를 보호할 수 있습니다. 사이트 복구는 응용 프로그램 인식 복제를 제공하므로 앱을 지능형 상태로 복구할 수 있습니다. SharePoint, Exchange, Dynamics, SQL Server, Active Directory와 같은 Microsoft 응용 프로그램과 통합되고, Oracle, SAP, IBM, Red Hat 등의 선두 공급 업체 제품과 긴밀하게 작동합니다. 워크로드 보호에 대해 [자세히 알아보세요](site-recovery-workload.md).

### Hyper-V 호스트가 VMM 클라우드에 있어야 합니까?
보조 데이터 센터에 복제하려는 경우 Hyper-V VM은 VMM 클라우드에 위치한 Hyper-V 호스트 서버에 있어야 합니다. Azure로 복제하려는 경우 VMM 클라우드 존재 여부와 상관없이 Hyper-V 호스트 서버에서 VM을 복제할 수 있습니다. [자세히 알아보기](site-recovery-hyper-v-site-to-azure.md)

### VMM 서버가 하나밖에 없는 경우 VMM을 사용하여 Site Recovery를 배포할 수 있습니까?
예. VMM 클라우드의 Hyper-V 서버에서 VM을 Azure로 복제하거나 같은 서버의 VMM 클라우드 간에 복제할 수 있습니다. 온-프레미스 간 복제는 기본 사이트와 보조 사이트에 VMM 서버가 있는 경우에 사용하는 것이 좋습니다. [자세히 알아보기](site-recovery-single-vmm.md)

### 어떤 물리적 서버를 보호할 수 있습니까?
Windows 및 Linux를 실행하는 물리적 서버를 Azure 또는 보조 사이트로 복제할 수 있습니다. 운영 체제 요구 사항에 대해 [알아봅니다](site-recovery-vmware-to-azure.md#protected-machine-prerequisites). 물리적 서버에서 Azure로 복사하든 아니면 보조 사이트로 복사하든 관계없이 동일한 요구 사항이 적용됩니다.

온-프레미스 서버가 중지되면 물리적 서버가 Azure에서 VM으로 실행됩니다. 온-프레미스 물리적 서버로의 장애 복구는 현재 지원되지 않지만 Hyper-V 또는 VMware에서 실행되는 가상 컴퓨터로의 장애 복구는 가능합니다.

### 어떤 VMware VM을 보호할 수 있습니까?
VMware VM을 보호하려면 vSphere 하이퍼바이저 및 VMware 도구를 실행 중인 가상 컴퓨터가 필요합니다. 하이퍼바이저를 관리하는 VMware vCenter 서버가 있는 것이 좋습니다. VMware 서버 및 VM을 Azure 또는 보조 사이트로 복제하기 위한 정확한 요구 사항에 대해 [자세히 알아보세요](site-recovery-vmware-to-azure.md#protected-machine-prerequisites).

### Site Recovery로 지사의 재해 복구를 관리할 수 있습니까?
예. 지사에서 Site Recovery를 사용하여 복제를 오케스트레이션하고 장애 조치(failover)를 수행하면 중앙의 한 위치에 모든 지사 워크로드의 통합되지 않은 오케스트레이션 및 보기가 표시됩니다. 지사를 방문하지 않고 본사에서 간편하게 모든 지사의 장애 조치(failover)를 수행하고 재해 복구를 관리할 수 있습니다.

## 보안
### 복제 데이터가 사이트 복구 서비스로 전송되나요?
아니요, 사이트 복구는 복제된 데이터를 가로채거나 사용자의 가상 컴퓨터 또는 물리적 서버에서 실행 중인 항목에 대한 정보를 보유하지 않습니다. 복제 데이터는 온-프레미스 Hyper-V 호스트, VMware 하이퍼바이저 또는 물리적 서버와 Azure 저장소 또는 보조 사이트 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.

Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA 인증을 받았으며, SOC2 및 FedRAMP JAB 평가를 수행하는 중입니다.

### 규정 준수를 위해 온-프레미스 환경의 메타데이터까지도 지리적으로 같은 지역에 남아 있어야 합니다. Site Recovery가 도움이 되나요?
예. 사용자가 한 지역에 Site Recovery 자격 증명 모음을 만들면 복제 및 장애 조치(failover)를 활성화하고 오케스트레이션하는 데 필요한 모든 메타데이터가 해당 지역의 지리적 경계 내에 남아 있습니다.

### Site Recovery는 복제를 암호화합니까?
온-프레미스 사이트 간에 가상 컴퓨터와 물리적 서버를 복제할 때 전송 중 암호화가 지원됩니다. Azure에 복제되는 가상 컴퓨터와 물리적 서버의 경우 전송 중 암호화 및 정지된 암호화(Azure 내)가 모두 지원됩니다.

## 복제
### Azure에 가상 컴퓨터를 복제하기 위한 필수 조건은 무엇입니까?
Azure로 복제하려는 가상 컴퓨터가 [Azure 요구 사항](site-recovery-best-practices.md#virtual-machines)을 충족해야 합니다.

### Hyper-V 2세대 가상 컴퓨터를 Azure로 복제할 수 있습니까?
예. 장애 조치(failover) 동안 Site Recovery가 컴퓨터를 2세대에서 1세대로 변환합니다. 장애 복구 시 컴퓨터가 다시 2세대로 변환됩니다. [자세히 알아보기](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Azure로 복제할 경우 Azure VM 요금을 어떻게 결제합니까?
주기적 복제 동안 데이터가 지역 중복 저장소에 복제되므로 어떤 Azure IaaS 가상 컴퓨터 요금도 지불할 필요가 없습니다(큰 장점). Azure로 장애 조치(failover)를 수행하면 Site Recovery에서 자동으로 Azure IaaS 가상 컴퓨터를 만듭니다. 그 후 Azure에서 소비한 계산 리소스만큼 요금이 청구됩니다.

### ASR 워크플로 자동화에 사용할 수 있는 SDK가 있습니까?
예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. 현재 PowerShell을 사용하여 Site Recovery를 배포하기 위한 지원되는 시나리오:

* [VMM 클라우드의 Hyper-V VM을 Azure PowerShell 클래식으로 복제](site-recovery-deploy-with-powershell.md)
* [VMM 클라우드의 Hyper-V VM을 Azure PowerShell Resource Manager로 복제](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Hyper-V VM을 VMM 없이 Azure PowerShell 클래식으로 복제](site-recovery-hyper-v-site-to-azure-classic.md)
* [Hyper-V VM을 VMM 없이 Azure PowerShell Resource Manager로 복제](site-recovery-deploy-with-powershell-resource-manager.md)

### Azure로 복제할 때 어떤 유형의 저장소 계정이 필요합니까?
* **Azure 클래식 포털**: Azure 클래식 포털에서 Site Recovery를 배포하는 경우 [표준 지역 중복 저장소 계정](../storage/storage-redundancy.md#geo-redundant-storage)이 있어야 합니다. 프리미엄 저장소는 현재 지원되지 않습니다. 계정은 Site Recovery 자격 증명 모음과 동일한 지역에 있어야 합니다.
* **Azure 포털**: Azure 포털에서 Site Recovery를 배포하는 경우 LRS 또는 GRS 저장소 계정이 있어야 합니다. 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 GRS를 사용하는 것이 좋습니다. 계정은 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다. 현재 프리미엄 저장소는 VMware VM 또는 물리적 서버를 복제하는 경우에만 지원됩니다.

### 데이터를 얼마나 자주 복제할 수 있나요?
* **Hyper-V:** Hyper-V VM을 30초, 5분 또는 15분마다 복제할 수 있습니다. SAN 복제를 설정하면 복제가 동기화됩니다.
* **VMware 및 물리적 서버:** 복제 빈도는 이와 관련이 없습니다. 복제가 계속 됩니다.

### 기존 복구 사이트에서 3차 사이트로 복제를 확장할 수 있습니까?
확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)을 통해 이 기능에 대한 의견을 보내 주세요.

### Azure에 처음으로 복제할 때 오프라인으로 복제할 수 있습니까?
지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### 특정 디스크를 복제에서 제외할 수 있습니까?
이는 Azure 포털을 사용하여 [VMware VM 및 물리적 서버를 Azure에 복제](site-recovery-vmware-to-azure.md#exclude-disks-from-replication)하는 경우에만 지원됩니다.

### 동적 디스크를 사용하여 가상 컴퓨터를 복제할 수 있습니까?
동적 디스크는 Hyper-V 가상 컴퓨터를 복제할 때 지원됩니다. 또한 [Azure 포털](site-recovery-vmware-to-azure.md) 또는 [향상된 배포를 포함한 Azure 클래식 포털](site-recovery-vmware-to-azure-classic.md)을 사용하는 경우 VMware VM 및 물리적 컴퓨터를 Azure에 복제할 때에도 지원됩니다. OS 디스크가 기본 디스크여야 합니다.

### Hyper-V 복제 트래픽에 할당된 대역폭을 제한할 수 있습니까?
예. 배포 문서에서 대역폭 제한에 대해 더 자세히 검토할 수 있습니다.

* [VMware VM 및 물리적 서버를 복제하기 위한 용량 계획](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
* [을 복제하기 위한 용량 계획](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
* [VMM 없이 Hyper-V VM을 복제하기 위한 용량 계획](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## 장애 조치(Failover)
### Azure로 장애 조치(Failover)하는 경우 장애 조치(Failover) 후에 어떻게 Azure 가상 컴퓨터에 액세스할 수 있습니까?
보안 인터넷 연결 또는 사이트 간 VPN 또는 Azure Express 경로로 Azure VM에 액세스할 수 있습니다. 연결하려면 여러 가지 사항을 준비해야 합니다. 자세히 알아보기:

* [VMware VM 또는 물리적 서버의 장애 조치 후 Azure VM에 연결](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
* [VMM 클라우드에서 Hyper-V VM의 장애 조치 후 Azure VM에 연결](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
* [VMM 없이 Hyper-V VM의 장애 조치 후 Azure VM에 연결](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)

### Azure로 장애 조치(Failover)하는 경우 Azure는 어떻게 데이터 복원을 보장합니까?
Azure는 복원을 위해 디자인되었습니다. Site Recovery는 이미 Azure SLA를 따라 보조 Azure 데이터 센터에 대한 장애 조치(Failover)를 위해 엔지니어링되었습니다. 이 상황이 발생하면 사용자가 자격 증명 모음에 대해 선택한 지리적으로 동일한 지역에 메타데이터 및 자격 증명 모음이 남아 있습니다.

### 두 데이터 센터 간에 복제하는 동안 주 데이터 센터에서 예기치 않게 정전이 발생하면 어떻게 됩니까?
보조 사이트에서 계획되지 않은 장애 조치(Failover)를 트리거할 수 있습니다. Site Recovery는 기본 사이트가 연결되지 않아도 장애 조치(Failover)를 수행할 수 있습니다.

### 장애 조치(Failover)는 자동입니까?
자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치(Failover)를 시작하거나 [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx)을 사용하여 장애 조치(Failover)를 트리거할 수 있습니다. 장애 복구(failback) 작업 역시 Site Recovery 포털에서 간단하게 수행할 수 있습니다.

온-프레미스 Orchestrator 또는 Operations Manager를 사용하여 가상 컴퓨터 오류를 감지하면 SDK를 사용하여 장애 조치(Failover)를 트리거하도록 자동화할 수 있습니다.

* [자세한](site-recovery-create-recovery-plans.md) 복구 계획을 알아봅니다.
* 장애 조치에 대해 [자세히 알아보세요](site-recovery-failover.md).
* VMware VM 및 물리적 서버 장애 복구에 대해 [자세히 알아보기](site-recovery-failback-azure-to-vmware.md)

## 서비스 공급자
### 서비스 공급자입니다. Site Recovery는 전용 또는 공유 인프라 모델에 대해 작동합니까?
예, Site Recovery는 전용 및 공유 인프라 모델 모두에 대해 작동합니다.

### 서비스 공급자의 경우 내 테넌트의 ID가 Site Recovery 서비스와 공유됩니까?
아니요. 테넌트 ID는 익명으로 유지됩니다. 사용자의 테넌트는 사이트 복구 포털에 액세스할 필요가 없습니다. 서비스 공급자 관리자만 포털과 상호 작용합니다.

### 테넌트 응용 프로그램 데이터가 Azure로 이동될까요?
서비스 공급자가 소유하고 있는 사이트 간에 복제할 경우 응용 프로그램 데이터가 Azure로 이동되지 않습니다. 데이터는 전송 중에 암호화되어 서비스 공급자 사이트 간에 직접 복제됩니다.

Azure로 복제하는 경우 응용 프로그램 데이터가 Azure 저장소로 전송되지만 Site Recovery 서비스로는 전송되지 않습니다. 데이터는 전송 중에 암호화되어 Azure에 암호화된 상태로 남아 있습니다.

### 내 테넌트로 Azure 서비스에 대한 청구서가 발급됩니까?
아니요. Azure의 청구 관계는 서비스 공급자와 직접 유지됩니다. 서비스 공급자는 해당 테넌트에 대한 특정 청구서를 생성하는 일을 담당합니다.

### Azure로 복제할 때 항상 Azure에서 가상 컴퓨터를 실행해야 합니까?
아니요, 데이터는 구독의 Azure 저장소 계정에 복제됩니다. 테스트 장애 조치(Failover)(DR 드릴) 또는 실제 장애 조치(Failover)를 수행하면 Site Recovery가 구독에서 자동으로 가상 컴퓨터를 만듭니다.

### Azure에 복제할 때 테넌트 수준의 격리가 보장되나요?
예.

### 현재 어떤 플랫폼이 지원됩니까?
Azure Pack, 클라우드 플랫폼 시스템 및 시스템 센터 기반(2012 이상)의 배포가 지원됩니다. Azure Pack 및 Site Recovery 통합에 대해 [자세히 알아보세요](https://technet.microsoft.com/library/dn850370.aspx).

### 단일 Azure Pack 및 단일 VMM 서버 배포가 지원되나요?
예, Hyper-V 가상 컴퓨터를 Azure에 복제하거나 서비스 공급자 사이트 간에 복제할 수 있습니다. 서비스 공급자 사이트 간에 복제할 경우 Azure Runbook 통합을 사용할 수 없습니다.

## 다음 단계
* [Site Recovery 개요](site-recovery-overview.md) 보기
* [Site Recovery 아키텍처](site-recovery-components.md)에 대해 알아보기

<!---HONumber=AcomDC_0720_2016-->