---
title: SAP 솔루션 공급자에 대 한 Azure Monitor | Microsoft Docs
description: 이 문서에서는 SAP 솔루션에 대 한 Azure monitor에 대 한 질문과 대답을 제공 합니다.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 267d600270e834cf4f1f077452fda7459fac3029
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525452"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>SAP 솔루션 공급자 용 Azure monitor (미리 보기)

## <a name="overview"></a>개요  

SAP 솔루션에 대 한 Azure Monitor 컨텍스트에서 *공급자 유형은* 특정 *공급자*를 나타냅니다. 예를 들어 *SAP HANA*SAP HANA 데이터베이스와 같이 SAP 환경 내에서 특정 구성 요소에 대해 구성 됩니다. 공급자는 해당 구성 요소에 대 한 연결 정보를 포함 하 고 해당 구성 요소에서 원격 분석 데이터를 수집 하는 데 도움이 됩니다. Sap 솔루션 리소스 (SAP Monitor 리소스 라고도 함)에 대 한 Azure Monitor 하나는 동일한 공급자 유형의 여러 공급자 또는 여러 공급자 유형의 여러 공급자를 사용 하 여 구성할 수 있습니다.
   
고객은 SAP 환경에서 해당 구성 요소에 대 한 데이터 수집을 사용할 수 있도록 다양 한 공급자 유형을 구성 하도록 선택할 수 있습니다. 예를 들어 고객은 SAP HANA 공급자 유형, 고가용성 클러스터 공급자 유형에 대 한 다른 공급자 등을 위해 하나의 공급자를 구성할 수 있습니다.  

또한 고객은 특정 공급자 유형의 여러 공급자를 구성 하 여 동일한 SAP 모니터 리소스 및 연결 된 관리 그룹을 다시 사용 하도록 선택할 수 있습니다. 관리 되는 리소스 그룹에 대해 자세히 알아봅니다. 공개 미리 보기의 경우 다음 공급자 유형이 지원 됩니다.   
- SAP HANA
- 고가용성 클러스터
- Microsoft SQL Server

![SAP 솔루션 공급자에 대 한 Azure Monitor](./media/azure-monitor-sap/azure-monitor-providers.png)

고객은 SAP Monitor 리소스를 배포할 때 사용 가능한 공급자 유형에 서 하나 이상의 공급자를 구성 하는 것이 좋습니다. 공급자를 구성 하 여 고객은 공급자가 구성 된 해당 구성 요소에서 데이터 수집을 시작 합니다.   

SAP monitor 리소스를 배포할 때 고객이 공급자를 구성 하지 않은 경우 SAP monitor 리소스가 성공적으로 배포 되는 경우에도 원격 분석 데이터가 수집 되지 않습니다. 고객은 Azure Portal 내에서 SAP monitor 리소스를 통해 배포 후 공급자를 추가 하는 옵션을 제공 합니다. 고객은 언제 든 지 SAP monitor 리소스에서 공급자를 추가 하거나 삭제할 수 있습니다.

> [!Tip]
> Microsoft에서 특정 공급자를 구현 하려면이 문서의 끝에 있는 링크를 통해 피드백을 남겨 두거나 계정 팀에 문의 하세요.  

## <a name="provider-type-sap-hana"></a>공급자 유형 SAP HANA

고객은 공급자 유형 *SAP HANA* 공급자를 하나 이상 구성 하 여 SAP HANA 데이터베이스에서 데이터 수집을 사용 하도록 설정할 수 있습니다. SAP HANA 공급자는 SQL 포트를 통해 SAP HANA 데이터베이스에 연결 하 여 데이터베이스에서 원격 분석 데이터를 가져오고 고객 구독의 Log Analytics 작업 영역으로 푸시합니다. SAP HANA 공급자는 SAP HANA 데이터베이스에서 1 분 마다 데이터를 수집 합니다.  

