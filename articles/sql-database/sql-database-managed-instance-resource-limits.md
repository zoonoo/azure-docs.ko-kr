---
title: Azure SQL Database 리소스 제한 사항 - 관리되는 인스턴스 | Microsoft Docs
description: 이 문서는 관리되는 인스턴스의 Azure SQL Database 리소스 제한 사항에 대한 개요를 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 05/22/2019
ms.openlocfilehash: ef431754db222554c6543e12e4cb6cf0431f7b51
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755053"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>개요 Azure SQL Database 관리 인스턴스 리소스 제한

이 문서에서는 Azure SQL Database 관리 되는 인스턴스에 대 한 리소스 제한의 개요를 제공 하 고 이러한 한도 증가 요청 하는 방법에 대 한 정보를 제공 합니다.

> [!NOTE]
> 지원되는 기능 및 T-SQL 문의 차이점은 [기능 차이](sql-database-features.md) 및 [T-SQL 문 지원](sql-database-managed-instance-transact-sql-information.md)을 참조하세요.

## <a name="instance-level-resource-limits"></a>인스턴스 수준 리소스 제한

관리 되는 인스턴스는 특성 및 기본 인프라 및 아키텍처에 종속 된 리소스 제한에 있습니다. 제한은 하드웨어 세대 및 서비스 계층에 따라 다릅니다.

### <a name="hardware-generation-characteristics"></a>하드웨어 세대 특성

Azure SQL Database 관리 되는 인스턴스는 두 세대의 하드웨어에 배포할 수 있습니다. Gen4와 Gen5 합니다. 하드웨어 세대에는 다음 표에서 설명하는 다양한 특성이 있습니다.

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 하드웨어 | Intel E5-2673 v3(Haswell) 2.4GHz 프로세서, 연결형 SSD, vCore = 1PP(물리적 코어) | Intel E5-2673 v4(Broadwell) 2.3GHz 프로세서, 고속 NVMe SSD, vCore = 1LP(하이퍼스레드) |
| vCore 수 | 8, 16, 24개 vCore | 8, 16, 24, 32, 40, 64, 80개 vCore |
| 메모리 (메모리/코어 비율) | vCore당 7GB | vCore당 5.1GB |
| 최대 메모리 내 OLTP 메모리 | 인스턴스 제한: vCore당 3GB<br/>데이터베이스 제한:<br/> -8 코어: 데이터베이스당 8 GB<br/> -16 코어: 데이터베이스당 20GB<br/> -24 코어: 데이터베이스당 36 GB | 인스턴스 제한: VCore 당 2.5GB<br/>데이터베이스 제한:<br/> -8 코어: 데이터베이스당 13 GB<br/> -16 코어: 데이터베이스당 32GB |
| 최대 인스턴스 저장소 (범용) |  8 TB | 8 TB |
| 최대 인스턴스 저장소 (비즈니스 위험) | 1TB | 코어 수에 따라 1TB, 2TB 또는 4TB |

### <a name="service-tier-characteristics"></a>서비스 계층 특성

관리 되는 인스턴스 범용 및 중요 비즈니스용 두 가지 서비스 계층에 있습니다. 이러한 계층은 아래 표에서 설명한 대로 다양한 기능을 제공합니다.

