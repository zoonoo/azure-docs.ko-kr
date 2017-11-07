---
title: "SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 아키텍처 및 시나리오 | Microsoft Docs"
description: "Azure Virtual Machines의 SAP NetWeaver에 대한 HA(고가용성) 아키텍처 및 시나리오"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 867fe2835418a48e4e616d8137ba9fa4182c8fb7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2017
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>SAP NetWeaver에 대한 고가용성 아키텍처 및 시나리오

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="definition-of-terminologies"></a>용어 정의

**HA(고가용성)**라는 용어는 **동일한** 데이터 센터 내의 중복성, 내결함성 또는 장애 조치(failover) 보호 구성 요소를 통해 IT 서비스의 비즈니스 연속성을 제공함으로써 IT 작업 중단을 최소화하는 일련의 기술과 관련되어 있습니다. 우리의 경우에는 단일 Azure 지역 내부에서의 고가용성이 고려됩니다.

**DR(재해 복구)** 또한 IT 서비스 중단을 최소화하고 복구를 수행하는 것을 목표로 하지만 수백 킬로미터 거리에 있는 **여러 다른** 데이터 센터 간에 발생하게 됩니다. 우리의 경우에는 동일한 지정학적 지역 내의 다양한 Azure 지역 간이나 고객이 설정한 Azure 지역 사이를 의미합니다.


## <a name="overview-of-high-availability"></a>고가용성 개요
Azure의 SAP 고가용성에 대한 논의는 다음과 같은 세 부분으로 구분할 수 있습니다.

* **Azure 인프라 고가용성** - 계산(VM), 네트워크, 저장소 등의 HA와 이러한 HA가 SAP 응용 프로그램 가용성 향상에 주는 이점

* **Azure 인프라 VM 다시 시작을 활용하여 SAP 응용 프로그램의 "고가용성" 확보**

  WSFC(Windows 서버 장애 조치(failover) 클러스터링) 또는 Linux의 Peacemaker 등의 기능을 사용하지 않기로 결정하는 경우 Azure VM 다시 시작 기능을 활용하여 Azure 물리적 서버 인프라 및 전반적인 기본 Azure 플랫폼의 예정되었거나 갑작스러운 가동 중지 시간으로부터 SAP 시스템을 보호합니다.


* **SAP 응용 프로그램 고가용성**

  완전한 SAP 시스템 고가용성을 달성하려면 다음과 같이 모든 중요 SAP 시스템 구성 요소를 보호해야 합니다.
  * 이중화 **SAP 응용 프로그램 서버** 및
  * 다음과 같은 고유의 구성 요소(예: **SPOF(단일 실패 지점)**)
    * **SAP (A)SCS 인스턴스** 및
    *  **DBMS**.


Azure의 SAP 고가용성은 온-프레미스 물리적 또는 가상 환경의 SAP 고가용성에 비해 몇 가지 차이점이 있습니다. 다음 [Microsoft Windows에서 VMware 및 Hyper-V를 사용하는 가상 환경의 SAP NetWeaver 고가용성 및 비즈니스 연속성][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] 문서에서는 Windows에서 가상화된 환경의 표준 SAP 고가용성 구성에 대해 설명합니다.

Windows의 경우와 마찬가지로 Linux용 sapinst 통합 SAP-HA 구성은 없습니다. Linux용 SAP HA 온-프레미스에 대한 자세한 내용은 [고가용성 파트너 정보][sap-ha-partner-information]에서 확인할 수 있습니다.

## <a name="azure-infrastructure-high-availability"></a>Azure 인프라 고가용성

### <a name="single-instance-virtual-machine-sla"></a>단일 인스턴스 가상 컴퓨터 SLA

현재 Premium Storage를 사용하는 단일 VM SLA는 99.9%입니다. 다양한 [Azure 서비스 수준 계약][azure-sla]의 제품을 빌드해 보면 단일 VM의 가용성이 어떻게 나타나는지 이해하는 데 도움이 됩니다.

이 계산은 한 달이 30일 또는 43200분이라는 기준에서 출발합니다. 따라서 가동 중지 시간 0.05%는 21.6분에 해당합니다. 일반적으로 다양한 서비스의 가용성은 다음과 같이 배가됩니다.

