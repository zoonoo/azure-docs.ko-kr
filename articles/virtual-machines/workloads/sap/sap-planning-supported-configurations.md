---
title: 'Azure에서 SAP: Azure VM을 사용하면 지원되는 시나리오'
description: SAP 워크로드를 통해 지원되는 Azure 가상 머신 시나리오
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137631"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure 가상 시스템 지원 시나리오에서 SAP 워크로드
Azure에서 SAP NetWeaver, `Hybris` Business one 또는 S/4HANA 시스템 아키텍처를 설계하면 확장 가능하고 효율적이며 가용성이 높은 배포에 사용할 수 있는 다양한 아키텍처및 도구가 많이 있습니다. 사용되는 운영 체제 또는 DBMS에 따라 다르지만 제한사항이 있습니다. 또한 온-프레미스에서 지원되는 모든 시나리오가 Azure에서 동일한 방식으로 지원되는 것은 아닙니다. 이 문서에서는 Azure VM을 단독으로 사용하여 지원되는 비가용성 구성 및 고가용성 구성 및 아키텍처를 안내합니다. [HANA 대형 인스턴스에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)지원되는 시나리오의 경우 [HANA 대형 인스턴스에 대해 지원되는 아티클 시나리오를](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)확인합니다. 


## <a name="2-tier-configuration"></a>2계층 구성
SAP 2 계층 구성은 동일한 서버 또는 VM 장치에서 실행되는 SAP DBMS 및 응용 프로그램 계층의 결합된 계층으로 구성된 것으로 간주됩니다. 두 번째 계층은 사용자 인터페이스 계층으로 간주됩니다. 2계층 구성의 경우 DBMS 및 SAP 응용 프로그램 계층은 Azure VM의 리소스를 공유합니다. 따라서 리소스를 위해 경쟁하지 않는 방식으로 다른 구성 요소를 구성해야 합니다. 또한 VM의 리소스를 초과 구독하지 않도록 주의해야 합니다. 이러한 구성은 관련된 다른 Azure 구성 요소의 [Azure 서비스 수준 계약을](https://azure.microsoft.com/support/legal/sla/) 벗어나는 고가용성을 제공하지 않습니다.

이러한 구성의 그래픽 표현은 다음과 같습니다.

![간단한 2계층 구성](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

이러한 구성은 프로덕션 및 비프로덕션 사례를 위해 SQL Server, Oracle, Db2, maxDB 및 SAP ASE의 DBMS 시스템을 위한 Windows, Red Hat, SUSE 및 Oracle Linux에서 지원됩니다. SAP HANA를 DBMS로 사용하면 이러한 유형의 구성이 비프로덕션 사례에만 지원됩니다. 여기에는 [Azure HANA 대형 인스턴스의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 배포 사례도 포함됩니다.
Azure에서 지원되는 모든 OS/DBMS 조합에 대해 이러한 유형의 구성이 지원됩니다. 그러나 DBMS 및 SAP 구성 요소가 메모리 및 CPU 리소스에 대해 경쟁하지 않고 사용 가능한 실제 리소스를 초과하는 방식으로 DBMS 및 SAP 구성 요소의 구성을 설정해야 합니다. 이 작업은 DBMS가 할당할 수 있는 메모리를 제한하여 수행해야 합니다. 또한 응용 프로그램 인스턴스에서 SAP 확장 메모리를 제한해야 합니다. 또한 구성 요소가 CPU 리소스를 최대화하지 않도록 전체 VM의 CPU 사용량을 모니터링해야 합니다. 

> [!NOTE]
> 프로덕션 SAP 시스템의 경우 이 문서의 후반부에서 설명한 대로 추가 고가용성 및 최종 재해 복구 구성을 권장합니다.


## <a name="3-tier-configuration"></a>3계층 구성
이러한 구성에서는 SAP 응용 프로그램 계층과 DBMS 계층을 다른 VM으로 구분합니다. 일반적으로 대규모 시스템과 SAP 응용 프로그램 계층의 리소스에 대해 보다 유연해야 하는 이유에서 그렇게 합니다. 가장 간단한 설정에서는 관련된 다른 Azure 구성 요소의 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 을 초과하는 고가용성이 없습니다. 

그래픽 표현은 다음과 같습니다.

![간단한 2계층 구성](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

이러한 유형의 구성은 SQL Server, 오라클, Db2, SAP HANA, maxDB 및 프로덕션 및 비프로덕션 사례의 DBMS 시스템에 대한 Windows, Red Hat, SUSE 및 오라클 Linux에서 지원됩니다. Azure [HANA 대형 인스턴스에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)대한 기본 배포 구성입니다. 단순화를 위해 SAP 응용 프로그램 계층에서 SAP 중앙 서비스 및 SAP 대화 상자 인스턴스를 구분하지 않았습니다. 이 간단한 3계층 구성에서는 SAP 중앙 서비스에 대한 고가용성 보호가 없습니다.

> [!NOTE]
> 프로덕션 SAP 시스템의 경우 이 문서의 후반부에서 설명한 대로 추가 고가용성 및 최종 재해 복구 구성을 권장합니다.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>VM 또는 HANA 대형 인스턴스 단위당 여러 DBMS 인스턴스
이 구성 유형에서는 Azure VM 또는 HANA 대형 인스턴스 단위당 여러 DBMS 인스턴스를 호스트합니다. 유지 관리해야 할 운영 체제가 줄어들고 비용을 절감할 수 있습니다. 다른 동기는 여러 DBMS 인스턴스 간에 더 큰 VM 또는 HANA 대형 인스턴스 단위의 리소스를 공유하여 더 많은 유연성과 효율성을 가질 수 있습니다. 지금까지 이러한 구성은 대부분 비프로덕션 시스템에 대해 표시되었습니다.

다음과 같은 구성은 다음과 같습니다.

![하나의 단위로 여러 DBMS 인스턴스](./media/sap-planning-supported-configurations/multiple-database-instances.png)

이러한 유형의 DBMS 배포는 다음에 대해 지원됩니다.

- Windows의 SQL Server
- IBM Db2. 문서에서 세부 정보 찾기 [여러 인스턴스 (리눅스, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- 오라클용. 자세한 내용은 [SAP 지원 노트 #1778431](https://launchpad.support.sap.com/#/notes/1778431) 및 관련 SAP 노트를 참조하십시오.
- SAP HANA의 경우 한 VM에서 여러 인스턴스가 이 배포 메서드 MCOS를 호출합니다. 자세한 내용은 SAP 문서 [하나의 호스트에 여러 SAP HANA 시스템 (MCOS)]을 참조하십시오(https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f18.html)

한 호스트에서 여러 데이터베이스 인스턴스를 실행하면 다른 인스턴스가 리소스에 대해 경쟁하지 않고 VM의 물리적 리소스 제한을 초과해야 합니다. VM을 공유하는 인스턴스의 모든 사용자가 할당할 수 있는 메모리를 제한해야 하는 메모리의 경우 특히 그렇습니다. 다른 데이터베이스 인스턴스가 활용할 수 있는 CPU 리소스에도 마찬가지일 수 있습니다. 언급된 모든 DBMS에는 인스턴스 수준에서 메모리 할당 및 CPU 리소스를 제한할 수 있는 구성이 있습니다.
Azure VM에 대한 이러한 구성을 지원하기 위해 다른 인스턴스에서 관리하는 데이터베이스의 데이터 및 로그/다시 관리 로그 파일에 사용되는 디스크 또는 볼륨이 분리되어 있어야 합니다. 또는 다른 DBMS 인스턴스에서 관리하는 데이터베이스의 데이터 또는 로그/다시 재생 로그 파일은 동일한 디스크 또는 볼륨을 공유하도록 되어 있지 않습니다. 

HANA 대형 인스턴스에 대한 디스크 구성은 구성되어 제공되며 [HANA 대형 인스턴스에 대한 지원되는 시나리오에서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos)자세히 설명합니다. 

> [!NOTE]
> 프로덕션 SAP 시스템의 경우 이 문서의 후반부에서 설명한 대로 추가 고가용성 및 최종 재해 복구 구성을 권장합니다. DBMS 인스턴스가 여러 개인 VM은 이 문서의 후반부에서 설명하는 고가용성 구성에서는 지원되지 않습니다.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>하나의 VM에서 여러 SAP 대화 상자 인스턴스
많은 경우에, 여러 대화 상자 인스턴스는 베어 메탈 서버 또는 개인 클라우드에서 실행 되는 VM에 배포 있어. 이러한 구성의 이유는 특정 SAP 대화 상자 인스턴스를 특정 워크로드, 비즈니스 기능 또는 워크로드 유형에 맞게 조정하기 위한 것이었습니다. 이러한 인스턴스를 별도의 VM으로 격리하지 않는 이유는 운영 체제 유지 관리 및 작업의 노력때문이었습니다. 또는 VM의 호스트 또는 운영자가 운영 및 관리된 VM당 월별 요금을 요구하는 경우 의 경우 비용이 많이 듭니다. Azure에서는 Windows, Red Hat, SUSE 및 Oracle Linux의 운영 체제에서 프로덕션 및 비프로덕션 목적으로 지원되는 단일 VM 내에서 여러 SAP 대화 상자 인스턴스를 호스팅하는 시나리오입니다. Windows 및 최신 Linux 커널에서 사용할 수 있는 SAP 커널 매개 변수 PHYS_MEMSIZE 여러 SAP 응용 프로그램 서버 인스턴스가 단일 VM에서 실행되는 경우 설정해야 합니다. 또한 SAP 확장 메모리의 자동 증가가 구현되는 Windows와 같은 운영 체제에서 SAP 확장 메모리의 확장을 제한하는 것이 좋습니다. 이 작업은 SAP 프로필 매개 `em/max_size_MB`변수로 수행할 수 있습니다.

Azure VM 내에서 여러 SAP 대화 상자 인스턴스가 실행되는 3계층 구성에서는 다음과 같이 될 수 있습니다.

![하나의 단위로 여러 DBMS 인스턴스](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

단순화를 위해 SAP 응용 프로그램 계층에서 SAP 중앙 서비스 및 SAP 대화 상자 인스턴스를 구분하지 않았습니다. 이 간단한 3계층 구성에서는 SAP 중앙 서비스에 대한 고가용성 보호가 없습니다. 프로덕션 시스템의 경우 SAP 중앙 서비스를 보호되지 않은 상태로 두는 것이 좋습니다. SAP 중앙 인스턴스와 이러한 다중 SID 구성의 고가용성을 둘러싼 소위 다중 SID 구성에 대한 자세한 내용은 이 문서의 이후 섹션을 참조하십시오.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>SAP DBMS 계층을 위한 고가용성 보호
SAP 프로덕션 시스템을 배포하려면 핫 대기 유형의 고가용성 구성을 고려해야 합니다. 특히 전체 성능과 확장성을 다시 얻기 전에 데이터를 메모리에 로드해야 하는 SAP HANA의 경우 Azure 서비스 복구는 고가용성을 위한 이상적인 척도가 아닙니다. 

일반적으로 Microsoft는 docs.microsoft.com SAP 워크로드 섹션에 설명된 고가용성 구성 및 소프트웨어 패키지만 지원합니다. [#1928533](https://launchpad.support.sap.com/#/notes/1928533)SAP 노트에서 동일한 문을 읽을 수 있습니다. Microsoft는 SAP 워크로드와 함께 Microsoft에서 문서화하지 않은 다른 고가용성 타사 소프트웨어 프레임워크에 대한 지원을 제공하지 않습니다. 이러한 경우 고가용성 프레임워크의 타사 공급업체는 고객이 지원 프로세스에 참여해야 하는 고가용성 구성을 지원하는 지원 당사자입니다. 예외는 이 문서에서 언급될 것입니다. 

일반적으로 Microsoft는 Azure VM 또는 HANA 대형 인스턴스 단위에서 제한된 고가용성 구성 집합을 지원합니다. HANA 대형 인스턴스의 지원되는 시나리오에 대 한 문서 지원 된 [시나리오를 읽어보시고 큰 인스턴스](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario).

Azure VM의 경우 DBMS 수준에서 다음과 같은 고가용성 구성이 지원됩니다.

- SUSE와 레드 햇에 리눅스 페이스 메이커를 기반으로 SAP HANA 시스템 복제. 자세한 문서를 참조하십시오.
    - [SUSE Linux Enterprise Server의 Azure VM에 있는 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Red Hat Enterprise Linux의 Azure VM에 있는 SAP HANA의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- SAP HANA는 SUSE 및 Red Hat의 [Azure NetApp 파일을](https://azure.microsoft.com/services/netapp/) 사용하여 n+m 구성을 확장합니다. 자세한 내용은 다음 문서에 나와 있습니다.
    - [SUSE Linux 엔터프라이즈 서버에서 Azure NetApp 파일을 사용하여 Azure VM에서 대기 노드가 있는 SAP HANA 확장 시스템 배포}](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Red Hat 엔터프라이즈 Linux에서 Azure NetApp 파일을 사용하여 Azure VM에서 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- Windows 확장 파일 서비스를 기반으로 하는 SQL Server 장애 조치 클러스터입니다. 프로덕션 시스템에 대한 권장 사항은 클러스터링 대신 SQL Server Always On을 사용하는 것입니다. SQL Server Always On은 별도의 저장소를 사용하여 더 나은 가용성을 제공합니다. 자세한 내용은 이 문서에서 설명합니다. 
    - [Azure 가상 컴퓨터에서 SQL Server 장애 조치 클러스터 인스턴스 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- SQL Server Always On은 Azure의 SQL Server용 Windows 운영 체제에서 지원됩니다. Azure의 프로덕션 SQL Server 인스턴스에 대한 기본 권장 사항입니다. 자세한 내용은 이 문서에 설명되어 있습니다.
    - [Azure Virtual Machines의 SQL Server Always On 가용성 그룹 소개](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
    - [다른 하위 지역의 Azure Virtual Machines에서 Always On 가용성 그룹 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)
    - [Azure에서 Always On 가용성 그룹에 대한 부하 분산 장치 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)
- 윈도우와 오라클 리눅스에 대한 오라클 데이터 가드. 오라클 리눅스에 대한 자세한 내용은이 문서에서 찾을 수 있습니다 :
    - [Azure Linux 가상 머신에서 Oracle Data Guard 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- 맥박 조정기사용 수세 및 RHEL에 대한 IBM Db2 HADR 및 RHEL 상세 설명서는 다음과 같습니다.
    - [페이스 메이커와 SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 IBM Db2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Red Hat Enterprise Linux Server의 Azure VM에서 IBM DB2 LUW의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- SAP ASE 및 SAP maxDB 구성은 다음 문서에 자세히 설명되어 있습니다.
    - [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [Azure VM에서 SAP MaxDB, liveCache 및 Content Server 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- HANA 대형 인스턴스 고가용성 시나리오는 다음과 같습니다.
    - [고가용성을 위해 STONITH를 갖춘 HANA 대형 인스턴스- HSR에 대한 지원 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [HANA 대형 인스턴스지원 시나리오 - 호스트 자동 장애 조치(1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> 위에서 설명한 시나리오 중 어느 것도 하나의 VM에서 여러 DBMS 인스턴스의 구성을 지원합니다. 각 경우에 평균은 VM당 하나의 데이터베이스 인스턴스만 배포할 수 있으며 설명된 고가용성 방법으로 보호될 수 있습니다. 동일한 Windows 또는 Pacemaker 장애 조치 클러스터에서 여러 DBMS 인스턴스를 보호하는 것은 이 시점에서 **지원되지 않습니다.** 또한 오라클 데이터 가드는 VM 배포 사례당 단일 인스턴스에 대해서만 지원됩니다. 

다양한 데이터베이스 시스템을 사용하면 하나의 DBMS 인스턴스에서 여러 데이터베이스를 호스트할 수 있습니다. SAP HANA의 경우와 마찬가지로 여러 데이터베이스를 여러 데이터베이스 컨테이너(MDC)에서 호스팅할 수 있습니다. 이러한 다중 데이터베이스 구성이 하나의 장애 조치 클러스터 리소스 내에서 작동하는 경우 이러한 구성이 지원됩니다. 지원되지 않는 구성은 여러 클러스터 리소스가 필요한 경우입니다. 하나의 SQL Server 인스턴스에서 여러 SQL Server 가용성 그룹을 정의하는 구성에 대해


![DBMS HA 구성](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

DBMS 에 따라 Azure 로드 밸런서와 같은 구성 요소는 솔루션 아키텍처의 일부로 필요할 수도 있습니다. 

특히 maxDB의 경우 저장소 구성이 달라야 합니다. maxDB에서 데이터와 로그 파일은 고가용성 구성을 위해 공유 저장소에 위치해야 합니다. maxDB의 경우에만 공유 저장소가 고가용성으로 지원됩니다. 다른 모든 DBMS의 경우 노드당 별도의 저장소 스택은 지원되는 디스크 구성만입니다.

다른 고가용성 프레임워크가 있는 것으로 알려져 있으며 Microsoft Azure에서도 실행되는 것으로 알려져 있습니다. 그러나 Microsoft는 이러한 프레임워크를 테스트하지 않았습니다. 이러한 프레임워크를 사용하여 고가용성 구성을 구축하려면 해당 소프트웨어 공급자와 협력하여 다음을 수행해야 합니다.

- 배포 아키텍처 개발
- 아키텍처 배포
- 아키텍처 지원

> [!IMPORTANT]
> Microsoft Azure Marketplace는 Azure 네이티브 저장소 위에 저장소 솔루션을 제공하는 다양한 소프트 어플라이언스를 제공합니다. 이러한 소프트 어플라이언스는 이론적으로 대기 노드가 필요한 SAP HANA 스케일 아웃 배포에 사용할 수 있는 NFS 공유를 만드는 데 사용할 수 있습니다. 여러 가지 이유로 인해 Microsoft 및 Azure의 SAP에서 DBMS 배포에 대해 이러한 저장소 소프트 어플라이언스가 지원되지 않습니다. SMB 주식에 대한 DBMS 배포는 이 시점에서 전혀 지원되지 않습니다. NFS 주식에 DBMS배포는 [Azure NetApp 파일의](https://azure.microsoft.com/services/netapp/)NFS 4.1 주식으로 제한됩니다.


## <a name="high-availability-for-sap-central-service"></a>SAP 중앙 서비스를 위한 고가용성
SAP 중앙 서비스는 SAP 구성의 두 번째 단일 실패 지점입니다. 따라서 이러한 중앙 서비스 프로세스도 보호해야 합니다. SAP 워크로드에 대해 지원되고 문서화된 오퍼는 다음과 같습니다.

- sapmnt 및 전역 전송 디렉터리용 Windows 확장 파일 서비스를 사용하는 Windows 장애 조치 클러스터 서버입니다. 자세한 내용은 이 문서에 설명되어 있습니다.
    - [Azure에서 파일 공유를 사용하여 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스 클러스터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- Sapmnt 및 글로벌 전송 디렉터리용 [Azure NetApp 파일을](https://azure.microsoft.com/services/netapp/) 기반으로 SMB 공유를 사용하는 Windows 장애 조치 클러스터 서버입니다. 자세한 내용은 문서에 나열되어 있습니다.
    - [SAP 응용 프로그램에 대한 Azure NetApp 파일(SMB)이 있는 Windows의 Azure VM에서 SAP NetWeaver용 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- SIOS를 `Datakeeper`기반으로 하는 Windows 장애 조치 클러스터 서버. Microsoft에서 문서화했지만 이 솔루션을 사용할 때 SIOS 지원에 참여할 수 있도록 SIOS와의 지원 관계가 필요합니다. 자세한 내용은 이 문서에 설명되어 있습니다.
    - [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS를 위한 SAP HA용 Azure 인프라 준비](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- 두 개의 SUSE VM을 사용하고 파일 복제를 위해 가용성이 `drdb` 높은 NFS 공유를 만드는 SUSE 운영 체제의 Pacemaker. 자세한 내용은 문서에 설명되어 있습니다.
    - [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [Azure NetApp 파일에서](https://azure.microsoft.com/services/netapp/)제공하는 NFS 공유를 활용하는 페이스 메이커 SUSE 운영 체제 . 자세한 내용은
    - [SAP 응용 프로그램에 대 한 Azure NetApp 파일 사용 SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 SAP NetWeaver에 대 한 높은 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- 클러스터에서 호스팅되는 NFS 공유가 있는 `glusterfs` Red Hat 운영 체제의 페이스메이커입니다. 자세한 내용은 문서에서 찾을 수 있습니다.
    - [Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`SAP 넷위버에 대한 레드 햇 엔터프라이즈 리눅스에 Azure VM에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- [Azure NetApp 파일에서](https://azure.microsoft.com/services/netapp/)호스팅되는 NFS 공유가 있는 Red Hat 운영 체제의 페이스메이커. 자세한 내용은 이 문서에 설명되어 있습니다.
    - [SAP 응용 프로그램에 대 한 Azure NetApp 파일 과 레드 햇 엔터프라이즈 리눅스에 SAP NetWeaver에 대 한 Azure 가상 머신 높은 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

나열된 솔루션 중 `Datakeeper` 제품을 지원하고 문제가 발생시 SIOS와 직접 협력하려면 SIOS와의 지원 관계가 필요합니다. Windows, Red Hat 및/또는 SUSE OS에 라이선스를 부여한 방식에 따라 나열된 고가용성 구성을 완전히 지원하려면 OS 공급자와 지원 계약을 맺어야 할 수도 있습니다.

구성은 다음과 같이 표시될 수도 있습니다.

![DBMS 및 ASCS HA 구성](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

그래픽의 오른쪽에는 고가용성 SAP 중앙 서비스가 표시됩니다. 문제가 발생했을 때 장애 조치(failover) 클러스터 프레임워크로 보호되는 SAP Central 서비스를 보유하는 것 외에도 가용성이 높은 NFS 또는 SMB 공유 또는 Sapmnt 및 전역 전송 디렉터리에 대한 Windows 공유 디스크가 필요합니다. 단일 VM의 존재와 는 별개로 사용할 수 있습니다. Windows 장애 조치 클러스터 서버 및 Pacemaker와 같은 추가 솔루션 중 일부는 Azure 로드 밸런서가 정상 노드로 트래픽을 직접 또는 다시 리디렉션하도록 요구할 것입니다.

표시된 목록에서 오라클 Linux 운영 체제에 대한 언급은 없습니다. 오라클 리눅스는 클러스터 프레임워크로 페이스메이커를 지원하지 않습니다. 오라클 Linux에 SAP 시스템을 배포하고 오라클 Linux를 위한 고가용성 프레임워크가 필요한 경우 타사 공급업체와 협력해야 합니다. 공급업체 중 하나는 Azure에서 SAP에서 지원하는 Linux용 보호 제품군을 갖춘 SIOS입니다. 자세한 내용은 SAP 노트 #1662610 - 자세한 내용은 [Linux용 SIOS 보호 제품군에 대한 지원 세부 정보를](https://launchpad.support.sap.com/#/notes/1662610) 참조하십시오.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>위에 나열된 SAP 중앙 서비스 시나리오에서 지원되는 스토리지
Azure 저장소 유형의 하위 집합만 SAP 중앙 서비스 클러스터 시나리오에서 사용 품질에 대한 고품질의 가용성을 제공하는 NFS 또는 SMB 공유를 지원되는 저장소 유형 목록으로 제공하기 때문에

- Windows 확장 파일 서버가 있는 Windows 장애 조치 클러스터 서버는 Azure NetApp 파일을 제외한 모든 네이티브 Azure 저장소 유형에 배포할 수 있습니다. 그러나 처리량 및 IOPS에서 우수한 서비스 수준 계약으로 인해 프리미엄 저장소를 활용하는 것이 좋습니다.
- Azure NetApp 파일에 SMB가 있는 Windows 장애 조치 클러스터 서버는 Azure NetApp 파일에서 지원됩니다. Azure File 서비스의 SMB 공유는 이 시점에서 **지원되지 않습니다.**
- SIOS를 `Datakeeper` 기반으로 하는 Windows 공유 디스크가 있는 Windows 장애 조치 클러스터 서버는 Azure NetApp 파일을 제외한 모든 네이티브 Azure 저장소 유형에 배포할 수 있습니다. 그러나 처리량 및 IOPS에서 우수한 서비스 수준 계약으로 인해 프리미엄 저장소를 활용하는 것이 좋습니다.
- Azure NetApp 파일에서 NFS 공유를 사용하는 SUSE 또는 Red Hat Pacemaker는 Azure NetApp 파일에서 지원됩니다. 
- 두 VM 사이의 `drdb` 구성을 사용하는 SUSE Pacemaker는 Azure NetApp 파일을 제외한 네이티브 Azure 저장소 형식을 사용하여 지원됩니다. 그러나 처리량 및 IOPS에서 우수한 서비스 수준 계약으로 인해 프리미엄 저장소를 활용하는 것이 좋습니다.
- NFS 공유를 제공하는 데 사용하는 `glusterfs` Red Hat Pacemaker는 Azure NetApp 파일을 제외한 네이티브 Azure 저장소 형식을 사용하여 지원됩니다. 그러나 처리량 및 IOPS에서 우수한 서비스 수준 계약으로 인해 프리미엄 저장소를 활용하는 것이 좋습니다.

> [!IMPORTANT]
> Microsoft Azure Marketplace는 Azure 네이티브 저장소 위에 저장소 솔루션을 제공하는 다양한 소프트 어플라이언스를 제공합니다. 이러한 소프트 어플라이언스는 이론적으로 장애 조치 클러스터된 SAP 중앙 서비스에서도 사용할 수 있는 NFS 또는 SMB 공유를 만드는 데 사용할 수 있습니다. 이러한 솔루션은 Microsoft에서 SAP 워크로드에 대해 직접 지원되지 않습니다. 이러한 솔루션을 사용하여 NFS 또는 SMB 공유를 만들기로 결정한 경우 스토리지 소프트 어플라이언스에서 소프트웨어를 소유한 타사에서 SAP 중앙 서비스 구성에 대한 지원을 제공해야 합니다.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>다중 SID SAP 중앙 서비스 장애 조치 클러스터
대규모 SAP 환경에서 필요한 VM 수를 줄이기 위해 SAP를 사용하면 장애 조치 클러스터 구성에서 여러 다른 SAP 시스템의 SAP 중앙 서비스 인스턴스를 실행할 수 있습니다. NetWeaver 또는 S/4HANA 프로덕션 시스템이 30개 이상 있는 경우를 상상해 보십시오. 다중 SID 클러스터링이 없으면 이러한 구성에는 30개 이상의 Windows 또는 Pacemaker 장애 조치 클러스터 구성에서 60개 이상의 VM이 필요합니다. 필요한 DBMS 장애 조치 클러스터 외에. 장애 조치 클러스터 구성에서 두 노드에 여러 SAP 중앙 서비스를 배포하면 VM 수를 크게 줄일 수 있습니다. 그러나 단일 두 노드 클러스터 구성에 여러 SAP Central 서비스 인스턴스를 배포하는 데도 몇 가지 단점이 있습니다. 클러스터 구성의 단일 VM과 관련된 문제는 여러 SAP 시스템에 적용됩니다. 클러스터 구성에서 실행되는 게스트 OS에 대한 유지 관리는 여러 프로덕션 SAP 시스템이 영향을 받므로 더 많은 조정이 필요합니다. SAP LaMa와 같은 도구는 시스템 복제 프로세스에서 다중 SID 클러스터링을 지원하지 않습니다.

Azure에서 ENSA1 및 ENSA2를 사용할 수 있는 Windows 운영 체제에 대해 다중 SID 클러스터 구성이 지원됩니다. 이전 Enqueue 복제 서비스 아키텍처(ENSA1)와 하나의 다중 SID 클러스터에서 새 아키텍처(ENSA2)를 결합하지 않는 것이 좋습니다. 이러한 아키텍처에 대한 자세한 내용은 문서에 설명되어 있습니다.

- [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

SUSE의 경우 Pacemaker를 기반으로 하는 다중 SID 클러스터도 지원됩니다. 지금까지 구성은 다음을 위해 지원됩니다.

- 최대 5개의 SAP ASCS/SCS 인스턴스
- 이전 큐 복제 서버 얼음 아키텍처 (ENSA1)
- 두 개의 노드 페이스메이커 클러스터 구성

구성은 [SAP 응용 프로그램 다중 SID 가이드에 대 한 SUSE 리눅스 엔터프라이즈 서버에서 Azure VM에 SAP NetWeaver에 대 한 고가용성에](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) 문서화 되어 있습니다.

Enqueue 복제 서버가 있는 다중 SID 클러스터는 다음과 같습니다.

![DBMS 및 ASCS HA 구성](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA 스케일 아웃 시나리오
SAP HANA 확장 시나리오는 [SAP HANA 하드웨어 디렉토리에](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)나열된 HANA 인증 Azure VM의 하위 집합에 대해 지원됩니다. '클러스터링' 열에 '예'로 표시된 모든 VM은 OLAP 또는 S/4HANA 스케일 아웃에 사용할 수 있습니다. 대기가 없는 구성은 다음과 같은 Azure 저장소 유형에서 지원됩니다. 

- /hana/로그 볼륨에 대한 Azure 쓰기 가속기를 포함한 Azure 프리미엄 저장소
- [Ultra disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

대기 노드가 있는 OLAP 또는 S/4HANA에 대한 SAP HANA 확장 구성은 Azure NetApp 파일에서 호스팅되는 NFS 공유와 함께 독점적으로 지원됩니다.

대기 노드유무에 관계없이 정확한 스토리지 구성에 대한 자세한 내용은 다음 문서를 확인하십시오.

- [SAP HANA Azure 가상 머신 스토리지 구성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [SUSE Linux 엔터프라이즈 서버에서 Azure NetApp 파일을 사용하여 Azure VM에서 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Red Hat 엔터프라이즈 Linux에서 Azure NetApp 파일을 사용하여 Azure VM에서 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [SAP 지원 노트 #2080991](https://launchpad.support.sap.com/#/notes/2080991)

HANA 대규모 인스턴스 지원 HANA 스케일 아웃 구성에 대한 자세한 내용은 다음 설명서를 적용합니다.

- [대기를 통해 HANA 대규모 인스턴스 확장지원 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [대기 없이 HANA 대규모 인스턴스 확장에 대한 지원되는 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>재해 복구 시나리오
지원되는 다양한 재해 복구 시나리오가 있습니다. 재해 아키텍처를 그리드에서 벗어나는 전체 Azure 영역을 보정해야 하는 아키텍처로 정의합니다. 즉, 재해 복구 대상이 SAP 환경을 실행하기 위해 다른 Azure 영역이어야 합니다. DBMS 계층과 DBMS가 아닌 계층에서 메서드와 구성을 분리합니다. 

### <a name="dbms-layer"></a>DBMS 레이어
DBMS 계층의 경우 Always On, Oracle 데이터 가드, Db2 HADR, SAP ASE 상시 켜짐 또는 HANA 시스템 복제와 같은 DBMS 기본 복제 메커니즘을 사용하는 구성이 지원됩니다. 이러한 경우 복제 스트림은 단일 Azure 지역 내에 배포되는 일반적인 고가용성 시나리오와 마찬가지로 동기가 아닌 비동기적이야 합니다. 이러한 지원되는 DBMS 재해 복구 구성의 일반적인 예는 [Azure 지역 전체의 SAP HANA 가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions)문서에 설명되어 있습니다. 이 섹션의 두 번째 그래픽에서는 HANA가 있는 시나리오를 예로 들어 설명합니다. SAP 응용 프로그램에 지원되는 기본 데이터베이스는 모두 이러한 시나리오에서 배포할 수 있습니다.

VM이 전체 워크로드 트래픽을 경험하지 않으므로 더 작은 VM을 재해 복구 지역의 대상 인스턴스로 사용할 수 있습니다. 이렇게 하면 다음 사항을 염두에 두어야 합니다.

- VM 유형이 작을수록 더 작은 VM보다 많은 디스크가 연결되지 않습니다.
- VM이 작을수록 네트워크 및 스토리지 처리량이 줄어듭니다.
- VM 제품군 간의 크기 조정은 하나의 Azure 가용성 집합에서 서로 다른 VM을 수집하거나 M-시리즈 제품군과 Mv2 VM 제품군 간에 다시 크기 조정이 발생하는 경우 문제가 될 수 있습니다.
- 데이터베이스 인스턴스에 대한 CPU 및 메모리 소비는 최소한의 지연으로 변경 스트림을 수신할 수 있고 충분한 CPU 및 메모리 리소스로 데이터에 최소한의 지연으로 이러한 변경 내용을 적용할 수 있습니다.  

다양한 VM 크기의 제한 사항에 대한 자세한 내용은 [여기에서](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 확인할 수 있습니다. 

DR 대상을 배포하는 또 다른 지원되는 방법은 비프로덕션 SAP 인스턴스의 비프로덕션 DBMS 인스턴스를 실행하는 VM에 두 번째 DBMS 인스턴스를 설치하는 것입니다. DR 시나리오에서 주 인스턴스로 작동해야 하는 특정 대상 인스턴스에 대해 메모리, CPU 리소스, 네트워크 대역폭 및 저장소 대역폭이 필요한 지 파악해야 하므로 이 방법은 좀 더 어려울 수 있습니다. 특히 HANA에서는 데이터가 DR 대상 인스턴스에 미리 로드되지 않도록 공유 호스트에서 DR 대상으로 작동하는 인스턴스를 구성하는 것이 좋습니다.

HANA 대형 인스턴스 DR 시나리오의 경우 다음 문서를 확인합니다.

- [스토리지 복제를 사용하여 DR이 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [스토리지 복제를 사용하는 DR(다목적)이 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [스토리지 복제를 사용하는 DR(다목적)이 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [스토리지 복제를 통해 HSR 및 DR을 갖춘 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [스토리지 복제를 사용하여 DR을 사용하여 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [HSR을 사용하는 DR이 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [단일 노드 HSR에서 DR까지(비용 최적화)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [HSR을 통해 고가용성 및 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [HSR을 통해 고가용성 및 재해 복구(비용 최적화)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [HSR을 사용하여 DR을 사용한 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> [SAP](https://azure.microsoft.com/services/site-recovery/) 워크로드에서 DBMS 배포에 대해 Azure 사이트 복구 사용량이 테스트되지 않았습니다. 따라서 이 시점에서 SAP 시스템의 DBMS 계층에 대해 지원되지 않습니다. 나열되지 않은 Microsoft 및 SAP의 다른 복제 방법은 지원되지 않습니다. 서로 다른 Azure 지역 간에 SAP 시스템의 DBMS 계층을 복제하기 위해 타사 소프트웨어를 사용하면 소프트웨어 공급업체에서 지원해야 하며 Microsoft 및 SAP 지원 채널을 통해 지원되지 않습니다. 
 
## <a name="non-dbms-layer"></a>비 DBMS 계층
SAP 응용 프로그램 계층과 필요한 최종 공유 또는 저장소 위치의 경우 두 가지 주요 시나리오는 고객이 활용합니다.

- 두 번째 Azure 지역의 재해 복구 대상은 프로덕션 또는 비프로덕션 용도로 사용되지 않습니다. 이 시나리오에서는 재해 복구 대상으로 작동 하는 VM은 이상적으로 배포 되지 않습니다 및 이미지 및 프로덕션 SAP 응용 프로그램 계층의 이미지 변경 재해 복구 지역에 복제 됩니다. 이러한 작업을 수행할 수 있는 기능은 [Azure 사이트 복구](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview)입니다. Azure 사이트 복구는 이와 같은 Azure-Azure 복제 시나리오를 지원합니다. 
- 재해 복구 대상은 비프로덕션 시스템에서 실제로 사용 중인 VM입니다. 전체 SAP 환경은 일반적으로 한 리전에 프로덕션 시스템과 다른 지역의 비프로덕션 시스템을 통해 두 개의 서로 다른 Azure 지역에 분산되어 있습니다. 많은 고객 배포에서 고객은 프로덕션 시스템과 동일한 비프로덕션 시스템을 가지고 있습니다. 고객은 응용 프로그램 계층 비프로덕션 시스템에 프로덕션 응용 프로그램 인스턴스를 미리 설치했습니다. 장애 조치의 경우 비프로덕션 인스턴스가 종료되고 프로덕션 VM의 가상 이름이 비프로덕션 VM(DNS에 새 IP 주소를 할당한 후)으로 이동되고 미리 설치된 프로덕션 인스턴스가 시작됩니다.

### <a name="sap-central-services-clusters"></a>SAP 중앙 서비스 클러스터
공유 디스크(Windows), SMB 공유(Windows) 또는 NFS 공유를 사용하는 SAP 중앙 서비스 클러스터는 복제하기가 약간 어렵습니다. Windows 측에서는 Windows 저장소 복제가 가능한 솔루션입니다. 리눅스 rsync에서 실행 가능한 솔루션입니다.



## <a name="non-supported-scenario"></a>지원되지 않는 시나리오
Azure 아키텍처에서 SAP 워크로드에 대해 지원되지 않는 시나리오 목록이 있습니다. **지원되지 않음을** 의미하는 SAP및 Microsoft는 이러한 구성을 지원할 수 없으며 이러한 아키텍처를 설정하는 소프트웨어를 제공한 최종 관련 타사로 연기해야 합니다. 두 가지 범주는 다음과 같습니다.

- 저장소 소프트 어플라이언스: Azure 마켓플레이스에서 제공되는 여러 저장소 소프트 어플라이언스가 있습니다. 일부 공급업체는 SAP 소프트웨어와 관련된 Azure에서 이러한 저장소 소프트 어플라이언스를 사용하는 방법에 대한 자체 설명서를 제공합니다. 이러한 저장소 소프트 어플라이언스와 관련된 구성 또는 배포에 대한 지원은 해당 저장소 소프트 어플라이언스의 공급업체에서 제공해야 합니다. 이 사실은 [또한 SAP 지원 노트 #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- 고가용성 프레임워크: Azuremaker 및 Windows 서버 장애 조치 클러스터만 Azure에서 SAP 워크로드에 대해 고가용성 프레임워크를 지원합니다. 앞에서 설명한 것처럼 SIOS의 `Datakeeper` 솔루션은 Microsoft에서 설명하고 문서화합니다. 그럼에도 불구하고 SIOS의 `Datakeeper` 구성 요소는 이러한 구성 요소를 제공하는 공급업체로서 SIOS를 통해 지원되어야 합니다. SAP는 또한 다양한 SAP 노트에 인증된 다른 고가용성 프레임워크를 나열했습니다. 그 중 일부는 Azure에 대한 타사 공급업체의 인증을 받았습니다. 그럼에도 불구하고 이러한 제품을 사용하는 구성에 대한 지원은 제품 공급업체에서 제공해야 합니다. 공급업체마다 SAP 지원 프로세스에 서로 다른 통합이 있습니다. Azure에 배포된 SAP 구성에서 제품을 사용하기로 결정하기 전에 특정 공급업체에 가장 적합한 지원 프로세스를 명확히 해야 합니다.
- 데이터베이스 파일이 공유 디스크에 있는 공유 디스크 클러스터는 maxDB를 제외하고 지원되지 않습니다. 다른 모든 데이터베이스의 경우 지원되는 솔루션은 SMB 또는 NFS 공유 또는 공유 디스크 대신 별도의 저장소 위치를 사용하여 고가용성 시나리오를 구성하는 것입니다.

지원되지 않는 다른 시나리오는 다음과 같은 시나리오입니다.

- NetWeaver, S/4HANA 및 예: `Hybris`SAP의 공통 아키텍처에서 SAP 응용 프로그램 계층과 SAP DBMS 계층 간에 더 큰 네트워크 대기 시간을 제공하는 배포 시나리오 다음 내용이 포함됩니다.
    - 온-프레미스 계층 중 하나를 배포하는 반면 다른 계층은 Azure에 배포됩니다.
    - DBMS 계층이 아닌 다른 Azure 리전에서 시스템의 SAP 응용 프로그램 계층 배포
    - Azure 네이티브 서비스에서 이러한 아키텍처 패턴을 제공하는 경우를 제외하고 Azure및 Azure의 다른 계층에 공동 으로 위치한 데이터 센터에 한 계층 배포
    - SAP 애플리케이션 계층과 DBMS 계층 간에 네트워크 가상 어플라이언스 배포
    - SAP DBMS 계층 또는 SAP 글로벌 전송 디렉터리용 Azure 데이터 센터에 공동 위치한 데이터 센터에서 호스팅되는 스토리지 활용
    - 두 개의 서로 다른 클라우드 공급업체를 통해 두 계층을 배포합니다. 예를 들어 오라클 클라우드 인프라에 DBMS 계층을 배포하고 Azure의 응용 프로그램 계층을 배포합니다.
- 다중 인스턴스 HANA 페이스메이커 클러스터 구성
- Windows에서 지원되는 SAP 데이터베이스에 대해 ANF의 SOFS 또는 SMB를 통해 공유 디스크가 있는 Windows 클러스터 구성입니다. 대신 특정 데이터베이스의 기본 고가용성 복제를 사용하고 별도의 저장소 스택을 사용하는 것이 좋습니다.
- SAP HANA를 제외한 NFS 에 있는 데이터베이스 파일이 ANF 위에 있는 Linux에서 지원되는 SAP 데이터베이스 배포
- 윈도우와 오라클 리눅스 이외의 다른 게스트 OS에 오라클 DBMS의 배포. 또한 [SAP 지원 노트 #2039619](https://launchpad.support.sap.com/#/notes/2039619) 참조

시나리오(들)는 테스트하지 않았기 때문에 다음과 같은 목록을 경험하지 못했습니다.

- DBMS 계층 VM을 복제하는 Azure 사이트 복구입니다. 따라서 잠재적인 재해 복구 구성에 대 한 데이터베이스 네이티브 비동기 복제 기능을 활용 하는 것이 좋습니다.
 

## <a name="next-steps"></a>다음 단계
[SAP NetWeaver에 대한 Azure 가상 시스템 계획 및 구현의](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) 다음 단계를 읽어보십시오.




  



