---
title: Get started with SAP on Azure VMs | Microsoft Docs
description: Learn about SAP solutions that run on virtual machines (VMs) in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9db368b634d79e66275b43196ed133ced331ce38
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328007"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Use Azure to host and run SAP workload scenarios

When you use Microsoft Azure, you can reliably run your mission-critical SAP workloads and scenarios on a scalable, compliant, and enterprise-proven platform. You get the scalability, flexibility, and cost savings of Azure. With the expanded partnership between Microsoft and SAP, you can run SAP applications across development and test and production scenarios in Azure and be fully supported. From SAP NetWeaver to SAP S/4HANA, SAP BI on Linux to Windows, and SAP HANA to SQL, we've got you covered.

Besides hosting SAP NetWeaver scenarios with the different DBMS on Azure, you can host other SAP workload scenarios, like SAP BI on Azure. 

The uniqueness of Azure for SAP HANA is an offer that sets Azure apart. To enable hosting more memory and CPU resource-demanding SAP scenarios that involve SAP HANA, Azure offers the use of customer-dedicated bare-metal hardware. Use this solution to run SAP HANA deployments that require up to 24 TB (120-TB scale-out) of memory for S/4HANA or other SAP HANA workload. 

Hosting SAP workload scenarios in Azure also can create requirements of identity integration and single sign-on. This situation can occur when you use Azure Active Directory (Azure AD) to connect different SAP components and SAP software-as-a-service (SaaS) or platform-as-a-service (PaaS) offers. A list of such integration and single sign-on scenarios with Azure AD and SAP entities is described and documented in the section "AAD SAP identity integration and single sign-on."

## <a name="changes-to-the-sap-workload-section"></a>Changes to the SAP workload section
Changes to documents in the SAP on Azure workload section are listed at the end of this article.


## <a name="sap-hana-on-azure-large-instances"></a>Azure의 SAP HANA(큰 인스턴스)

A series of documents leads you through SAP HANA on Azure (Large Instances), or for short, HANA Large Instances. For information on the following areas of HANA Large Instances, see:

