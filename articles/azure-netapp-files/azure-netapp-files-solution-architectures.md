---
title: Azure NetApp 파일을 사용하는 솔루션 아키텍처 | 마이크로 소프트 문서
description: Azure NetApp 파일을 사용하는 솔루션 아키텍처에 대한 모범 사례에 대한 참조를 제공합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: b-juche
ms.openlocfilehash: 8eae528c965e599e7adfb546a09b0d5879e7c54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369518"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Azure NetApp Files를 사용하는 솔루션 아키텍처
이 문서에서는 Azure NetApp 파일 사용에 대한 솔루션 아키텍처를 이해하는 데 도움이 되는 모범 사례에 대한 참조를 제공합니다.  

## <a name="azure-high-performance-computing-hpc-solutions"></a>Azure 고성능 컴퓨팅(HPC) 솔루션

* [Azure에서 저장소 시뮬레이션 소프트웨어 실행](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)
* [Azure 일괄 처리 및 Azure NetApp 파일로 MPI 워크로드 실행](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)

## <a name="azure-kubernetes-service-aks-solutions"></a>Azure Kubernetes 서비스(AKS) 솔루션

* [AKS와 Azure 넷앱 파일 통합](https://github.com/andyzhangx/demo/tree/master/linux/nfs)
* [Azure NetApp 파일을 Azure Kubernetes 서비스와 통합](https://docs.microsoft.com/azure/aks/azure-netapp-files)

## <a name="oracle-database-solutions"></a>오라클 데이터베이스 솔루션

* [Azure NetApp 파일을 사용하는 Azure 배포 모범 사례 가이드에 대한 오라클](https://www.netapp.com/us/media/tr-4780.pdf)
* [오라클 VM 이미지 및 Microsoft Azure에 대한 배포: 공유 저장소 구성 옵션](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)

## <a name="sap-application-solutions"></a>SAP 애플리케이션 솔루션 

* [Azure NetApp 파일을 사용하여 Microsoft Azure의 SAP 응용 프로그램](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SAP 응용 프로그램에 대 한 Azure NetApp 파일 사용 SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 SAP NetWeaver에 대 한 높은 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [SAP 응용 프로그램에 대한 Azure NetApp 파일이 있는 Red Hat 엔터프라이즈 Linux의 Azure VM에서 SAP NetWeaver용 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [SAP 응용 프로그램 다중 SID 가이드에 대한 레드 햇 엔터프라이즈 리눅스에서 Azure VM에 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [SAP 응용 프로그램에 대한 Azure NetApp 파일(SMB)이 있는 Windows의 Azure VM에서 SAP NetWeaver용 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [SAP HANA SUSE Linux 엔터프라이즈 서버의 Azure NetApp 파일이 있는 Azure VM에서 대기 노드로 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [레드햇 엔터프라이즈 리눅스에서 Azure NetApp 파일이 있는 Azure VM에서 대기 노드를 사용하는 SAP HANA 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
* [Azure NetApp 파일 - 몇 초 만에 SAP HANA 백업](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp 파일 - 스냅샷 백업에서 HANA 데이터베이스 복원](https://blog.netapp.com/azure-netapp-files-backup-sap-hana/)
* [Azure NetApp 파일 - SAP HANA클라우드 동기화로 백업 오프로딩](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Azure NetApp 파일을 사용하여 SAP HANA 시스템 복사 속도 향상](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [클라우드 볼륨 ONTAP 및 Azure NetApp 파일: SAP HANA 시스템 마이그레이션이 간편해졌습니다.](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/) 

## <a name="global-file-caching-solutions"></a>글로벌 파일 캐싱 솔루션

* [Azure NetApp 파일 및 Talon FAST를 통해 전 세계적으로 분산된 엔터프라이즈 파일 공유™](https://www.talonstorage.com/products/azure-netapp-files)

## <a name="windows-virtual-desktopvdi-solutions"></a>윈도우 가상 데스크톱/VDI 솔루션

* [Azure NetApp 파일을 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 만들기](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)

