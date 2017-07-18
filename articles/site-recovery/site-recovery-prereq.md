---
title: "Azure Site Recovery를 사용하여 Azure에 복제하기 위한 필수 구성 요소 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery 서비스를 사용하여 Azure에 VM 및 물리적 컴퓨터를 복제하기 위한 필수 구성 요소를 설명합니다."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/23/2017
ms.author: rajanaki
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 1ed0bfe4f1b77db00dc858f010f72e084e77039e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/31/2017

---

#  <a name="prerequisites-for-replication-from-on-premises-to-azure-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 온-프레미스에서 Azure로 복제하기 위한 필수 조건

> [!div class="op_single_selector"]
> * [Azure에서 Azure로 복제](site-recovery-azure-to-azure-prereq.md)
> * [온-프레미스에서 Azure로 복제](site-recovery-prereq.md)

Azure Site Recovery 서비스는 Azure 가상 컴퓨터를 다른 Azure 지역으로, 그리고 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터센터로 복제하는 것을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지할 수 있습니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갈 수 있습니다. Site Recovery에 대한 자세한 내용은 [Site Recovery란?](site-recovery-overview.md)을 참조하세요.

이 문서에서는 온-프레미스에서 Azure로 Site Recovery 복제를 시작하는 데 필요한 필수 조건을 설명합니다.

이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 기술적인 질문을 합니다.

## <a name="azure-requirements"></a>Azure 요구 사항