- [Azure의 SAP HANA(대규모 인스턴스) 개요](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure의 SAP HANA(대규모 인스턴스) 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastructure and connectivity to SAP HANA on Azure (Large Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Install SAP HANA on Azure (Large Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [High availability and disaster recovery of SAP HANA on Azure (Large Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Troubleshoot and monitor SAP HANA on Azure (Large Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

다음 단계:

- Read [Overview and architecture of SAP HANA on Azure (Large Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA on Azure virtual machines
설명서의이 섹션에서는 SAP HANA의 다양한 측면을 다룹니다. As a prerequisite, you should be familiar with the principal services of Azure that provide elementary services of Azure IaaS. So, you need knowledge of Azure compute, storage, and networking. Many of these subjects are handled in the SAP NetWeaver-related [Azure planning guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

For information on HANA on Azure, see the following articles and their subarticles:

- [빠른 시작: Azure VM에서 단일 인스턴스 SAP HANA 수동 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure에서 SAP S/4HANA 또는 BW/4HANA 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure Virtual Machines의 SAP HANA 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [단일 Azure 지역 내 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure 지역 간 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 가상 머신에서 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Backup guide for SAP HANA on Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [파일 수준의 SAP HANA Azure Backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [스토리지 스냅샷에 기반한 SAP HANA 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver deployed on Azure virtual machines
This section lists planning and deployment documentation for SAP NetWeaver and Business One on Azure. The documentation focuses on the basics and the use of non-HANA databases with an SAP workload on Azure. The documents and articles for high availability are also the foundation for HANA high availability in Azure, such as:

- [SAP Business One on Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Azure에서 SAP IDES EHP7 SP3 for SAP ERP 6.0 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Run SAP NetWeaver on Microsoft Azure SUSE Linux VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Protect a multitier SAP NetWeaver application deployment by using Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure용 SAP LaMa 커넥터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

For information on non-HANA databases under an SAP workload on Azure, see:

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, Live Cache, and Content Server deployment on Azure VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

For information on SAP HANA databases on Azure, see the section "SAP HANA on Azure virtual machines."

For information on high availability of an SAP workload on Azure, see:

- [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

This document points to various other architecture and scenario documents. In later scenario documents, links to detailed technical documents that explain the deployment and configuration of the different high-availability methods are provided. The different documents that show how to establish and configure high availability for an SAP NetWeaver workload cover Linux and Windows operating systems.


For information on integration between Azure Active Directory (Azure AD) and SAP services and single sign-on, see:

- [자습서: SAP Cloud for Customer와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Cloud Platform과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP NetWeaver와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP Business ByDesign과 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [자습서: SAP HANA와 Azure Active Directory 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Your S/4HANA environment: Fiori Launchpad SAML single sign-on with Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

For information on integration of Azure services into SAP components, see:

- [Power BI Desktop에서 SAP HANA 사용](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 및 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop에서 SAP BW 커넥터 사용](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory는 SAP HANA 및 Business Warehouse 데이터 통합을 제공합니다.](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>변경 로그
- 11/21/2019: Changes in [SAP HANA scale-out with standby node on Azure VMs with Azure NetApp Files on SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) to simplify the configuration for NFS ID mapping and change the recommended primary network interface to simplify routing.
- 11/15/2019: Minor changes in [High availability for SAP NetWeaver on SUSE Linux Enterprise Server with Azure NetApp Files for SAP applications](high-availability-guide-suse-netapp-files.md) and [High availability for SAP NetWeaver on Red Hat Enterprise Linux with Azure NetApp Files for SAP applications](high-availability-guide-rhel-netapp-files.md) to clarify capacity pool size restrictions and remove statement that only NFSv3 version is supported.
- 11/12/2019: Release of [High availability for SAP NetWeaver on Windows with Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: Changes in [High availability of SAP HANA on Azure VMs on SUSE Linux Enterprise Server](sap-hana-high-availability.md), [Set up SAP HANA System Replication on Azure virtual machines (VMs)](sap-hana-high-availability-rhel.md), [Azure Virtual Machines high availability for SAP NetWeaver on SUSE Linux Enterprise Server for SAP applications](high-availability-guide-suse.md), [Azure Virtual Machines high availability for SAP NetWeaver on SUSE Linux Enterprise Server with Azure NetApp Files](high-availability-guide-suse-netapp-files.md), [Azure Virtual Machines high availability for SAP NetWeaver on Red Hat Enterprise Linux](high-availability-guide-rhel.md), [Azure Virtual Machines high availability for SAP NetWeaver on Red Hat Enterprise Linux  with Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [High availability for NFS on Azure VMs on SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS on Azure VMs on Red Hat Enterprise Linux for SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) to recommend Azure standard load balancer  
- 11/08/2019: Changes in [SAP workload planning and deployment checklist](sap-deployment-checklist.md) to clarify encryption recommendation  
- 11/04/2019: Changes in [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) to create the cluster directly with unicast configuration  
- 10/29/2019: Release of [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: Changes in [SAP HANA Azure virtual machine storage configurations](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) and [SAP HANA scale-out with standby node on Azure VMs with Azure NetApp Files on SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) to clarify NFS protocol for /hana/shared volume
- 10/22/2019: Change in [High availability for SAP NetWeaver on Azure VMs on SUSE Linux Enterprise Server for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [High availability for SAP NetWeaver on Azure VMs on SUSE Linux Enterprise Server with Azure NetApp Files for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [High availability for NFS on Azure VMs on SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [High availability of IBM Db2 LUW on Azure VMs on SUSE Linux Enterprise Server with Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), and [High availability of SAP HANA on Azure VMs on SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) for Azure Load-Balancer Detection Hardening
- Changes ANF section and header section in [SAP HANA Azure virtual machine storage configurations](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: Release of [SAP HANA scale-out with standby node on Azure VMs with Azure NetApp Files on SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: Fix broken links in [Backup and restore](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: Change the minimum recommended OS from SLES 12 SP3 to SLES 12 SP4 in [High availability of IBM Db2 LUW on Azure VMs on SUSE Linux Enterprise Server with Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: Changes to Ultra disk storage configurations and introduction of ANF in [SAP HANA Azure virtual machine storage configurations](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: Change in graphics of [Azure proximity placement groups for optimal network latency with SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) to get more clarity
- 10/01/2019: Change in [SAP HANA infrastructure configurations and operations on Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) to correct statements around highly available NFS share for /hana/shared. 
- 09/28/2019: Change in [Setting up Pacemaker on Red Hat Enterprise Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) to clarify SBD as a fencing mechanism is not supported on RHEL clusters  
- 09/17/2019: Change in NetWeaver Planning and Deployment Guide to unify terms around VM Extension for SAP  
- 08/22/2019: Changes in [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) to update the URLs for custom role creation  
- 08/16/2019: Changes in [Setting up Pacemaker on Red Hat Enterprise Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) to remind customers to update the actions in the custom role, if updating to the new version of the Azure fence agent  
- 08/15/2019: Changes in [SAP HANA Azure virtual machine storage configurations](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) to reflect General Availability of Ultra disk (formerly Ultra SSD)
- 08/01/2019: Changes to [Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) to integrate changes specifically for SLES 15 
- 07/23/2019: Changes in [Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a file share in Azure](sap-high-availability-guide-wsfc-file-share.md) to reflect support of Storage Space Direct by Azure Site Recovery Services
- 07/14/2019: Release of [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md)
- 07/11/2019: Changes in various documents covering HANA Large Instances to cover Revision 4 of HANA Large Instances
- 07/09/2019: Release of new guide for [IBM Db2 HADR in Red Hat Enterprise Server](high-availability-guide-rhel-ibm-db2-luw.md)
- 06/13/2019: Release of [High availability for SAP NetWeaver on Red Hat Enterprise Linux with Azure NetApp Files for SAP applications](high-availability-guide-rhel-netapp-files.md)


