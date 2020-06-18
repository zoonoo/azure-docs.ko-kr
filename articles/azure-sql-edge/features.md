---
title: Azure SQL Edge(미리 보기)에서 지원하는 기능
description: Azure SQL Edge(미리 보기)에서 지원하는 기능에 대한 자세한 내용
keywords: SQL Edge 소개, SQL Edge란, SQL Edge 개요
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e3bac1cbb0e57f9be4a489207f2fe8b30b5d457e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233204"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Azure SQL Edge(미리 보기)에서 지원하는 기능 

이 문서에서는 Azure SQL Edge에서 지원하는 기능에 대해 자세히 설명합니다. Azure SQL Edge는 최신 버전의 Microsoft SQL Server Database Engine on Linux를 기반으로 빌드되며, SQL Server 2019 on Linux 또는 SQL Server 2019 on Windows에서 현재 지원되지 않거나 사용할 수 없는 기능 외에도 SQL Server 2019 for Linux에서 지원되는 하위 기능 세트를 지원합니다. SQL Server on Linux에서 지원되는 기능의 전체 목록은 [SQL Server 2019 on Linux 버전 및 지원되는 기능](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019)을 참조하세요. SQL Server on Windows 버전 및 지원되는 기능은 [SQL Server 2019(15.x) 버전 및 지원되는 기능](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15)을 참조하세요.

> [!NOTE]
> Azure SQL Edge는 현재 (미리 보기)로 제공되므로 프로덕션 환경에서는 사용할 수 없습니다. Microsoft에서 배포 및 사용 사례 시나리오의 유효성 검사를 마치면 프로덕션 환경에서 Azure SQL Edge를 실행해도 좋다고 알려드립니다.

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge 버전

Azure SQL Edge는 두 가지 버전 또는 소프트웨어 플랜으로 제공됩니다. 두 버전의 기능 세트는 동일하고 사용 권한과 호스트 시스템에서 액세스할 수 있는 cpu/메모리의 양만 다릅니다.

   |**계획**  |**설명**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  개발 전용 sku이며, 각 Azure SQL Edge Developer 컨테이너는 최대 4코어, 32GB 메모리로 제한됩니다.  |
   |Azure SQL Edge    |  프로덕션 sku이며, 각 Azure SQL Edge 컨테이너는 최대 8코어, 64GB 메모리로 제한됩니다.  |

## <a name="operating-system"></a>운영 체제

Azure SQL Edge 컨테이너는 현재 Ubuntu 16.04를 기반으로 하므로 Ubuntu 16.04(권장) 또는 Ubuntu 18.04 중 하나를 실행하는 docker 호스트에서만 실행할 수 있습니다. Azure SQL Edge는 다른 운영 체제 호스트(예: Docker CE 또는 Docker EE를 사용하는 Linux 또는 Windows의 다른 배포판)에서 실행할 수 있지만, 이러한 구성은 Microsoft의 광범위한 테스트를 거치지 않았습니다.

