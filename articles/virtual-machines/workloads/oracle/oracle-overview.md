---
title: Microsoft Azure의 Oracle 솔루션 | Microsoft Docs
description: Azure 인프라에서 실행 하거나 OCI (Oracle Cloud Infrastructure)와의 클라우드 간 연결을 사용 하 여 Microsoft Azure에 Oracle 응용 프로그램 및 솔루션을 배포 하는 옵션에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: 6ab41182d59f8c96edfca4854aad5f9a13f53436
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806612"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure의 Oracle 응용 프로그램 및 솔루션 개요

이 문서에서는 Azure 인프라를 사용 하 여 Oracle 솔루션을 실행 하는 기능을 소개 합니다. 또한 Azure Marketplace에서 사용 가능한 [ORACLE VM 이미지](oracle-vm-solutions.md) 에 대 한 자세한 소개와 [Azure를 OCI (Oracle Cloud Infrastructure)와 상호](oracle-oci-overview.md)연결 하는 미리 보기 기능을 참조 하세요.

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 인프라의 Oracle 데이터베이스

Azure Marketplace에서 제공 하는 Oracle Linux 이미지에 대 한 Oracle Database를 사용 하 여 Azure 인프라에서 Oracle 데이터베이스를 실행 합니다.

* Oracle Database 12.1, 12.2 및 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 및 18.3 Standard Edition 

Azure에서 사용할 수 있는 Oracle Linux 되지 않은 이미지에 대 한 Oracle Database를 설정 하거나, Azure에서 처음부터 만들거나 온-프레미스 환경에서 사용자 지정 이미지를 업로드 하는 사용자 지정 이미지를 기반으로 하도록 선택할 수도 있습니다.

필요에 따라 여러 연결 된 디스크를 사용 하 여 구성 하 고 Oracle ASM (자동 저장소 관리)을 설치 하 여 데이터베이스 성능을 향상 시킵니다.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux 및 WebLogic 서버의 응용 프로그램

지원 되는 Oracle 운영 체제에서 Azure의 엔터프라이즈 응용 프로그램을 실행 합니다. Azure Marketplace에서 사용할 수 있는 이미지는 다음과 같습니다.

* Oracle WebLogic 서버 12.1.2

* UEK (Unbreakable Enterprise Kernel) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 및 7.6을 사용 하 여 Oracle Linux 

## <a name="high-availability-and-disaster-recovery-options"></a>고가용성 및 재해 복구 옵션

* Azure 인프라에서 [분할](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) 또는 [골든 게이트](https://www.oracle.com/middleware/technologies/goldengate.html) 를 사용 하 여 [Oracle Data guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [활성 데이터 가드](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)를 지역 내 고가용성을 위한 [가용성 영역](../../../availability-zones/az-overview.md) 와 함께 구성 합니다. 또한 추가 가용성 및 재해 복구를 위해 여러 Azure 지역에서 이러한 구성을 설정할 수 있습니다.

* [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 를 사용 하 여 Azure 및 온-프레미스 또는 물리적 서버에서 Oracle Linux vm에 대 한 재해 복구를 오케스트레이션 하 고 관리 합니다. 

* Azure [VMWare 솔루션](https://docs.azure.cloudsimple.com/oracle-rac/) 또는 [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)를 사용 하 여 azure에서 Oracle RAC (실제 응용 프로그램 클러스터)를 사용 하도록 설정 합니다.

## <a name="backup-oracle-workloads"></a>Oracle 워크 로드 백업

* [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) 를 사용 하 여 Oracle vm 백업

* Oracle RMAN을 사용 하 여 Oracle Database를 백업 하 고, 필요에 따라 [Azure Blob 퓨즈](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) 를 사용 하 여 [매우 redudant Azure Blob Storage 계정을](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 탑재 하 고, 추가 복원 력을 위해 rman 백업을 작성 합니다.

## <a name="integration-of-azure-with-oci-preview"></a>Azure를 OCI와 통합 (미리 보기)

Azure 인프라에서 oracle 응용 프로그램을 실행 하 고, OCI (Oracle Cloud Infrastructure)의 백 엔드 데이터베이스에 연결 합니다. 이 솔루션은 다음과 같은 기능을 사용 합니다. 

* **클라우드 간 네트워킹** -Azure express 경로와 Oracle fastconnect 사이에서 사용 가능한 직접 상호 연결을 사용 하 여 응용 프로그램과 데이터베이스 계층 간에 고대역폭, 전용 및 짧은 대기 시간 연결을 설정 합니다.
* **통합 id** -솔루션에 대 한 단일 id 원본을 만들기 위해 Azure AD와 ORACLE idcs 간의 페더레이션 id를 설정 합니다. Single Sign-On를 사용 하도록 설정 하 여 OCI 및 Azure에서 리소스를 관리 합니다.

### <a name="deploy-oracle-applications-on-azure"></a>Azure에서 Oracle 응용 프로그램 배포

Terraform 템플릿을 사용 하 여 Azure 인프라를 설정 하 고, Oracle 응용 프로그램을 설치 하 고, 클라우드 간 구성에서 실행할 수 있도록 지원 합니다.

* E-비즈니스 제품군
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 소매 응용 프로그램
* Oracle Hyperion 금융 관리

또한 Azure에서 OCI 및 기타 Azure 서비스와 연결 되는 사용자 지정 응용 프로그램을 배포 합니다.

### <a name="set-up-oracle-databases-in-oci"></a>OCI에서 Oracle 데이터베이스 설정

Azure에서 실행 되는 Oracle 응용 프로그램과 함께 Oracle Database Cloud Services (자치 데이터베이스, RAC, Exadata, DBaaS, 단일 노드)를 사용 합니다. [OCI 데이터베이스 옵션](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)에 대해 자세히 알아보세요. 
 

## <a name="licensing"></a>라이선스

Azure에서 Oracle 응용 프로그램을 배포 하는 것은 "사용자의 라이선스 가져오기" 모델을 기반으로 합니다. Oracle 소프트웨어를 사용 하는 데 적절 한 라이선스가 있으며 Oracle과 현재 지원 계약이 준비 되어 있다고 가정 합니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. Oracle-Azure [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure 인프라에 [ORACLE VM 이미지](oracle-vm-solutions.md) 를 배포 하는 방법에 대해 자세히 알아보세요.

* [Azure와 OCI를 상호](oracle-oci-overview.md)연결 하는 방법에 대해 자세히 알아보세요.

* Ignite 2019에서 [Azure의 Oracle 개요 세션](https://myignite.techcommunity.microsoft.com/sessions/82915) 을 확인 하세요. 
