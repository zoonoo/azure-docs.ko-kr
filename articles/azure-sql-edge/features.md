---
title: Azure SQL Edge의 지원 되는 기능
description: Azure SQL Edge에서 지 원하는 기능에 대해 자세히 알아보세요.
keywords: SQL Edge 소개, SQL Edge란, SQL Edge 개요
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392030"
---
# <a name="supported-features-of-azure-sql-edge"></a>Azure SQL Edge의 지원 되는 기능 

Azure SQL Edge는 최신 버전의 SQL Database 엔진을 기반으로 합니다. Linux의 SQL Server 2019에서 지원 되는 기능 및 Linux의 SQL Server 2019 (또는 Windows의 SQL Server)에서 지원 되지 않는 일부 기능을 지원 합니다.

SQL Server on Linux에서 지원되는 기능의 전체 목록은 [SQL Server 2019 on Linux 버전 및 지원되는 기능](/sql/linux/sql-server-linux-editions-and-components-2019)을 참조하세요. Windows의 버전 및 지원 되는 SQL Server 기능에 대 한 자세한 내용은 [SQL Server 2019 (4.x)의 버전 및 지원 되는 기능](/sql/sql-server/editions-and-components-of-sql-server-version-15)을 참조 하세요.

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge 버전

Azure SQL Edge는 두 가지 버전 또는 소프트웨어 플랜으로 제공됩니다. 이러한 버전은 동일한 기능 집합을 가지 며 사용 권한 및 호스트 시스템에서 액세스할 수 있는 메모리 및 코어의 양에 따라 다릅니다.

   |**계획**  |**설명**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  개발 전용입니다. 각 Azure SQL Edge 개발자 컨테이너는 최대 4 개의 코어 및 32 GB 메모리로 제한 됩니다.  |
   |Azure SQL Edge    |  프로덕션의 경우. 각 Azure SQL Edge 컨테이너는 최대 8 개의 코어 및 64 GB 메모리로 제한 됩니다.  |

## <a name="operating-system"></a>운영 체제

Azure SQL Edge 컨테이너는 Ubuntu 18.04를 기반으로 하며, ubuntu 18.04 LTS (권장) 또는 Ubuntu 20.04 LTS를 실행 하는 Docker 호스트 에서만 실행할 수 있습니다. 예를 들어 다른 운영 체제 호스트에서 Azure SQL Edge 컨테이너를 실행할 수 있습니다. 예를 들어 Docker CE 또는 Docker EE를 사용 하 여 Linux 또는 Windows의 다른 배포판에서 실행할 수 있지만,이 구성은 광범위 하 게 테스트 되지 않으므로 Microsoft는 권장 하지 않습니다.

Windows에서 Azure SQL Edge를 실행 하는 데 권장 되는 구성은 Windows 호스트에서 Ubuntu VM을 구성한 후 Linux VM 내에서 Azure SQL Edge를 실행 하는 것입니다.

Azure SQL Edge에 대해 권장 되는 지원 되는 파일 시스템은 EXT4 및 XFS입니다. 영구 볼륨을 사용 하 여 Azure SQL Edge 데이터베이스 저장소를 다시 사용 하는 경우 기본 호스트 파일 시스템을 EXT4 및 XFS로 사용 해야 합니다.

## <a name="hardware-support"></a>하드웨어 지원

Azure SQL Edge에는 64 비트 프로세서 (x64 또는 ARM64)가 필요 하며, 하나 이상의 프로세서와 1GB RAM이 호스트에 있습니다. Azure SQL Edge의 시작 메모리 공간이 450MB의에 가까우면 Edge 장치에서 실행 되는 다른 IoT Edge 모듈 또는 프로세스에 추가 메모리가 필요 합니다. Azure SQL Edge에 대 한 실제 메모리 및 CPU 요구 사항은 처리 되는 데이터의 양과 작업의 복잡성에 따라 달라 집니다. 솔루션에 대 한 하드웨어를 선택 하는 경우 솔루션에 필요한 성능 특성이 충족 되도록 광범위 한 성능 테스트를 실행 하는 것이 좋습니다.  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge 구성 요소

Azure SQL Edge는 데이터베이스 엔진만 지원 합니다. Windows의 SQL Server 2019에서 사용할 수 있는 다른 구성 요소에 대 한 지원과 Linux의 SQL Server 2019이 포함 되어 있지 않습니다. 특히, Azure SQL Edge는 Analysis Services, Reporting Services, Integration Services, MDS(Master Data Services), Machine Learning Services (데이터베이스 내) 및 Machine Learning Server (독립 실행형)와 같은 SQL Server 구성 요소를 지원 하지 않습니다.

## <a name="supported-features"></a>지원되는 기능

Azure SQL Edge는 SQL Server on Linux 기능 하위 집합을 지 원하는 것 외에도 다음과 같은 새로운 기능을 지원 합니다. 

- Azure Stream Analytics를 지 원하는 것과 동일한 엔진을 기반으로 하는 SQL streaming은 Azure SQL Edge의 실시간 데이터 스트리밍 기능을 제공 합니다. 
- `Date_Bucket`Time-Series 데이터 분석에 대 한 t-sql 함수 호출입니다.
- SQL 엔진과 함께 제공 되는 ONNX 런타임을 통해 기계 학습 기능을 제공 합니다.

