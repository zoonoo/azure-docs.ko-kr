---
title: FAQ - Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 데이터베이스 마이그레이션을 수행하는 방법에 대해 자주 묻는 질문입니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: f4d65c97bfccd223453583b25ee0586c5bc0b1ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091444"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Azure Database Migration Service 사용에 대한 FAQ

이 문서에는 Azure Database Migration Service 사용하는 방법에 대해 자주 묻는 질문이 관련 답변과 함께 열거되어 있습니다.

## <a name="overview"></a>개요

**Q. Azure Database Migration Service란 무엇인가요?**
Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완전 관리형 서비스입니다. 서비스는 현재 진행 중인 개발 활동과 함께 일반 공급되고 있고 다음에 집중합니다.

* 안정성 및 성능.
* 원본 -대상 쌍의 반복적 추가
* 충돌 없는 마이그레이션에 대한 지속적인 투자

**Q. Azure Database Migration Service에서 현재 지원하는 원본/대상 쌍은 무엇인가요?**
이 서비스는 현재 다양한 원본/대상 쌍 또는 마이그레이션 시나리오를 지원합니다. 사용 가능한 각 마이그레이션 시나리오의 상태에 대한 전체 목록은 [Azure Database Migration Service에서 지원하는 마이그레이션 시나리오의 상태](./resource-scenario-status.md) 문서를 참조하세요.

