---
title: "SQL Data Warehouse 용량 한도 | Microsoft Docs"
description: "SQL 데이터 웨어하우스의 연결, 데이터베이스, 테이블 및 쿼리에 대한 최대값입니다."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 10/31/2016
ms.author: barbkess;jrj
translationtype: Human Translation
ms.sourcegitcommit: 3a9ea64c464a74c70e75634a3e5c1e49862a74e7
ms.openlocfilehash: c6b44392c0b3a241d41ae55bd6bb3f544d867e9e
ms.lasthandoff: 02/22/2017


---
# <a name="sql-data-warehouse-capacity-limits"></a>SQL 데이터 웨어하우스 용량 제한
아래 표에는 Azure SQL Data Warehouse의 다양한 구성 요소에 대해 허용되는 최대값이 나와 있습니다.

## <a name="workload-management"></a>워크로드 관리
| Category | 설명 | 최대 |
|:--- |:--- |:--- |
| [DWU(데이터 웨어하우스 단위)][Data Warehouse Units (DWU)] |단일 SQL 데이터 웨어하우스에 대한 최대 DWU |6000 |
| [DWU(데이터 웨어하우스 단위)][Data Warehouse Units (DWU)] |단일 SQL Server에 대한 최대 DWU |기본값&6000;<br/><br/> 기본적으로 각 SQL Server(예: myserver.database.windows.net)에는 DTU 할당량인 45,000이 있으며 최대 6000DWU가 허용됩니다. 이 할당량은 안전을 위한 제한일 뿐입니다. [지원 티켓을 만들고][creating a support ticket] *할당량*을 요청 형식으로 선택하여 할당량을 늘릴 수 있습니다.  DTU 요구 사항을 계산하려면 7.5를 필요한 총 DWU로 곱합니다. 포털의 SQL Server 블레이드에서 현재 DTU 사용량을 볼 수 있습니다. 일시 중지되거나 일시 중지되지 않은 데이터베이스는 모두 DTU 할당량에 포함됩니다. |
| 데이터베이스 연결 |열린 동시 세션 |1024<br/><br/>최대 1,024개의 활성 연결을 지원하며 각각에서 동시에 요청을 SQL 데이터 웨어하우스 데이터베이스에 제출할 수 있습니다. 실제로 동시에 실행할 수 있는 쿼리 수에 제한이 있습니다. 동시성 제한을 초과하는 경우 요청이 처리될 때까지 대기하는 내부 큐로 이동합니다. |
| 데이터베이스 연결 |준비된 문에 대한 최대 메모리 |20MB |
| [워크로드 관리][Workload management] |최대 동시 쿼리 수 |32<br/><br/> 기본적으로 SQL Data Warehouse는 최대 32개의 동시 쿼리 및 큐에 대기 중인 남은 쿼리를 실행할 수 있습니다.<br/><br/>사용자가 보다 상위의 리소스 클래스에 할당되었거나 SQL Data Warehouse가 낮은 DWU로 구성된 경우 동시성 수준을 줄일 수 있습니다. DMV 쿼리와 같은 일부 쿼리는 항상 실행할 수 있습니다. |
| [Tempdb][Tempdb] |Tempdb의 최대 크기 |DW100당&399;GB입니다. 따라서 DWU1000 Tempdb의 크기는 3.99TB로 조정됩니다. |

## <a name="database-objects"></a>데이터베이스 개체
| Category | 설명 | 최대 |
|:--- |:--- |:--- |
| 데이터베이스 |최대 크기 |디스크에서 압축된&240;TB<br/><br/>이 공간은 tempdb 또는 로그 공간과 독립적이므로 영구 테이블에만 사용됩니다.  클러스터형 columnstore의 압축에 따른 예상 크기 증가 비율은 5배입니다.  즉, 모든 테이블이 클러스터형 columnstore(기본 테이블 유형)일 때 이러한 압축을 통해 데이터베이스를 약 1PB로 확장할 수 있습니다. |
| 테이블 |최대 크기 |디스크에서 압축된&60;TB |
| 테이블 |데이터베이스 당 테이블 |20억 |
| 테이블 |테이블 당 열 |1024열 |
| 테이블 |열 당 바이트 |열 [데이터 형식][data type]에 따라 다릅니다.  char 데이터 형식의 경우 8,000자, nvarchar의 경우 4,000자, MAX 데이터 형식의 경우 2GB로 제한됩니다. |
| 테이블 |행 당 바이트, 정의된 크기 |8,060바이트<br/><br/>행당 바이트 수는 페이지 압축이 설정된 SQL Server에 대한 방법과 동일하게 계산됩니다. SQL Server와 마찬가지로, SQL Data Warehouse는 **가변 길이 열** 을 행 외부로 밀어 넣을 수 있게 하는 행 오버플로 저장소를 지원합니다. 가변 길이 행을 행 외부로 밀어 넣으면 주 레코드에는 24바이트 루트만 저장됩니다. 자세한 내용은 MSDN 문서 [8KB를 초과하는 행 오버플로 데이터][Row-Overflow Data Exceeding 8 KB]를 참조하세요. |
| 테이블 |테이블 당 파티션 |15,000<br/><br/>높은 성능을 위해서는 계속해서 비즈니스 요구사항을 지원하면서 파티션 수를 줄이는 것이 좋습니다. 파티션 수가 늘어나면 DDL(데이터 정의 언어) 및 DML(데이터 조작 언어) 작업에 대한 오버헤드가 증가하고 성능이 저하됩니다. |
| 테이블 |파티션 경계 값 당 문자. |4000 |
| 인덱스 |테이블 당 비클러스터형 인덱스. |999<br/><br/>rowstore 테이블에만 적용됩니다. |
| 인덱스 |테이블 당 클러스터형 인덱스. |1<br><br/>rowstore 및 columnstore 테이블 모두에 적용됩니다. |
| 인덱스 |인덱스 키 크기. |900바이트.<br/><br/>rowstore 인덱스에만 적용됩니다.<br/><br/>인덱스를 만들 때 열에 있는 기존 데이터가 900바이트를 초과하지 않는 경우 최대 크기가 900바이트 보다 큰 varchar 열에 인덱스를 만들 수 있습니다. 그러나 나중에 전체 크기가 900바이트를 초과하는 열에서 삽입 또는 업데이트 동작이 실패합니다. |
| 인덱스 |인덱스 당 키 열. |16<br/><br/>rowstore 인덱스에만 적용됩니다. 클러스터형 columnstore 인덱스는 모든 열을 포함합니다. |
| 통계 |결합된 열 값의 크기. |900바이트. |
| 통계 |통계 개체 당 열. |32 |
| 통계 |테이블 당 열에 만든 통계. |30,000 |
| 저장 프로시저 |최대 수준의 중첩. |8 |
| 보기 |보기 당 열 |1,024 |

