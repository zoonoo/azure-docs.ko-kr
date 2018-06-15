---
title: SQL Data Warehouse 서비스에 대한 모든 항목 | Microsoft Docs
description: http://azure.microsoft.com/documentation/articles/에 있는 SQL Data Warehouse라는 Azure 서비스에 대한 모든 항목, 제목 및 설명에 대한 테이블입니다.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: reference
ms.date: 03/30/2017
ms.author: barbkess
ms.openlocfilehash: 9fe41f12960dc099700e01573b4f03ebf63f8749
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678334"
---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Azure SQL Data Warehouse 서비스에 대한 모든 항목
이 항목에서는 Azure의 **SQL Data Warehouse** 서비스에 직접 적용되는 모든 항목을 나열합니다. 이 웹 페이지에서 **Ctrl+F**를 사용해 키워드를 검색하여 현재 관심 있는 항목을 찾을 수 있습니다.

## <a name="new"></a>새로 만들기
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 1 |[SQL Data Warehouse 백업](sql-data-warehouse-backups.md) |Azure SQL Data Warehouse를 복원 지점 또는 다른 지역에 복원할 수 있는 SQL Data Warehouse 기본 제공 데이터베이스 백업에 대해 알아봅니다. |

## <a name="updated-articles-sql-data-warehouse"></a>업데이트된 문서, SQL Data Warehouse
이 섹션에서는 최근에 대규모로 수행되거나 중요하게 업데이트된 문서가 목록으로 표시됩니다. 업데이트된 각 문서에 대해 추가된 markdown 텍스트의 대략적인 코드 조각이 표시됩니다. 날짜 범위 **2016-08-22**부터 **2016-10-05**까지 문서가 업데이트되었습니다.

