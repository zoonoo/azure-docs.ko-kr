---
title: Azure Database Migration Service 사용에 대한 FAQ | Microsoft Docs
description: Azure Database Migration Service를 사용 하 여 데이터베이스 마이그레이션을 수행 하는 방법에 대 한 질문과 대답을 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: f59af0bd0297b0413673fc42b26654c36351b815
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566236"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Azure Database Migration Service 사용에 대 한 FAQ

이 문서에서는 Azure Database Migration Service를 사용 하 여 관련 된 답변과 함께 하는 방법에 대 한 자주 묻는 질문을 나열 합니다.

### <a name="q-what-is-azure-database-migration-service"></a>Q. Azure Database Migration Service란 무엇인가요?

Azure Database Migration Service는 Azure 데이터 플랫폼 최소 가동 중지 시간으로 여러 데이터베이스 소스에서 원활 하 게 마이그레이션할 수 있도록 설계 된 완전히 관리 되는 서비스입니다. 서비스는 현재 다음에 집중한 진행 중인 개발 노력과 함께 일반 공급되고 있습니다.

* 안정성 및 성능.
* 원본 -대상 쌍의 반복적 추가
* 충돌 없는 마이그레이션에 대한 지속적인 투자

### <a name="q-what-source-target-pairs-does-azure-database-migration-service-currently-support"></a>Q. 어떤 원본-대상 쌍 Azure Database Migration Service 현재 지원 하나요?

서비스는 현재 다양한 마이그레이션 시나리오를 지원합니다. 사용 가능한 각 마이그레이션 시나리오의 상태에 대한 전체 목록은 [Azure Database Migration Service에서 지원하는 마이그레이션 시나리오의 상태](https://docs.microsoft.com/azure/dms/resource-scenario-status) 문서를 참조하세요. 다른 마이그레이션 시나리오는 미리 보기로 있으며 DMS 미리 보기 사이트를 통해 추천을 제출 해야 합니다. 미리 보기로 제공 되며 이러한 제품 중 하나에 참여 하도록 등록 하는 시나리오의 전체 목록에 대해서는 [DMS 미리 보기 사이트](https://aka.ms/dms-preview/)합니다.

### <a name="q-how-does-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>Q. Azure Database Migration Service 데이터베이스 마이그레이션 길잡이 (DMA) 또는 SQL Server Migration Assistant (SSMA)와 같은 다른 Microsoft 데이터베이스 마이그레이션 도구를 어떻게 비교 되나요?

Azure Database Migration Service는 대규모 Microsoft Azure로 데이터베이스 마이그레이션에 대 한 기본 메서드입니다. 다른 microsoft Azure Database Migration Service와 비교 하는 방법에 대 한 자세한 데이터베이스 마이그레이션 도구와 다양 한 시나리오에 대 한 서비스를 사용 하 여 권장 사항 블로그 게시물을 참조 하세요. [구분 Microsoft 데이터베이스 마이그레이션 도구 및 서비스](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)합니다.

### <a name="q-how-does-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>Q. Azure Database Migration Service Azure Migrate 제품과 어떻게 비교 되나요?

Azure Migrate는 온-프레미스 가상 머신을 Azure IaaS로의 마이그레이션을 지원합니다. 이 서비스는 마이그레이션 적합성 및 성능 기반 크기 조정을 평가하며, Azure에서 온-프레미스 가성 머신을 실행할 때 드는 비용을 예측합니다. Azure Migrate는 온-프레미스 VM 기반 워크로드를 Azure IaaS VM으로 리프트 앤 시프트 마이그레이션하는 데 유용합니다. 그러나 Azure Database Migration Service와 달리 Azure SQL Database 또는 Azure SQL Database Managed Instance와 같은 Azure PaaS 관계형 데이터베이스 플랫폼을 제공 하는 특수 한 데이터베이스 마이그레이션 서비스 아닙니다 Azure Migrate.

### <a name="q-what-versions-of-sql-server-does-azure-database-migration-service-support-as-a-source"></a>Q. Azure Database Migration Service를 원본으로 어떤 버전의 SQL Server 지원 합니까?

SQL Server에서로 마이그레이션할 때 Azure Database Migration Service는 SQL Server 2017을 통해 SQL Server 2005를 지원 합니다.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-azure-database-migration-service-to-perform-a-database-migration"></a>Q. Azure Database Migration Service를 사용 하 여 데이터베이스 마이그레이션을 수행 하는 데 필요한 단계 요약 란?

일반적이고 간단한 데이터베이스 마이그레이션 단계:

1. 대상 데이터베이스를 만듭니다.
2. [Database Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595)를 사용하여 데이터베이스 스키마를 마이그레이션합니다.
3. Azure Database Migration Service 인스턴스를 만듭니다.
4. 마이그레이션할 원본 데이터베이스, 대상 데이터베이스 및 테이블을 지정하는 마이그레이션 프로젝트를 만듭니다.
5. 전체 로드를 시작 합니다.
6. 후속 유효성 검사를 선택합니다.
7. 새 클라우드 기반 데이터베이스로 프로덕션 환경의 수동 전환을 수행합니다.

### <a name="q-what-are-the-prerequisites-for-using-azure-database-migration-service"></a>Q. Azure Database Migration Service를 사용 하 여 필수 구성 요소는 무엇입니까?

데이터베이스 마이그레이션을 수행 하는 경우 Azure Database Migration Service 원활 하 게 실행 되도록 하는 데 필요한 몇 가지 필수 구성 요소가 있습니다. 일부 필수 구성 요소는 서비스가 지원하는 모든 시나리오(원본-대상 쌍)에 적용되는 반면에 특정 시나리오에만 적용되는 필수 구성 요소도 있습니다.

지원되는 모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필구 구성 요소는 다음을 수행해야 합니다.

* 사용 하 여 온-프레미스 원본 서버에 대 한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service 용 VNet을 만듭니다 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)합니다.
* Azure Virtual Network (VNet)를 확인 하는 네트워크 보안 그룹 규칙에는 다음 차단 되지 않도록 통신 포트 443, 53, 9354, 445, 12000과 합니다. Azure VNet NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 문서를 참조하세요.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 원본 데이터베이스에 액세스하여 마이그레이션할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.

