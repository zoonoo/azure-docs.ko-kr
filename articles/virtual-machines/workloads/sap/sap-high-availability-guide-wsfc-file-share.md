---
title: "Azure에서 파일 공유를 사용하여 SAP (A) SCS 인스턴스를 Windows 장애 조치(failover) 클러스터에 클러스터링 | Microsoft Docs"
description: "파일 공유를 사용하여 SAP (A)SCS 인스턴스를 Windows 장애 조치(failover) 클러스터에 클러스터링"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 94d725cfb072091e57c96d3b2aca7b2e73657eef
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#ds-series

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


# <a name="clustering-sap-ascs-instance-on-windows-failover-cluster-using-file-share-on-azure"></a>Azure에서 파일 공유를 사용하여 SAP (A)SCS 인스턴스를 Windows 장애 조치(failover) 클러스터에 클러스터링

> ![Windows][Logo_Windows] Windows
>

Windows Server 장애 조치 클러스터링은 Windows에서 고가용성 SAP ASCS/SCS를 설치하고 DBMS를 사용하기 위한 기반이 됩니다.

장애 조치 클러스터는 함께 작동하여 응용 프로그램 및 서비스의 가용성을 높이는 1+n개 독립 서버(노드) 그룹입니다. 노드에 장애가 발생하는 경우 Windows Server 장애 조치 클러스터링은 응용 프로그램 및 서비스를 제공하기 위해 정상 클러스터를 유지 관리하는 동안 발생할 수 있는 장애 횟수를 계산합니다. 장애 조치 클러스터링을 달성하기 위해 여러 다른 쿼럼 모드 중에서 선택할 수 있습니다.

## <a name="prerequisite"></a>필수 요소
이 문서를 시작하기 전에 다음 문서를 검토해야 합니다.

