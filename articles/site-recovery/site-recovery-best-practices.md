---
title: "Site Recovery 배포 준비 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery를 사용하여 복제를 배포하기 위한 준비 과정을 설명합니다."
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
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 8a4d265694e5eef438b0560a42ea5a95c04f9b02


---
# <a name="prepare-for-azure-site-recovery-deployment"></a>Azure Site Recovery 배포 준비
Azure Site Recovery 서비스에서 지 원하는 각 복제 시나리오에 대한 배포 요구 사항의 수준 높은 개요는 이 문서를 참고합니다. 각 시나리오에 대한 일반적인 요구 사항을 참고한 후에 각 배포에 대한 특정 배포 세부 정보에 연결할 수 있습니다.

이 문서의 내용을 확인한 후 문서 아래쪽의 의견 입력란이나 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 의견이나 질문을 게시해 주시기 바랍니다.

## <a name="overview"></a>개요
조직에서는 계획된 중단 또는 불의의 중지 시간에 앱, 워크로드 및 데이터를 실행 중이고 가용 상태로 유지하고 가능한 신속히 정상적인 작업 상태로 복귀하기 위한 BCDR 전략이 필요합니다. BCDR 전략은 재해가 발생했을 때 비즈니스 데이터를 안전하고 복구 가능하게 유지하고 워크로드를 지속적으로 가용 상태로 유지해야 합니다.