(가용성 서비스 #1/100) * (가용성 서비스 #2/100) * (가용성 서비스 #3/100) \*…

결과는 다음과 같습니다.

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 또는 전체 가용성 99.75%

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>동일한 가용성 집합의 여러 Virtual Machines 인스턴스
동일한 **가용성 집합**에 배포된 두 개 이상의 인스턴스가 있는 모든 Virtual Machines의 경우에는 최소 99.95%의 시간 동안 Virtual Machines 연결을 사용할 수 있도록 보장합니다.

두 대 이상의 VM이 동일한 가용성 집합에 포함된 경우 해당 가용성 집합의 각 가상 컴퓨터는 기본 Azure 플랫폼에 의해 **업데이트 도메인** 및 **장애 도메인**에 할당됩니다.

**장애 도메인**은 공통 전원과 네트워크 스위치를 공유하지 않는 다른 하드웨어에 VM이 배포되도록 보장합니다. 서버, 네트워크 스위치 또는 전원의 계획되지 않은 가동 중지 시 VM만 영향을 받습니다.

**업데이트 도메인**은 Azure 인프라의 계획된 유지 관리 동안 여러 VM이 동시에 다시 부팅되지 않고 한 번에 하나의 VM만 다시 부팅되도록 보장합니다.

자세한 내용은 [Azure에서 Windows 가상 컴퓨터의 가용성 관리][azure-virtual-machines-manage-availability]를 참조하세요.

가용성 집합은 다음 항목의 고가용성을 얻기 위해 사용됩니다.

* 이중화 SAP 응용 프로그램 서버  

* SAP (A)SCS 인스턴스 및 DBMS처럼 SPOF를 보호하는 노드(즉, VM)가 두 개 이상인 클러스터

### <a name="virtual-machine-vm-planned-and-unplanned-maintenance"></a>VM(가상 컴퓨터)의 계획된 유지 관리 및 계획되지 않은 유지 관리

가상 컴퓨터의 가용성에 영향을 줄 수 있는 두 가지 유형의 Azure 플랫폼 이벤트인 계획된 유지 관리와 계획되지 않은 유지 관리가 있습니다.

* **계획된 유지 관리** 이벤트는 가상 컴퓨터가 실행 중인 플랫폼 인프라의 전체적인 안정성, 성능 및 보안을 향상시키기 위해 Microsoft가 기본 Azure 플랫폼에 적용하는 주기적인 업데이트입니다.

* **계획되지 않은 유지 관리** 이벤트는 가상 컴퓨터의 기반이 되는 하드웨어 또는 물리적 인프라에 어떠한 식으로든지 오류가 있을 때 발생합니다. 여기에는 로컬 네트워크 오류, 로컬 디스크 오류 또는 기타 랙 수준의 오류가 포함될 수도 있습니다. 이러한 오류가 감지될 때 Azure 플랫폼은 가상 컴퓨터를 호스팅 중인 비정상 물리적 서버에서 정상 물리적 서버로 가상 컴퓨터를 자동으로 마이그레이션합니다. 이러한 이벤트는 흔치 않지만 가상 컴퓨터가 재부팅되도록 할 수도 있습니다.

자세한 내용은 [Azure에서 Windows 가상 컴퓨터의 가용성 관리][azure-virtual-machines-manage-availability]를 참조하세요.

### <a name="azure-storage-redundancy"></a>Azure 저장소 중복성
Microsoft Azure Storage 계정의 데이터는 항상 내구성 및 고가용성을 보증하도록 복제되며 일시적인 하드웨어 오류가 발생하는 경우에도 Azure Storage SLA를 충족합니다.

Azure Storage는 기본적으로 데이터 이미지 3개를 유지하므로 여러 Azure 디스크에 있는 RAID5 또는 RAID1은 필요하지 않습니다.

자세한 내용은 [Azure Storage 복제][azure-storage-redundancy]를 참조하세요.

### <a name="azure-managed-disks"></a>Azure Managed Disks
Managed Disks는 Azure Storage 계정에 저장된 VHD 대신 사용할 수 있는 Azure Resource Manager의 새로운 리소스 종류입니다. Managed Disks는 연결되어 있는 가상 컴퓨터의 가용성 집합에 맞게 자동으로 조정되므로, 가상 컴퓨터에서 실행되는 서비스와 가상 컴퓨터의 가용성을 높여 줍니다.
자세한 내용은 [Azure Managed Disks 개요][azure-storage-managed-disks-overview]를 참조하세요.

관리 디스크를 사용하면 가상 컴퓨터를 간편하고 배포 및 관리할 수 있으므로 사용하는 것이 좋습니다.
**SAP는 현재 프리미엄 Managed Disks만 지원합니다**. 자세한 내용은 SAP Note [1928533]을 참조하세요.

## <a name="utilizing-azure-infrastructure-ha-to-achieve-sap-application-higher-availability"></a>HA Azure 인프라를 활용하여 SAP 응용 프로그램의 "더 높은" 가용성 확보

WSFC(Windows 서버 장애 조치(failover) 클러스터링) 또는 Linux의 Peacemaker(현재 SLES 12 이상에서만 지원됨) 등의 기능을 사용하지 않기로 결정하는 경우 Azure VM 다시 시작 기능을 활용하여 Azure 물리적 서버 인프라 및 전반적인 기본 Azure 플랫폼의 예정되었거나 갑작스러운 가동 중지 시간으로부터 SAP 시스템을 보호합니다.

이 방법은 [Azure 인프라 VM 다시 시작을 활용하여 SAP 시스템의 "고가용성" 확보][sap-higher-availability] 문서에 자세히 설명되어 있습니다.

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Azure IaaS의 SAP 응용 프로그램 고가용성

완전한 SAP 시스템 고가용성을 달성하려면 다음과 같이 모든 중요 SAP 시스템 구성 요소를 보호해야 합니다.

* 이중화 **SAP 응용 프로그램 서버** 및

* 다음과 같은 고유의 구성 요소(예: **SPOF(단일 실패 지점)**)
  * **SAP (A)SCS 인스턴스** 및
  *  **DBMS**.

아래에서는 세 가지 중요 SAP 시스템 구성 요소의 고가용성을 확보하는 방법에 대해 자세히 살펴봅니다.

### <a name="high-availability-for-sap-application-servers"></a>SAP 응용 프로그램 서버의 고가용성

> 이 챕터는 둘 다에 적용됩니다.
>
> ![Windows][Logo_Windows] Windows 및 ![Linux][Logo_Linux] Linux
>

일반적으로 SAP 응용 프로그램 서버 및 대화 상자 인스턴스의 경우 특정 고가용성 솔루션이 필요하지 않습니다. 중복성으로 고가용성을 달성하고 다른 Azure Virtual Machines 인스턴스에서 여러 대화 상자 인스턴스를 구성합니다. 두 개의 Azure Virtual Machines 인스턴스에 2개 이상의 SAP 응용 프로그램 인스턴스가 설치되어 있어야 합니다.

![그림 1: 고가용성 SAP 응용 프로그램 서버][sap-ha-guide-figure-2000]

_**그림 1:** 고가용성 SAP 응용 프로그램 서버_

SAP 응용 프로그램 서버 인스턴스를 호스트하는 모든 가상 컴퓨터를 동일한 Azure 가용성 집합에 배치해야 합니다. Azure 가용성 집합은 다음을 확인합니다.

* 모든 가상 컴퓨터가 동일한 **업그레이드 도메인**에 속하는지 여부. 예를 들어 업그레이드 도메인은 가상 컴퓨터가 계획된 유지 관리 가동 중지 시간 동안 동시에 업데이트되지 않는지 확인합니다.
Azure 배율 단위 내의 다른 업그레이드 도메인 및 장애 도메인을 기반으로 하는 기본 기능은 이미 [업그레이드 도메인][planning-guide-3.2.2] 챕터에서 살펴보았습니다.

* 모든 가상 컴퓨터가 동일한 **장애 도메인**에 속하는지 여부. 예를 들어 장애 도메인은 어떤 단일 장애 지점도 모든 가상 컴퓨터의 가용성에 영향을 주지 않도록 가상 컴퓨터가 배포되어 있는지 확인합니다.

Azure 배율 단위 내에서 Azure 가용성 집합이 사용할 수 있는 장애 도메인 및 업그레이드 도메인 수는 제한되어 있습니다. 즉, 여러 VM을 가용성 집합 하나에 포함하면 최종적으로는 동일한 장애 도메인이나 업그레이드 도메인에 VM이 여러 개 포함됩니다.

전용 VM에서 일부 SAP 응용 프로그램 서버 인스턴스를 배포하고 업그레이드 도메인을 5개 받았다고 가정하면 결과적으로 다음과 같은 그림이 완성됩니다. 가용성 집합 내의 장애 및 업그레이드 도메인의 실제 최대 개수는 나중에 변경될 수 있습니다.

![그림 2: Azure 가용성 집합의 SAP 응용 프로그램 서버 고가용성][planning-guide-figure-3000]
_**그림 2:** Azure 가용성 집합의 SAP 응용 프로그램 서버 고가용성_ 자세한 내용은 [가상 컴퓨터의 가용성 관리][virtual-machines-manage-availability] 문서에서 확인할 수 있습니다.


Azure 가용성 집합은 SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현의 [Azure 가용성 집합][planning-guide-3.2.3] 챕터에 제공되었습니다.


**비관리 디스크에만 해당하는 설명:** Azure Storage 계정은 잠재적인 단일 실패 지점일 수 있으므로 둘 이상의 가상 컴퓨터가 배포될 둘 이상의 Azure Storage 계정이 있어야 합니다. 이상적인 설치에서는 SAP 대화 상자 인스턴스를 실행하는 각 가상 컴퓨터의 디스크를 다른 저장소 계정에 배포합니다.

> [!IMPORTANT]
>
> SAP HA 설치 시 Azure Managed Disks를 사용할 것을 강력하게 권장합니다. 연결되어 있는 가상 컴퓨터의 가용성 집합에 맞게 자동으로 조정되어 가상 컴퓨터에서 실행되는 서비스와 가상 컴퓨터의 가용성을 높여주기 때문입니다.  
>


### <a name="high-availability-architecture-for-the-sap-ascs-instance"></a>SAP (A)SCS 인스턴스의 고가용성 아키텍처

### <a name="high-availability-architecture-for-the-sap-ascs-instance-on-windows"></a>Windows에서 SAP (A)SCS 인스턴스의 고가용성 아키텍처


> ![Windows][Logo_Windows] Windows
>

**WSFC**(**Windows Server 장애 조치(failover) 클러스터링**) 솔루션을 사용하여 SAP (A)SCS 인스턴스를 보호할 수 있습니다. 솔루션의 두 가지 변형이 있습니다.

* **클러스터형 공유 디스크**를 사용하여 SAP (A)SCS 인스턴스 클러스터링

   클러스터형 공유 디스크를 사용하는 HA 아키텍처에 대한 자세한 내용은 [클러스터 공유 디스크를 사용하여 SAP (A)SCS 인스턴스를 Windows 장애 조치(failover) 클러스터에 클러스터링][sap-high-availability-guide-wsfc-shared-disk] 문서에서 찾을 수 있습니다.   

* **파일 공유**를 사용하여 SAP (A)SCS 인스턴스 클러스터링

  파일 공유를 사용하는 HA 아키텍처에 대한 자세한 내용은 [파일 공유를 사용하여 SAP (A)SCS 인스턴스를 Windows 장애 조치(failover) 클러스터에 클러스터링][sap-high-availability-guide-wsfc-file-share] 문서에서 찾을 수 있습니다.

### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Linux에서 SAP (A)SCS 인스턴스의 고가용성


> ![Linux][Logo_Linux] Linux
>

SUSE Linux Enterprise Server 클러스터 프레임워크를 사용한 SAP (A)SCS 인스턴스 클러스터링에 대한 자세한 내용은 [SAP 응용 프로그램용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성][sap-suse-ascs-ha] 문서에서 찾을 수 있습니다.

### <a name="sap-netweaver-multi-sid-configuration-for-clustered-sap-ascs-instance"></a>클러스터형 SAP (A)SCS 인스턴스에 대한 SAP NetWeaver 다중 SID 구성

> ![Windows][Logo_Windows] Windows
>
>현재 다중 SID는 WSFC(**Windows Server 장애 조치(failover) 클러스터**)에서만 지원됩니다. 다중 SID는 **파일 공유** 및 **공유 디스크**를 사용하여 지원됩니다.
>

다중 SID HA 아키텍처에 자세한 내용은 다음 문서에서 찾을 수 있습니다.

* [Windows Server 장애 조치(failover) 클러스터링 및 파일 공유에 대한 SAP (A)SCS 인스턴스 다중 SID 고가용성][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Windows Server 장애 조치(failover) 클러스터링 및 공유 디스크에 대한 SAP (A)SCS 인스턴스 다중 SID 고가용성][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>고가용성 DBMS 인스턴스

DBMS는 SAP 시스템의 단일 연락 지점이기도 합니다. 따라서 고가용성 솔루션을 사용하여 보호해야 합니다. 다음 그림은 Windows Server 장애 조치(failover) 클러스터링 및 Azure 내부 부하 분산 장치를 사용하여 Azure의 SQL Server Always On 고가용성 솔루션을 보여 줍니다. SQL Server Always On은 자체 DBMS 복제를 사용하여 DBMS 데이터 및 로그 파일을 복제합니다. 이 경우 전체 설정을 간소화하는 클러스터 공유 디스크가 필요하지 않습니다.

![그림 3: SQL Server Always On을 사용하는 고가용성 SAP DBMS 예제][sap-ha-guide-figure-2003]

_**그림 3:** SQL Server Always On을 사용하는 고가용성 SAP DBMS 예제_

Azure Resource Manager 배포 모델을 사용하는 Azure의 **SQL Server DBMS** 클러스터링에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Resource Manager를 사용하여 Azure Virtual Machines에서 수동으로 Always On 가용성 그룹 구성][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Azure에서 AlwaysOn 가용성 그룹에 대한 Azure 내부 부하 분산 장치 구성][virtual-machines-windows-portal-sql-alwayson-int-listener]

Azure Resource Manager 배포 모델을 사용하는 Azure의 **SAP HANA DBMS** 클러스터링에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]