| &nbsp; | 문서 | 업데이트된 텍스트, 코드 조각 | 업데이트 시기 |
| ---:|:--- |:--- |:--- |
| 2 |[Azure blob 저장소에서 SQL Data Warehouse로 데이터를 로드합니다(PolyBase).](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/- 바이트 및 파일을 추적하려면 SELECT  r.command,  s.request_id,  r.status,  count(distinct input_name) as nbr_files,  sum(s.bytes_processed)/1024/1024 as gb_processed FROM  sys.dm_pdw_exec_requests r  inner join sys.dm_pdw_dms_external_work s  on r.request_id = s.request_id WHERE  r. label  = 'CTAS : Load  cso . DimProduct  '  OR r. label  = 'CTAS : Load  cso . FactOnlineSales  ' GROUP BY  r.command,  s.request_id,  r.status ORDER BY  nbr_files desc,  gb_processed desc; |2016-09-07 |
| 3 |[SQL Data Warehouse 복원](sql-data-warehouse-restore-database-overview.md) |** 일시 중지된 데이터 웨어하우스를 복원할 수 있나요?** 일시 중지된 데이터 웨어하우스를 복원하려면 먼저 데이터 웨어하우스를 온라인으로 전환해야 합니다. 데이터 웨어하우스가 다시 온라인 상태가 되면 7일의 복원 지점 중에 선택할 수 있습니다. ** 지역 중복 지역에 복원** 지역 중복 저장소를 사용하는 경우 다른 지역에 쌍으로 구성된 데이터 센터에 데이터 웨어하우스를 복원할 수 있습니다. 데이터 웨어하우스는 마지막 일일 백업에서 복원됩니다. ** 타임라인 복원** 지난 7일 이내의 복원 지점으로 데이터베이스를 복원할 수 있습니다. 스냅숏은 4~8시간마다 시작되며 7일 동안 사용할 수 있습니다. 7일보다 오래된 스냅숏은 만료되고 해당 복원 지점을 더 이상 사용할 수 없게 됩니다. ** 복원 비용** 복원된 데이터 웨어하우스에 대한 저장소 비용은 Azure Premium Storage 요금으로 청구됩니다. 복원된 데이터 웨어하우스를 일시 중지하면 저장소에 Azure Premium Storage 요금이 부과됩니다. 일시 중지의 이점은 |2016-09-29 |

## <a name="get-started"></a>시작하기
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 4 |[Azure SQL Data Warehouse에 대한 인증](sql-data-warehouse-authentication.md) |Azure SQL Data Warehouse에 대한 AAD(Azure Active Directory) 및 SQL Server 인증 |
| 5 |[Azure SQL Data Warehouse에 대한 모범 사례](sql-data-warehouse-best-practices.md) |Azure SQL Data Warehouse에 대한 솔루션을 개발하면서 알아야 할 권장 사항 및 모범 사례입니다. 이 내용은 성공적인 개발에 도움이 됩니다. |
| 6 |[Azure SQL Data Warehouse용 드라이버](sql-data-warehouse-connection-strings.md) |SQL Data Warehouse용 드라이버 및 연결 문자열 |
| 7 |[Azure SQL Data Warehouse에 연결](sql-data-warehouse-connect-overview.md) |Azure SQL Data Warehouse의 서버 이름 및 연결 문자열을 찾는 방법 |
| 8 |
            [Azure Machine Learning을 사용하여 데이터 분석](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |Azure Machine Learning을 사용하여 Azure SQL Data Warehouse에 저장된 데이터를 기반으로 예측 기계 학습 모델을 구축합니다. |
| 9 |[Azure SQL Data Warehouse 쿼리(sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |sqlcmd 명령줄 유틸리티를 사용하여 Azure SQL Data Warehouse를 쿼리합니다. |
| 10 |[TRANSACT-SQL(TSQL)를 사용하여 SQL Data Warehouse 데이터베이스 만들기](sql-data-warehouse-get-started-create-database-tsql.md) |TSQL를 사용하여 SQL Data Warehouse를 만드는 방법을 알아봅니다. |
| 11 |[SQL Data Warehouse에 대한 지원 티켓을 만드는 방법](sql-data-warehouse-get-started-create-support-ticket.md) |Azure SQL Data Warehouse에서 지원 티켓을 만드는 방법 |
| 12 |[Azure Data Factory를 사용하여 데이터 로드](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |Azure Data Factory를 사용하여 데이터를 로드하는 방법을 알아보세요. |
| 13 |[SQL Data Warehouse에서 PolyBase를 사용하여 데이터 로드](sql-data-warehouse-get-started-load-with-polybase.md) |PolyBase 정의 및 데이터 웨어하우징 시나리오에 대해 사용하는 방법에 대해 알아봅니다. |
| 14 |[Azure SQL Data Warehouse 만들기](sql-data-warehouse-get-started-provision.md) |Azure Portal에서 SQL Data Warehouse를 만드는 방법을 알아봅니다. |
| 15 |[PowerShell을 사용하여 SQL Data Warehouse 만들기](sql-data-warehouse-get-started-provision-powershell.md) |PowerShell을 사용하여 SQL Data Warehouse 만들기 |
| 16 |[Power BI를 사용하여 데이터 시각화](sql-data-warehouse-get-started-visualize-with-power-bi.md) |Power BI로 SQL Data Warehouse 데이터 시각화 |
| 17 |[Azure SQL Data Warehouse 쿼리(Visual Studio)](sql-data-warehouse-query-visual-studio.md) |Visual Studio를 사용하여 SQL Data Warehouse를 쿼리합니다. |

## <a name="develop"></a>개발
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 18 |[SQL Data Warehouse에 대해 트랜잭션 최적화](sql-data-warehouse-develop-best-practices-transactions.md) |Azure SQL Data Warehouse에서 효율적인 트랜잭션 업데이트를 작성하는 모범 사례 가이드 |
| 19 |[SQL Data Warehouse의 동시성 및 워크로드 관리](sql-data-warehouse-develop-concurrency.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 동시성 및 워크로드 관리를 이해합니다. |
| 20 |[SQL Data Warehouse의 CTAS(Create Table As Select)](sql-data-warehouse-develop-ctas.md) |솔루션 개발을 위해 Azure SQL Data Warehouse의 CTAS(Create Table As Select) 문으로 코딩에 대한 팁. |
| 21 |[SQL Data Warehouse의 동적 SQL](sql-data-warehouse-develop-dynamic-sql.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 동적 SQL 사용 팁. |
| 22 |[SQL Data Warehouse의 GROUP BY 옵션](sql-data-warehouse-develop-group-by-options.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 GROUP BY 옵션 구현을 위한 팁. |
| 23 |[SQL Data Warehouse에서 레이블을 사용하여 쿼리 계측](sql-data-warehouse-develop-label.md) |솔루션 개발을 위해 Azure SQL Data Warehouse에서 레이블을 사용하여 쿼리를 계측하기 위한 팁. |
| 24 |[SQL Data Warehouse의 루프](sql-data-warehouse-develop-loops.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 Transact-SQL 루프 및 커서 대체를 위한 팁. |
| 25 |[SQL Data Warehouse의 저장된 프로시저](sql-data-warehouse-develop-stored-procedures.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 저장 프로시저 구현을 위한 팁 |
| 26 |[SQL Data Warehouse의 트랜잭션](sql-data-warehouse-develop-transactions.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 트랜잭션 구현을 위한 팁 |
| 27 |[SQL Data Warehouse의 사용자 정의 스키마](sql-data-warehouse-develop-user-defined-schemas.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 Transact-SQL 스키마 사용을 위한 팁 |
| 28 |[SQL Data Warehouse의 변수 할당](sql-data-warehouse-develop-variable-assignment.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 Transact-SQL 변수 할당을 위한 팁 |
| 29 |[SQL Data Warehouse의 뷰](sql-data-warehouse-develop-views.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 Transact-SQL 뷰 사용을 위한 팁 |
| 30 |[SQL Data Warehouse에 대한 디자인 결정 및 코딩 기술](sql-data-warehouse-overview-develop.md) |SQL Data Warehouse에 대한 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술입니다. |

## <a name="manage"></a>관리
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 31 |[Azure SQL Data Warehouse의 계산 능력 관리(개요)](sql-data-warehouse-manage-compute-overview.md) |Azure SQL Data Warehouse의 성능 확장 기능입니다. 또는 DWU를 조정하거나 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다. |
| 32 |[Azure SQL Data Warehouse의 계산 능력 관리(Azure Portal)](sql-data-warehouse-manage-compute-portal.md) |계산 능력을 관리하는 Azure 포털 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다. |
| 33 |[Azure SQL Data Warehouse의 계산 능력 관리(PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |계산 능력을 관리하는 PowerShell 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다. |
| 34 |[Azure SQL Data Warehouse의 계산 능력 관리(REST)](sql-data-warehouse-manage-compute-rest-api.md) |계산 능력을 관리하는 PowerShell 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다. |
| 35 |[Azure SQL Data Warehouse의 계산 능력 관리(T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |DWU를 조정하여 성능을 확장하는 Transact-SQL (T-SQL) 작업입니다. 사용량이 많지 않은 시간 동안 다시 조정하여 비용을 절감합니다. |
| 36 |[DMV를 사용하여 작업 모니터링](sql-data-warehouse-manage-monitor.md) |DMV를 사용하여 작업을 모니터링하는 방법을 알아봅니다. |
| 37 |[Azure SQL Data Warehouse의 데이터베이스 관리](sql-data-warehouse-overview-manage.md) |SQL Data Warehouse 데이터베이스 관리 개요. 관리 도구, DWU 및 성능 확장, 쿼리 성능 문제 해결, 보안 정책 설정, 데이터 손상 또는 지역적 중단으로부터 데이터베이스 복원 등이 포함되어 있습니다. |
| 38 |[Azure SQL Data Warehouse의 사용자 쿼리 모니터링](sql-data-warehouse-overview-manage-user-queries.md) |고려 사항, 모범 사례 및 Azure SQL Data Warehouse의 사용자 쿼리 모니터링 작업 개요입니다. |
| 39 |[SQL Data Warehouse 복원](sql-data-warehouse-restore-database-overview.md) |Azure SQL Data Warehouse의 데이터베이스를 복구하기 위한 데이터베이스 복원 옵션 개요입니다. |
| 40 |[Azure SQL Data Warehouse 복원(포털)](sql-data-warehouse-restore-database-portal.md) |Azure SQL Data Warehouse 복원을 위한 Azure Portal 작업. |
| 41 |[Azure SQL Data Warehouse 복원(PowerShell)](sql-data-warehouse-restore-database-powershell.md) |Azure SQL Data Warehouse 복원을 위한 PowerShell 작업. |
| 42 |[Azure SQL Data Warehouse 복원(REST API)](sql-data-warehouse-restore-database-rest-api.md) |Azure SQL Data Warehouse 복원을 위한 REST API 작업. |

## <a name="tables-and-indexes"></a>테이블 및 인덱스
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 43 |[SQL Data Warehouse 테이블의 데이터 형식](sql-data-warehouse-tables-data-types.md) |Azure SQL Data Warehouse 테이블에 대한 데이터 형식으로 시작 |
| 44 |[SQL Data Warehouse의 테이블 배포](sql-data-warehouse-tables-distribute.md) |Azure SQL Data Warehouse에서 테이블 배포 시작 |
| 45 |[SQL Data Warehouse의 테이블 인덱싱](sql-data-warehouse-tables-index.md) |Azure SQL Data Warehouse에서 테이블 인덱싱 시작 |
| 46 |[SQL Data Warehouse의 테이블 개요](sql-data-warehouse-tables-overview.md) |Azure SQL Data Warehouse 테이블로 시작 |
| 47 |[SQL Data Warehouse의 테이블 분할](sql-data-warehouse-tables-partition.md) |Azure SQL Data Warehouse에서 테이블 분할 시작 |
| 48 |[SQL Data Warehouse의 테이블에 대한 통계 관리](sql-data-warehouse-tables-statistics.md) |Azure SQL Data Warehouse에서 테이블에 대한 통계 시작 |
| 49 |[SQL Data Warehouse의 임시 테이블](sql-data-warehouse-tables-temporary.md) |Azure SQL Data Warehouse에서 임시 테이블로 시작 |

## <a name="integrate"></a>통합
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 50 |[SQL Data Warehouse와 함께 Azure 데이터 팩터리 사용](sql-data-warehouse-integrate-azure-data-factory.md) |솔루션 개발을 위한 Azure SQL Data Warehouse의 Azure 데이터 팩터리(ADF) 사용을 위한 팁 |
| 51 |
            [SQL Data Warehouse와 함께 Azure Machine Learning 사용](sql-data-warehouse-integrate-azure-machine-learning.md) |솔루션 개발을 위한 Azure SQL Data Warehouse와 함께 Azure Machine Learning 사용을 위한 팁. |
| 52 |[SQL Data Warehouse와 함께 Azure Stream Analytics 사용](sql-data-warehouse-integrate-azure-stream-analytics.md) |솔루션 개발을 위한 Azure SQL Data Warehouse와 함께 Azure Stream Analytics 사용을 위한 팁 |
| 53 |[SQL Data Warehouse와 함께 Power BI 사용](sql-data-warehouse-integrate-power-bi.md) |솔루션 개발을 위한 Azure SQL Data Warehouse와 함께 Power BI 사용을 위한 팁 |
| 54 |[SQL Data Warehouse와 함께 기타 서비스 활용](sql-data-warehouse-overview-integrate.md) |SQL Data Warehouse와 통합된 솔루션과 파트너 및 도구 |

## <a name="load"></a>로드
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 55 |[Azure Blob 저장소에서 Azure SQL Data Warehouse로 데이터 로드(Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |Azure Data Factory를 사용하여 데이터를 로드하는 방법을 알아보세요. |
| 56 |[Azure blob 저장소에서 SQL Data Warehouse로 데이터를 로드합니다(PolyBase).](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |PolyBase를 사용하여 Azure blob 저장소에서 SQL Data Warehouse로 데이터를 로드하는 방법을 알아봅니다. 몇 개의 테이블을 공용 데이터에서 Contoso 소매 데이터 웨어하우스 스키마로 로드합니다. |
| 57 |[SQL Server에서 Azure SQL Data Warehouse로 데이터 로드(AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |bcp를 사용하여 SQL Server에서 플랫 파일로 데이터를 내보내고 AZCopy를 사용하여 Azure Blob 저장소로 데이터를 가져오고, PolyBase를 사용하여 Azure SQL Data Warehouse로 데이터를 수집합니다. |
| 58 |[SQL Server에서 Azure SQL Data Warehouse로 데이터 로드(플랫 파일)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |데이터의 크기가 작으면, bcp를 사용하여 SQL Server의 데이터를 플랫 파일로 내보내고 Azure SQL Data Warehouse로 데이터를 직접 가져옵니다. |
| 59 |[SQL Server에서 Azure SQL Data Warehouse로 데이터 로드(SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |SSIS(SQL Server Integration Services) 패키지를 만들어 다양한 데이터 원본에서 SQL Data Warehouse로 데이터를 이동하는 방법을 보여줍니다. |
| 60 |[SQL Data Warehouse에서 PolyBase를 사용하여 데이터 로드](sql-data-warehouse-load-from-sql-server-with-polybase.md) |bcp를 사용하여 SQL Server에서 플랫 파일로 데이터를 내보내고 AZCopy를 사용하여 Azure Blob 저장소로 데이터를 가져오고, PolyBase를 사용하여 Azure SQL Data Warehouse로 데이터를 수집합니다. |
| 61 |[SQL Data Warehouse의 PolyBase 사용을 위한 가이드](sql-data-warehouse-load-polybase-guide.md) |SQL Data Warehouse 시나리오에서 PolyBase를 사용하는 방법에 대한 지침 및 권장 사항입니다. |
| 62 |[SQL Data Warehouse로 샘플 데이터를 로드](sql-data-warehouse-load-sample-databases.md) |SQL Data Warehouse로 샘플 데이터를 로드 |
| 63 |[bcp를 사용하여 데이터 로드](sql-data-warehouse-load-with-bcp.md) |bcp 정의 및 데이터 웨어하우징 시나리오에 대해 사용하는 방법에 대해 알아봅니다. |
| 64 |[Azure SQL Data Warehouse에 데이터 로드](sql-data-warehouse-overview-load.md) |SQL Data Warehouse에 데이터 로드를 위한 일반적인 시나리오에 대해 알아봅니다. 여기에는 PolyBase, Azure Blob 저장소, 플랫 파일 및 디스크 배송 사용이 포함됩니다. 타사 도구를 사용할 수도 있습니다. |

## <a name="migrate"></a>마이그레이션
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 65 |[SQL Data Warehouse에 SQL 코드 마이그레이션](sql-data-warehouse-migrate-code.md) |솔루션 개발을 위한 Azure SQL Data Warehouse로 SQL 코드를 마이그레이션하기 위한 팁 |
| 66 |[데이터 마이그레이션](sql-data-warehouse-migrate-data.md) |솔루션 개발을 위한 Azure SQL Data Warehouse로 데이터를 마이그레이션하기 위한 팁 |
| 67 |[데이터 웨어하우스 마이그레이션 유틸리티(미리 보기)](sql-data-warehouse-migrate-migration-utility.md) |SQL Data Warehouse로 마이그레이션합니다. |
| 68 |[SQL Data Warehouse로 스키마 마이그레이션](sql-data-warehouse-migrate-schema.md) |솔루션 개발을 위한 Azure SQL Data Warehouse로 스키마를 마이그레이션하기 위한 팁 |
| 69 |[SQL Data Warehouse에 솔루션 마이그레이션](sql-data-warehouse-overview-migrate.md) |Azure SQL Data Warehouse 플랫폼에 솔루션을 가져오기 위한 마이그레이션 지침 |

## <a name="partners"></a>파트너
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 70 |[SQL Data Warehouse 비즈니스 인텔리전스 파트너](sql-data-warehouse-partner-business-intelligence.md) |SQL Data Warehouse를 지원하는 솔루션을 제공하는 타사 비즈니스 인텔리전스 파트너 목록 |
| 71 |[SQL Data Warehouse 데이터 통합 파트너](sql-data-warehouse-partner-data-integration.md) |Azure SQL Data Warehouse를 지원하는 데이터 통합 솔루션을 제공하는 타사 파트너 목록 |
| 72 |[SQL Data Warehouse 데이터 관리 파트너](sql-data-warehouse-partner-data-management.md) |SQL Data Warehouse를 지원하는 솔루션을 제공하는 타사 데이터 관리 파트너 목록 |

## <a name="reference"></a>참고 자료
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 73 |[SQL Data Warehouse에 대한 참조 항목](sql-data-warehouse-overview-reference.md) |SQL Data Warehouse에 대한 참조 콘텐츠 링크 |
| 74 |[SQL Data Warehouse용 PowerShell cmdlet 및 REST API](sql-data-warehouse-reference-powershell-cmdlets.md) |데이터베이스를 일시 중지하고 다시 시작하는 방법을 포함하여 Azure SQL Data Warehouse용 PowerShell cmdlet을 확인합니다. |
| 75 |[언어 요소](sql-data-warehouse-reference-tsql-language-elements.md) |SQL Data Warehouse에 사용되는 TRANSACT-SQL 언어 요소에 대한 참조 내용에 대한 링크 목록입니다. |
| 76 |[Transact-SQL 항목](sql-data-warehouse-reference-tsql-statements.md) |SQL Data Warehouse에 의해 사용되는 TRANSACT-SQL 항목에 대한 참조 내용에 대한 링크 |
| 77 |[시스템 뷰](sql-data-warehouse-reference-tsql-system-views.md) |SQL Data Warehouse에 대한 시스템 보기 콘텐츠에 연결하는 링크. |

## <a name="security"></a>보안
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 78 |[SQL Data Warehouse - 감사 및 동적 데이터 마스킹에 대한 하위 수준 클라이언트 지원](sql-data-warehouse-auditing-downlevel-clients.md) |데이터 감사에 대한 SQL Data Warehouse 하위 수준 클라이언트 지원에 대해 알아보기 |
| 79 |[Azure SQL Data Warehouse 감사](sql-data-warehouse-auditing-overview.md) |Azure SQL Data Warehouse 감사 시작 |
| 80 |[SQL Data Warehouse에서 투명한 데이터 암호화(TDE) 시작](sql-data-warehouse-encryption-tde.md) |SQL Data Warehouse의 TDE(투명한 데이터 암호화) |
| 81 |[투명한 데이터 암호화(TDE) 시작](sql-data-warehouse-encryption-tde-tsql.md) |SQL Data Warehouse의 TDE(투명한 데이터 암호화)(T-SQL) |
| 82 |[SQL Data Warehouse에서 데이터베이스 보호](sql-data-warehouse-overview-manage-security.md) |솔루션 개발을 위해 Azure SQL Data Warehouse에서 데이터베이스를 보호하는 팁 |

## <a name="miscellaneous"></a>기타
| &nbsp; | 제목 | 설명 |
| ---:|:--- |:--- |
| 83 |[SQL Data Warehouse용 Visual Studio 및 SSDT 설치](sql-data-warehouse-install-visual-studio.md) |Azure SQL Data Warehouse용 Visual Studio 및 SSDT(SQL Server 개발 도구) 설치 |
| 84 |[Premium Storage 세부 정보로 마이그레이션](sql-data-warehouse-migrate-to-premium-storage.md) |기존 SQL Data Warehouse를 프리미엄 저장소로 마이그레이션하기 위한 지침 |
| 85 |[위협 감지 시작](sql-data-warehouse-security-threat-detection.md) |위협 감지를 시작하는 시기 |
| 86 |[SQL Data Warehouse 용량 제한](sql-data-warehouse-service-capacity-limits.md) |SQL Data Warehouse의 연결, 데이터베이스, 테이블 및 쿼리에 대한 최대값입니다. |
| 87 |[Azure SQL Data Warehouse 문제 해결](sql-data-warehouse-troubleshoot.md) |Azure SQL Data Warehouse 문제 해결 |