사이트 복구는 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터센터에 복제하는 것을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다.  [Site Recovery란?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>배포 모델 선택
Azure는 리소스를 만들고 작업하기 위한 두 가지 [배포 모델](../resource-manager-deployment-model.md) 로 Azure Resource Manager 모델과 클래식 서비스 관리 모델을 제공합니다. 또한 Azure에는 두 가지 포털이 있는데, 하나는 클래식 배포 모델을 지원하는 [Azure 클래식 포털](https://manage.windowsazure.com/)이고 다른 하나는 두 가지 배포 모델을 모두 지원하는 [Azure Portal](https://ms.portal.azure.com/)입니다.

Site Recovery는 클래식 포털과 Azure 포털 모두에서 지원됩니다. Azure 클래식 포털에서 기존 서비스 관리 모델을 사용하여 Site Recovery를 지원할 수 있습니다. Azure 포털에서 기존 모델 또는 Resource Manager 배포를 지원할 수 있습니다. [자세히 읽어 봅니다](site-recovery-overview.md#site-recovery-in-the-azure-portal) .

배포 모델을 선택할 때 다음 사항에 유의하십시오.

* [Azure 포털](https://portal.azure.com) 및 가능한 경우 리소스 관리자 모델을 사용하는 것이 좋습니다.
* Site Recovery는 Azure 포털에서 더 간단하고 더 직관적으로 시작하는 경험을 제공합니다.
* Azure 포털을 사용하면 Azure에서 컴퓨터를 클래식 및 Resource Manager 저장소에 복제할 수 있습니다. 또한 Azure 포털에서 LRS 또는 GRS 저장소 계정을 사용할 수 있습니다.
* Azure 포털은 단일 위치에서 BCDR 서비스를 설정하고 관리할 수 있도록 Site Recovery와 백업 서비스를 단일 복구 서비스 자격 증명 모음으로 결합합니다.
* CSP(클라우드 솔루션 공급자) 프로그램이 포함된 Azure 구독이 있는 사용자는 이제 Azure 포털에서 사이트 복구 작업을 관리할 수 있습니다.
* VMware VM 또는 물리적 컴퓨터를 Azure 포털의 Azure에 복제하면 프리미엄 저장소에 대한 지원 및 복제에서 특정 디스크를 제외하는 기능을 포함하여 다양한 새 기능을 제공합니다.

## <a name="select-your-deployment-scenario"></a>배포 시나리오 선택
| **배포웹사이트를** | **세부 정보** | **Azure 포털** | **클래식 포털** | **PowerShell** |
| --- | --- | --- | --- | --- |
| **VMware VM에서 Azure로** |VMware VM을 Azure 저장소로 복제 |Azure 포털에서 VM은 클래식 또는 Resource Manager 저장소로 장애 조치할 수 있음<br/><br/>  [Azure 포털](site-recovery-vmware-to-azure.md) 의 배포는 간소화된 배포 경험 및 다양한 기능 이익을 제공합니다. |Azure 클래식 포털에서는 [향상된 모델](site-recovery-vmware-to-azure-classic.md)을 사용하여 배포하고 클래식 Azure Storage에 장애 조치할 수 있습니다.<br/><br/>  새 배포에 사용하지 않아야 할 구형 모델도 있습니다. |PowerShell은 현재 지원되지 않습니다. |
| **Hyper-V VM에서 Azure로** |Hyper-V VM에서 Azure 저장소로. VM은 시스템 센터 VMM 클라우드 내에서 관리되거나 VMM 없는 Hyper-V 호스트에 있을 수 있습니다. |Azure 포털에서 VM은 클래식 또는 Resource Manager 저장소로 장애 조치할 수 있습니다.<br/><br/>  Azure 포털은 간소화된 배포 환경을 제공합니다. [자세히 알아봅니다](site-recovery-vmm-to-azure.md) . [자세히 알아봅니다](site-recovery-hyper-v-site-to-azure.md) . |클래식 Azure 포털에서 VM을 클래식 Azure 저장소로 장애 조치할 수 있습니다. |PowerShell 배포가 지원됩니다. |
| **물리적 서버에서 Azure로** |물리적 Windows/Linux 서버에서 Azure 저장소로 복제 |Azure 포털에서 서버는 클래식 또는 Resource Manager 저장소로 장애 조치할 수 있습니다.<br/><br/>  [Azure 포털](site-recovery-vmware-to-azure.md) 의 배포는 간소화된 배포 환경 및 다양한 기능 이점을 제공합니다. |Azure 클래식 포털에서는 [향상된 모델](site-recovery-vmware-to-azure-classic.md)을 사용하여 배포하고 클래식 Azure Storage에 장애 조치할 수 있습니다.<br/><br/>  새 배포에 사용하지 않아야 할 구형 모델도 있습니다. |PowerShell 배포는 현재 지원되지 않습니다. |
| **VMware VM/물리적 서버에서 보조 사이트로* |VMware VM 또는 물리적 Windows/Linux 서버에서 보조 사이트로 복제합니다. [자세히 알아보고 다운로드](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) 합니다. |Azure 포털에서 사용할 수 없음 |클래식 포털에서는 Site Recovery 자격 증명 모음에서 InMage Scout를 다운로드합니다. |PowerShell 배포는 현재 지원되지 않음 |
| **Hyper-V VM에서 보조 사이트로** |VMM 클라우드의 Hyper-V VM에서 보조 클라우드로 복제 |[Azure 포털](site-recovery-vmm-to-vmm.md) 에서는 Hyper-V 복제본만 사용하여 VMM의 Hyper-V VM을 보조 사이트로 복제할 수 있습니다. SAN은 현재 지원되지 않습니다. |Azure 클래식 포털에서는 [Hyper-V 복제본](site-recovery-vmm-to-vmm-classic.md) 또는 [SAN 복제](site-recovery-vmm-san.md)를 사용하여 VMM 클라우드의 Hyper-V VM을 보조 사이트로 복제할 수 있습니다. |PowerShell 배포가 지원됨 |

## <a name="check-what-you-need-for-deployment"></a>배포에 대해 필요한 사항 확인
### <a name="replicate-to-azure"></a>Azure에 복제
| **요구 사항** | **세부 정보** |
| --- | --- |
| **Azure 계정** |[Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다.<br/><br/> [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/) . |
| **Azure 저장소** |복제된 데이터는 Azure 저장소에 저장되고 장애 조치(Failover) 발생 시 Azure VM이 생성됩니다. Azure로 복제하려면 [Azure Storage 계정](../storage/storage-introduction.md)이 필요합니다.<br/><br/>클래식 포털에서 Site Recovery를 배포하는 경우 하나 이상의 [표준 GRS 저장소 계정](../storage/storage-redundancy.md#geo-redundant-storage)이 있어야 합니다.<br/><br/> Azure Portal에서 배포하는 경우 GRS 또는 LRS 저장소를 사용할 수 있습니다.<br/><br/>  Azure 포털에서 VMware VM 또는 물리적 서버를 복제하는 경우 프리미엄 저장소가 지원됩니다. 참고로 프리미엄 저장소 계정을 사용하는 경우 온-프레미스 데이터에 대한 지속적인 변화를 캡처하는 복제 로그를 저장하는 표준 저장소 계정도 있어야 합니다. [프리미엄 저장소](../storage/storage-premium-storage.md)는 IO를 많이 사용하는 워크로드를 호스트하기 위해 일관된 IO 고성능과 짧은 대기 시간이 요구되는 가상 컴퓨터에 일반적으로 사용됩니다.<br/><br/>  프리미엄 계정을 사용하여 복제된 데이터를 저장하려는 경우 온-프레미스 데이터에 대한 지속적인 변화를 캡처하는 복제 로그를 저장하는 표준 저장소 계정이 필요할 수도 있습니다. |
| **Azure 네트워크** |Azure에 복제하려면 Azure VM에서 장애 조치 후 만들어질 때 연결하는 Azure 네트워크가 있어야 합니다.<br/><br/>  클래식 포털에서 배포하는 경우에 클래식 네트워크를 사용합니다. Azure 포털에서 배포하는 경우 클래식 또는 Resource Manager 네트워크를 사용할 수 있습니다.<br/><br/>  네트워크는 자격 증명 모음과 동일한 지역에 있어야 합니다. |
| **네트워크 매핑(VMM에서 Azure로)** |VMM에서 Azure로 복제하는 경우 [네트워크 매핑](site-recovery-network-mapping.md)은 장애 조치 후 Azure VM이 올바른 네트워크에 연결되어 있어야 합니다.<br/><br/>  네트워크 매핑을 설정하려면 VMM 포털에서 VM 네트워크를 구성해야 합니다. |
| **온-프레미스** |**VMware VM**: Site Recovery 구성 요소, VMware vSphere 호스트/vCenter 서버 및 복제할 VM을 실행하는 온-프레미스 컴퓨터가 있어야 합니다. [자세히 알아보기](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).<br/><br/> **물리적 서버**: 물리적 서버를 복제하는 경우 Site Recovery를 실행하는 온-프레미스 컴퓨터 및 복제할 물리적 서버가 있어야 합니다. [자세히 알아보기](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). Azure로 장애 조치 후 [장애 복구](site-recovery-failback-azure-to-vmware.md)하려는 경우 이를 수행할 VMware 인프라가 있어야 합니다.<br/><br/> **Hyper-V VM**: If you want to replicate Hyper-V VM in VMM clouds you'll need a VMM server, and Hyper-V hosts on which VMs you want to protect are located. [자세히 알아보기](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/>  VMM 없이 Hyper-V VM을 복제하려는 경우 VM이 위치한 Hyper-V 호스트가 있어야 합니다. [자세히 알아보기](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites). |
| **보호된 컴퓨터** |Azure에 복제할 보호된 컴퓨터는 아래에서 설명하는 [Azure 필수 조건](#azure-virtual-machine-requirements) 을 준수해야 합니다. |

### <a name="replicate-to-a-secondary-site"></a>보조 사이트에 복제
| **요구 사항** | **세부 정보** |
| --- | --- |
| **Azure 계정** |[Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다.<br/><br/> [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/) . |
| **온-프레미스** |**VMware VM**: 기본 사이트에는 복제 데이터를 보조 사이트에 보내기 전에 캐싱, 압축 및 암호화하기 위해 프로세스 서버가 있어야 합니다. 보조 사이트에는 배포 및 마스터 대상 서버를 관리하고 모니터링하기 위해 구성 서버를 설치합니다. 또한 보다 쉽게 관리할 수 있도록 vContinuum 서버를 권장합니다. 이에 더하여 VMware vSphere 호스트 하나 이상 및 선택적으로 vCenter 서버가 있어야 합니다. [자세히 읽어 봅니다](site-recovery-vmware-to-vmware.md)<br/><br/> **VMM 클라우드의 Hyper-V VM**: VMM 서버 및 복제할 VM이 포함된 Hyper-V 호스트를 설정해야 합니다. [자세히 알아보기](site-recovery-vmm-to-vmm.md#on-premises-prerequisites). |
| **네트워크 매핑(VMM에서 VMM으로)** |VMM에서 VMM으로 복제하는 경우 [네트워크 매핑](site-recovery-network-mapping.md) 에서 장애 조치 후 복제본 VM이 올바른 네트워크에 연결되었는지, Hyper-V 호스트에 최적으로 배치되었는지 확인합니다. 네트워크 매핑을 설정하려면 VMM 서버에서 VM 네트워크를 구성해야 합니다. |
| **저장소 매핑** |VMM에서 VMM으로 복제하는 경우 선택적으로 [저장소 매핑](site-recovery-storage-mapping.md) 을 설정하여 복제된 VM에 대한 저장소 대상을 지정할 수 있습니다. 저장소 매핑은 Hyper-V 복제본 및 SAN 복제에 대해 모두 설정할 수 있습니다.<br/><br/>  저장소 매핑은 현재 Azure 포털에서 지원되지 않습니다. |

## <a name="verify-url-access"></a>URL 액세스 확인
서버에서 다음 URL에 액세스할 수 있는지 확인합니다.

| **URL** | **VMM에서 VMM으로** | **VMM에서 Azure로** | **Hyper-V에서 Azure로** | **VMware에서 Azure로** |
| --- | --- | --- | --- | --- |
| *.accesscontrol.windows.net |허용 |허용 |허용 |허용 |
| *.backup.windowsazure.com |필요하지 않음 |허용 |허용 |허용 |
| *.hypervrecoverymanager.windowsazure.com |허용 |허용 |허용 |허용 |
| *.store.core.windows.net |허용 |허용 |허용 |허용 |
| *.blob.core.windows.net |필요하지 않음 |허용 |허용 |허용 |
| https://www.msftncsi.com/ncsi.txt |허용 |허용 |허용 |허용 |
| https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi |필요하지 않음 |필요하지 않음 |필요하지 않음 |허용 |

## <a name="azure-virtual-machine-requirements"></a>Azure 가상 컴퓨터 요구 사항
Azure에서 지원하는 운영 체제를 실행하는 가상 컴퓨터와 물리적 서버를 복제하기 위해 Site Recovery를 배포할 수 있습니다. 여기에는 대부분 버전의 Windows 및 Linux가 포함됩니다. 보호할 온-프레미스 가상 컴퓨터가 Azure 요구 사항을 준수하는지 확인해야 합니다.

| **기능** | **요구 사항** | **세부 정보** |
| --- | --- | --- |
| Hyper-V 호스트 |Windows Server 2012 R2를 실행해야 합니다. |운영 체제가 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| VMware 하이퍼바이저 |지원되는 운영 체제 |[요구 사항 확인](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) |
| 게스트 운영 체제 |Hyper-V에서 Azure로 복제: Site Recovery에서는 [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)모든 운영 체제를 지원합니다. <br/><br/> VMware 및 물리적 서버 복제의 경우: Windows 및 Linux [필수 구성 요소](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| 게스트 운영 체제 아키텍처 |64비트 |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| 운영 체제 디스크 크기 |최대 1023GB |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| 운영 체제 디스크 수 |1 |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| 데이터 디스크 수 |16 개 이하(최대 값은 만들고자 하는 가상 컴퓨터 크기의 함수) 16 = XL) |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| 데이터 디스크 VHD 크기 |최대 1023GB |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| 네트워크 어댑터 |여러 어댑터가 지원됩니다. | |
| 고정 IP 주소 |지원됨 |주 가상 컴퓨터가 고정 IP 주소를 사용하는 경우 Azure에서 생성되는 가상 컴퓨터에 대해 고정 IP 주소를 지정할 수 있습니다. Hyper-v에서 실행되는 Linux 가상 컴퓨터에 대한 고정 IP 주소는 지원되지 않습니다. |
| iSCSI 디스크 |지원되지 않음 |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| 공유 VHD |지원되지 않음 |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| FC 디스크 |지원되지 않음 |지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. |
| 하드 디스크 형식 |VHD  <br/><br/>  VHDX |현재 Azure에서는 VHDX가 지원되지 않지만 Azure로 장애 조치하면 Site Recovery가  자동으로 VHDX를 VHD로 변환합니다. 온-프레미스에 장애 복구 시 가상 컴퓨터에서 계속해서 VHDX 형식을 사용합니다. |
| Bitlocker |지원되지 않음 |가상 컴퓨터를 보호하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다. |
| 가상 컴퓨터 이름 |1 자에서 63자 사이입니다. 문자, 숫자 및 하이픈으로 제한됩니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |Site Recovery에서 가상 컴퓨터 속성 내 값 업데이트 |
| 가상 컴퓨터 유형 |<p>1세대</p> <p>2세대 - Windows</p> |300GB보다 작은 VHDX의 디스크 형식인 데이터 볼륨 1 또는 2를 포함하는 기본 디스크의 OS 디스크 형식인 2세대 가상 컴퓨터입니다. Linux 2세대 가상 컴퓨터는 지원되지 않습니다. [자세한 정보](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="optimizing-your-deployment"></a>배포 최적화
다음 팁을 사용하면 배포의 최적화 및 크기 조정에 도움이 됩니다.

* **운영 체제 볼륨 크기**: Azure에 가상 컴퓨터를 복제하는 경우 운영 체제 볼륨이 1TB 미만이어야 합니다. 이것보다 더 많은 볼륨이 있는 경우 배포를 시작하기 전에 볼륨을 다른 디스크로 수동으로 이동할 수 있습니다.
* **데이터 디스크 크기**: Azure에 복제하는 경우 최대 1TB의 각 가상 컴퓨터에서 최대 32 개의 데이터 디스크를 사용할 수 있습니다. ~32 TB 가상 컴퓨터를 효과적으로 복제 및 장애 조치할 수 있습니다.
* **복구 계획 제한**: 사이트 복구는 수천 개의 가상 컴퓨터로 확장할 수 있습니다. 복구 계획은 다 함께 장애 조치되어야 하는 응용 프로그램에 대한 모델로 설계되었으므로 복구 계획에서 컴퓨터의 수를 50으로 제한할 수 있습니다.
* **Azure 서비스 제한**: 모든 Azure 구독은 코어, 클라우드 서비스 등에 대한 일련의 기본 제한값이 제공됩니다. 구독에서 리소스의 가용성을 확인하기 위해 테스트 장애 조치(Failover)를 실행하는 것이 좋습니다. Azure 지원을 통해 이러한 제한을 수정할 수 있습니다.
* **용량 계획**: Site Recovery에 대한 [용량 계획](site-recovery-capacity-planner.md) 을 읽어 보세요.
* **복제 대역폭**: 복제 대역폭이 부족한 경우 다음에 유의하십시오.
  * **Express 경로**: 사이트 복구는 Riverbed와 같은 Azure Express 경로 및 WAN 최적화 프로그램과 함께 작동합니다. [여기서](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) Express 경로에 대해 더 알아볼 수 있습니다.
  * **복제 트래픽**: 사이트 복구는 전체 VHD가 아닌 데이터 블록만 사용하여 스마트 초기 복제를 수행합니다. 복제가 진행 중일 때는 변경 내용만 복제됩니다.
  * **네트워크 트래픽**: 대상 IP 주소 및 포트 기반 정책을 포함하는 [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)를 설정하여 복제에 사용된 네트워크 트래픽을 제어할 수 있습니다.  또한 Azure 백업 에이전트를 사용하여 Azure Site Recovery에 복제하는 경우 해당 에이전트에 대한 제한을 구성할 수 있습니다. [자세히 알아보기](https://support.microsoft.com/kb/3056159).
* **RTO**: Site Recovery를 통해 예상할 수 있는 RTO(복구 시간 목표)를 측정하려면 테스트 장애 조치(Failover)를 실행하고 Site Recovery 작업을 검토하여 작업 완료까지 시간이 얼마나 걸리는지 분석하는 것이 좋습니다. Azure에 장애 조치하는 경우 최선의 RTO를 위해 Azure 자동화 및 복구 계획과 통합하여 모든 수동 작업을 자동화하는 것이 좋습니다.
* **RPO**: Azure에 복제하는 경우 사이트 복구는 가까운 동기 복구 지점 목표 (RPO)를 지원 합니다. 이때 데이터 센터와 Azure 사이에서 충분한 대역폭을 가정합니다.

## <a name="service-urls"></a>서비스 URL
서버에서 다음 URL에 액세스할 수 있는지 확인합니다.

| **URL** | **VMM에서 VMM으로** | **VMM에서 Azure로** | **Hyper-V 사이트에서 Azure로** | **VMware에서 Azure로** |
| --- | --- | --- | --- | --- |
|  \*.accesscontrol.windows.net |액세스 필요 |액세스 필요 |액세스 필요 |액세스 필요 |
|  \*.backup.windowsazure.com | |액세스 필요 |액세스 필요 |액세스 필요 |
|  \*.hypervrecoverymanager.windowsazure.com |액세스 필요 |액세스 필요 |액세스 필요 |액세스 필요 |
|  \*.store.core.windows.net |액세스 필요 |액세스 필요 |액세스 필요 |액세스 필요 |
|  \*.blob.core.windows.net | |액세스 필요 |액세스 필요 |액세스 필요 |
|  https://www.msftncsi.com/ncsi.txt |액세스 필요 |액세스 필요 |액세스 필요 |액세스 필요 |
|  https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | | | |액세스 필요 |

## <a name="next-steps"></a>다음 단계
일반적인 배포 요구 사항을 이해하고 비교한 후에는 구체적인 필수 조건을 읽고 각 시나리오 배포를 시작할 수 있습니다.

* [Azure에 VMWare 가상 컴퓨터 복제](site-recovery-vmware-to-azure-classic.md)
* [물리적 서버를 Azure에 복제](site-recovery-vmware-to-azure-classic.md)
* [VMM 클라우드의 Hyper-V 서버를 Azure로 복제](site-recovery-vmm-to-azure.md)
* [Hyper-V 가상 컴퓨터(VMM 없음)를 Azure로 복제](site-recovery-hyper-v-site-to-azure.md)
* [Hyper-V VM을 보조 사이트로 복제](site-recovery-vmm-to-vmm.md)
* [SAN을 사용하여 Hyper-V VM을 보조 사이트로 복제](site-recovery-vmm-san.md)
* [단일 VMM 서버를 사용하여 Hyper-V VM 복제](site-recovery-single-vmm.md)



<!--HONumber=Nov16_HO3-->


