---
title: "Azure에서 SAP HANA 운영 | Microsoft Docs"
description: "Azure 기본 VM에서 SAP HANA 운영"
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
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>Azure의 SAP HANA 운영 가이드
이 가이드에서는 Azure Virtual Machines에 배포된 SAP HANA 시스템 운영을 위한 지침을 제공합니다. 이 문서는 어떠한 표준 SAP 문서도 대신하지 않습니다. SAP 가이드와 참고는 다음 위치에서 제공합니다.

- [SAP 관리 가이드](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 설치 가이드](https://service.sap.com/instguides)
- [SAP 참고](https://sservice.sap.com/notes)

먼저 다음의 여러 Azure 구성 요소에 대한 기본 지식이 있어야 합니다.

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure Networking 및 VNet](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

SAP NetWeaver 및 기타 Azure의 SAP 구성 요소에 관한 추가적인 문서는 [Azure 문서](https://docs.microsoft.com/azure/)의 [Azure의 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 섹션에서 찾을 수 있습니다.

## <a name="basic-setup-considerations"></a>기본 설치 고려 사항
### <a name="connecting-into-azure"></a>Azure에 연결
[SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현][계획 -가이드]에서 설명한 것처럼 두 가지 기본 방법으로 Azure Virtual Machines에 연결할 수 있습니다. 

- 인터넷과, Jump VM 또는 SAP HANA를 실행하는 VM의 공개 끝점을 통해 연결
- [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 또는 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 통해 연결

프로덕션 시나리오나, SAP 소프트웨어와 연계하여 프로덕션 시나리오를 준비하는 비 프로덕션 시나리오의 경우 이 그림에서 보듯 VPN이나 ExpressRoute를 통한 사이트 간 연결이 필요합니다.

![사이트 간 연결](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>선택한 Azure VM 형식
프로덕션 시나리오에 사용할 수 있는 Azure VM 유형은 [여기](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 확인할 수 있습니다. 비 프로덕션 시나리오의 경우 더 다양한 기본 Azure VM을 선택할 수 있습니다. 그러나 [SAP 참고 #1928533](https://launchpad.support.sap.com/#/notes/1928533)에 해당하는 VM 유형을 사용하도록 합니다. Azure에서 그러한 VM의 배포는 다음을 통해 수행할 수 있습니다.

- Azure 포털
- Azure Powershell Cmdlet
- Azure CLI

[여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)에서 설명한 대로 [SAP 클라우드 플랫폼](https://cal.sap.com/)을 통해 Azure Virtual machines 서비스에 전체 설치된 SAP HANA 플랫폼을 배포할 수 있습니다.

### <a name="choice-of-azure-storage"></a>선택한 Azure Storage 
Azure는 SAP HANA를 실행하는 Azure VM에 적합한 두 가지 주 저장소 유형을 제공합니다.

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure는 Azure Standard 및 Premium Storage에서 VHD를 위한 두 가지 배포 방법을 제공합니다. 전반적인 시나리오에서 가능하다면 [Azure Managed Disk](https://azure.microsoft.com/services/managed-disks/) 배포를 활용하는 것이 좋습니다.

이러한 저장소 유형과 관련한 정확한 저장소 유형과 SLA는 [이 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)를 확인하세요.

/hana/data 및 /hana/log 볼륨에는 Azure Premium Disk를 사용하는 것이 좋습니다. 여러 Premium Storage 디스크에 걸쳐 LVM RAID를 빌드하고 이러한 RAID 볼륨을 /hana/data 및 /hana/log 볼륨으로 사용할 수 있습니다.

고객이 지금까지 Azure VM에서 SAP HANA를 호스팅하는 데 사용한 여러 일반적인 VM 유형에 가능한 구성은 다음과 같을 수 있습니다.

| VM SKU | RAM | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Azure 네트워킹
VPN이나, Azure에 대한 ExpressRoute 사이트 간 연결이 있다고 가정하면 최소한 가상 게이트웨이를 통해 VPN이나 ExpressRoute 회로에 연결된 하나의 [Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)이 있을 것입니다. 가상 게이트웨이는 Azure Vnet의 서브넷에서 실행됩니다. HANA를 설치하려면 VNet 안에 다른 두 서브넷을 만들게 됩니다. 한 서브넷은 SAP HANA 인스턴스를 실행하는 VM을 호스팅하고, 다른 서브넷은 SAP HANA Studio나 다른 관리 소프트웨어를 호스팅할 수 있는 최종 점프박스나 관리 VM을 실행합니다.
HANA를 실행하는 VM을 설치할 때 이 VM은 다음을 갖게 됩니다.

- 두 가상 NIC 설치. 하나의 NIC는 관리 서브넷에 연결되고 다른 NIC는 온-프레미스나 다른 네트워크에서 Azure VM의 SAP HANA 인스턴스에 연결하는 데 사용됩니다.
- 두 vNIC에 대해 배포된 고정 개인 IP 주소

다양한 IP 주소 할당 방식의 개요는 [여기](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)에서 확인할 수 있습니다. 

SAP HANA 인스턴스에 직접 라우팅되거나 점프박스에 라우팅되는 트래픽은 HANA 서브넷과 관리 서브넷에 연결된 [Azure Network Security Groups](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)에서 지시합니다.

대략적인 전체 배포 스키마는 다음과 같습니다.

![SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking.PNG)


사이트 간 연결(VPN이나 ExpressRoute에서 Azure로) 없이 Azure에 SAP HANA를 배포한 경우, 점프박스 VM을 실행하는 Azure VM에 공개 IP 주소가 할당되지만 SAP HANA 인스턴스에는 액세스할 수 있습니다. 간단한 경우 Azure 기본 제공 DNS 서비스를 사용하여 호스트 이름을 확인합니다. 특히 공용 연결 IP 주소를 사용할 경우, Azure Network Security Groups를 사용하여 공용 연결 IP 주소를 통해 자산을 실행하는 Azure 서브넷에 연결할 수 있는 개방 포트나 IP 주소 범위를 제한합니다. 이러한 배포의 스키마는 다음과 유사합니다.
  
![사이트 간 연결 없는 SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>작업
### <a name="backup-and-restore-operations-on-azure-vms"></a>Azure VM의 백업 및 복원 작업
SAP HANA 백업 및 복원 가능성은 다음 문서에서 설명합니다.

- [SAP HANA 백업 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 파일 수준 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 저장소 스냅숏 벤치마크](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>SAP HANA가 들어 있는 VM의 시작 및 다시 시작
Azure 공용 클라우드의 장점 중 하나는 지출한 연산 시간에 대해서만 요금이 청구된다는 것입니다. 즉 SAP HANA를 실행하는 VM을 종료했다면 해당 시간 동안은 저장소 비용만 청구됩니다. 다시 SAP HANA가 있는 VM을 시작하면 VM이 시작되고 같은 IP 주소를 갖게 됩니다(고정 IP 주소로 배포한 경우). 


### <a name="saprouter-enabling-sap-remote-support"></a>SAP 원격 지원 구현하는 SAPRouter 
온-프레미스 위치와 Azure 간에 사이트 간 연결이 있고 SAP 구성 요소를 실행하고 있다면 이미 SAProuter를 실행하고 있을 가능성이 높습니다. 이 경우 Azure에서 SAP HANA 인스턴스 작업이 필요하지 않습니다. 단 SAPRouter 구성에서 HANA를 호스팅하는 VM의 개인 및 고정 IP 주소를 그대로 유지하고 HANA VM을 호스팅하는 서브넷의 NSG를 조절해야 합니다(포트 TCP/IP 3299를 통한 트래픽 허용).

SAP HANA를 배포하고 있고 인터넷을 통해 Azure에 연결하며 SAP HANA가 있는 VM을 실행하는 Vnet에 SAP 라우터를 설치하지 않은 경우 아래와 같이 관리 서브넷에서 별도의 VM에 SAPRouter를 설치해야 합니다.


![사이트 간 연결 및 SAPRouter 없는 SAP HANA의 대략적인 배포 스키마](media/hana-vm-operations/hana-simple-networking3.PNG)

SAPRouter는 점프박스 VM이 아니라 별도의 VM에 설치해야 합니다. 별도의 VM에는 고정 IP 주소가 필요합니다. SAP가 호스팅하는 SAPRouter(VM을 설치한 SAPRouter 인스턴스의 짝)에 SAPRouter를 연결하려면 SAP에 문의하여 SAPRouter 인스턴스 구성에 필요한 IP 주소를 SAP로부터 받아야 합니다. 필요한 유일한 포트는 TCP 포트 3299입니다.
SAPRouter를 통한 원격 지원 연결의 설정 및 유지 관리 방법에 대한 자세한 내용은 이 [SAP 원본](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)을 참조하세요.

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>기본 Azure VM의 SAP HANA 고가용성 
SUSE Linux 12 SP1 이상을 실행하면 STONITH 장치로 페이스메이커를 설정하여 HANA 시스템 복제 및 자동 장애 조치(failover)를 통해 동기화 복제를 사용하는 SAP HANA 구성을 설정할 수 있습니다. 설정 절차는 [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability)에서 설명합니다.

 