* [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 아키텍처 및 시나리오][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
>파일 공유를 사용하여 SAP (A)SCS 인스턴스를 클러스터링하는 것은 **SAP 커널 7.49 이상**의 **SAP NetWeaver 7.40 이상**에서 지원됩니다.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Azure의 Windows Server 장애 조치(failover) 클러스터링

운영 체제 미설치 또는 사설 클라우드 배포에 비해, Azure Virtual Machines는 Windows Server 장애 조치 클러스터링을 구성하기 위한 추가 단계가 필요합니다. 클러스터를 빌드할 때 SAP ASCS/SCS 인스턴스에 대해 여러 개의 IP 주소 및 가상 호스트 이름을 설정해야 합니다.

### <a name="name-resolution-in-azure-and-cluster-virtual-host-name"></a>Azure 및 클러스터 가상 호스트 이름에서 이름 확인

Azure 클라우드 플랫폼은 부동 IP 주소와 같은 가상 IP 주소를 구성하는 옵션을 제공하지 않습니다. 클라우드의 클러스터 리소스에 연결하도록 가상 IP 주소를 설정하기 위한 대체 솔루션이 필요합니다. Azure의 Azure Load Balancer 서비스에는 **내부 부하 분산 장치**가 있습니다. 내부 부하 분산 장치를 사용하면 클라이언트는 클러스터 가상 IP 주소를 통해 클러스터에 도달합니다. 클러스터 노드를 포함하는 리소스 그룹에 부하 분산 장치를 배포해야 합니다. 그런 후 내부 부하 분산 장치의 프로브 포트를 사용하여 필요한 모든 포트 전달 규칙을 구성합니다. 클라이언트는 가상 호스트 이름을 통해 연결할 수 있습니다. DNS 서버는 클러스터 IP 주소를 확인하고 내부 부하 분산 장치는 클러스터의 활성 노드에 대한 포트 전달을 처리합니다.

![그림 1: 공유 디스크를 사용하지 않는 Azure의 Windows Server 장애 조치(failover) 클러스터링 구성][sap-ha-guide-figure-1001]

_**그림 1:** 공유 디스크를 사용하지 않는 Azure의 Windows Server 장애 조치(failover) 클러스터링 구성_

## <a name="sap-ascs-ha-with-file-share"></a>파일 공유를 사용하는 SAP (A)SCS HA

SAP는 Windows 장애 조치(failover) 클러스터에 SAP (A)SCS 인스턴스를 클러스터링하기 위한 새로운 접근 방법을 개발했습니다. 이것은 클러스터 공유 디스크의 대안이 됩니다.

여기에서 사용된 **SMB 파일 공유**는 **SAP GLOBAL HOST 파일**을 배포하기 위한 옵션입니다.

> [!NOTE]
>SMB 파일 공유는 SAP (A)SCS 인스턴스를 클러스터링하기 위한 추가적인 공유 디스크 클러스터링 옵션입니다.  
>

이 아키텍처의 고유 특징은 다음과 같습니다.

* **(자체 파일 구조와 메시지 및 큐에 넣기 프로세스를 갖는 )SAP 중앙 서비스가 SAP GLOBAL Host 파일과 분리됨**
* **SAP 중앙 서비스가 SAP (A)SCS 인스턴스 아래에서 실행됨**
* SAP (A)SCS 인스턴스를 가상 호스트 이름 **<(A)SCSVirtualHostName>**을 사용하여 클러스터링되고 액세스 가능함
* SAP GLOBAL 파일이 SMB 파일 공유에 배치되고 <SAPGLOBALHost> 호스트 이름 \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\.를 사용하여 액세스됨
* SAP (A)SCS 인스턴스가 양 클러스터 노드에서 로컬 디스크에 설치됨
* **<(A)SCSVirtualHostName>** 네트워크 이름이 **&lt;SAPGLOBALHost&gt;**와 다름

![그림 2: SMB 파일 공유를 사용하는 새로운 SAP (A)SCS HA 아키텍처][sap-ha-guide-figure-8004]

_**그림 2:** SMB 파일 공유를 사용하는 새로운 SAP (A)SCS HA 아키텍처_

SMB 파일 공유의 필수 조건:

* SMB 3.0 이상의 프로토콜
* **AD 사용자 그룹** 및 **컴퓨터 개체 컴퓨터$**에 대해 AD(Active Directory) ACL(액세스 제어 목록)을 설정할 수 있어야 함
* 파일 공유에서 HA를 사용하도록 설정되어 있어야 함:
    * 파일 저장에 사용되는 디스크는 단일 실패 지점이 될 수 없음
    * 서버/VM의 가동 중지 시간이 파일 공유의 가동 중지 시간을 유발하지 않도록 해야 함

**SAP &lt;SID&gt;** 클러스터 역할에 클러스터 공유 디스크 또는 일반 파일 공유 클러스터 리소스가 포함되어 있지 않습니다.


![그림 3: 파일 공유를 사용하는 경우 SAP <SID> 클러스터 역할 리소스][sap-ha-guide-figure-8005]

_**그림 3:** 파일 공유를 사용하는 경우 **SAP &lt;SID&gt;** 클러스터 역할 리소스_


## <a name="scale-out-file-share-sofs-with-storage-spaces-direct-s2d-on-azure-as-sapmnt-file-share"></a>S2D(저장소 공간 다이렉트)가 적용된 Azure의 SOFS(Scale-Out File Share)를 SAPMNT 파일 공유로 사용

SAP GLOBAL 호스트 파일을 호스트 및 보호하고 고가용성 SAPMNT 파일 공유 서비스를 제공하기 위해 SOFS를 사용할 수 있습니다.

![그림 4: SAP GLOBAL 호스트 파일을 보호하는 데 사용된 SOFS 파일 공유][sap-ha-guide-figure-8006]

_**그림 4:** SAP GLOBAL 호스트 파일을 보호하는 데 사용된 SOFS 파일 공유_

> [!IMPORTANT]
>SOFS 파일 공유는 Microsoft Azure 클라우드와 온-프레미스 환경에서 모두 지원됩니다.
>

**SOFS**는 수평 확장 가능한 고가용성 SAPMNT 파일 공유를 제공하고 있습니다.

**S2D(저장소 공간 다이렉트)**를 SOFS의 **공유 디스크**로 사용하면 로컬 저장소가 있는 서버를 사용하여 확장성 있는 고가용성 저장소를 구축할 수 있습니다. 따라서 SOFS에 사용되는 공유 저장소(예: SAP GLOBALHOST 파일의 경우)는 단일 실패 지점(SPOF)이 아닙니다.

> [!IMPORTANT]
>재해 복구를 설정할 계획이라면 Azure의 고가용성 파일 공유를 위해 SOFS를 사용하는 것이 좋습니다.
>

### <a name="sap-prerequisites-for-sofs-in-azure"></a>Azure에서 SOFS를 위한 SAP 필수 조건

SOFS를 사용하려면 다음이 필요합니다.

* SOFS에 사용할 둘 이상의 클러스터 노드

* 각 노드는 둘 이상의 로컬 디스크를 가져야 합니다.

* 성능상의 이유로 **미러링 복원**을 사용해야 합니다.
    * **2방향** 미러링: 2개의 클러스터 노드를 갖는 SOFS
    * **3방향** 미러링: 3개 이상의 클러스터 노드를 갖는 SOFS


* **3방향 미러링의 경우 SOFS가 3개 이상의 (클러스터) 노드를 갖는 것이 좋습니다**.
이 설정은 2개의 클러스터 노드와 2방향 미러링을 갖는 SOFS 설정보다 확장성과 저장소 복원력이 좋습니다.

* **Azure Premium 디스크**를 사용해야 합니다.

* **Azure Managed Disks**가 **권장**됩니다. 

* 새 **ReFS(Resilient File System)**를 이용하여 볼륨을 포맷하는 것이 **권장**됩니다.
    * [SAP 참고 1869038 - SAP의 ReFs 파일 시스템 지원][1869038]
    * 저장소 공간 다이렉트에서의 볼륨 계획에 대한 자세한 내용은 [파일 시스템 선택][planning-volumes-s2d-choosing-filesystem] 장을 참조하세요.
    * 이 [MS **KB4025334** 누적 업데이트][kb4025334]를 설치해야 합니다.


* **DS-시리즈** 또는 **DSv2-시리즈** Azure VM 크기를 사용할 수 있습니다.

* 저장소 공간 다이렉트 디스크 동기화에 필요한 양질의 VM간 네트워크 성능을 실현하려면 **“높음” 이상의 네트워크 대역폭**을 갖는 VM 유형을 사용해야 합니다.
자세한 내용은 [DSv2-시리즈][dv2-series] 및 [DS-시리즈][ds-series] 사양을 참조하세요.

* **할당되지 않은 저장소 풀에 일부 용량을 남겨 두고 예약하는 것**이 **권장**됩니다. 이렇게 하면 드라이브에 장애가 발생하는 경우 “원본 위치”에 복구를 위한 공간이 확보되기 때문에 데이터 안전과 성능이 개선됩니다.

 자세한 내용은 [볼륨 크기 선택][choosing-the-size-of-volumes-s2d]을 참조하세요.


* SOFS Azure VM은 **자체 Azure 가용성 집합**에 배포되어야 합니다.

* SOFS 파일 공유 네트워크 이름(예: <SAPGlobalHostName>)에 대해 Azure Internal Load Balancer를 구성할 필요는 없습니다. SAP (A)SCS 인스턴스의 <(A)SCSVirtualHostname> 또는 DBMS에 대해 구성되기 때문입니다. SOFS는 모든 클러스터 노드로 부하를 분산하기 때문에 <SAPGlobalHostName>이 모든 클러스터 노드의 로컬 IP를 사용합니다.


> [!IMPORTANT]
>SAPGLOBALHOST를 가리키는 SAPMNT 파일 공유는 변경할 수 없습니다. SAP는 다른 공유 이름을 sapmnt로서 지원하지 않습니다.
>[SAP 참고 2492395 - 공유 이름 sapmnt를 변경할 수 있나요?][2492395]

### <a name="configuring-sap-ascs-instances-and-sofs-in-two-clusters"></a>2개의 클러스터에서 SAP (A)SCS 인스턴스와 SOFS 구성

하나의 클러스터에서 자체 SAP <SID> 클러스터 역할을 갖는 SAP (A)SCS 인스턴스를 배포할 수 있습니다. SOFS 파일 공유는 다른 클러스터 역할을 갖는 다른 클러스터에서 구성됩니다.

> [!IMPORTANT]
>이 시나리오에서 SAP (A)SCS 인스턴스가 UNC 경로 \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\.를 사용하여 SAP GLOBALHost를 액세스하도록 구성됩니다.
>

![그림 5: 두 클러스터에 배포된 SAP (A)SCS 인스턴스 및 SOFS][sap-ha-guide-figure-8007]

_**그림 5**: 두 클러스터에 배포된 SAP (A)SCS 인스턴스 및 SOFS_

> [!IMPORTANT]
>Azure 클라우드에서 SAP 및 SOFS 파일 공유에 사용되는 각 클러스터는 클러스터 VM이 기반 Azure 인프라에 분산 배치되도록 자체 Azure 가용성 집합에 배포되어야 합니다.
>

## <a name="generic-file-share-with-sios-as-cluster-shared-disks"></a>SIOS를 클러스터 공유 디스크로 사용하는 일반 파일 공유


> [!IMPORTANT]
>고가용성 파일 공유를 위해서는 SOFS를 사용하는 것이 좋습니다.
>
>그러나 고가용성 파일 공유에 **재해 복구**를 설정할 계획이라면 클러스터 공유 디스크 기술로 SISO와 일반 파일 공유를 사용해야 합니다.
>

일반 파이 공유는 고가용성 파일 공유를 달성할 수 있는 또 다른 옵션입니다.

여기서는 타사 SIOS 솔루션을 클러스터 공유 디스크로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [SAP (A)SCS 인스턴스에 Windows 장애 조치(failover) 클러스터 및 파일 공유를 사용하여 SAP HA를 위한 Azure 인프라 준비][sap-high-availability-infrastructure-wsfc-file-share]

* [SAP (A)SCS 인스턴스에 대한 Windows 장애 조치(failover) 클러스터 및 파일 공유에 SAP NetWeaver HA 설치][sap-high-availability-installation-wsfc-shared-disk]

* [Azure에서 UPD 저장소에 대해 2노드 저장소 공간 다이렉트 스케일아웃 파일 서버 배포][deploy-sofs-s2d-in-azure]

* [Windows Server 2016의 저장소 공간 다이렉트][s2d-in-win-2016]

* [심층 조사: 저장소 공간 다이렉트의 볼륨][deep-dive-volumes-in-s2d]