공개 미리 보기에서 고객은 SAP HANA 공급자를 사용 하 여 기본 인프라 사용률, SAP HANA 호스트 상태, SAP HANA 시스템 복제 및 SAP HANA 백업 원격 분석 데이터와 같은 데이터를 확인할 수 있습니다. SAP HANA 공급자를 구성 하려면 호스트 IP 주소, HANA SQL 포트 번호 및 SYSTEMDB 사용자 이름 및 암호가 필요 합니다. 고객은 SYSTEMDB에 대해 SAP HANA 공급자를 구성 하는 것이 좋지만 다른 데이터베이스 테 넌 트에 대해 추가 공급자를 구성할 수 있습니다.

![SAP 솔루션 공급자에 대 한 Azure Monitor-SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>공급자 유형 고가용성 클러스터
고객은 공급자 유형 *고가용성 클러스터* 의 공급자를 하나 이상 구성 하 여 SAP 환경 내에서 Pacemaker 클러스터의 데이터 수집을 사용 하도록 설정할 수 있습니다. 고가용성 클러스터 공급자는 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 끝점을 사용 하 여 Pacemaker에 연결 하 고, 데이터베이스에서 원격 분석 데이터를 가져와서 고객 구독의 Log Analytics 작업 영역으로 푸시합니다. 고가용성 클러스터 공급자는 Pacemaker에서 60 초 마다 데이터를 수집 합니다.  

공개 미리 보기에서 고객은 고가용성 클러스터 공급자를 사용 하 여 다음 데이터를 확인할 수 있습니다.   
 - 노드 및 리소스 상태의 롤업으로 표시 된 클러스터 상태 
 - [다른](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![SAP 솔루션 공급자에 대 한 Azure Monitor-고가용성 클러스터](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

고가용성 클러스터 공급자를 구성 하려면 다음 두 가지 주요 단계를 수행 해야 합니다. 
1. Pacemaker 클러스터 내의 *각* 노드에 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 설치 
    - 고객은 Azure Automation 스크립트를 사용 하 여 고가용성 클러스터를 배포할 수 있습니다. 스크립트는 각 클러스터 노드에 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 를 설치 합니다.  
    - 또는 [이 페이지](https://github.com/ClusterLabs/ha_cluster_exporter) 의 단계에 따라 고객은 수동 설치를 수행할 수 있습니다. 
2. Pacemaker 클러스터 내의 *각* 노드에서 고가용성 클러스터 공급자 구성  
  고가용성 클러스터 공급자, 프로메테우스 URL, 클러스터 이름, 호스트 이름 및 시스템 ID가 필요 합니다.   
  고객은 클러스터 노드당 하나의 공급자를 구성 하는 것이 좋습니다.   

## <a name="provider-type-microsoft-sql-server"></a>공급자 유형 Microsoft SQL server

고객은 공급자 유형 *Microsoft SQL Server* 공급자를 하나 이상 구성 하 여 [Virtual Machines의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)에서 데이터 수집을 사용 하도록 설정할 수 있습니다. SQL Server 공급자는 SQL 포트를 통해 Microsoft SQL Server에 연결 하 고, 원격 분석 데이터를 데이터베이스에서 가져오고, 고객 구독의 Log Analytics 작업 영역으로 푸시합니다. SQL 인증을 위해 SQL Server를 구성 하 고 SAP DB를 공급자의 기본 데이터베이스로 사용 하 여 SQL Server 로그인을 만들어야 합니다. SQL Server 공급자는 SQL Server에서 매시간 60 초 마다 데이터를 수집 합니다.  

공개 미리 보기에서 고객은 기본 인프라 사용률, 상위 SQL 문, 상위 가장 큰 테이블, SQL Server 오류 로그에 기록 된 문제, 차단 프로세스 및 기타 데이터를 SQL Server 하 여 다음 데이터를 볼 수 있습니다.  

Microsoft SQL Server 공급자를 구성 하려면 SAP 시스템 ID, 호스트 IP 주소, SQL Server 포트 번호 및 SQL Server 로그인 이름 및 암호가 필요 합니다.

![SAP 솔루션 공급자에 대 한 Azure Monitor-SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>다음 단계

- SAP solutions 리소스에 대 한 첫 번째 Azure Monitor를 만듭니다.
- SAP 솔루션의 Azure Monitor에 대 한 질문이 있나요? [FAQ](./azure-monitor-faq.md) 섹션 확인
