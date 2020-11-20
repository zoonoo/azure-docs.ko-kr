---
title: Azure에서 SAP BusinessObjects BI 플랫폼 배포 | Microsoft Docs
description: Azure에서 SAP BusinessObjects BI 플랫폼 계획, 배포 및 구성
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: ee7cc1c81bd35bf2b51c712c3a60a046435ee700
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951286"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Azure의 SAP BusinessObjects BI 플랫폼 계획 및 구현 가이드

## <a name="overview"></a>개요

이 가이드의 목적은 SAP BusinessObjects BI 플랫폼을 계획, 배포 및 구성 하기 위한 지침을 제공 하는 것입니다 (Azure의 SAP BOBI 플랫폼 이기도 함). 이 가이드는 SAP BOBI 플랫폼과 관련 된 일반적인 Azure 서비스 및 기능을 설명 하기 위한 것입니다. 이 가이드는 가능한 모든 구성 옵션의 완전 한 목록이 아닙니다. 일반적인 배포 시나리오에 공통적으로 적용 되는 솔루션을 다룹니다.

이 가이드는 표준 SAP BOBI 플랫폼 설치 및 관리 가이드, 운영 체제 또는 데이터베이스 설명서를 대체 하기 위한 것이 아닙니다.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Azure에서 SAP BusinessObjects BI 플랫폼 계획 및 구현

Microsoft Azure는 컴퓨팅, 저장소, 네트워킹 등의 다양 한 서비스를 제공 하 고 비즈니스에서 긴 조달 주기 없이 응용 프로그램을 빌드할 수 있도록 합니다. Azure VM (가상 머신)은 비즈니스 요구 사항에 따라 SAP NetWeaver 기반 응용 프로그램, SAP Hybris, SAP BusinessObjects BI 플랫폼과 같은 여러 SAP 응용 프로그램에 대해 주문형 및 확장 가능한 컴퓨팅 리소스를 배포할 수 있도록 지원 합니다. 또한 azure는 크로스-프레미스 연결을 지원 하므로 기업에서 Azure virtual machines를 온-프레미스 도메인, 사설 클라우드 및 SAP 시스템 환경에 통합할 수 있습니다.

이 문서에서는 Azure의 SAP BusinessObjects BI 플랫폼에 대 한 계획 및 구현 고려 사항에 대 한 지침을 제공 합니다. 이 문서에서는 sap BOBI의 설치 및 배포에 대 한 기본 리소스를 나타내는 sap 설치 설명서 및 SAP 정보를 보완 합니다.

### <a name="architecture-overview"></a>아키텍처 개요

SAP BusinessObjects BI 플랫폼은 단일 Azure 가상 컴퓨터에 존재할 수 있거나 다른 구성 요소를 실행 하는 여러 Azure Virtual Machines 클러스터로 확장할 수 있는 자체 포함 시스템입니다. SAP BOBI 플랫폼은 클라이언트 계층, 웹 계층, 관리 계층, 저장소 계층, 처리 계층 및 데이터 계층의 6 가지 개념적 계층으로 구성 됩니다. 각 계층에 대 한 자세한 내용은 [SAP BusinessObjects Business Intelligence Platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) help Portal의 관리자 가이드를 참조 하세요. 다음은 각 계층에 대 한 대략적인 정보입니다.

- **클라이언트 계층:** 여기에는 BI 플랫폼과 상호 작용 하 여 다양 한 종류의 보고, 분석 및 관리 기능을 제공 하는 모든 데스크톱 클라이언트 응용 프로그램이 포함 됩니다.
- **웹 계층:** JAVA 웹 응용 프로그램 서버에 배포 된 웹 응용 프로그램을 포함 합니다. 웹 응용 프로그램은 웹 브라우저를 통해 최종 사용자에 게 BI 플랫폼 기능을 제공 합니다.
- **관리 계층:** BI 플랫폼을 만드는 모든 구성 요소를 조정 하 고 제어 합니다. 여기에는 CMS (중앙 관리 서버)와 이벤트 서버 및 관련 서비스가 포함 됩니다.
- **저장소 계층:** 문서 및 보고서와 같은 파일을 처리 하는 일을 담당 합니다. 또한 사용자가 보고서에 액세스할 때 시스템 리소스를 저장 하기 위해 보고서 캐싱을 처리 합니다.
- **처리 계층:** 데이터를 분석 하 고 보고서 및 기타 출력 유형을 생성 합니다. 보고서 데이터를 포함 하는 데이터베이스에 액세스 하는 유일한 계층입니다.
- **데이터 계층:** CMS 시스템 데이터베이스를 호스트 하는 데이터베이스 서버와 감사 데이터 저장소로 구성 됩니다.