## <a name="loads"></a>로드
| Category | 설명 | 최대 |
|:--- |:--- |:--- |
| Polybase 로드 |행당 MB |1<br/><br/>PolyBase 부하는 1MB보다 작고 VARCHR(MAX), NVARCHAR(MAX) 또는 VARBINARY(MAX)로 로드할 수 없는 행을 로드할 때만 사용됩니다.<br/><br/> |

## <a name="queries"></a>쿼리
| Category | 설명 | 최대 |
|:--- |:--- |:--- |
| 쿼리 |사용자 테이블에서 쿼리된 쿼리입니다. |1000 |
| 쿼리 |시스템 뷰에서 동시 쿼리입니다. |100 |
| 쿼리 |시스템 뷰에서 쿼리된 쿼리입니다. |1000 |
| 쿼리 |최대 매개 변수 |2098 |
| 배치 |최대 크기 |65,536*4096 |
| 결과 선택 |행 당 열 |4096<br/><br/>결과에는 행마다 4096개 이상의 열이 있어서는 안 됩니다. 항상 4096이 있다고 보장할 수 없습니다. 쿼리 계획에 임시 테이블이 필요한 경우 테이블 당 최대 1024 열이 적용될 수 있습니다. |
| SELECT |중첩된 하위 쿼리 |32<br/><br/>SELECT 문에는 32개 보다 많은 중첩된 하위 쿼리가 있어서는 안 됩니다. 항상 32가 있다고 보장할 수 없습니다. 예를 들어 조인은 쿼리 계획에 하위 쿼리를 제공할 수 있습니다. 또한 사용 가능한 메모리에서 하위 쿼리의 수를 제한할 수 있습니다. |
| SELECT |조인 당 열 |1024열<br/><br/>조인에는 1024개 이상의 열이 있어서는 안 됩니다. 항상 1024가 있다고 보장할 수 없습니다. 조인 계획에 조인 결과보다 많은 열을 가진 임시 테이블이 필요한 경우 1024 제한은 임시 테이블에 적용됩니다. |
| SELECT |그룹화 기준 열 당 바이트. |8060<br/><br/>GROUP BY 절의 열은 최대 8060바이트를 포함할 수 있습니다. |
| SELECT |정렬 기준 열 당 바이트 |8060 바이트.<br/><br/>GROUP BY 절의 열은 최대 8060바이트를 포함할 수 있습니다. |
| 식별자 및 문 당 상수 |참조된 식별자 및 상수의 수. |65,535<br/><br/>SQL 데이터 웨어하우스는 쿼리의 단일 수식에 포함될 수 있는 식별자 및 상수의 수를 제한합니다. 이 제한은 65,535입니다. 이 숫자를 초과하면 SQL Server 오류 8632가 발생합니다. 자세한 내용은 [내부 오류: 식 서비스 제한에 도달했습니다.][Internal error: An expression services limit has been reached]를 참조하세요. |

## <a name="metadata"></a>Metadata
| 시스템 뷰 | 최대 행 |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |가장 최근 1000 SQL 요청에 대한 DMS 작업자의 수. |
| sys.dm_pdw_errors |10000 |
| sys.dm_pdw_exec_requests |10000 |
| sys.dm_pdw_exec_sessions |10000 |
| sys.dm_pdw_request_steps |sys.dm_pdw_exec_requests에 저장된 가장 최근 1000 SQL 요청에 대한 단계의 총 수입니다. |
| sys.dm_pdw_os_event_logs |10000 |
| sys.dm_pdw_sql_requests |sys.dm_pdw_exec_requests에 저장된 가장 최근 1000 SQL 요청입니다. |

## <a name="next-steps"></a>다음 단계
자세한 참조 정보는 [SQL Data Warehouse 참조 개요][SQL Data Warehouse reference overview]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050

