---
title: Microsoft Azure의 Oracle 솔루션 | Microsoft Docs
description: Oracle 응용 프로그램 및 Azure 인프라에서 완전히 실행 또는 클라우드 간 연결을 사용 하 여 클라우드 인프라 OCI (Oracle) 사용을 포함 하 여 Microsoft Azure에서 솔루션을 배포 하는 옵션에 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b5e40975fca491f289d949ee273d13053897fc6d
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743637"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Oracle 응용 프로그램 및 솔루션을 Azure에 대 한 개요

이 문서에서는 Azure 인프라를 사용 하 여 Oracle 솔루션을 실행 하는 기능을 소개 합니다. 사용 가능한 자세한 소개를 참조 하세요. [Oracle VM 이미지](oracle-vm-solutions.md) Azure Marketplace에서 미리 보기 기능 [Azure 사용 하 여 클라우드 인프라 OCI (Oracle)를 상호 연결](oracle-oci-overview.md)합니다.

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 인프라에서 oracle 데이터베이스

Azure Marketplace에서 사용할 수 있는 Linux 이미지를 사용 하 여 Azure 인프라에서 Oracle 데이터베이스를 실행 합니다.

* Oracle Database 12.1, 12.2, 및 18.3 Enterprise Edition 

* Oracle 데이터베이스 12.1, 12.2, 및 18.3 Standard Edition 

또한 Azure에서 처음부터 만들거나 온-프레미스 환경에서 사용자 지정 이미지를 업로드 하는 사용자 지정 이미지를 기준으로 솔루션을 수 있습니다.

필요에 따라 여러 연결 된 디스크를 사용 하 여 구성 하 고 Oracle 자동 저장소 관리 (ASM)를 설치 하 여 데이터베이스 성능을 향상 시킵니다.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux 및 WebLogic Server의 응용 프로그램

지원 되는 Oracle 운영 체제에서 Azure에서 엔터프라이즈 응용 프로그램을 실행 합니다. 다음 이미지는 Azure Marketplace에서 사용할 수 있습니다.

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 및 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>고가용성 및 재해 복구 옵션

* 와 함께에서 Azure 인프라에서 Oracle Data Guard, Active Data Guard, GoldenGate를 구성 [가용성 영역](../../../availability-zones/az-overview.md) 고가용성에 대 한 합니다.

* 사용 하 여 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 를 오케스트레이션 하 고 Azure와 온-프레미스 또는 물리적 서버에서 Oracle Linux Vm에 대 한 재해 복구를 관리 합니다. 

* 사용 하 여 Azure에서 Oracle Real Application Clusters (RAC)를 사용 하도록 설정 [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)합니다.

## <a name="integration-of-azure-with-oci-preview"></a>OCI (미리 보기)를 사용 하 여 Azure의 통합

백 엔드 데이터베이스에 클라우드 인프라 OCI (Oracle)에 연결 하는 Azure 인프라에서 Oracle 응용 프로그램을 실행 합니다. 이 솔루션에서는 다음과 같은 기능을 사용합니다. 

* **클라우드 간 네트워킹** -직접 사용 하 여 Azure ExpressRoute 및 Oracle FastConnect 응용 프로그램과 데이터베이스 계층 간의 높은 대역폭, private 및 지연율이 낮은 연결을 설정할 수 간에 사용 가능한 상호 연결 합니다.
* **Identity 통합** -Azure AD 간의 페더레이션된 id를 설정 하 고 솔루션에 대 한 단일 id 원본을 만들려면 Oracle IDCS 합니다. OCI 및 Azure 간에 리소스를 관리 하에서 single sign-on을 사용 하도록 설정 합니다.

### <a name="deploy-oracle-applications-on-azure"></a>Azure에서 Oracle 응용 프로그램 배포

Terraform 템플릿을 사용 하 여 Azure 인프라를 설정 하 고 Oracle 응용 프로그램 설치의 유효성을 검사 하 고 지원 되는 클라우드 간 구성에서에서 실행 하려면:

* E-business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 소매 응용 프로그램
* Oracle Hyperion 재무 관리

또한 OCI 및 기타 Azure 서비스와 연결 하는 Azure에서 사용자 지정 응용 프로그램을 배포 합니다.

### <a name="set-up-oracle-databases-in-oci"></a>Oracle OCI 데이터베이스 설정

Azure에서 실행 되는 Oracle 응용 프로그램과 함께에서 Oracle 데이터베이스 클라우드 서비스 (자치 데이터베이스, RAC, Exadata, DBaaS, 단일 노드)를 사용 합니다. 에 대해 자세히 알아보세요 [OCI 데이터베이스 옵션](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)합니다. 
 

## <a name="licensing"></a>라이선스

Azure에서 Oracle 응용 프로그램의 배포는 bring your own license 모델을 기반으로 합니다. Oracle 소프트웨어 및 oracle에 현재 지원 계약이 있는지 사용 하 여 적절 한 라이선스가 있으며 간주 됩니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. Oracle-Azure를 참조 하세요 [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)합니다.

## <a name="next-steps"></a>다음 단계

* 배포에 대해 자세히 알아보려면 [Oracle VM 이미지](oracle-vm-solutions.md) Azure 인프라에서.

* 하는 방법에 자세히 알아보려면 [OCI 사용 하 여 Azure를 상호 연결](oracle-oci-overview.md)합니다.