SAP BI 플랫폼은 하나 이상의 호스트에서 실행 되는 서버 컬렉션으로 구성 됩니다. 크기 조정, 비즈니스 요구 및 환경의 유형에 따라 올바른 배포 전략을 선택 하는 것이 중요 합니다. 개발 또는 테스트와 같은 소규모 설치의 경우 웹 응용 프로그램 서버, 데이터베이스 서버 및 모든 BI 플랫폼 서버에 단일 Azure 가상 컴퓨터를 사용할 수 있습니다. Azure에서 DBaaS (Database as a Service) 제품을 사용 하는 경우 데이터베이스 서버는 다른 구성 요소와 별도로 실행 됩니다. 중간 규모 및 대기업 설치의 경우 여러 Azure virtual machines에서 실행 되는 서버를 사용할 수 있습니다.

아래 그림에서는 Azure virtual machines에서 SAP BOBI 플랫폼의 대규모 배포 아키텍처를 보여 줍니다. 여기서 각 구성 요소는 배포 되 고 서비스 중단 시 장애 조치 (failover)를 수행할 수 있는 가용성 집합에 배치 됩니다.

![Azure의 SAP BusinessObjects BI 플랫폼 아키텍처](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>아키텍처 세부 정보

- 부하 분산 장치

  SAP BOBI 다중 인스턴스 배포에서 웹 응용 프로그램 서버 (또는 웹 계층)가 두 개 이상의 호스트에서 실행 되 고 있습니다. 사용자 부하를 웹 서버 간에 균등 하 게 배포 하기 위해 최종 사용자와 웹 서버 간에 부하 분산 장치를 사용할 수 있습니다. Azure에서 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 또는 [Azure 애플리케이션 게이트웨이](../../../application-gateway/overview.md) 를 사용 하 여 웹 서버에 대 한 트래픽을 관리할 수 있습니다.

- 웹 응용 프로그램 서버

  웹 서버는 CMC 및 BI 시작 Pad와 같은 SAP BOBI 플랫폼의 웹 응용 프로그램을 호스팅합니다. 웹 서버에 대 한 고가용성을 구현 하려면 중복성 및 부하 분산을 관리 하기 위해 두 개 이상의 웹 응용 프로그램 서버를 배포 해야 합니다. Azure에서 이러한 웹 응용 프로그램 서버는 가용성 집합 또는 가용성 영역에 배치 하 여 가용성을 높일 수 있습니다.

  Tomcat는 SAP BI 플랫폼용 기본 웹 응용 프로그램입니다. Tomcat에 대 한 고가용성을 구현 하려면 Azure에서 [정적 멤버 자격 인터셉터](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) 를 사용 하 여 세션 복제를 사용 하도록 설정 합니다. Tomcat 서비스가 중단 되는 경우에도 사용자가 SAP BI 웹 응용 프로그램에 액세스할 수 있도록 합니다.

  > [!Important]
  > 기본적으로 Tomcat는 Azure에서 지원 되지 않는 클러스터링에 멀티 캐스트 IP 및 포트를 사용 합니다 (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- BI 플랫폼 서버

  BI Platform 서버는 SAP BOBI 응용 프로그램의 일부인 모든 서비스 (관리 계층, 처리 계층 및 저장소 계층)를 포함 합니다. 웹 서버는 요청을 받으면 각 BI 플랫폼 서버 (특히 클러스터의 모든 CMS 서버)를 검색 하 고 해당 요청에 대 한 부하를 자동으로 분산 합니다. BI 플랫폼 호스트 중 하나에서 오류가 발생 하는 경우 웹 서버는 다른 호스트로 요청을 자동으로 보냅니다.

  BI 플랫폼에 대 한 고가용성 또는 중복성을 얻으려면 적어도 두 개의 Azure virtual machines에 응용 프로그램을 배포 해야 합니다. 크기 조정에 따라 더 많은 Azure virtual machines에서 실행 되도록 BI 플랫폼을 확장할 수 있습니다.

- FRS (파일 리포지토리 서버)

  파일 리포지토리 서버는 생성 된 모든 보고서 및 기타 BI 문서를 포함 합니다. 다중 인스턴스 배포에서 BI Platform 서버는 여러 가상 머신에서 실행 되며 각 VM은 이러한 보고서 및 기타 BI 문서에 액세스할 수 있어야 합니다. 따라서 filesystem은 모든 BI 플랫폼 서버에서 공유 해야 합니다.

  Azure에서 파일 리포지토리 서버에 대해 [Azure Premium 파일](../../../storage/files/storage-files-introduction.md) 또는 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 를 사용할 수 있습니다. 이러한 Azure 서비스는 모두 기본 제공 중복성을 제공 합니다.

  > [!Important]
  > Azure Files에 대 한 SMB 프로토콜은 일반적으로 사용할 수 있지만 Azure Files에 대 한 NFS 프로토콜 지원은 현재 미리 보기 상태입니다. 자세한 내용은 [현재 preview로 제공 되는 Azure Files에 대 한 NFS 4.1 지원](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) 을 참조 하세요.

- CMS & 감사 데이터베이스
  
  SAP BOBI 플랫폼에는 CMS 데이터베이스 라고 하는 시스템 데이터를 저장 하는 데이터베이스가 필요 합니다. 사용자, 서버, 폴더, 문서, 구성 및 인증 세부 정보와 같은 BI 플랫폼 정보를 저장 하는 데 사용 됩니다.

  Azure는 CMS 데이터베이스 및 감사 데이터베이스에 사용할 수 있는 [MySQL 데이터베이스](https://azure.microsoft.com/en-us/services/mysql/) 및 [azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) As a Service (dbaas) 제품을 제공 합니다. 이로 인해 고객은 PaaS를 제공 하기 때문에 데이터베이스의 작업, 가용성 및 유지 관리에 대해 걱정할 필요가 없습니다. 고객은 비즈니스 요구에 따라 CMS 및 감사 리포지토리에 대해 자신의 데이터베이스를 선택할 수도 있습니다.

## <a name="support-matrix"></a>지원 매트릭스

이 섹션에서는 Azure의 SAP BusinessObjects BI 플랫폼 버전, 운영 체제 및 데이터베이스와 같은 다양 한 SAP BOBI 구성 요소의 지원 가능성에 대해 설명 합니다.

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 플랫폼

Azure IaaS (Infrastructure as a Service)를 사용 하 여 Azure Compute에서 SAP BusinessObjects BI 플랫폼을 배포 하 고 구성할 수 있습니다. 다음 버전의 SAP BOBI 플랫폼을 지원 합니다.

- SAP BusinessObjects BI 플랫폼 4.3
- SAP BusinessObjects BI Platform 4.2 SP04 +
- SAP BusinessObjects BI Platform 4.1 SP05 +

SAP BI 플랫폼은 다른 운영 체제 및 데이터베이스에서 실행 됩니다. 운영 체제와 데이터베이스 버전 간 SAP BOBI 플랫폼의 지원 가능성은 SAP BOBI 용 [제품 가용성 매트릭스](https://apps.support.sap.com/sap/support/pam) 에서 찾을 수 있습니다.

### <a name="operating-system"></a>운영 체제

Azure는 SAP BusinessObjects BI 플랫폼 배포에 대해 다음과 같은 운영 체제를 지원 합니다.

- Microsoft Windows Server
- SLES(SUSE Linux Enterprise Server)
- RHEL(Red Hat Enterprise Linux)
- Oracle Linux (OL)

[SAP BUSINESSOBJECTS BI 플랫폼용 PAM (제품 가용성 매트릭스)](https://support.sap.com/pam) 에 나열 된 운영 체제 버전은 Azure 인프라에서 실행 하는 데 호환 되는 경우에만 지원 됩니다.

### <a name="databases"></a>데이터베이스

BI 플랫폼은 CMS 및 감사 데이터 저장소에 대해 데이터베이스를 요구 하며, 다음을 포함 하는 [SAP 제품 가용성 매트릭스](https://support.sap.com/pam) 에 나열 된 지원 되는 모든 데이터베이스에 설치할 수 있습니다.

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) (WINDOWS의 SAP Bobi 플랫폼에 대해서만 지원 되는 데이터베이스)

  안정적인 최신 Enterprise Edition SQL Server를 기반으로 하는 완전히 관리 되는 SQL Server 데이터베이스 엔진입니다. Azure SQL database는 사용자 개입 없이 업그레이드, 패치 및 모니터링과 같은 대부분의 데이터베이스 관리 기능을 처리 합니다. Azure SQL Database를 사용하면 Azure의 애플리케이션 및 솔루션을 위한 고가용성 고성능 데이터 스토리지 레이어를 만들 수 있습니다. 자세한 내용은 [Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) 설명서를 참조 하세요.

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (SAP PAM의 MySQL AB에 대해 설명한 것과 동일한 호환성 지침 준수)

  MySQL community edition에서 제공 하는 관계형 데이터베이스 서비스입니다. 완전히 관리 되는 DBaaS (Database as a Service) 제품으로 서, 예측 가능한 성능과 동적 확장성으로 중요 업무용 워크 로드를 처리할 수 있습니다. 기본 제공 되는 고가용성, 자동 백업, 소프트웨어 패치, 자동 오류 검색 및 최대 35 일 동안 지정 시간 복원 기능을 사용 하 여 작업 태스크를 크게 줄입니다. 자세한 내용은 [Azure Database for MySQL](../../../mysql/overview.md) 설명서를 참조 하세요.

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (버전 및 제한의 경우 SAP Note [2039619](https://launchpad.support.sap.com/#/notes/2039619)확인)

- MaxDB

이 문서에서는 **Azure Database for MySQL를 사용 하 여 Linux에서 Azure SQL Database 및 SAP Bobi 플랫폼** 을 **사용 하 여 WINDOWS에 Sap bobi 플랫폼** 을 배포 하는 지침을 설명 합니다. Azure에서 SAP BusinessObjects BI 플랫폼을 실행 하기 위한 권장 접근 방법 이기도 합니다.

## <a name="sizing"></a>크기 조정

크기 조정은 응용 프로그램을 효율적으로 실행 하기 위한 하드웨어 요구 사항을 결정 하는 프로세스입니다. SAP BOBI 플랫폼의 경우 [빠른 Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)이라는 sap 크기 조정 도구를 사용 하 여 크기를 조정 해야 합니다. 이 도구는 입력을 기반으로 하는 SAPS를 제공 하며,이는 SAP의 인증 된 Azure virtual machines 유형에 매핑되어야 합니다. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) 은 SAPS와 함께 지원 되는 sap 제품 및 Azure VM 유형 목록을 제공 합니다. 크기를 조정 하는 방법에 대 한 자세한 내용은 [SAP BI 크기 조정 가이드를 참조](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons)하세요.

SAP BOBI 플랫폼에 대 한 저장소 요구 사항이 있는 경우 Azure는 다양 한 유형의 [Managed Disks](../../managed-disks-overview.md)을 제공 합니다. SAP BOBI 설치 디렉터리의 경우 가상 머신에서 실행 되는 데이터베이스와 premium managed disk를 사용 하는 것이 좋습니다. [sap 워크 로드 용 DBMS 배포](dbms_guide_general.md)에 제공 된 지침을 따르세요.

Azure는 SAP BOBI 플랫폼 데이터 계층 [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database) (Windows에서 실행 되는 Bi 응용 프로그램) 및 [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql) (Linux 및 Windows에서 실행 되는 bi 응용 프로그램)에 대 한 두 개의 dbaas 제공을 지원 합니다. 따라서 크기 조정 결과에 따라 필요에 가장 잘 맞는 구매 모델을 선택할 수 있습니다.

> [!Tip]
> 빠른 크기 조정 참조는 SAP BOBI 플랫폼 데이터베이스 계층의 SAPS 결과를 Azure SAPS (Azure SQL Database 또는 Azure Database for MySQL)에 매핑하는 동안 800 개의 vCPU를 고려 합니다.

### <a name="sizing-models-for-azure-sql-database"></a>Azure SQL database에 대 한 모델 크기 조정

Azure SQL Database는 다음과 같은 세 가지 구매 모델을 제공 합니다.

- vCore 기반

  VCores 수, 메모리 양 및 저장소의 양과 속도를 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다. 이 모델은 유연성, 제어 및 투명성에 대 한 가치를 고객에 게 적합 합니다.

  VCore 모델에서 제공 되는 세 가지 [서비스 계층 옵션이](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) 있습니다. 여기에는 범용, 중요 비즈니스용 및 hyperscale이 포함 됩니다. 서비스 계층은 가용성 및 재해 복구와 관련 된 저장소 아키텍처, 공간, i/o 제한 및 비즈니스 연속성 옵션을 정의 합니다. 각 서비스 계층 옵션에 대 한 개략적인 정보는 다음과 같습니다.

  1. **범용** 서비스 계층은 비즈니스 작업에 가장 적합 합니다. 예산 지향적이 고 균형이 조정 되며 확장 가능한 계산 및 저장소 옵션을 제공 합니다. 자세한 내용은 [리소스 옵션 및 제한](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)을 참조 하세요.
  2. **중요 비즈니스용** 서비스 계층은 여러 개의 격리 된 복제본을 사용 하 여 비즈니스 응용 프로그램에 오류에 대 한 가장 높은 복원 력을 제공 하 고, 데이터베이스 복제본 별로 최고 i/o 성능을 제공 합니다. 자세한 내용은 [리소스 옵션 및 제한](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)을 참조 하세요.
  3. **대규모** 서비스 계층은 확장성이 뛰어난 저장소 및 읽기 확장 요구 사항이 있는 비즈니스 워크 로드에 가장 적합 합니다. 둘 이상의 격리 된 데이터베이스 복제본의 구성을 허용 하 여 오류에 대 한 더 높은 복원 력을 제공 합니다. 자세한 내용은 [리소스 옵션 및 제한](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)을 참조 하세요.

- DTU 기반

  DTU 기반 구매 모델은 가볍고 많은 데이터베이스 작업을 지원 하기 위해 3 개의 서비스 계층에서 계산, 메모리 및 i/o 리소스를 혼합 하 여 제공 합니다. 각 계층 내의 컴퓨팅 크기는 이러한 리소스의 다양한 조합을 제공하여 추가 스토리지 리소스를 추가할 수 있습니다. 간단 하 고 미리 구성 된 리소스 옵션을 원하는 고객에 게 가장 적합 합니다.

  DTU 기반 구매 모델의 [서비스 계층](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) 은 포함 된 저장소의 고정 된 크기, 백업 보존 기간 및 고정 가격이 포함 된 다양 한 계산 크기로 구분 됩니다.

- 서버를 사용하지 않음

  서버리스 모델은 워크로드 요구 사항에 따라 자동으로 컴퓨팅 크기를 조정하고 초당 사용된 컴퓨팅 양에 대한 요금을 청구합니다. 서버를 사용 하지 않는 계산 계층은 저장소가 청구 될 때 비활성 기간 동안 데이터베이스를 자동으로 일시 중지 하 고 작업이 반환 될 때 자동으로 데이터베이스를 다시 시작 합니다. 자세한 내용은 [리소스 옵션 및 제한](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)을 참조 하세요.
  
  시간이 지남에 따라 평균 계산 사용률이 낮은 간헐적이 고 예측할 수 없는 사용에 보다 적합 합니다. 따라서이 모델은 비프로덕션 SAP BOBI 배포에 사용할 수 있습니다.

> [!Note]
> SAP BOBI의 경우 비즈니스 요구 사항에 따라 vCore 기반 모델을 사용 하 고 범용 또는 중요 비즈니스용 서비스 계층을 선택 하는 것이 편리 합니다.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Azure database for MySQL에 대 한 모델 크기 조정

Azure Database for MySQL는 세 가지 가격 책정 계층으로 제공 됩니다. Vcores의 계산 용량, Vcores 당 메모리 및 날짜를 저장 하는 데 사용 되는 저장소 기술로 구분 됩니다. 다음은 옵션에 대 한 개략적인 정보 이며 다양 한 특성에 대 한 자세한 내용은 Azure Database for MySQL [가격 책정 계층](../../../mysql/concepts-pricing-tiers.md) 을 참조 하세요.

- Basic

  간단한 계산 및 i/o 성능이 필요한 대상 워크 로드에 사용 됩니다.

- 범용

  확장 가능한 i/o 처리량과 함께 부하가 분산 된 계산 및 메모리가 필요한 대부분의 비즈니스 워크 로드에 적합 합니다.

- 메모리 최적화

  빠른 트랜잭션 처리와 높은 동시성을 위해 메모리 내 성능이 필요한 고성능 데이터베이스 워크 로드.

> [!Note]
> SAP BOBI의 경우 비즈니스 워크 로드에 따라 범용 또는 메모리 액세스에 최적화 된 가격 책정 계층을 사용 하는 것이 편리 합니다.

## <a name="azure-resources"></a>Azure 리소스

### <a name="choosing-regions"></a>영역 선택

Azure 지역은 여러 Azure 서비스를 실행 하 고 호스트 하는 인프라를 포함 하는 데이터 센터의 컬렉션입니다. 이 인프라는 계산 노드나 저장소 노드 역할을 하는 많은 수의 노드를 포함 하거나 네트워크 기능을 실행 합니다. 모든 지역이 동일한 서비스를 제공 하는 것은 아닙니다.

SAP BI 플랫폼에는 특정 지역에서 사용할 수 없는 데이터 계층에 대 한 특정 VM 유형, 저장소 (예: Azure Files 또는 Azure NetApp Files) 또는 DBaaS (Database as a Service)가 필요할 수 있는 다양 한 구성 요소가 포함 되어 있습니다. VM 유형, Azure Storage 유형 또는 [지역 사이트에서 제공 하는 제품](https://azure.microsoft.com/en-us/global-infrastructure/services/) 의 다른 Azure 서비스에 대 한 정확한 정보를 확인할 수 있습니다. Azure에서 SAP 시스템을 이미 실행 중인 경우에는 지역이 확인 된 것일 수 있습니다. 이 경우 먼저 필요한 서비스를 해당 지역에서 사용할 수 있는지 조사 하 여 SAP BI 플랫폼의 아키텍처를 결정 해야 합니다.

### <a name="availability-zones"></a>가용성 영역

가용성 영역은 Azure 지역 내에서 물리적으로 떨어진 위치입니다. 각 가용성 영역은 독립 된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성 됩니다.

SAP BI 플랫폼의 각 계층에서 고가용성을 얻기 위해 고가용성 프레임 워크를 구현 하 여 가용성 영역에 Vm을 배포 하 여 Azure에서 최상의 SLA를 제공할 수 있습니다. Azure의 가상 컴퓨터 SLA에 대 한 최신 버전의 [가상 컴퓨터 sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 확인 하세요.

데이터 계층의 경우 Azure DBaaS (Database as a Service) 서비스는 기본적으로 고가용성 프레임 워크를 제공 합니다. 모든 추가 구성 요소를 구성 하지 않고도 계획 되거나 계획 되지 않은 중단 으로부터 데이터베이스 가동 중지 시간을 완화 하기 위해 지역 및 서비스 고유의 고가용성, 중복성 및 복원 력 기능을 선택 하기만 하면 됩니다. Azure에서 지원 되는 DBaaS에 대 한 SLA에 대 한 자세한 내용은 [Azure Database for MySQL 고가용성](../../../mysql/concepts-high-availability.md) 및 [Azure SQL Database에 대 한](../../../azure-sql/database/high-availability-sla.md)고가용성을 참조 하세요.

### <a name="availability-sets"></a>가용성 집합

가용성 집합은 배포 되는 VM (가상 컴퓨터) 리소스를 서로 격리 하는 논리적 그룹화 기능입니다. Azure는 가용성 집합 내에 배치한 Vm을 여러 물리적 서버, 계산 랙, 저장소 단위 및 네트워크 스위치에서 실행 되도록 합니다. 하드웨어 또는 소프트웨어 오류가 발생할 경우 Vm의 하위 집합에만 영향을 받으며 전체 솔루션은 작동 합니다. 따라서 가상 컴퓨터가 가용성 집합에 배치 되 면 Azure 패브릭 컨트롤러는 여러 [장애](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) 도메인 및 [업그레이드](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) 도메인에 vm을 분산 하 여 단일 장애 도메인 내에서 인프라 유지 관리 또는 실패로 인해 모든 vm에 액세스할 수 없도록 합니다.

SAP BI 플랫폼에는 다양 한 구성 요소가 포함 되어 있으며 아키텍처를 디자인 하는 동안이 구성 요소 각각의 중단에 대해 복원 력이 있는지 확인 해야 합니다. 가용성 집합 내의 각 구성 요소에 대 한 Azure 가상 컴퓨터를 배치 하 여 달성할 수 있습니다. 하나의 가용성 집합 내에서 서로 다른 VM 제품군의 Vm을 혼합할 때 특정 VM 유형을 이러한 가용성 집합에 포함 하지 못하게 하는 문제가 발생할 수 있습니다. 따라서 아키텍처 개요에 강조 표시 된 것 처럼 웹 응용 프로그램에 대 한 별도의 가용성 집합과 SAP BI 플랫폼용 BI 응용 프로그램이 있습니다.

또한 Azure 배율 단위 내에서 Azure 가용성 집합에 사용할 수 있는 업데이트 및 장애 도메인의 수는 유한 합니다. 따라서 단일 가용성 집합에 Vm을 계속 추가 하는 경우 두 개 이상의 Vm이 결과적으로 동일한 장애 또는 업데이트 도메인에서 종료 됩니다. 자세한 내용은 SAP 용 Azure virtual machines 계획 및 구현 문서에서 [Azure 가용성 집합](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) 섹션을 참조 하세요.

Azure 가용성 집합의 개념 및 가용성 집합이 장애 및 업그레이드 도메인과 관련 되는 방식을 이해 하려면 [가용성 관리](../../manage-availability.md) 문서를 참조 하세요.

> [!Important]
> Azure 가용성 영역과 Azure 가용성 집합의 개념은 함께 사용할 수 없습니다. 즉, 한 쌍 또는 여러 VM을 특정 가용성 영역 또는 Azure 가용성 집합에 배포할 수 있지만 두 개에 모두 배포할 수는 없다는 의미입니다.

### <a name="virtual-machines"></a>가상 머신

Azure 가상 머신은 Azure에 사용자 지정 이미지를 IaaS (Infrastructure as a Service) 인스턴스로 배포할 수 있게 해 주는 서비스 제품입니다. 이 플랫폼은 웹 애플리케이션 및 연결된 애플리케이션을 호스트, 확장 및 관리하기 위한 주문형 컴퓨팅 및 스토리지를 제공하여 애플리케이션 유지 관리 및 운영을 간소화합니다.

Azure는 모든 응용 프로그램 요구 사항에 맞는 다양 한 가상 컴퓨터를 제공 합니다. 하지만 SAP 워크 로드의 경우 Azure는 SAP 워크 로드에 적합 하 고 더 구체적으로 SAP HANA 워크 로드에 적합 한 다양 한 VM 제품군으로 선택을 축소 했습니다. 자세한 내용은 [Azure 배포에 대해 지원 되는 SAP 소프트웨어](sap-supported-product-on-azure.md)를 확인 하세요.

SAP BI 플랫폼 크기 조정에 따라 azure에서 SAP 제품에 대해 지원 되는 Azure Virtual Machine에 요구 사항을 매핑해야 합니다. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) 는 Windows 및 LINUX에서 sap 제품에 대해 지원 되는 Azure VM 유형을 나열 하는 좋은 출발점입니다. 또한 지원 되는 VM 유형을 선택 하는 것 외에도 특정 지역에서 해당 VM 유형을 사용할 수 있는지 여부도 확인 해야 합니다. [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/) 페이지에서 VM 유형의 가용성을 확인할 수 있습니다. 가격 책정 모델을 선택 하는 경우 [SAP 워크 로드에 대 한 Azure virtual machines](planning-guide.md#azure-virtual-machines-for-sap-workload) 를 참조할 수 있습니다.

### <a name="storage"></a>스토리지

Azure Storage는 고가용성, 보안, 내구성, 확장성 및 중복성을 제공 하는 저장소를 제공 하는 Azure로 관리 되는 클라우드 서비스입니다. 일부 저장소 유형은 SAP 시나리오에 대해 제한적으로 사용 됩니다. 하지만 몇 가지 Azure Storage 형식이 특정 SAP 워크 로드 시나리오에 적합 하거나 최적화 되어 있습니다. 자세한 내용은 sap에 적합 한 다양 한 저장소 옵션을 강조 표시 하는 [Sap 워크 로드 가이드에 대 한 Azure Storage 유형을](planning-guide-storage.md) 참조 하세요.

사용자에 게 사용할 수 있는 다른 저장소 유형이 있고 동일한 정보에 대 한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](../../disks-types.md)문서에서 읽을 수 있습니다. Azure Storage SAP BOBI 플랫폼은 다음 Azure Storage를 사용 하 여 응용 프로그램을 빌드합니다.

- Azure 관리 디스크

  Azure에서 관리 하는 블록 수준 저장소 볼륨입니다. Azure 가상 컴퓨터에 설치 된 경우 SAP BOBI 플랫폼 응용 프로그램 서버 및 데이터베이스용 디스크를 사용할 수 있습니다. 사용할 수 있는 다양 한 유형의 [Azure Managed Disks](../../managed-disks-overview.md) 있지만 SAP Bobi 플랫폼 응용 프로그램 및 데이터베이스에 대해 [Premium ssd](../../disks-types.md#premium-ssd) 를 사용 하는 것이 좋습니다.

  아래 예제에서 Premium Ssd는 BOBI 플랫폼 설치 디렉터리에 사용 됩니다. 가상 컴퓨터에 설치 된 데이터베이스의 경우 지침에 따라 데이터 및 로그 볼륨에 대해 managed disks를 사용할 수 있습니다. CMS 및 감사 데이터베이스는 일반적으로 작으며 다른 SAP OLTP/OLAP 데이터베이스의 저장소 성능 요구 사항과 동일 하지 않습니다.

- Azure Premium 파일 또는 Azure NetApp Files

  SAP BOBI 플랫폼에서 FRS (파일 리포지토리 서버)는 해당 시스템의 모든 응용 프로그램 서버에서 사용 하는 보고서, universe 및 연결과 같은 내용이 저장 된 디스크 디렉터리를 나타냅니다. [Azure Premium 파일](../../../storage/files/storage-files-introduction.md) 또는 [AZURE NETAPP FILES](../../../azure-netapp-files/azure-netapp-files-introduction.md) 저장소는 SAP BOBI 응용 프로그램 FRS 용 공유 파일 시스템으로 사용할 수 있습니다. 이 저장소 제공은 모든 지역에서 사용할 수 없으므로 지역 사이트 [에서 사용 가능한 제품](https://azure.microsoft.com/en-us/global-infrastructure/services/) 을 참조 하 여 최신 정보를 확인 하세요.

  해당 지역에서 서비스를 사용할 수 없는 경우 파일 시스템을 SAP BOBI 응용 프로그램에 공유할 수 있는 NFS 서버를 만들 수 있습니다. 하지만 고가용성도 고려해 야 합니다.

![Azure의 SAP BusinessObjects BI 플랫폼 저장소 레이아웃](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>네트워킹

SAP BOBI는 비즈니스 데이터를 포함 하지 않는 보고 및 분석 BI 플랫폼입니다. 따라서 시스템은 모든 데이터를 가져오고 사용자에 대 한 통찰력을 제공 하는 다른 데이터베이스 서버에 연결 됩니다. Azure는 온-프레미스 시스템에 연결 하는 것과 같은 SAP BI 플랫폼을 사용 하 여 실현할 수 있는 모든 시나리오와 다른 가상 네트워크의 시스템에 대 한 매핑을 가능 하 게 하는 네트워크 인프라를 제공 합니다. 자세한 내용은 [SAP 워크 로드에 대 한 Microsoft Azure 네트워킹](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)을 참조 하세요.

데이터베이스 서비스 제공을 위해 새로 만든 모든 데이터베이스 (Azure SQL Database 또는 Azure Database for MySQL)에는 모든 외부 연결을 차단 하는 방화벽이 있습니다. BI Platform 가상 머신에서 DBaaS 서비스에 대 한 액세스를 허용 하려면, DBaaS 서버에 액세스할 수 있도록 하나 이상의 서버 수준 방화벽 규칙을 지정 해야 합니다. 자세한 내용은 Azure SQL Database에 대 한 [방화벽 규칙](../../../mysql/concepts-firewall-rules.md) Azure Database for MySQL 및 [네트워크 액세스 제어](../../../azure-sql/database/network-access-controls-overview.md) 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Linux에서 SAP BusinessObjects BI 플랫폼 배포](businessobjects-deployment-guide-linux.md)
- [SAP용 Azure Virtual Machines 계획 및 구현](planning-guide.md)
- [SAP용 Azure Virtual Machines 배포](deployment-guide.md)
- [SAP용 Azure Virtual Machines DBMS 배포](dbms-guide.md)