Azure Database Migration Service를 사용 하 여 특정 마이그레이션 시나리오에 필요한 모든 필수 구성 요소 목록에 대 한 Azure Database Migration Service의 관련된 자습서를 참조 하세요 [설명서](https://docs.microsoft.com/azure/dms/dms-overview) docs.microsoft.com에서 제공 합니다.

### <a name="q-how-do-i-find-the-ip-address-for-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>Q. 찾기 IP 주소를 Azure Database Migration Service에 대 한 마이그레이션을 위해 내 원본 데이터베이스에 액세스 하는 데 사용 하는 방화벽 규칙에 대 한 허용 목록에서 만들 수 있나요?

Azure Database Migration Service 마이그레이션에 대 한 원본 데이터베이스에 대 한 액세스를 허용 하는 방화벽 규칙을 추가 해야 합니다. 서비스에 대한 IP 주소는 동적이지만 Express 경로를 사용하는 경우에는 이 주소가 회사 네트워크에서 비공개로 할당됩니다. 적절 한 IP 주소를 식별 하는 가장 쉬운 방법은 관련된 네트워크 인터페이스를 찾으려면 프로 비전 된 Azure Database Migration Service 리소스와 동일한 리소스 그룹에서 확인 됩니다. 일반적으로 네트워크 인터페이스 리소스의 이름은 NIC 접두사로 시작되고 그 뒤에 고유한 문자와 숫자 시퀀스가 붙습니다(예 : NIC-jj6tnztnmarpsskr82rbndyp). 이 네트워크 인터페이스 리소스를 선택하면 Azure Portal 리소스 개요 페이지에서 허용 목록에 포함되어야 하는 IP 주소를 볼 수 있습니다.

SQL Server가 수신 대기하는 포트 원본을 허용 목록에 포함해야 할 수도 있습니다. 기본적으로 이 포트는 1433이지만 원본 SQL Server는 다른 포트도 수신 대기하도록 구성될 수 있습니다. 이 경우 해당 포트도 허용 목록에 포함시켜야 합니다. 동적 관리 뷰 쿼리를 사용하여 SQL Server가 수신 대기하는 포트를 확인할 수 있습니다.

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

SQL Server 오류 로그를 쿼리하여 SQL Server가 수신 대기하는 포트를 확인할 수도 있습니다.

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>Q. Azure Virtual Network는 어떻게 설정하나요?

Azure VNET 설정 프로세스를 단계별로 안내하는 Microsoft 자습서는 많이 있으며, 공식적인 문서는 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 문서에 나와있습니다.


### <a name="q-im-setting-up-a-migration-project-in-dms-and-im-having-difficulty-connecting-to-my-source-database-what-should-i-do"></a>Q. DMS에서 마이그레이션 프로젝트를 설정 하 고 내 원본 데이터베이스에 연결할 때 문제가 있습니다. 어떻게 해야 하나요?

마이그레이션에 대 한 작업 하는 동안 원본 데이터베이스 시스템에 연결 하는 데 문제가 있으면 DMS 인스턴스 설정 된 VNet의 가상 컴퓨터를 만듭니다. 가상 컴퓨터의 SQL Server에 대 한 연결 테스트를 UDL 파일을 사용 하 여 MongoDB 연결을 테스트 하려면 Robo 3T 다운로드 등의 연결 테스트를 실행할 수 있어야 합니다. 연결 테스트가 성공 하면 원본 데이터베이스에 연결 문제가 없어야 합니다. 연결 테스트가 성공 하지 않으면 네트워크 관리자에 게 문의 합니다.

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-azure-database-migration-service"></a>Q. Azure Database Migration Service의 성능을 최적화 하기 위한 권장 사항이 있습니까?

몇 가지 작업을 수행하면 서비스를 사용한 데이터베이스 마이그레이션 속도를 높일 수 있습니다.

* 서비스 인스턴스를 만들 때 다중 CPU 범용 가격 책정 계층을 사용하면 서비스에서 다중 vCPU를 활용하여 병렬 처리 및 빠른 데이터 전송이 가능합니다.
* 하위 수준의 SKU를 사용할 때 데이터 전송 작업에 영향을 줄 수 있는 Azure SQL 데이터베이스 제한을 최소화하기 위해 데이터 마이그레이션 작업 중에 Azure SQL Database 대상 인스턴스를 프리미엄 계층 SKU로 일시 강화합니다.

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>Q. 내 Azure Database Migration Service를 사용할 수 없거나 중지된 이유는 무엇입니까?

사용자가 명시적으로 Azure DMS(Database Migration Service)를 중지하거나 서비스가 24시간 동안 비활성 상태인 경우 서비스는 중지되거나 자동 일시 중지된 상태에 있게 됩니다. 각각의 경우에서 서비스는 사용할 수 없으며 중지된 상태에 있게 됩니다.  활성 마이그레이션을 다시 시작하려면 서비스를 다시 시작합니다.

### <a name="q-where-can-i-leave-feedback-about-azure-database-migration-service"></a>Q. Azure Database Migration Service에 대 한 피드백 두어도 위치

많은 의견 부탁드립니다. 모든 사용자 의견 보내기 및 사용자 의견을 통해 Azure Database Migration Service에 대 한 아이디어 / [여기](https://feedback.azure.com/forums/906100-azure-database-migration-service), 또는 팀에 문의 하 여 [Azure Database Migrations 요청](mailto:AskAzureDatabaseMigrations@service.microsoft.com)합니다.

## <a name="next-steps"></a>다음 단계

Azure Database Migration Service 및 국가별 가용성에 대한 개요는 [Azure Database Migration Service란?](dms-overview.md) 문서를 참조하세요.