Azure SQL Edge는 현재 Azure IoT Edge를 통한 배포만 지원합니다. Azure IoT Edge를 지원하는 시스템에 대한 자세한 내용은 [Azure IoT Edge 지원 시스템](https://docs.microsoft.com/azure/iot-edge/support)을 참조하세요.

Windows에서 Azure SQL Edge를 실행할 때 권장되는 구성은 Windows 호스트에서 Ubuntu VM을 구성한 다음, Linux VM 내에서 SQL Edge를 실행하는 것입니다.

## <a name="hardware-support"></a>하드웨어 지원

Azure SQL Edge를 사용하려면 Intel, AMD 또는 ARM의 64비트 프로세서가 필요하며, 호스트에 적어도 프로세서 하나와 1GB RAM이 있어야 합니다. Azure SQL Edge의 시작 메모리 공간이 500MB에 육박하지만, 에지 디바이스에서 실행되는 다른 IoT Edge 모듈을 위한 추가 메모리가 필요합니다.

## <a name="azure-sql-edge-components"></a>Azure SQL Edge 구성 요소

Azure SQL Edge는 데이터베이스 엔진만 지원하며, SQL Server 2019 on Windows 또는 SQL Server 2019 on Linux와 함께 제공되는 다른 구성 요소를 지원하지 않습니다. 특히 Azure SQL Edge는 Analysis Services, Reporting Services, Integration Services, MDS(Master Data Services), Machine Learning Services(데이터베이스 내) 및 Machine Learning Server(독립 실행형) 같은 SQL Server 구성 요소를 지원하지 않습니다.

## <a name="supported-features"></a>지원되는 기능

Azure SQL Edge는 SQL Server on Linux 기능의 하위 세트를 지원할 뿐 아니라 다음과 같은 새 기능을 지원합니다. 

- SQL 스트리밍 - Azure Stream Analytics를 구동하는 엔진과 동일한 엔진을 기반으로 하며, Azure SQL Edge의 실시간 데이터 스트리밍 기능을 제공합니다. 
- 시계열 데이터 분석을 위한 새로운 T-SQL 함수 호출 Date_Bucket
- ONNX 런타임을 통해 SQL 엔진과 함께 제공되는 Machine Learning 기능

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Azure SQL Edge에서 지원되지 않는 SQL Server on Linux 기능

아래 목록에는 현재 Azure SQL Edge에서 지원되지 않는 SQL Server 2019 on Linux 기능이 포함되어 있습니다.

| 영역 | 지원되지 않는 기능 또는 서비스 |
|-----|-----|
| **데이터베이스 디자인** | 메모리 내 OLTP와 관련 DDL 명령 및 Transact-SQL 함수, 카탈로그 뷰, 동적 관리 뷰 |
| &nbsp; | HierarchyID 데이터 형식과 관련 DDL 명령 및 Transact-SQL 함수, 카탈로그 뷰, 동적 관리 뷰 |
| &nbsp; | 공간 데이터 형식과 관련 DDL 명령 및 Transact-SQL 함수, 카탈로그 뷰, 동적 관리 뷰 |
| &nbsp; | Stretch DB와 관련 DDL 명령 및 Transact-SQL 함수, 카탈로그 뷰, 동적 관리 뷰 |
| &nbsp; | 전체 텍스트 인덱스 및 검색과 관련 DDL 명령 및 Transact-SQL 함수, 카탈로그 뷰, 동적 관리 뷰|
| &nbsp; | FileTable, FILESTREAM, 관련 DDL 명령 및 Transact-SQL 함수, 카탈로그 뷰, 동적 관리 뷰|
| **데이터베이스 엔진** | 복제. 그러나 Azure SQL Edge를 복제 토폴로지의 밀어넣기 구독자로 구성할 수 있습니다. |
| &nbsp; | Polybase. 그러나 Azure SQL Edge를 Polybase의 외부 테이블에 대한 대상으로 구성할 수 있습니다. |
| &nbsp; | Java 및 Spark를 통한 언어 확장성 |
| &nbsp; | Active Directory 통합 |
| &nbsp; | 데이터베이스 스냅샷 |
| &nbsp; | 영구 메모리 지원 |
| &nbsp; | Microsoft Distributed Transaction Coordinator |
| &nbsp; | Resource Governor 및 IO 리소스 관리 |
| &nbsp; | Buffer Pool Extension |
| &nbsp; | 타사 연결을 사용하는 분산 쿼리 |
| &nbsp; | 연결된 서버 |
| &nbsp; | 시스템 확장 저장 프로시저(XP_CMDSHELL 등) |
| &nbsp; | CLR 어셈블리와 관련 DDL 명령 및 Transact-SQL 함수, 카탈로그 뷰, 동적 관리 뷰 |
| &nbsp; | ASSEMBLYPROPERTY, FORMAT, PARSE, TRY_PARSE처럼 CLR에 종속된 T-SQL 함수 |
| &nbsp; | CLR에 종속된 날짜 및 시간 카탈로그 뷰, 함수, 쿼리 절 |
| &nbsp; | Buffer Pool Extension |
| &nbsp; | 데이터베이스 메일 |
| **SQL Server 에이전트** |  하위 시스템: CmdExec, PowerShell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | 경고 |
| &nbsp; | 관리되는 백업 |
| **고가용성** | Always On 가용성 그룹  |
| &nbsp; | 기본 가용성 그룹 |
| &nbsp; | Always On 장애 조치(failover) 클러스터 인스턴스 |
| &nbsp; | 데이터베이스 미러링 |
| &nbsp; | Hot Add 메모리 및 CPU |
| **보안** | 확장 가능 키 관리 |
| &nbsp; | Active Directory 통합|
| &nbsp; | Secure Enclaves 지원|
| **Services** | SQL Server Browser |
| &nbsp; | R 및 Python을 통한 Machine Learning |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | 데이터베이스 엔진 서비스 |
| &nbsp; | Master  Data  Services |
| &nbsp; | Distributed Replay |
| **관리 효율** | SQL Server 유틸리티 제어 지점 |

## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge 배포](deploy-portal.md)
- [Azure SQL Edge 구성](configure.md)
- [SQL Server 클라이언트 도구를 사용하여 Azure SQL Edge 인스턴스에 연결](connect.md)
- [Azure SQL Edge에서 데이터베이스 백업 및 복원](backup-restore.md)