| **기능** | **범용** | **중요 비즈니스** |
| --- | --- | --- |
| vCore 수\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| 메모리 (메모리/코어 비율) | Gen4: 56GB-168GB (7GB/vCore)<br/>Gen5: 40.8 GB-408 GB (5.1 GB/vCore) | Gen4: 56GB-168GB (7GB/vCore)<br/>Gen5: 40.8 GB-408 GB (5.1 GB/vCore) |
| 최대 인스턴스 저장소 크기 | 8 TB | Gen4: 1TB <br/> Gen5: <br/>- 1TB(8, 16개 vCore용)<br/>- 2TB(24개 vCore용)<br/>- 4TB(32, 40, 64, 80개 vCore용) |
| 데이터베이스당 최대 저장소 | 인스턴스당 최대 저장소 크기에 따라 결정됨 | 인스턴스당 최대 저장소 크기에 따라 결정됨 |
| 인스턴스당 최대 데이터베이스 수 | 100 | 100 |
| 인스턴스당 최대 데이터베이스 파일 수 | 최대 280개 | 데이터베이스당 32,767개 파일 |
| 데이터/로그 IOPS(근사치) | 파일당 500~7,500<br/>\*[파일 크기에 따라 다름](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 K-110 K (1375/vCore) |
| 로그 처리량 | vCore당 3MB/초<br/>인스턴스당 최대 22 MB/s | VCore 당 4 MB/s<br/>인스턴스당 최대 48 MB/s|
| 데이터 처리량(근사치) | 파일당 100~250MB/초<br/>\*[파일 크기에 따라 다름](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | N/A |
| IO 대기 시간(근사치) | 5~10ms | 1~2ms |
| 최대 tempDB 크기 | 192~1,920GB(vCore당 24GB) | 제약 조건 없음 - 최대 인스턴스 스토리지 크기로 제한됨 |
| 최대 세션 | 30000 | 30000 |

**참고**:

- 사용자 및 시스템 데이터베이스의 데이터 및 로그 파일 크기는 최대 스토리지 크기 제한과 비교되는 인스턴스 스토리지 크기에 포함됩니다. <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 시스템 뷰를 사용하여 데이터베이스에서 사용되는 총 공간을 확인합니다. 오류 로그는 영구적이지 않으며 크기에 포함되지 않습니다. 백업은 스토리지 크기에 포함되지 않습니다.
- 처리량 및 IOPS도 종속 페이지 크기를 명시적으로 관리 되는 인스턴스에서 제한 되지 않습니다.

## <a name="supported-regions"></a>지원되는 지역

관리 되는 인스턴스 에서만 만들 수 있습니다 [지원 되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)합니다. 현재 지원 되지 않는 지역에서 관리 되는 인스턴스를 만들 수 있습니다 [Azure portal 통해 지원 요청을 보낼](#obtaining-a-larger-quota-for-sql-managed-instance)합니다.

## <a name="supported-subscription-types"></a>지원되는 구독 유형

관리 되는 인스턴스는 현재 다음과 같은 유형의 구독에만 배포를 지원합니다.

- [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [CSP(클라우드 서비스 공급자)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> 이 제한은 일시적입니다. 앞으로 새 구독 유형이 사용될 예정입니다.

## <a name="regional-resource-limitations"></a>지역별 리소스 제한

지원되는 구독 유형에는 지역당 제한된 수의 리소스가 포함될 수 있습니다. 관리 되는 인스턴스 형식에 구독에 따라 Azure 지역 마다 두 가지 기본 제한을 있습니다.

- **서브넷 제한**: 관리되는 인스턴스가 단일 지역에 배포되는 서브넷의 최대 수입니다.
- **인스턴스 수 제한**: 단일 지역에 배포할 수 있는 인스턴스의 최대 수입니다.

> [!Note]
> 이러한 제한은 기본 설정 및 없습니다 기술적 제한 사항을 적용 합니다. 특별 한 만들어 제한 증가 주문형 수 [Azure portal에서 지원 요청](#obtaining-a-larger-quota-for-sql-managed-instance) 현재 지역에서 관리 되는 인스턴스에 더 해야 합니다. 지원 요청을 보내지 않고 대신 다른 Azure 지역에 새 관리 되는 인스턴스를 만들 수 있습니다.

다음 표에서 지원 되는 구독에 대 한 기본 국가별 제한을 보여 줍니다.

|구독 유형| 관리 되는 인스턴스 서브넷의 최대 수 | 인스턴스의 최대 수 |GP 관리되는 인스턴스의 최대 수*|BC 관리되는 인스턴스의 최대 수*|
| :---| :--- | :--- |:--- |:--- |
|종량제|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|종량제 개발/테스트|1*|4*|4*|1*|
|Enterprise 개발/테스트|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* 단위의 총 수 "인스턴스"는 서브넷에서 4를 초과 하지 않습니다 있도록 1 BC 또는 GP 인스턴스 4 개 이상의 서브넷에 하거나 배포할 수 있습니다.

** 다른 서비스 계층에 인스턴스가 없는 경우 한 서비스 계층의 최대 인스턴스 수가 적용됩니다. 동일한 서브넷 내에 GP 및 BC 인스턴스를 혼합 하려는 경우에 허용 되는 조합에 대 한 다음 섹션 참조로 사용 합니다. 간단한 규칙으로, 서브넷의 총 수는 3개를 초과할 수 없으며, 인스턴스 단위의 총 수는 12개를 초과할 수 없습니다.


> [!IMPORTANT]
> 배포를 계획할 때는 일반적으로 중복성 추가로 인해 BC(중요 비즈니스용) 인스턴스에서 GP(범용) 인스턴스보다 4배 많은 용량을 사용한다고 가정합니다. 따라서 계산상 1개 GP 인스턴스 = 1개 인스턴스 단위 및 1개 BC 인스턴스 = 4개 인스턴스 단위입니다. 기본 제한 값에 대 한 사용량 분석을 단순화 하기 위해 여러 지역에 있는 관리 되는 인스턴스에 배포 된 인스턴스 단위 제한 구독 유형에 대 한 결과 비교 하는 모든 서브넷 인스턴스 단위 요약 되어 있습니다.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>범용 및 중요 비즈니스용 인스턴스 혼합 배포 전략

[EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/) 구독에서는 GP 및 BC 인스턴스가 조합될 수 있습니다. 그러나 서브넷에 인스턴스를 배치하는 것과 관련하여 몇 가지 제약 조건이 있습니다.

> [!Note]
> [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 및 [CSP(클라우드 서비스 공급자)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) 구독 유형에는 하나의 중요 비즈니스용 또는 최대 4개의 범용 인스턴스가 있을 수 있습니다.

다음 예에서는 비어 있지 않은 서브넷과 GP 및 BC 혼합 서비스 계층이 있는 배포 사례를 설명합니다.

|서브넷 수|서브넷 1|서브넷 2|서브넷 3|
|:---|:---|:---|:---|
|1|1개 BC 및 최대 8개 GP<br>2개 BC 및 최대 4개 GP|N/A| N/A|
|2|0개 BC, 최대 4개 GP|1개 BC, 최대 4개 GP<br>2개 BC, 0개 GP|N/A|
|2|1개 BC, 0개 GP|0개 BC, 최대 8개 GP<br>1개 BC, 최대 4개 GP|N/A|
|2|2개 BC, 0개 GP|0개 BC, 최대 4개 GP|N/A|
|3|1개 BC, 0개 GP|1개 BC, 0개 GP|0개 BC, 최대 4개 GP|
|3|1개 BC, 0개 GP|0개 BC, 최대 4개 GP|0개 BC, 최대 4개 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>관리 되는 인스턴스를 SQL에 대 한 더 많은 할당량을 가져오지

에 현재 지역에서 관리 되는 인스턴스에 더 해야 하는 경우 Azure portal을 사용 하 여 할당량을 확장 하기 위한 지원 요청을 보냅니다.
더 많은 할당량을 가져오는 프로세스를 시작하려면 다음을 수행합니다.

1. **도움말 + 지원**을 열고, **새 지원 요청**을 클릭합니다.

   ![도움말 및 지원](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 새 지원 요청에 대한 [기본 사항] 탭에서 다음을 수행합니다.
   - **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.
   - **구독**의 경우 사용자의 구독을 선택합니다.
   - **할당량 유형**에 대해 **SQL Database Managed Instance**를 선택합니다.
   - **지원 계획**에 대해 해당 지원 계획을 선택합니다.

     ![문제점 유형 할당량](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. **다음**을 클릭합니다.
4. 새 지원 요청에 대한 [문제점] 탭에서 다음을 수행합니다.
   - **심각도**에 대해 문제점의 심각도 수준을 선택합니다.
   - **세부 정보**에 대해 오류 메시지를 포함하여 문제점에 대한 추가 정보를 제공합니다.
   - **파일 업로드**에 대해 자세한 정보가 포함된 파일(최대 4MB)을 첨부합니다.

     ![문제점 세부 정보](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 유효한 요청에 포함되어야 하는 다음과 같습니다.
     > - 구독 제한를 늘려야 하는 지역
     > - 할당량 증가 후 기존 서브넷의 서비스 계층당 필요한 인스턴스 수(기존 서브넷 중 하나를 확장해야 하는 경우)
     > - 필요한 서브넷의 수 및 새 서브넷 내의 서비스 계층당 총 인스턴스 수(새 서브넷에 관리되는 인스턴스를 배포해야 하는 경우).

5. **다음**을 클릭합니다.
6. 새 지원 요청에 대한 [연락처 정보] 탭에서 기본 연락 방법(이메일 또는 전화)과 연락처 세부 정보를 입력합니다.
7. **만들기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- 관리 되는 인스턴스에 대 한 자세한 내용은 참조 하세요. [관리 되는 인스턴스란 무엇 인가요?](sql-database-managed-instance.md)합니다.
- 가격 정보는 [SQL Database Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
- 첫 번째 관리 되는 인스턴스를 만드는 방법에 알아보려면 참조 [빠른 시작 가이드](sql-database-managed-instance-get-started.md)합니다.