**요구 사항** | **세부 정보**
--- | ---
**Azure 계정** | [Microsoft Azure](http://azure.microsoft.com/) 계정.<br/><br/> [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.
**Site Recovery 서비스** | Site Recovery 가격 책정에 대한 자세한 내용은 [Site Recovery 가격](https://azure.microsoft.com/pricing/details/site-recovery/)을 참조하세요. |
**Azure 저장소** | 복제된 데이터를 저장할 Azure Storage 계정이 필요하고 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다. 복제된 데이터는 Azure 저장소에 저장되고 장애 조치(Failover) 발생 시 Azure VM이 생성됩니다.<br/><br/> 장애 조치(failover)된 Azure VM에 사용하려는 리소스 모델에 따라 [Azure Resource Manager 모델](../storage/storage-create-storage-account.md) 또는 [클래식 모델](../storage/storage-create-storage-account-classic-portal.md)에서 계정을 설정할 수 있습니다.<br/><br/>[지역 중복 저장소](../storage/storage-redundancy.md#geo-redundant-storage) 또는 로컬 중복 저장소를 사용할 수 있습니다. 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 지역 중복 저장소를 사용하는 것이 좋습니다.<br/><br/> Standard 또는 Premium Storage를 사용할 수 있습니다. [프리미엄 저장소](https://docs.microsoft.com/azure/storage/storage-premium-storage)는 IO를 많이 사용하는 워크로드를 호스트하기 위해 일관된 IO 고성능과 짧은 대기 시간이 요구되는 가상 컴퓨터에 일반적으로 사용됩니다. 복제된 데이터에 Premium Storage를 사용하는 경우 온-프레미스 데이터에 대한 지속적인 변화를 캡처하는 복제 로그를 저장할 표준 저장소 계정도 있어야 합니다.<br/><br/>
**저장소 제한** | Site Recovery에서 사용하는 저장소 계정은 리소스 그룹 간이나 동일한 구독 내 또는 구독 간에 이동할 수 없습니다.<br/><br/> 인도 중부 및 인도 남부의 Premium Storage 계정에 대한 복제는 현재 지원되지 않습니다.
**Azure 네트워크** | 장애 조치 후 Azure VM을 연결할 Azure 네트워크가 있어야 하며 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.<br/><br/> Azure Portal에서, [Resource Manager 모델](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) 또는 [클래식 모델](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)에서 네트워크를 만들 수 있습니다.<br/><br/> System Center Virtual Machine Manager에서 Azure로 복제하는 경우 Virtual Machine Manager VM 네트워크와 Azure 네트워크 간 네트워크 매핑을 설정하면 장애 조치 후 Azure VM을 해당 네트워크로 연결할 수 있습니다.
**네트워크 제한 사항** | Site Recovery에서 사용하는 네트워크 계정은 리소스 그룹 간이나 동일한 구독 내 또는 구독 간에 이동할 수 없습니다.
**네트워크 매핑** | Virtual Machine Manager 클라우드에서 Hyper-V VM을 복제하려면 장애 조치 후 Azure VM을 만들 때 적절한 네트워크에 연결되도록 네트워크 매핑을 설정해야 합니다.

>[!NOTE]
>아래 섹션에서는 고객 환경에 있는 다양한 구성 요소의 필수 구성 요소에 대해 설명합니다. 특정 구성의 지원에 대한 자세한 내용은 [지원 매트릭스](site-recovery-support-matrix.md)를 참조하세요.
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windows-or-linux-servers-to-azure"></a>Azure에 대한 VMware 가상 컴퓨터 또는 Windows나 Linux 물리적 서버의 재해 복구
[Azure 요구 사항](#Azure requirements)에 언급한 것 외에도 VMware 가상 컴퓨터 또는 Windows나 Linux 물리적 서버의 재해 복구에 필요한 필수 구성 요소는 아래와 같습니다.


### <a name="configuration-server-or-additional-process-server-you-will-need-to-set-up-an-on-premises-machine-as-the-configuration-server-to-coordinate-communications-between-the-on-premises-site-and-azure-and-to-manage-data-replication-brbr"></a>**서버 구성 또는 추가 프로세스 서버**: 온-프레미스 컴퓨터를 구성 서버로 설정하여 온-프레미스 사이트와 Azure 간의 통신을 조정하고 데이터 복제를 관리해야 합니다. <br></br>

1. **VMware vCenter 또는 vSphere 호스트**

| **구성 요소** | **요구 사항** |
| --- | --- |
| **vSphere** | 하나 이상의 VMware vSphere 하이퍼바이저.<br/><br/>하이퍼바이저는 최신 업데이트를 설치한 vSphere 버전 6.0, 5.5 또는 5.1을 실행해야 합니다.<br/><br/>vSphere 호스트와 vCenter 서버가 프로세스 서버와 동일한 네트워크에 있는 것이 좋습니다. 전용 프로세스 서버를 설정한 경우가 아니라면 구성 서버가 위치한 네트워크입니다. |
| **vCenter** | vSphere 호스트를 관리하는 VMware vCenter Server를 배포하는 것이 좋습니다. 최신 업데이트를 설치한 vCenter 버전 6.0 또는 5.5를 실행해야 합니다.<br/><br/>**제한**: Site Recovery는 크로스 vCenter vMotion을 지원하지 않습니다. 저장소 DRS 및 저장소 vMotion은 마스터 대상 가상 컴퓨터에서 재보호 작업을 게시하는 경우에도 지원되지 않습니다.||

1. **복제된 컴퓨터 필수 조건**


| **구성 요소** | **요구 사항** |
| --- | --- |
| **온-프레미스**(VMware VM) | 복제된 VM에 VMware 도구가 설치되어 있고 실행 중이어야 합니다.<br/><br/> VM은 Azure VM을 만드는 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)에 부합해야 합니다.<br/><br/>보호되는 컴퓨터의 개별 디스크 용량이 1,023GB 이하여야 합니다. <br/><br/>구성 요소 설치를 위해 설치 드라이브에는 최소 2GB의 사용 가능한 공간이 있어야 합니다.<br/><br/>다중 VM 일관성을 사용하려면 VM의 로컬 방화벽에서 포트 20004가 열려 있어야 합니다.<br/><br/>컴퓨터 이름은 1-63자(문자, 숫자 및 하이픈)를 포함해야 합니다. 이름은 문자나 숫자로 시작하고 문자나 숫자로 끝나야 합니다. 컴퓨터에 대한 복제를 활성화한 후 Azure 이름을 수정할 수 있습니다.<br/><br/> |
| **Windows 컴퓨터**(물리적 또는 VMware) | 컴퓨터에서 지원되는 64비트 운영 체제(Windows Server 2012 R2, Windows Server 2012 또는 Windows Server 2008 R2 SP1 이상)를 실행해야 합니다.<br/><br/> C 드라이브에 운영 체제를 설치해야 합니다. OS 디스크는 동적이 아닌 Windows 기본 디스크여야 합니다. 데이터 디스크는 동적일 수 있습니다.<br/><br/>|
| **Linux 컴퓨터**(물리적 또는 VMware) | 다음과 같은 지원되는 64비트 운영 체제가 필요합니다. Red Hat Enterprise Linux 6.7, 6.8, 7.1 또는 7.2. Centos 6.5, 6.6, 6.7, 6.8, 7.0, 7.1 또는 7.2. Ubuntu 14.04 LTS 서버(Ubuntu에서 지원되는 커널 버전 목록은 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) 참조). Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4 또는 6.5. SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4.<br/><br/>보호된 컴퓨터의 /etc/hosts 파일은 로컬 호스트 이름을 모든 네트워크 어댑터와 연관된 IP 주소에 매핑하는 항목을 포함해야 합니다.<br/><br/>장애 조치(Failover) 후에 보안 셸 클라이언트(ssh)를 사용하여 Linux를 실행하는 Azure 가상 컴퓨터에 연결하려는 경우 보호된 컴퓨터의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되었는지, 그리고 방화벽 규칙에서 ssh 연결을 허용하는지 확인합니다.<br/><br/>호스트 이름, 마운트 지점, 장치 이름 및 Linux 시스템 경로와 파일 이름(예: /etc/, /usr)에는 영어만 사용해야 합니다.<br/><br/>/(root), /boot, /usr, /usr/local, /var, /etc 디렉터리(별도의 파티션/파일 시스템으로 설정된 경우)는 모두 원본 서버의 동일한 디스크(OS 디스크)에 있어야 합니다.<br/><br/>메타데이터 체크섬과 같은 XFS v5 기능은 현재 XFS 파일 시스템의 ASR에서 지원하지 않습니다. XFS 파일 시스템이 v5 기능을 사용하고 있지 않은지 확인합니다. xfs_info 유틸리티를 사용하여 파티션에 대한 XFS 수퍼 블록을 확인할 수 있습니다. ftype이 1로 설정되면 XFSv5 기능이 사용됩니다.<br/><br/>Red Hat Enterprise Linux 7 및 CentOS 7 서버에서는 lsof 유틸리티를 설치하여 사용할 수 있어야 합니다.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager"></a>Azure에 대한 Hyper-V 가상 컴퓨터의 재해 복구(Virtual Machine Manager 없음)

[Azure 요구 사항](#Azure requirements)에 언급한 것 외에도 Virtual Machine Manager 클라우드에서 Hyper-V 가상 컴퓨터의 재해 복구에 필요한 필수 구성 요소는 아래와 같습니다.

| **필수 요소** | **세부 정보** |
| --- | --- |
| **Hyper-V 호스트** |최신 업데이트가 설치되고 Hyper-V 역할이 설정된 Windows Server 2012 R2 또는 Microsoft Hyper-V Server 2012 R2를 실행하는 하나 이상의 온-프레미스 서버.<br/><br/>Hyper-V 서버에 가상 컴퓨터가 하나 이상 포함되어야 합니다.<br/><br/>Hyper-V 서버가 직접 또는 프록시를 통해 인터넷에 연결되어야 합니다.<br/><br/>Hyper-V 서버에는 [KB2961977](https://support.microsoft.com/kb/2961977) 에 언급된 수정이 적용되어 있어야 합니다.
|**공급자 및 에이전트**| Azure Site Recovery 배포 중에 Azure Site Recovery 공급자를 설치합니다. 공급자 설치는 보호하려는 가상 컴퓨터를 실행하고 있는 각 Hyper-V 서버에도 Azure Recovery Services 에이전트를 설치합니다. <br/><br/>사이트 복구 자격 증명 모음에 있는 모든 Hyper-V 서버는 반드시 동일한 버전의 공급자 및 에이전트를 가지고 있어야 합니다.<br/><br/>공급자는 인터넷을 통해 Azure Site Recovery에 연결해야 합니다. 트래픽은 직접 또는 프록시를 통해 보낼 수 있습니다. HTTPS 기반 프록시는 지원되지 않습니다. 프록시 서버는 다음에 대한 액세스를 허용해야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>서버에 IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/><br/> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/><br/> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용합니다(액세스 제어 및 ID 관리에 사용됨).


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-azure"></a>Azure에 대한 Virtual Machine Manager 클라우드의 Hyper-V 가상 컴퓨터의 재해 복구

[Azure 요구 사항](#Azure requirements)에 언급한 것 외에도 Virtual Machine Manager 클라우드에서 Hyper-V 가상 컴퓨터의 재해 복구에 필요한 필수 구성 요소는 아래와 같습니다.

| **필수 요소** | **세부 정보** |
| --- | --- |
| **Virtual Machine Manager** |**System Center 2012 R2 이상**이 실행되는 하나 이상의 Virtual Machine Manager 서버. 각 Virtual Machine Manager 서버에 하나 이상의 클라우드가 구성되어 있어야 합니다. <br/><br/>클라우드에 <br>- 하나 이상의 Virtual Machine Manager 호스트 그룹<br/>- 각 호스트 그룹에 있는 하나 이상의 Hyper-V 호스트 서버 또는 클러스터<br/><br/>Virtual Machine Manager 클라우드 설정에 대한 자세한 내용은 [VMM 2012에서 클라우드를 만드는 방법](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx)을 참조하세요. |
| **Hyper-V** |Hyper-V 호스트 서버는 Windows Server 2012 R2 이상(Hyper-V 역할 수행) 또는 Microsoft Hyper-V Server 2012 R2를 실행하고 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> Hyper-V 서버에 VM이 하나 이상 있어야 합니다.<br/><br/> 복제하려는 VM을 포함하고 있는 Hyper-V 호스트 서버 또는 클러스터는 Virtual Machine Manager 클라우드에서 관리되어야 합니다.<br/><br/>Hyper-V 서버가 직접 또는 프록시를 통해 인터넷에 연결되어야 합니다.<br/><br/>[2961977](https://support.microsoft.com/kb/2961977) 문서에서 언급한 픽스가 Hyper-V 서버에 설치되어 있어야 합니다.<br/><br/>Azure에 데이터를 복제하려면 Hyper-V 호스트 서버가 인터넷에 액세스할 수 있어야 합니다. |
| **공급자 및 에이전트** |Azure Site Recovery를 배포하는 동안 Virtual Machine Manager 서버에 Azure Site Recovery 공급자를, Hyper-V 호스트에 Recovery Services 에이전트를 설치합니다. 공급자 및 에이전트는 직접 또는 프록시를 통해 인터넷상에서 Azure에 연결되어야 합니다. HTTPS 기반 프록시는 지원되지 않습니다. Virtual Machine Manager 서버의 프록시 서버와 Hyper-V 호스트에서 다음에 대한 액세스가 허용되어야 합니다. <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Virtual Machine Manager 서버에 IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/><br/> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/><br/>구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용합니다(액세스 제어 및 ID 관리에 사용됨).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>복제된 컴퓨터 필수 조건
| **구성 요소** | **세부 정보** |
| --- | --- |
| **보호되는 VM** | Site Recovery에서는 [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)에서 지원하는 모든 운영 체제를 지원합니다.<br/><br/>VM은 Azure VM을 만드는 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)에 부합해야 합니다. 컴퓨터 이름은 1-63자(문자, 숫자 및 하이픈)를 포함해야 합니다. 이름은 문자나 숫자로 시작하고 문자나 숫자로 끝나야 합니다. <br/><br/>VM에 대한 복제를 활성화한 후 이름을 수정할 수 있습니다. <br/><br/> 보호되는 컴퓨터의 개별 디스크 용량이 1,023GB 이하여야 합니다. VM은 최대 16개의 디스크(따라서 최대 16TB)를 갖출 수 있습니다.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-a-customer-owned-site"></a>고객 소유 사이트에 대한 Virtual Machine Manager 클라우드의 Hyper-V 가상 컴퓨터의 재해 복구

[Azure 요구 사항](#Azure requirements)에 언급한 것 외에도 Virtual Machine Manager 클라우드에서 Hyper-V 가상 컴퓨터의 고객 소유 사이트에 대한 재해 복구에 필요한 필수 구성 요소는 아래와 같습니다.

| **구성 요소** | **세부 정보** |
| --- | --- |
| **Virtual Machine Manager** |  기본 사이트에 Virtual Machine Manager 서버를 배포하고 보조 사이트에 Virtual Machine Manager 서버를 배포하는 것이 좋습니다.<br/><br/> [단일 VMM 서버의 클라우드 간에 복제](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment)할 수 있습니다. 이렇게 하려면 Virtual Machine Manager 서버에 둘 이상의 클라우드가 구성되어 있어야 합니다.<br/><br/> Virtual Machine Manager 서버는 최신 업데이트를 설치한 System Center 2012 SP1 이상을 실행해야 합니다.<br/><br/> 각 Virtual Machine Manager 서버에 하나 이상의 클라우드가 있어야 합니다. 모든 클라우드에 Hyper-V 용량 프로필이 설정되어 있어야 합니다. <br/><br/>클라우드에는 하나 이상의 Virtual Machine Manager 호스트 그룹을 포함해야 합니다. Virtual Machine Manager 클라우드 설정에 대한 자세한 내용은 [Azure Site Recovery 배포 준비](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)를 참조하세요. |
| **Hyper-V** | Hyper-V 서버는 Hyper-V 역할로 Windows Server 2012 이상을 실행해야 하며 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> Hyper-V 서버에 VM이 하나 이상 있어야 합니다.<br/><br/>  Hyper-V 호스트 서버는 기본 및 보조 VMM 클라우드의 호스트 그룹에 있어야 합니다.<br/><br/> Windows Server 2012 R2의 클러스터에서 Hyper-V를 실행하는 경우 [업데이트 2961977](https://support.microsoft.com/kb/2961977)을 설치하는 것이 좋습니다.<br/><br/> Windows Server 2012의 클러스터에서 Hyper-V를 실행하는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성해야 합니다. 클러스터 브로커에 대한 자세한 내용은 [복제본 브로커 역할 클러스터를 클러스터 복제에 구성](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)을 참조하세요. |
| **공급자** | Site Recovery 배포 중에 Virtual Machine Manager 서버에 Azure Site Recovery 공급자를 설치합니다. 공급자는 HTTPS 443을 통해 Site Recovery와 통신하여 복제를 오케스트레이션합니다. LAN 또는 VPN 연결을 통해 기본 및 보조 Hyper-V 서버 간에 데이터 복제가 발생합니다.<br/><br/> Virtual Machine Manager 서버에서 실행 중인 공급자는 다음 URL에 액세스할 수 있어야 합니다.<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>공급자는 Virtual Machine Manager 서버에서 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)로의 방화벽 통신을 허용하고 HTTPS(443) 프로토콜을 허용해야 합니다. |


## <a name="url-access"></a>URL 액세스
VMware, VMM, Hyper-V 호스트 서버에서 이러한 URL을 사용할 수 있어야 합니다.

|**URL** | **VMM에서 VMM으로** | **VMM에서 Azure로** | **Hyper-V에서 Azure로** | **VMware에서 Azure로** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | 허용 | 허용 | 허용 | 허용 |
|``*.backup.windowsazure.com`` | 필요하지 않음 | 허용 | 허용 | 허용 |
|``*.hypervrecoverymanager.windowsazure.com`` | 허용 | 허용 | 허용 | 허용 |
|``*.store.core.windows.net`` | 허용 | 허용 | 허용 | 허용 |
|``*.blob.core.windows.net`` | 필요하지 않음 | 허용 | 허용 | 허용 |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 필요하지 않음 | 필요하지 않음 | 필요하지 않음 | SQL 다운로드 허용 |
|``time.windows.com`` | 허용 | 허용 | 허용 | 허용|
|``time.nist.gov`` | 허용 | 허용 | 허용 | 허용 |

