---
title: Microsoft Azure의 Oracle 솔루션 | Microsoft Docs
description: Azure 인프라에서 전적으로 실행하거나 OCI(오라클 클라우드 인프라)와의 교차 클라우드 연결을 사용하는 등 Microsoft Azure에서 Oracle 응용 프로그램 및 솔루션을 배포하는 옵션에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: 8c266986ac795be48f3a8439afc5ae4752ff3ed7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263253"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure의 Oracle 응용 프로그램 및 솔루션 개요

이 문서에서는 Azure 인프라를 사용하여 Oracle 솔루션을 실행하는 기능을 소개합니다. 또한 Azure 마켓플레이스에서 사용 가능한 [Oracle VM 이미지에](oracle-vm-solutions.md) 대한 자세한 소개 및 [Azure와 OCI(Oracle 클라우드 인프라) 상호 연결](oracle-oci-overview.md)기능을 참조하십시오.

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 인프라의 오라클 데이터베이스

Azure 마켓플레이스에서 사용할 수 있는 Oracle Linux 이미지에서 Oracle 데이터베이스를 사용하여 Azure 인프라에서 오라클 데이터베이스를 실행합니다.

* 오라클 데이터베이스 12.1, 12.2 및 18.3 엔터프라이즈 에디션 

* 오라클 데이터베이스 12.1, 12.2 및 18.3 스탠다드 에디션 

Azure에서 사용할 수 있는 비 Oracle Linux 이미지에 Oracle Database를 설정하거나, Azure에서 처음부터 만든 사용자 지정 이미지에 대한 솔루션을 기반으로 하거나 온-프레미스 환경에서 사용자 지정 이미지를 업로드하도록 선택할 수도 있습니다.

오라클 자동 스토리지 관리(ASM)를 설치하여 여러 개의 연결된 디스크로 선택적으로 구성하고 데이터베이스 성능을 향상시킵니다.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>오라클 리눅스와 웹로직 서버의 애플리케이션

지원되는 Oracle 운영 체제에서 Azure에서 엔터프라이즈 응용 프로그램을 실행합니다. 다음 이미지는 Azure 마켓플레이스에서 사용할 수 있습니다.

* 오라클 웹로직 서버 12.1.2

* 깨지지 않는 엔터프라이즈 커널 (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 및 7.6을 가진 오라클 리눅스 

## <a name="high-availability-and-disaster-recovery-options"></a>고가용성 및 재해 복구 옵션

* [오라클 데이터 가드,](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956) [FSFO를 사용하여 활성 데이터 가드,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)Azure 인프라의 [샤딩](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) 또는 [골든 게이트를](https://www.oracle.com/middleware/technologies/goldengate.html) 구성하여 지역 내 고가용성 을 위한 [가용성 영역과](../../../availability-zones/az-overview.md) 함께 합니다. 또한 가용성 및 재해 복구를 추가하기 위해 여러 Azure 지역에 이러한 구성을 설정할 수도 있습니다.

* [Azure 사이트 복구를](../../../site-recovery/site-recovery-overview.md) 사용하여 Azure 및 온-프레미스 또는 물리적 서버의 Oracle Linux VM에 대한 재해 복구를 오케스트레이션하고 관리합니다. 

* [Azure VMWare 솔루션](https://docs.azure.cloudsimple.com/oracle-rac/) 또는 [플래시그리드 스카이클러스터를](https://www.flashgrid.io/oracle-rac-in-azure/)사용하여 Azure에서 Oracle 실제 응용 프로그램 클러스터(RAC)를 활성화합니다.

## <a name="backup-oracle-workloads"></a>백업 오라클 워크로드

* [Azure 백업을](https://docs.microsoft.com/azure/backup/backup-overview) 사용하여 Oracle VM 백업

* Oracle RMAN을 사용하여 Oracle 데이터베이스를 백업하고 선택적으로 [Azure Blob Fuse를](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) 사용하여 [매우 중복되는 Azure Blob Storage 계정을](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 탑재하고 RMAN 백업을 작성하여 복원력을 추가합니다.

## <a name="integration-of-azure-with-oci"></a>OCI와 Azure의 통합

OCI(오라클 클라우드 인프라)의 백엔드 데이터베이스에 연결된 Azure 인프라에서 오라클 애플리케이션을 실행합니다. 이 솔루션은 다음 기능을 사용합니다. 

* **클라우드 간 네트워킹** - Azure ExpressRoute와 Oracle FastConnect 간에 사용할 수 있는 직접 상호 연결을 사용하여 응용 프로그램과 데이터베이스 계층 간의 높은 대역폭, 개인 및 대기 시간 낮은 연결을 설정합니다.
* **통합 ID** - Azure AD와 Oracle IDCS 간에 페더레이션된 ID를 설정하여 솔루션에 대한 단일 ID 원본을 만듭니다. 단일 사인온을 사용하여 OCI 및 Azure 에서 리소스를 관리할 수 있습니다.

### <a name="deploy-oracle-applications-on-azure"></a>Azure에 Oracle 응용 프로그램 배포

테라폼 템플릿을 사용하여 Azure 인프라를 설정하고 Oracle 응용 프로그램을 설치합니다. 

> [!IMPORTANT]
> 오라클은 2020년 5월까지 Azure/Oracle Cloud 상호 연결 솔루션을 사용할 때 이러한 응용 프로그램을 Azure에서 실행하도록 인증합니다.

* E-비즈니스 스위트
* JD Edwards EnterpriseOne
* PeopleSoft
* 오라클 리테일 애플리케이션
* 오라클 하이페리온 재무 관리

또한 OCI 및 기타 Azure 서비스와 연결되는 사용자 지정 응용 프로그램을 Azure에 배포합니다.

### <a name="set-up-oracle-databases-in-oci"></a>OCI에서 오라클 데이터베이스 설정

Azure에서 실행되는 오라클 응용 프로그램과 함께 오라클 데이터베이스 클라우드 서비스(자율 데이터베이스, RAC, Exadata, DBaaS, 단일 노드)를 사용합니다. [OCI 데이터베이스 옵션에](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)대해 자세히 알아보십시오. 
 

## <a name="licensing"></a>라이선스

Azure에서 Oracle 응용 프로그램의 배포는 "사용자 고유의 라이선스 가져오기" 모델을 기반으로 합니다. 오라클 소프트웨어를 사용할 수 있는 적절한 라이선스가 있으며 오라클과 현재 지원 계약을 맺고 있다고 가정합니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. 오라클-Azure [FAQ를](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)참조하십시오.

## <a name="next-steps"></a>다음 단계

* Azure 인프라에 [Oracle VM 이미지를](oracle-vm-solutions.md) 배포하는 방법에 대해 자세히 알아봅니다.

* [Azure를 OCI와 상호 연결하는](oracle-oci-overview.md)방법에 대해 자세히 알아봅니다.

* Ignite 2019의 [Azure 개요 세션에서 오라클을](https://myignite.techcommunity.microsoft.com/sessions/82915) 확인하십시오. 