## <a name="unsupported-features"></a>지원되지 않는 기능

다음 목록에는 Azure SQL Edge에서 현재 지원 되지 않는 Linux의 SQL Server 2019 기능이 포함 되어 있습니다.

| 영역 | 지원되지 않는 기능 또는 서비스 |
|-----|-----|
| **데이터베이스 디자인** | 메모리 내 OLTP 및 관련 DDL 명령과 Transact-sql 함수, 카탈로그 뷰 및 동적 관리 뷰 |
| &nbsp; | `HierarchyID` 데이터 형식, 관련 DDL 명령 및 Transact-sql 함수, 카탈로그 뷰 및 동적 관리 뷰 |
| &nbsp; | `Spatial` 데이터 형식, 관련 DDL 명령 및 Transact-sql 함수, 카탈로그 뷰 및 동적 관리 뷰 |
| &nbsp; | 스트레치 DB 및 관련 DDL 명령과 Transact-sql 함수, 카탈로그 뷰 및 동적 관리 뷰 |
| &nbsp; | 전체 텍스트 인덱스 및 검색, 관련 DDL 명령 및 Transact-sql 함수, 카탈로그 뷰 및 동적 관리 뷰|
| &nbsp; | `FileTable`, `FILESTREAM` 및 관련 DDL 명령과 transact-sql 함수, 카탈로그 뷰 및 동적 관리 뷰|
| **데이터베이스 엔진** | 복제. Azure SQL Edge를 복제 토폴로지의 밀어넣기 구독자로 구성할 수 있습니다. |
| &nbsp; | Polybase. Azure SQL Edge를 Polybase의 외부 테이블에 대 한 대상으로 구성할 수 있습니다. |
| &nbsp; | Java 및 Spark를 통한 언어 확장성. |
| &nbsp; | Active Directory 통합. |
| &nbsp; | 데이터베이스 자동 축소입니다. 데이터베이스의 자동 축소 속성은 명령을 사용 하 여 설정할 수 있지만 `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` 변경 내용은 적용 되지 않습니다. 자동 축소 태스크는 데이터베이스에 대해 실행 되지 않습니다. 사용자는 여전히 ' DBCC ' 명령을 사용 하 여 데이터베이스 파일을 축소할 수 있습니다. |
| &nbsp; | 데이터베이스 스냅숏 |
| &nbsp; | 영구적 메모리 지원. |
| &nbsp; | Microsoft DTC(Distributed Transaction Coordinator). |
| &nbsp; | 리소스 관리자 및 IO 리소스 관리. |
| &nbsp; | 버퍼 풀 확장입니다. |
| &nbsp; | 타사 연결을 사용 하는 분산 쿼리 |
| &nbsp; | 연결 된 서버. |
| &nbsp; | 시스템 확장 저장 프로시저 (예: `XP_CMDSHELL` ) |
| &nbsp; | CLR 어셈블리 및 관련 DDL 명령과 Transact-sql 함수, 카탈로그 뷰 및 동적 관리 뷰 |
| &nbsp; | `ASSEMBLYPROPERTY`,, `FORMAT` `PARSE` , `TRY_PARSE` 등의 CLR 종속 t-sql 함수 |
| &nbsp; | CLR 종속 날짜 및 시간 카탈로그 뷰, 함수 및 쿼리 절 |
| &nbsp; | 버퍼 풀 확장입니다. |
| &nbsp; | 데이터베이스 메일. |
| &nbsp; | Service Broker |
| &nbsp; | 정책 기반 관리 |
| &nbsp; | 관리 데이터 웨어하우스 |
| &nbsp; | 포함된 데이터베이스 |
| **SQL Server 에이전트** |  하위 시스템: CmdExec, PowerShell, 큐 판독기, SSIS, SSAS 및 SSRS. |
| &nbsp; | 알립니다. |
| &nbsp; | 관리 되는 백업. |
| **고가용성** | Always On 가용성 그룹.  |
| &nbsp; | 기본 가용성 그룹. |
| &nbsp; | 장애 조치 (failover) 클러스터 인스턴스를 Always On 합니다. |
| &nbsp; | 데이터베이스 미러링. |
| &nbsp; | 메모리 및 CPU를 핫 추가 합니다. |
| **보안** | 확장 가능 키 관리. |
| &nbsp; | Active Directory 통합.|
| &nbsp; | Secure enclaves 지원.|
| **Services** | SQL Server Browser입니다. |
| &nbsp; | R 및 Python을 통해 Machine Learning 합니다. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services입니다. |
| &nbsp; | Reporting Services - |
| &nbsp; | Data Quality Services. |
| &nbsp; | MDS(Master Data Services). |
| &nbsp; | Distributed Replay. |
| **관리 효율** | 제어점을 SQL Server 유틸리티 합니다. |

## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge 배포](deploy-portal.md)
- [Azure SQL Edge 구성](configure.md)
- [SQL Server 클라이언트 도구를 사용하여 Azure SQL Edge 인스턴스에 연결](connect.md)
- [Azure SQL Edge에서 데이터베이스 백업 및 복원](backup-restore.md)