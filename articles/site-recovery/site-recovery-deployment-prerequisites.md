---
title: "Azure Site Recovery 배포 필수 구성 요소 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery로 복제를 설정하기 위한 필수 구성 요소에 대해 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 92abadf68e4acbd9daae9e15dcfdce53563f6eb2
ms.openlocfilehash: a407f5ebe096db74820ff93354a636bc63894b1d

---

# <a name="site-recovery-deployment-prerequisites"></a>Site Recovery 배포 필수 구성 요소

조직에서는 계획된 중단 또는 불의의 중지 시간에 앱, 워크로드 및 데이터를 실행 중이고 가용 상태로 유지하고 가능한 신속히 정상적인 작업 상태로 복귀하기 위한 BCDR 전략이 필요합니다. 사이트 복구는 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터센터에 복제하는 것을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다. [사이트 복구란?](site-recovery-overview.md)

이 문서에서는 Site Recovery 시나리오에 대한 배포 필수 구성 요소를 요약해서 설명합니다.  

이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 기술적인 질문을 합니다.



## <a name="azure-deployment-models"></a>Azure 배포 모델

Azure는 리소스를 만들고 작업하기 위한 두 가지 [배포 모델](../azure-resource-manager/resource-manager-deployment-model.md)로 Azure Resource Manager 모델과 클래식 모델을 제공합니다. 또한 Azure에는 두 가지 포털이 있는데, 하나는 클래식 배포 모델을 지원하는 [Azure 클래식 포털](https://manage.windowsazure.com/)이고 다른 하나는 두 가지 배포 모델을 모두 지원하는 [Azure Portal](https://ms.portal.azure.com/)입니다.

새 Site Recovery 시나리오는 [Azure Portal](https://portal.azure.com)에 배포해야 합니다. 이 포털에서는 새로운 기능 및 향상된 배포 환경을 제공합니다. 클래식 포털에서 자격 증명 모음을 유지 관리할 수 있지만 새 자격 증명 모음은 만들 수 없습니다.


## <a name="azure-account-requirements"></a>Azure 계정 요구 사항

**요구 사항** | **세부 정보**
--- | --- 
**Azure 계정** | [Microsoft Azure](http://azure.microsoft.com/) 계정.<br/><br/> [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. 사이트 복구 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/).


## <a name="azure-storage-requirements"></a>Azure Storage 요구 사항

복제된 데이터는 Azure 저장소에 저장되고 장애 조치(Failover) 발생 시 Azure VM이 생성됩니다.

**요구 사항** | **세부 정보**
--- | --- 
[Azure 저장소 계정](../storage/storage-introduction.md) | [GRS](../storage/storage-redundancy.md#geo-redundant-storage) 또는 LRS 저장소를 사용할 수 있습니다.<br/><br/> 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 GRS를 사용하는 것이 좋습니다. [자세히 알아보기](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy)
**Azure Portal** | Azure Portal에서 Resource Manager 저장소 또는 클래식 저장소 계정을 사용할 수 있습니다.
**Premium Storage** | Azure Portal을 사용하여 VMware VM 또는 물리적 서버를 Azure로 복제하는 경우 [Premium Storage](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)가 지원됩니다.<br/><br/> Premium Storage는 IO를 많이 사용하는 워크로드를 호스트하기 위해 일관된 IO 고성능과 짧은 대기 시간이 요구되는 가상 컴퓨터에 일반적으로 사용됩니다.<br/><br/> Premium Storage를 사용하는 경우 온-프레미스 데이터에 대한 지속적인 변화를 캡처하는 복제 로그를 저장하는 표준 저장소 계정도 있어야 합니다.
**저장소 제한** | GRS가 지원되는 클래식 포털만 해당됩니다.<br/><br/> Azure 포털에서 만든 저장소 계정은 리소스 그룹 간에 이동할 수 없습니다.<br/><br/> 인도 중부 및 인도 남부의 Premium Storage 계정에 대한 복제는 현재 지원되지 않습니다.

## <a name="azure-network-requirements"></a>Azure 네트워크 요구 사항

장애 조치(Failover) 후 생성된 Azure VM이 연결될 Azure Network가 필요합니다.

**요구 사항** | **세부 정보**
--- | ---
**네트워크 하위 지역** | 네트워크는 자격 증명 모음과 동일한 지역에 있어야 합니다.
**Azure Portal** | Azure Portal에서 Resource Manager 네트워크 또는 클래식 네트워크를 사용할 수 있습니다.
**네트워크 매핑** | VMM에서 Azure로 복제하는 경우 VMM VM 네트워크와 Azure 네트워크 간 [네트워크 매핑](site-recovery-network-mapping.md)을 설정하면 장애 조치 후 Azure VM을 해당 네트워크로 연결할 수 있습니다.


## <a name="vmware-replication-requirements-to-azure"></a>Azure에 대한 VMware 복제 요구 사항

**구성 요소** | **요구 사항**
--- | ---
**구성 서버** | Windows Server 2012 R2를 실행하는 온-프레미스 물리적 또는 가상 컴퓨터. 모든 온-프레미스 Site Recovery 구성 요소가 이 컴퓨터에 설치됩니다.<br/><br/>VMware VM 복제의 경우 항상 사용 가능한 VMware VM으로 서버를 배포하는 것이 좋습니다. <br/><br/>서버가 VMware VM인 경우 네트워크 어댑터 유형은 VMXNET3이어야 합니다. 다른 유형의 네트워크 어댑터를 사용하는 경우 [VMware 업데이트](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)를 vSphere 5.5 서버에 설치합니다.<br/><br/>서버에는 고정 IP 주소가 있어야 합니다.<br/><br/>서버는 도메인 컨트롤러일 수 없습니다.<br/><br/>서버의 호스트 이름은 15자 이내여야 합니다.<br/><br/>운영 체제는 영어여야 합니다.<br/><br/> VMware vSphere PowerCLI 6.0을 설치합니다. 서버.<br/><br/>구성 서버는 인터넷 액세스가 필요합니다. 아웃바운드 액세스가 다음과 같이 필요합니다.<br/><br/>MySQL를 다운로드하기 위해 Site Recovery 구성 요소를 설치하는 동안 HTTP 80에 대한 임시 액세스<br/><br/>복제 관리를 위한 HTTPS 443에 진행 중인 아웃바운드 액세스<br/><br/>복제 트래픽(이 포트를 수정할 수 있음)에 대한 HTTPS 9443에 진행 중인 아웃바운드 액세스<br/><br/> MySQL 다운로드에 다음 URL을 허용합니다. ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> 서버는 [이러한 URL](#requirements-for-url-access)에 대해서도 액세스 권한이 필요합니다.
**프로세스 서버** | 기본적으로 구성 서버에 설치합니다.<br/><br/> 복제 게이트웨이의 역할을 합니다. 보호된 원본 컴퓨터에서 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 데이터를 Azure 저장소로 전송합니다. 또한 보호되는 컴퓨터에서 모바일 서비스의 푸시 설치를 처리하며 VMware VM의 자동 복구를 수행합니다. 배포가 늘어나면 프로세스 서버로 실행하는 별도의 추가 전용 서버를 추가하여 증가하는 복제 트래픽을 처리할 수 있습니다.
**마스터 대상 서버** | 기본적으로 구성 서버에 설치합니다. Azure에서 장애 복구 중에 복제 데이터를 처리합니다.
**vSphere 호스트** | 하나 이상의 VMware vSphere 하이퍼바이저.<br/><br/>하이퍼바이저는 최신 업데이트를 설치한 vSphere 버전 6.0, 5.5 또는 5.1를 실행해야 합니다.<br/><br/>vSphere 호스트 및 vCenter Server는 프로세스 서버와 동일한 네트워크에 있는 것이 좋습니다. 이 네트워크는 전용 프로세스 서버를 설정하지 않는 한 구성 서버가 있는 네트워크입니다. 
**vCenter 서버** | vSphere 호스트를 관리하는 VMware vCenter Server를 배포하는 것이 좋습니다. 최신 업데이트를 설치한 vCenter 버전 6.0 또는 5.5를 실행해야 합니다.<br/><br/>**제한 사항**: Site Recovery에서는 교차 vCenter vMotion, 가상 볼륨 및 저장소 DRS와 같은 새로운 vCenter 및 vSphere 6.0 기능을 지원하지 않습니다. Site Recovery 지원은 버전 5.5에서도 사용할 수 있는 기능에 제한됩니다.

### <a name="vmware-vm-requirements-to-azure"></a>Azure에 대한 VMware VM 요구 사항

**구성 요소** | **요구 사항**
--- | ---
**VMware VM** | 복제된 VM에 VMware 도구가 설치되어 있고 실행 중이어야 합니다.<br/><br/> VM은 Azure VM을 만드는 [Azure 필수 조건](site-recovery-support-matrix.md#support-for-azure-vms)에 부합해야 합니다.<br/><br/>보호되는 컴퓨터의 개별 디스크 용량이 1023GB 이하여야 합니다. VM은 최대 64개의 디스크(따라서 최대 64TB)를 포함할 수 있습니다. <br/><br/>구성 요소 설치를 위해 설치 드라이브에 최소 2GB의 사용 가능한 공간이 있어야 합니다.<br/><br/>**제한 사항**: 암호화된 디스크로 VM을 보호하는 것은 지원되지 않습니다.<br/><br/>**제한 사항**: 공유 디스크 게스트 클러스터는 지원되지 않습니다.<br/><br/>다중 VM 일관성을 사용하려면 VM의 로컬 방화벽에서 **포트 20004**가 열려 있어야 합니다.<br/><br/>UEFI(Unified Extensible Firmware Interface)/EFI(Extensible Firmware Interface) 부팅을 사용하는 컴퓨터는 지원되지 않습니다.<br/><br/>컴퓨터 이름은 1-63자(문자, 숫자 및 하이픈)를 포함해야 합니다. 이름은 문자나 숫자로 시작하고 문자나 숫자로 끝나야 합니다. 컴퓨터에 대한 복제를 활성화한 후 Azure 이름을 수정할 수 있습니다.<br/><br/>원본 VM에 NIC 팀이 있는 경우 Azure에 장애 조치 후 단일 NIC로 변환됩니다.<br/><br/>보호된 가상 컴퓨터에 iSCSI 디스크가 있는 경우 VM이 Azure에 장애 조치될 때 Site Recovery는 보호된 VM iSCSI 디스크를 VHD 파일로 변환합니다. Azure VM에서 iSCSI 대상에 연결할 수 있는 경우 이 대상에 연결되며 기본적으로 두 개의 디스크(Azure VM의 VHD 디스크 및 원본 iSCSI 디스크)가 표시됩니다. 이 경우 Azure VM에 표시되는 iSCSI 대상의 연결을 끊어야 합니다.
**Windows가 실행되는 VM** | Windows 컴퓨터에서는 [지원되는](site-recovery-support-matrix.md#support-for-replicated-machines) 64비트 운영 체제가 실행되고 있어야 합니다.<br/><br/> 운영 체제는 C:\ 드라이브에 설치해야 합니다.<br/><br/> OS 디스크는 동적이 아닌 Windows 기본 디스크여야 합니다. 데이터 디스크는 동적일 수 있습니다.<br/><br/> Site Recovery에서는 RDM 디스크를 사용한 VM을 지원합니다. 원래 원본 VM과 RDM 디스크를 사용할 수 있는 경우 Site Recovery에서는 장애 복구(failback) 중에 RDM 디스크를 다시 사용합니다. 사용할 수 없는 경우 장애 복구(failback) 동안 Site Recovery가 각 디스크에 대한 새 VMDK 파일을 만듭니다.
**Linux가 실행되는 VM** | Linux 컴퓨터에서는 [지원되는 운영 체제](site-recovery-support-matrix.md#support-for-replicated-machines)가 실행되고 있어야 합니다.<br/><br/> 다음 저장소의 Linux 컴퓨터에 대해서만 보호를 사용할 수 있습니다.<br/><br/> 호스트 이름, 마운트 지점, 장치 이름 및 Linux 시스템 경로와 파일 이름(예: /etc/; /usr)에는 영어만 사용해야 합니다.<br/><br/> 저장소가 [지원](site-recovery-support-matrix.md#support-for-replicated-machines)되어야 합니다.<br/><br/> Site Recovery에서는 RDM 디스크를 사용한 VM을 지원합니다.  Linux에 대한 장애 복구(failback) 중에는 사이트 복구에서 RDM 디스크를 다시 사용하지 않습니다. 대신 각 해당 RDM 디스크에 대 해 새 VMDK 파일을 만듭니다.<br/><br/> VMware의 VM 구성 매개 변수에서 disk.enableUUID=true로 설정합니다. 없는 경우 항목을 만듭니다. 올바르게 탑재할 수 있도록 VMDK에 일관성 있는 UUID를 제공해야 합니다. 이 설정을 추가하면 장애 복구(failback) 동안 전체 복제가 아닌 델타 변경 내용만 온-프레미스로 다시 전송됩니다.



## <a name="physical-servers-replication-requirements-to-azure"></a>Azure에 대한 물리적 서버 복제 요구 사항

**구성 요소** | **요구 사항**
--- | ---
**구성 서버** | Windows Server 2012 R2를 실행하는 온-프레미스 물리적 또는 가상 컴퓨터. 모든 온-프레미스 Site Recovery 구성 요소가 이 컴퓨터에 설치됩니다.<br/><br/> 물리적 컴퓨터 복제의 경우 컴퓨터가 물리적 서버가 될 수 있습니다.<br/><br/>  구성 서버는 인터넷 액세스가 필요합니다. 아웃바운드 액세스가 다음과 같이 필요합니다.<br/><br/> MySQL를 다운로드하기 위해 Site Recovery 구성 요소를 설치하는 동안 HTTP 80에 대한 임시 액세스<br/><br/>복제 관리를 위한 HTTPS 443에 진행 중인 아웃바운드 액세스<br/><br/>복제 트래픽(이 포트를 수정할 수 있음)에 대한 HTTPS 9443에 진행 중인 아웃바운드 액세스<br/><br/> MySQL 다운로드에 다음 URL을 허용합니다. ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> 서버는 [이러한 URL](#requirements-for-url-access)에 대해서도 액세스 권한이 필요합니다.
**장애 복구** | Azure에서 장애 복구는 물리적 서버를 복제한 경우에도 항상 VMware VM으로 이루어집니다. VMware VM으로 구성 서버를 배포하지 않으면 장애 복구 전에 장애 복구 트래픽을 수신할 VMware VM을 설정해야 합니다.

### <a name="physical-machine-requirements-to-azure"></a>Azure에 대한 물리적 컴퓨터 요구 사항

**구성 요소** | **요구 사항**
--- | ---
**Windows가 실행되는 물리적 서버** | Windows 컴퓨터에서는 [지원되는](site-recovery-support-matrix.md#support-for-replicated-machines) 64비트 운영 체제가 실행되고 있어야 합니다.<br/><br/> 운영 체제는 C:\ 드라이브에 설치해야 합니다.<br/><br/> OS 디스크는 동적이 아닌 Windows 기본 디스크여야 합니다. 데이터 디스크는 동적일 수 있습니다.<br/><br/> Site Recovery에서는 RDM 디스크를 사용한 VM을 지원합니다. 원래 원본 VM과 RDM 디스크를 사용할 수 있는 경우 Site Recovery에서는 장애 복구(failback) 중에 RDM 디스크를 다시 사용합니다. 사용할 수 없는 경우 장애 복구(failback) 동안 Site Recovery가 각 디스크에 대한 새 VMDK 파일을 만듭니다.
**Linux가 실행되는 물리적 서버** | Linux 컴퓨터에서는 [지원되는 운영 체제](site-recovery-support-matrix.md#support-for-replicated-machines)가 실행되고 있어야 합니다.<br/><br/> 다음 저장소의 Linux 컴퓨터에 대해서만 보호를 사용할 수 있습니다.<br/><br/> 호스트 이름, 마운트 지점, 장치 이름 및 Linux 시스템 경로와 파일 이름(예: /etc/; /usr)에는 영어만 사용해야 합니다.<br/><br/> 저장소가 [지원](site-recovery-support-matrix.md#support-for-replicated-machines)되어야 합니다.

## <a name="hyper-v-replication-requirements-to-azure"></a>Azure에 대한 Hyper-V 복제 요구 사항

**구성 요소** | **요구 사항**
--- | ---
**VMM(선택 사항)** | 필요에 따라 VMM 클라우드에서 관리되는 Hyper-V 호스트에서 VM을 복제할 수 있습니다.<br/><br/> VMM을 사용하지 않을 경우 하나 이상의 Hyper-V 호스트 또는 클러스터를 Hyper-V 사이트에 수집한 후 해당 사이트에 대해 복제를 설정합니다.<br/><br/> VMM을 사용하는 경우 System Center 2012 R2에서 실행되어야 합니다.<br/><br/> VMM을 사용하는 경우 각 VMM 서버에 하나 이상의 클라우드가 구성되어 있어야 합니다. 클라우드의 각 호스트 그룹에는 하나 이상의 VMM 호스트 그룹 및 하나 이상의 Hyper-V 호스트 서버 또는 클러스터가 포함되어야 합니다.<br/><br/> VMM 서버는 [필수 URL](#requirements-for-url-access)에 액세스되는 인터넷에 연결되어야 합니다.<br/><br/> VMM에 IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인해야 합니다.<br/><br/> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/><br/> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요.
**Hyper-V 호스트** | Hyper-V 호스트 서버는 **Windows Server 2012 R2** 이상(Hyper-V 역할 수행) 또는 **Microsoft Hyper-V Server 2012 R2**를 실행하고 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> Hyper-V 서버에 VM이 하나 이상 있어야 합니다.<br/><br/>Hyper-V 서버가 직접 또는 프록시를 통해 인터넷에 연결되어야 합니다.<br/><br/>[2961977](https://support.microsoft.com/kb/2961977) 문서에서 언급한 픽스가 Hyper-V 서버에 설치되어 있어야 합니다.<br/><br/>Azure에 데이터를 복제하려면 Hyper-V 호스트 서버가 [필수 URL](#requirements-for-url-access)에 액세스되는 인터넷에 액세스할 수 있어야 합니다. 또한 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/><br/> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요.<br/><br/> Hyper-V 호스트가 VMM 클라우드에 있는 경우 [KB 2961977](https://support.microsoft.com/en-us/kb/2961977)에 설명된 수정 프로그램을 설치해야 합니다.

### <a name="hyper-v-vm-requirements-to-azure"></a>Azure에 대한 Hyper-V VM 요구 사항

**구성 요소** | **요구 사항**
--- | ---
**VM 규정 준수** |VM을 장애 조치(Failover)하려면 Azure VM에 할당될 이름이 [Azure 필수 조건](site-recovery-best-practices.md#azure-virtual-machine-requirements)을 준수하는지 확인해야 합니다. VM에 대한 복제를 활성화한 후 이름을 수정할 수 있습니다.
**디스크** | 보호되는 컴퓨터의 개별 디스크 용량이 1023GB 이하여야 합니다. VM은 최대 64개의 디스크(따라서 최대 64TB)를 포함할 수 있습니다.<br/><br/> 공유 디스크 게스트 클러스터는 지원되지 않습니다.
**UEFI** | UEFI(Unified Extensible Firmware Interface)/EFI(Extensible Firmware Interface) 부팅은 지원되지 않습니다.
**NIC 팀** | 원본 VM에 NIC 팀이 있는 경우 Azure에 장애 조치 후 단일 NIC로 변환됩니다.
**Linux 고정** | 고정 IP 주소를 사용하여 Linux를 실행하는 Hyper-V VM 보호는 지원되지 않습니다.



## <a name="vmwarephysical-servers-replication-requirements-secondary-site"></a>VMware/물리적 서버 복제 요구 사항(보조 사이트)

이 시나리오는 VMware VM 또는 물리적 서버를 보조 사이트로 복제하도록 선택할 때 Site Recovery Vault에서 다운로드할 수 있는 InMage 제품에 의해 처리됩니다. 이 시나리오는 Azure Portal에서 사용할 수 없습니다.

**구성 요소** | **요구 사항**
--- | ---
**기본 사이트** | 캐시, 압축 및 데이터 최적화를 처리하려면 프로세스 서버가 필요합니다. 이 서버는 복제하려는 각 컴퓨터에서 필요한 통합 에이전트의 강제 설치도 처리합니다.<br/><br/> VMware VM을 복제하려는 경우 하나 이상의 VMware ESX/ESXi 서버와 필요에 따라 VMware vCenter 서버가 필요합니다.
**보조 사이트** | 환경을 구성하고 관리하려면 보조 사이트에서 구성 서버를 설정해야 합니다.<br/><br/> 복제된 데이터를 포함할 마스터 대상 서버가 보조 사이트에 설치됩니다.<br/><br/> vContinuum 서버는 마스터 대상 서버에 기본적으로 설치되며, 환경을 관리하고 모니터링하기 위한 콘솔을 제공합니다.

### <a name="vmware-vmphysical-machine-requirements-secondary-site"></a>VMware VM/물리적 컴퓨터 요구 사항(보조 사이트)

[InMage 지원 매트릭스](https://aka.ms/asr-scout-cm)에서 요구 사항을 확인하세요.


## <a name="hyper-v-replication-requirements-secondary-site"></a>Hyper-V 복제 요구 사항(보조 사이트)

**구성 요소** | **요구 사항**
--- | ---
**VMM** | Hyper-V VM을 보조 사이트에 복제하려면 System Center VMM 클라우드에서 Hyper-V 호스트를 관리해야 합니다. <br/><br/> VMM은 최신 업데이트를 설치한 System Center 2012 SP1 이상을 실행해야 합니다.<br/><br/> 기본 사이트에 VMM 서버 1개, 보조 사이트에 다른 VMM 서버 1개가 있는 것이 좋습니다. [동일한 VMM 서버의 서로 다른 클라우드](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-single-vmm) 간에 복제할 수 있으나 이 시나리오에서는 몇 가지 수동 구성이 필요합니다.<br/><br/> VMM 서버는 [필수 URL](#requirements-for-url-access)에 액세스되는 인터넷에 연결되어야 합니다.<br/><br/> VMM에 IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인해야 합니다.<br/><br/> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/><br/> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요.
**Hyper-V** | Hyper-V 서버는 Hyper-V 역할로 Windows Server 2012 이상을 실행해야 하며 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> Hyper-V 서버에 VM이 하나 이상 있어야 합니다.<br/><br/> Hyper-V 호스트는 기본 및 보조 VMM 서버의 호스트 그룹에 있어야 합니다.<br/><br/> Windows Server 2012 R2의 클러스터에서 Hyper-V를 실행하는 경우 [업데이트 2961977](https://support.microsoft.com/kb/2961977)을 설치해야 합니다. Windows Server 2012의 클러스터에 Hyper-V가 있는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 수동으로 구성하려면 [자세히 알아보세요](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).

### <a name="hyper-v-vm-requirements-secondary-site"></a>Hyper-V VM 요구 사항(보조 사이트)

**구성 요소** | **세부 정보**
--- | ---
**VMM 클라우드** | VM은 VMM 클라우드의 Hyper-V 호스트에 있어야 합니다.






## <a name="url-access-requirements"></a>URL 액세스 요구 사항

VMware, VMM, Hyper-V 호스트 서버에서 이러한 URL을 사용할 수 있어야 합니다.

**URL** | **VMM에서 VMM으로** | **VMM에서 Azure로** | **Hyper-V에서 Azure로** | **VMware에서 Azure로** 
--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | 허용 | 허용 | 허용 | 허용
``*.backup.windowsazure.com`` | 필요하지 않음 | 허용 | 허용 | 허용
``*.hypervrecoverymanager.windowsazure.com`` | 허용 | 허용 | 허용 | 허용
``*.store.core.windows.net`` | 허용 | 허용 | 허용 | 허용
``*.blob.core.windows.net`` | 필요하지 않음 | 허용 | 허용 | 허용
``https://www.msftncsi.com/ncsi.txt`` | 허용 | 허용 | 허용 | 허용
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 필요하지 않음 | 필요하지 않음 | 필요하지 않음 | SQL 다운로드 허용
``time.windows.com`` | 허용 | 허용 | 허용 | 허용
``time.nist.gov`` | 허용 | 허용 | 허용 | 허용

## <a name="failover-requirements"></a>장애 조치 요구 사항

**시나리오** | **테스트** | **계획** | **비계획**
--- | --- | --- | ---
VMware에서 Azure로 | 지원됨 | 지원되지 않음 | 지원됨
물리적 서버에서 Azure로 | 지원됨 | 지원되지 않음 | 지원됨
Hyper-V(VMM)에서 Azure로 | 지원됨 | 지원됨 | 지원됨
Hyper-V(VMM 아님)에서 Azure로 | 지원됨 | 지원됨 | 지원되지 않음
Hyper-V(기본 VMM/클라우드)에서 보조 사이트로 | 지원됨 | 지원됨 | 지원됨

## <a name="failback-requirements"></a>장애 복구 요구 사항

**시나리오** | **테스트** | **계획** | **비계획**
--- | --- | --- | ---
**Azure에서 VMware로** | 지원되지 않음 | 지원되지 않음 | 지원됨
**Azure에서 물리적 서버로** | 지원되지 않음 | 지원되지 않음 | VMware으로만 지원
**Azure에서 VMM으로** | 지원되지 않음 | 지원됨 | 지원되지 않음
**Azure에서 Hyper-V(VMM 아님)로** | 지원되지 않음 | 지원됨 | 지원되지 않음
**Hyper-V(보조 VMM/클라우드)에서 기본 사이트로** | 지원됨 | 지원됨 | 지원됨

### <a name="failback-from-azure-to-vmware-vms"></a>Azure에서 VMware VM으로 장애 복구

**구성 요소** | **세부 정보**
--- | ---
**Azure의 임시 프로세스 서버** | 장애 조치 후 Azure에서 장애 복구하려면 Azure에서 복제를 처리하기 위해 프로세스 서버로 구성된 Azure VM을 설정해야 합니다. 장애 복구를 완료한 후 이 VM을 삭제할 수 있습니다.
**VPN 연결** | 장애 복구를 수행하려면 Azure Network에서 온-프레미스 사이트로 VPN 연결(또는 Azure ExpressRoute) 설정이 필요합니다.
**별도 온-프레미스 마스터 대상 서버** | 온-프레미스 마스터 대상 서버는 장애 복구를 처리합니다. 마스터 대상 서버는 기본적으로 관리 서버에 설치되지만 대량의 트래픽 볼륨을 장애 복구하는 경우 이 목적으로 별도의 온-프레미스 마스터 대상 서버를 설정해야 합니다.



### <a name="failback-from-azure-to-physical-machines"></a>Azure에서 물리적 컴퓨터로 장애 복구

**구성 요소** | **세부 정보**
--- | ---
**물리적 서버 간 장애 복구** | 지원되지 않습니다. 즉, 물리적 서버를 Azure로 장애 조치한 다음 장애 복구하려면 VMware VM에 장애 복구해야 합니다. 실제 서버로 장애 복구할 수 없습니다.
**VM** | 장애 복구를 수행할 VMware VM 온-프레미스가 필요합니다.
**구성 서버** | VMware VM으로 온-프레미스 구성 서버를 배포하지 않으면 VMware VM으로 별도 마스터 대상 서버를 설정해야 합니다. 마스터 대상 서버가 VMware 저장소와 상호 작용하고 연결되어 VMware VM에 디스크를 복원하기 때문에 필요합니다.



## <a name="deployment-optimization"></a>배포 최적화

다음 팁을 사용하여 배포를 최적화하고 크기 조정합니다.

- **운영 체제 볼륨 크기**: Azure에 가상 컴퓨터를 복제하는 경우 운영 체제 볼륨이 1TB 미만이어야 합니다. 이것보다 더 많은 볼륨이 있는 경우 배포를 시작하기 전에 볼륨을 다른 디스크로 수동으로 이동할 수 있습니다.
- **데이터 디스크 크기**: Azure에 복제하는 경우 가상 컴퓨터에는 최대 64개의 데이터 디스크를 포함할 수 있습니다(각각 최대 1TB). 64TB 이내의 가상 컴퓨터를 효과적으로 복제 및 장애 조치할 수 있습니다.
- **복구 계획 제한**: 사이트 복구는 수천 개의 가상 컴퓨터로 확장할 수 있습니다. 복구 계획은 다 함께 장애 조치되어야 하는 응용 프로그램에 대한 모델로 설계되었으므로 복구 계획에서 컴퓨터의 수를 50으로 제한할 수 있습니다.
- **Azure 서비스 제한**: 모든 Azure 구독은 코어, 클라우드 서비스 등에 대한 일련의 기본 제한값이 제공됩니다. 구독에서 리소스의 가용성을 확인하기 위해 테스트 장애 조치(Failover)를 실행하는 것이 좋습니다. Azure 지원을 통해 이러한 제한을 수정할 수 있습니다.
- **용량 계획**: Site Recovery에 대한 [용량 계획](site-recovery-capacity-planner.md) 을 읽어 보세요.
- **복제 대역폭**: 복제 대역폭이 부족한 경우 다음에 유의하십시오.
- **Express 경로**: 사이트 복구는 Riverbed와 같은 Azure Express 경로 및 WAN 최적화 프로그램과 함께 작동합니다. [여기서](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) Express 경로에 대해 더 알아볼 수 있습니다.
- **복제 트래픽**: 사이트 복구는 전체 VHD가 아닌 데이터 블록만 사용하여 스마트 초기 복제를 수행합니다. 복제가 진행 중일 때는 변경 내용만 복제됩니다.
- **네트워크 트래픽**: 대상 IP 주소 및 포트 기반 정책을 포함하는 [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)를 설정하여 복제에 사용된 네트워크 트래픽을 제어할 수 있습니다.  또한 Azure 백업 에이전트를 사용하여 Azure Site Recovery에 복제하는 경우 해당 에이전트에 대한 제한을 구성할 수 있습니다. [자세히 알아보기](https://support.microsoft.com/kb/3056159).
- **RTO**: Site Recovery를 통해 예상할 수 있는 RTO(복구 시간 목표)를 측정하려면 테스트 장애 조치(Failover)를 실행하고 Site Recovery 작업을 검토하여 작업 완료까지 시간이 얼마나 걸리는지 분석하는 것이 좋습니다. Azure에 장애 조치하는 경우 최선의 RTO를 위해 Azure 자동화 및 복구 계획과 통합하여 모든 수동 작업을 자동화하는 것이 좋습니다.
- **RPO**: Azure에 복제하는 경우 사이트 복구는 가까운 동기 복구 지점 목표 (RPO)를 지원 합니다. 이때 데이터 센터와 Azure 사이에서 충분한 대역폭을 가정합니다.



## <a name="next-steps"></a>다음 단계
일반적인 배포 요구 사항을 검토한 후에는 구체적인 필수 조건을 읽고 시나리오를 배포합니다.

* [Azure에 VMWare 가상 컴퓨터 복제](site-recovery-vmware-to-azure.md)
* [물리적 서버를 Azure에 복제](site-recovery-vmware-to-azure.md)
* [VMM 클라우드의 Hyper-V 서버를 Azure로 복제](site-recovery-vmm-to-azure.md)
* [Hyper-V 가상 컴퓨터(VMM 없음)를 Azure로 복제](site-recovery-hyper-v-site-to-azure.md)
* [Hyper-V VM을 보조 사이트로 복제](site-recovery-vmm-to-vmm.md)
* [SAN을 사용하여 Hyper-V VM을 보조 사이트로 복제](site-recovery-vmm-san.md)
* [단일 VMM 서버를 사용하여 Hyper-V VM 복제](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


