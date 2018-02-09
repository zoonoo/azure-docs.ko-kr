---
title: "Azure에서 SAP HANA 운영 | Microsoft Docs"
description: "Azure Virtual Machines에 배포된 SAP HANA 시스템에 대한 운영 가이드입니다."
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d6991d40b9bb8543898bbbdc9d7c905dfe11536
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>Azure의 SAP HANA 운영 가이드
이 문서에서는 Azure VM(Virtual Machines)에 배포된 SAP HANA 시스템 운영을 위한 지침을 제공합니다. 이 문서는 다음 내용을 포함하는 표준 SAP 설명서를 대체하기 위한 것이 아닙니다.

- [SAP administration guide](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)(SAP 관리 가이드)
- [SAP installation guides](https://service.sap.com/instguides)(SAP 설치 가이드)
- [SAP notes](https://sservice.sap.com/notes)(SAP 참고)

## <a name="prerequisites"></a>필수 조건
이 가이드를 사용하려면 다음 Azure 구성 요소에 대한 기본 지식이 필요합니다.

- [Azure 가상 머신](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 네트워킹 및 가상 네트워크](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Azure의 SAP NetWeaver 및 기타 SAP 구성 요소에 대해 자세히 알아보려면 [Azure 설명서](https://docs.microsoft.com/azure/)의 [Azure의 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 섹션을 참조하세요.

## <a name="basic-setup-considerations"></a>기본 설치 고려 사항
다음 섹션에서는 Azure VM에 SAP HANA 시스템을 배포하기 위한 기본 설치 고려 사항을 설명합니다.

### <a name="connect-into-azure-virtual-machines"></a>Azure Virtual Machines에 연결
[Azure Virtual Machines 계획 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)에 설명된 대로 Azure VM에 연결하기 위한 두 가지 기본 방법이 있습니다.

- SAP HANA를 실행하는 VM 또는 Jump VM의 공개 끝점과 인터넷을 통해 연결
- [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 또는 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 통해 연결

VPN 또는 ExpressRoute를 통한 사이트 간 연결은 프로덕션 시나리오에 필요합니다. 이 유형의 연결은 SAP 소프트웨어가 사용되는 프로덕션 시나리오로 제공되는 비프로덕션 시나리오에도 필요합니다. 다음 그림은 사이트 간 연결의 예를 보여 줍니다.

![사이트 간 연결](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure VM 유형 선택
프로덕션 시나리오에 사용할 수 있는 Azure VM 유형은 [SAP documentation for IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)(IAAS에 대한 SAP 설명서)에 나와 있습니다. 비프로덕션 시나리오의 경우 더 다양한 네이티브 Azure VM 유형을 사용할 수 있습니다.

>[!NOTE]
>비프로덕션 시나리오의 경우 [SAP note #1928533](https://launchpad.support.sap.com/#/notes/1928533)(SAP 참고 #1928533)에 나열된 VM 유형을 사용합니다.

다음을 사용하여 Azure에 VM을 배포합니다.

- Azure Portal
- Azure PowerShell cmdlet
- Azure CLI

[SAP 클라우드 플랫폼](https://cal.sap.com/)을 통해 Azure VM 서비스에 전체 설치된 SAP HANA 플랫폼을 배포할 수도 있습니다. 설치 프로세스는 [Azure에서 SAP S/4HANA 또는 BW/4HANA 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)에 설명되어 있습니다.

### <a name="choose-azure-storage-type"></a>Azure Storage 유형 선택
Azure에서는 SAP HANA를 실행하는 Azure VM에 적합한 두 가지 저장소 유형을 제공합니다.

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure는 Azure Standard 및 Premium Storage에서 VHD를 위한 두 가지 배포 방법을 제공합니다. 전반적인 시나리오에서 가능하다면 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 배포를 활용하세요.

저장소 유형 및 해당 SLA의 목록을 보려면 [관리 디스크에 대한 Azure 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

/hana/data 및 /hana/log 볼륨에는 Azure Premium Disks를 사용하는 것이 좋습니다. 여러 Premium Storage 디스크에 LVM RAID를 빌드하고 이러한 RAID 볼륨을 /hana/data 및 /hana/log 볼륨으로 사용할 수 있습니다.

다음 표에서는 고객이 Azure VM에서 SAP HANA를 호스트하는 데 일반적으로 사용하는 VM 유형의 구성을 보여 줍니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128GiB | 384MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256GiB | 768 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GiB | 1200GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448GiB | 2000GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000GiB | 1000GB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750GiB | 1000GB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000GiB | 2000GB |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800GiB | 2000GB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


> [!NOTE]
> 3 x P20을 사용하는 작은 VM 유형에 권장되는 디스크는 [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)(SAP TDI 저장소 백서)에 따른 공간 권장 사항과 관련된 볼륨 크기를 초과합니다. 하지만 이 표에 표시된 항목은 SAP HANA에 필요한 충분한 디스크 처리량을 제공하도록 선택되었습니다. 더 적은 I/O 처리량이 필요한 경우 /hana/data 및 /hana/log에 대한 Premium Storage 디스크 선택을 조정할 수 있습니다. 메모리 볼륨의 두 배인 백업을 유지하기 위해 크기 지정된 /hana/backup 볼륨의 크기 조정도 마찬가지입니다. 즉, 필요한 공간이 적으면 조정할 수 있습니다. VM의 크기를 조정하거나 결정할 때도 전체 VM I/O 처리량을 고려하세요. 전체 VM 처리량은 [메모리 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory) 문서에 설명되어 있습니다.  

> [!NOTE]
> [Azure 가상 머신 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)를 활용하려면 Standard Storage(Sxx)로 나열된 모든 VHD를 Premium Storage(Pxx)로 변경해야 합니다. 


### <a name="set-up-azure-virtual-networks"></a>Azure 가상 네트워크 설정
VPN 또는 ExpressRoute를 통해 Azure로의 사이트 간 연결이 있다면 가상 게이트웨이를 통해 VPN 또는 ExpressRoute 회로에 연결된 [Azure 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)가 하나 이상 있어야 합니다. 가상 게이트웨이는 Azure 가상 네트워크의 서브넷에서 실행됩니다. SAP HANA를 설치하려면 가상 네트워크 내에 두 개의 서브넷을 추가로 만듭니다. 한 서브넷은 SAP HANA 인스턴스를 실행하는 VM을 호스트하고, 다른 서브넷은 SAP HANA Studio 또는 기타 관리 소프트웨어를 호스트하는 Jumpbox 또는 관리 VM을 실행합니다.

SAP HANA를 실행할 VM을 설치할 때 VM에는 다음이 필요합니다.

- 두 가상 NIC 설치: 하나의 NIC는 관리 서브넷에 연결되고 다른 NIC는 온-프레미스 네트워크나 다른 네트워크에서 Azure VM의 SAP HANA 인스턴스에 연결됩니다.
- 두 가상 NIC에 대해 배포된 고정 개인 IP 주소

IP 주소를 할당하는 다른 방법에 대한 개요는 [IP 주소 유형 및 Azure에서 할당 방법](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)을 참조하세요. 

[Azure NSG(네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)는 SAP HANA 인스턴스 또는 Jumpbox로 라우팅된 트래픽을 전달하는 데 사용됩니다. NSG는 SAP HANA 서브넷 및 관리 서브넷에 연결됩니다.

다음 그림은 SAP HANA의 대략적인 배포 스키마를 간단하게 보여 줍니다.

![SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking.PNG)


사이트 간 연결 없이 Azure에 SAP HANA를 배포하려면 공용 IP 주소를 통해 SAP HANA 인스턴스에 액세스합니다. Jumpbox VM을 실행하는 Azure VM에 이 IP 주소를 할당해야 합니다. 이러한 기본 시나리오의 배포에서는 Azure 기본 제공 DNS 서비스가 호스트 이름을 확인해야 합니다. 공용 IP 주소가 사용되는 더 복잡한 배포에서는 Azure 기본 제공 DNS 서비스가 특히 중요합니다. Azure NSG를 사용하여 공용 IP 주소를 사용하는 자산이 있는 Azure 서브넷에 연결할 수 있는 열린 포트 또는 IP 주소 범위를 제한합니다. 다음 그림은 사이트 간 연결 없이 SAP HANA를 배포하는 경우의 대략적인 스키마를 보여 줍니다.
  
![사이트 간 연결 없는 SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Azure VM에서 SAP HANA를 배포하기 위한 작업
다음 섹션에서는 Azure VM에서 SAP HANA 시스템을 배포하는 일과 관련된 몇 가지 작업을 설명합니다.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM의 백업 및 복원 작업
다음 문서에서는 SAP HANA 배포를 백업 및 복원하는 방법을 설명합니다.

- [SAP HANA 백업 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 파일 수준 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 저장소 스냅숏 벤치마크](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANA를 포함하는 VM 시작 및 다시 시작
Azure 공용 클라우드의 두드러진 기능으로 사용한 컴퓨팅 시간(분)에 대해서만 요금이 부과되는 것을 들 수 있습니다. 예를 들어 SAP HANA를 실행 중인 VM을 종료하는 경우 해당 시간 동안의 저장소 비용만 청구됩니다. 초기 배포에서 VM에 대해 고정 IP 주소를 지정하는 경우 또 다른 기능을 사용할 수 있습니다. SAP HANA가 있는 VM을 다시 시작하면 VM이 이전 IP 주소로 다시 시작됩니다. 


### <a name="use-saprouter-for-sap-remote-support"></a>SAP 원격 지원에 SAProuter 사용
온-프레미스 위치와 Azure 사이에 사이트 간 연결이 있고 SAP 구성 요소를 실행하고 있는 경우 이미 SAProuter를 실행 중일 것입니다. 이 경우 원격 지원을 위해 다음 항목을 완료하세요.

- SAP HANA를 호스트하는 VM의 개인 및 고정 IP 주소를 SAProuter 구성에서 유지 관리합니다.
- HANA VM을 호스트하는 서브넷의 NSG에서 TCP/IP 포트 3299를 통해 트래픽을 허용하도록 구성합니다.

인터넷을 통해 Azure에 연결 중이고 SAP HANA를 사용하는 VM에 대한 SAP 라우터가 없는 경우 이 구성 요소를 설치해야 합니다. 관리 서브넷에서 별도의 VM에 SAProuter를 설치합니다. 다음 그림은 사이트 간 연결 없이 SAProuter를 사용하여 SAP HANA를 배포하는 경우의 대략적인 스키마를 보여 줍니다.

![사이트 간 연결 및 SAProuter 없는 SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking3.PNG)

SAPRouter는 Jumpbox VM이 아니라 별도의 VM에 설치해야 합니다. 별도의 VM에는 고정 IP 주소가 있어야 합니다. SAProuter를 SAP에서 호스트하는 SAProuter에 연결하려면 SAP에 IP 주소를 문의하세요. (SAP에서 호스트하는 SAProuter는 VM에 설치하는 SAProuter 인스턴스에 해당합니다.) SAP의 IP 주소를 사용하여 SAProuter 인스턴스를 구성합니다. 구성 설정에서 필수 포트는 TCP 포트 3299뿐입니다.

SAProuter를 통해 원격 지원 연결을 설정하고 유지 관리하는 방법에 대한 자세한 내용은 [SAP documentation](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)(SAP 설명서)을 참조하세요.

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure 네이티브 VM의 SAP HANA 고가용성
SUSE Linux 12 SP1 이상을 실행 중인 경우 STONITH 장치를 포함하는 Pacemaker 클러스터를 설정할 수 있습니다. 이 장치를 사용하면 HANA 시스템 복제를 통한 동기 복제와 자동 장애 조치(failover)를 사용하는 SAP HANA 구성을 설정할 수 있습니다. 설정 절차에 대한 자세한 내용은 [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)을 참조하세요.