기타 마이그레이션 시나리오는 미리 보기로 제공되며 DMS 미리 보기 사이트를 통해 추천을 제출해야 합니다. 미리 보기 상태인 시나리오의 전체 목록을 보고 해당 제안 중 하나에 참여하기 위해 등록하려면 [DMS 미리 보기 사이트](https://aka.ms/dms-preview/)를 참조하세요.

**Q. Azure Database Migration Service에서 원본으로 지원하는 SQL Server 버전은 무엇인가요?**
SQL Server에서 마이그레이션할 때 Azure Database Migration Service에 지원되는 원본은 SQL Server 2005부터 SQL Server 2019까지입니다.

**Q: Azure Database Migration Service를 사용하는 경우 오프라인 마이그레이션과 온라인 마이그레이션 간의 차이점은 무엇인가요?**
Azure Database Migration Service는 오프라인 및 온라인 데이터 마이그레이션을 수행하는 데 사용됩니다. ‘오프라인’ 마이그레이션의 경우 마이그레이션을 시작할 때부터 애플리케이션 가동 중지 시간이 시작됩니다. ‘온라인’ 마이그레이션의 경우 가동 중지 시간이 마이그레이션 후 이동 시간으로 한정됩니다. 오프라인 마이그레이션을 테스트하여 가동 중지 시간이 용납 가능한 수준인지 판단하고, 용납되지 않는다면 온라인 마이그레이션을 수행하는 것이 좋습니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 자세한 내용은 Azure Database Migration Service [가격 책정](https://azure.microsoft.com/pricing/details/database-migration/) 페이지를 참조하세요.

**Q. Azure Database Migration Service는 Database Migration Assistant(DMA)나 SQL Server Migration Assistant(SSMA)와 같은 다른 Microsoft 데이터베이스 마이그레이션 도구와 어떻게 비교될 수 있나요?**
Azure Database Migration Service는 데이터베이스를 Microsoft Azure로 대규모로 마이그레이션하기에 좋은 방법입니다. Azure Database Migration Service를 다른 Microsoft 데이터베이스 마이그레이션 도구와 어떻게 비교되는지 및 다양한 시나리오에 서비스를 사용하는 데 있어 권장하는 사항에 대한 자세한 내용은 [Microsoft의 데이터베이스 마이그레이션 도구 및 서비스 차별화](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)의 블로그 게시물을 참조하세요.

**Q. Azure Database Migration Service는 Azure Migrate 제품과 어떻게 비교될 수 있나요?**
Azure Migrate는 온-프레미스 가상 머신을 Azure IaaS로 마이그레이션하는 것을 도와줍니다. 이 서비스는 마이그레이션 적합성 및 성능 기반 크기 조정을 평가하며, Azure에서 온-프레미스 가성 머신을 실행할 때 드는 비용을 예측합니다. Azure Migrate는 온-프레미스 VM 기반 워크로드를 Azure IaaS VM으로 리프트 앤 시프트 마이그레이션하는 데 유용합니다. Azure Database Migration Service와 달리 Azure Migrate는 Azure SQL Database 또는 Azure SQL Database Managed Instance 등의 Azure PaaS 관계형 데이터베이스 플랫폼에 특화된 데이터베이스 마이그레이션 서비스 제품이 아닙니다.

**Q. Database Migration Service는 고객 데이터를 저장하나요?**
아니요. Database Migration Service는 고객 데이터를 저장하지 않습니다.

## <a name="setup"></a>설치 프로그램

**Q. Azure Database Migration Service 사용을 위한 필수 구성 요소에는 무엇이 있나요?**
데이터베이스 마이그레이션을 수행할 때 Azure Database Migration Service가 원활히 실행되도록 하기 위해 필요한 필수 구성 요소가 몇 가지 있습니다. 일부 필수 구성 요소는 서비스가 지원하는 모든 시나리오(원본-대상 쌍)에 적용되는 반면에 특정 시나리오에만 적용되는 필수 구성 요소도 있습니다.

지원되는 모든 마이그레이션 시나리오에 공통적인 Azure Database Migration Service 필구 구성 요소는 다음을 수행해야 합니다.

* Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 Microsoft Azure Virtual Network를 만듭니다. 그러면 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 사이트 간 연결이 제공됩니다.
* 가상 네트워크 보안 그룹 규칙이 ServiceBus, Storage 및 AzureMonitor의 ServiceTags용 443 포트를 차단하지 않는지 확인합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 원본 데이터베이스에 액세스하여 마이그레이션할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.

Azure Database Migration Service를 사용하여 특정 마이그레이션 시나리오를 경합하는 데 필요한 모든 필수 구성 요소 목록은 docs.microsoft.com의 Azure Database Migration Service [설명서](./dms-overview.md)에서 관련 자습서를 참조하세요.

**Q. 마이그레이션을 위해 원본 데이터베이스에 액세스하는 데 사용되는 방화벽 규칙에 대한 허용 목록을 만들려고 할 때 Azure Database Migration Service의 IP 주소를 어떻게 찾을 수 있나요?**
Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스하게 하려면 방화벽 규칙을 추가해야 할 수 있습니다. 서비스에 대한 IP 주소는 동적이지만 ExpressRoute를 사용하는 경우에는 이 주소가 회사 네트워크에서 프라이빗으로 할당됩니다. 적절한 IP 주소를 식별하는 가장 쉬운 방법은 프로비저닝된 Azure Database Migration Service 리소스와 동일한 리소스 그룹을 살펴보고 관련 네트워크 인터페이스를 찾는 것입니다. 일반적으로 네트워크 인터페이스 리소스의 이름은 NIC 접두사로 시작되고 그 뒤에 고유한 문자와 숫자 시퀀스가 붙습니다(예 : NIC-jj6tnztnmarpsskr82rbndyp). 이 네트워크 인터페이스 리소스를 선택하면 Azure Portal 리소스 개요 페이지에서 허용 목록에 포함되어야 하는 IP 주소를 볼 수 있습니다.

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

**Q. Microsoft Azure Virtual Network는 어떻게 설정하나요?**
가상 네트워크 설정 프로세스를 안내하는 Microsoft 자습서는 많이 있으며, 공식적으로는 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) 문서에 나와있습니다.

## <a name="usage"></a>사용량

**Q. Azure Database Migration Service를 사용하여 데이터베이스 마이그레이션을 수행하기 위해 필요한 단계를 어떻게 요약할 수 있나요?**
일반적이고 간단한 데이터베이스 마이그레이션 단계:

1. 대상 데이터베이스를 만듭니다.
2. 원본 데이터베이스 평가
    * 동일한 마이그레이션의 경우 [DMA](https://www.microsoft.com/download/details.aspx?id=53595)를 사용하여 기존 데이터베이스를 평가합니다.
    * 다른 유형의 마이그레이션(경쟁 원본에서)의 경우 [SSMA](/sql/ssma/sql-server-migration-assistant)를 사용하여 기존 데이터베이스를 평가합니다. 또한 SSMA를 사용하여 데이터베이스 개체를 변환하고 대상 플랫폼으로 스키마를 마이그레이션합니다.
3. Azure Database Migration Service 인스턴스를 만듭니다.
4. 마이그레이션할 원본 데이터베이스, 대상 데이터베이스 및 테이블을 지정하는 마이그레이션 프로젝트를 만듭니다.
5. 전체 로드를 시작합니다.
6. 후속 유효성 검사를 선택합니다.
7. 새 클라우드 기반 데이터베이스로 프로덕션 환경의 수동 전환을 수행합니다.

## <a name="troubleshooting-and-optimization"></a>문제 해결 및 최적화

**Q. DMS로 마이그레이션 프로젝트를 설정하고 있으며 원본 데이터베이스에 연결하는 데 어려움이 있습니다. 어떻게 해야 하나요?**
마이그레이션 작업을 수행할 때 원본 데이터베이스 시스템에 연결하는 데 문제가 있는 경우 DMS 인스턴스를 설정하는 가상 네트워크의 동일한 서브넷에 가상 머신을 만듭니다. 가상 머신에서 UDL 파일을 사용하여 SQL Server에 대한 연결을 테스트하거나 MongoDB 연결을 테스트하기 위해 Robo 3T를 다운로드하는 등의 연결 테스트를 실행할 수 있어야 합니다. 연결 테스트가 성공하면 원본 데이터베이스에 연결하는 데 문제가 없어야 합니다. 연결 테스트에 성공하지 못한 경우 네트워크 관리자에게 문의하세요.

**Q. Azure Database Migration Service를 사용할 수 없거나 중지된 이유는 무엇인가요?**
사용자가 Azure Database Migration Service(DMS)를 명시적으로 중지하거나 서비스가 24시간 동안 비활성 상태인 경우 서비스가 중지되거나 자동 일시 중지된 상태에 있게 됩니다. 각각의 경우에서 서비스는 사용할 수 없으며 중지된 상태에 있게 됩니다.  활성 마이그레이션을 다시 시작하려면 서비스를 다시 시작합니다.

**Q. Azure Database Migration Service의 성능을 최적화하기 위한 권장 사항이 있나요?**
몇 가지 작업을 수행하면 서비스를 사용한 데이터베이스 마이그레이션 속도를 높일 수 있습니다.

* 서비스 인스턴스를 만들 때 다중 CPU 범용 가격 책정 계층을 사용하면 서비스에서 다중 vCPU를 활용하여 병렬 처리 및 빠른 데이터 전송이 가능합니다.
* 하위 수준의 SKU를 사용할 때 데이터 전송 작업에 영향을 줄 수 있는 Azure SQL 데이터베이스 제한을 최소화하기 위해 데이터 마이그레이션 작업 중에 Azure SQL Database 대상 인스턴스를 프리미엄 계층 SKU로 일시 강화합니다.

## <a name="next-steps"></a>다음 단계

Azure Database Migration Service 및 국가별 가용성에 대한 개요는 [Azure Database Migration Service란?](dms-overview.md) 문서를 참조하세요.
