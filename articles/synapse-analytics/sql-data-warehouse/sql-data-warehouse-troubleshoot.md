---
title: 문제 해결
description: Azure 시냅스 분석 문제 해결.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b24706943cdf59fba89a8007c4914b628b9e34d5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632974"
---
# <a name="troubleshooting-sql-analytics-in-azure-synapse"></a>Azure 시냅스에서 SQL 분석 문제 해결

이 문서에서는 일반적인 문제 해결 질문을 나열합니다.

## <a name="connecting"></a>Connecting

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 'NT AUTHORITY\ANONYMOUS LOGON' 사용자에 대해 로그인 실패 (Microsoft SQL Server, 오류: 18456) | 이 오류는 Azure AD 사용자가 마스터 데이터베이스에 연결하려고 하지만 마스터에 있는 사용자가 없을 때 발생합니다.  이 문제를 해결하려면 연결 시 연결할 SQL 풀을 지정하거나 사용자를 마스터 데이터베이스에 추가합니다.  자세한 내용은 [보안 개요](sql-data-warehouse-overview-manage-security.md) 문서를 참조하세요. |
| 현재 보안 컨텍스트로는 서버 보안 주체 "MyUserName" 이(가) 데이터베이스 "master" 에 액세스할 수 없습니다. 사용자 기본 데이터베이스를 열 수 없습니다. 로그인이 실패했습니다. 사용자 'MyUserName'에 대한 로그인이 실패했습니다. (Microsoft SQL Server, 오류: 916) | 이 오류는 Azure AD 사용자가 마스터 데이터베이스에 연결하려고 하지만 마스터에 있는 사용자가 없을 때 발생합니다.  이 문제를 해결하려면 연결 시 연결할 SQL 풀을 지정하거나 사용자를 마스터 데이터베이스에 추가합니다.  자세한 내용은 [보안 개요](sql-data-warehouse-overview-manage-security.md) 문서를 참조하세요. |
| CTAIP 오류                                                  | 이 오류는 SQL Server 마스터 데이터베이스에 로그인이 만들어졌지만 SQL 데이터베이스에는 로그인하지 않은 경우에 발생할 수 있습니다.  이 오류가 발생하는 경우 [보안 개요](sql-data-warehouse-overview-manage-security.md) 문서를 살펴보세요.  이 문서에서는 마스터에 로그인 및 사용자를 만드는 방법과 SQL 데이터베이스에서 사용자를 만드는 방법에 대해 설명합니다. |
| 방화벽에 의해 차단                                          | SQL 풀은 알려진 IP 주소만 데이터베이스에 액세스할 수 있도록 방화벽에 의해 보호됩니다. 방화벽은 기본적으로 안전하며 이는 연결하기 전에 IP 주소 또는 주소 범위를 명시적으로 설정해야 한다는 의미입니다.  액세스를 위해 방화벽을 구성하려면 [프로비전 지침](create-data-warehouse-portal.md)의 [클라이언트 IP에 대한 서버 방화벽 액세스 구성](create-data-warehouse-portal.md) 단계를 따르세요. |
| 도구 또는 드라이버에 연결할 수 없음                           | 시냅스 SQL 풀은 [SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) [Visual Studio용 SSDT](sql-data-warehouse-install-visual-studio.md)또는 [sqlcmd를](sql-data-warehouse-get-started-connect-sqlcmd.md) 사용하여 데이터를 쿼리하는 것이 좋습니다. 드라이버 및 Azure 시냅스 연결에 대한 자세한 내용은 [Azure 시냅스 드라이버](sql-data-warehouse-connection-strings.md) 및 [Azure 시냅스](sql-data-warehouse-connect-overview.md) 에 연결 문서를 참조하세요. |

## <a name="tools"></a>도구

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio 개체 탐색기 Azure AD 사용자가 없습니다.           | 이것은 알려진 문제이며  해결 방법으로 [sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15)에서 사용자를 봅니다.  시냅스 SQL 풀에서 Azure Active Directory를 사용하는 방법에 대해 자세히 알아보려면 [Azure 시냅스](sql-data-warehouse-authentication.md) 인증을 참조하십시오. |
| 수동 스크립팅, 스크립팅 마법사 사용 또는 SSMS를 통한 연결은 느리거나 응답하지 않거나 오류가 발생합니다. | 사용자가 master 데이터베이스에서 만들어졌는지 확인합니다. 스크립팅 옵션에서는 엔진 버전이 "Microsoft Azure SQL 데이터 웨어하우스 에디션"으로 설정되어 있고 엔진 유형이 "Microsoft Azure SQL 데이터베이스"인지 확인합니다. |
| SSMS에서 스크립트 생성 실패                               | "종속 개체에 대한 스크립트 생성" 옵션이 "True"로 설정된 경우 Synapse SQL 풀에 대한 스크립트 생성이 실패합니다. 해결 방법은 사용자가 수동으로 **도구 > 옵션->SQL Server 개체 탐색기 -> 종속 옵션에 대 한 스크립트를 생성 하 고 false로 설정** 해야 합니다. |

## <a name="performance"></a>성능

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 쿼리 성능 문제 해결                            | 특정 쿼리 문제를 해결하려는 경우 [쿼리 모니터링 방법 알아보기](sql-data-warehouse-manage-monitor.md#monitor-query-execution)문서부터 참조합니다. |
| TempDB 공간 문제 | [TempDB](sql-data-warehouse-manage-monitor.md#monitor-tempdb) 공간 사용량을 모니터링합니다.  TempDB 공간이 부족한 일반적인 원인은 다음과 같습니다.<br>- 쿼리에 할당된 리소스가 부족하여 데이터가 TempDB로 유출됩니다.  [워크로드 관리](resource-classes-for-workload-management.md) 보기 <br>- 통계가 누락되었거나 오래된 데이터 이동을 유발합니다.  통계 를 만드는 방법에 대한 자세한 내용은 [테이블 통계 유지 를](sql-data-warehouse-tables-statistics.md) 참조하십시오.<br>- TempDB 공간은 서비스 수준에 따라 할당됩니다.  [SQL 풀을](sql-data-warehouse-manage-compute-overview.md#scaling-compute) 더 높은 DWU 설정으로 확장하면 TempDB 공간이 더 많이 할당됩니다.|
| 쿼리 성능 및 계획이 부적합하여 종종 통계가 누락됨 | 성능 저하의 가장 일반적인 원인은 테이블에 대한 통계 부족입니다.  통계를 만드는 방법 및 통계가 성능에 중요한 이유에 대한 자세한 내용은 [테이블 통계 유지 관리](sql-data-warehouse-tables-statistics.md)를 참조하세요. |
| 낮은 동시성/큐에 쿼리 대기                             | 동시성과 함께 메모리 할당을 적절히 적용하려면 [워크로드 관리](resource-classes-for-workload-management.md) 를 이해하는 것이 중요합니다. |
| 모범 사례 구현 방법                              | 쿼리 성능을 향상시키는 방법을 배우기 시작하는 가장 좋은 방법은 [Synapse SQL POOL 모범 사례](sql-data-warehouse-best-practices.md) 문서입니다. |
| 크기 조정을 통해 성능을 향상시키는 방법                      | 경우에 따라 성능을 향상시키는 솔루션은 [SQL 풀을 확장하여](sql-data-warehouse-manage-compute-overview.md)쿼리에 더 많은 컴퓨팅 성능을 추가하는 것입니다. |
| 인덱스 품질 저하로 인한 쿼리 성능 저하     | 경우에 따라 [columnstore 인덱스 품질 저하](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)로 인해 쿼리가 느려질 수 있습니다.  자세한 내용 및 [세그먼트 품질을 개선하기 위해 인덱스 다시 작성](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)방법에 대해서는 이 문서를 참조하세요. |

## <a name="system-management"></a>시스템 관리

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: 서버가 허용되는 데이터베이스 트랜잭션 단위 할당량인 45000을 초과하므로 작업을 수행할 수 없습니다. | 만들려는 데이터베이스의 [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md)를 줄이거나 또는 [할당량 증가를 요청](sql-data-warehouse-get-started-create-support-ticket.md)합니다. |
| 공간 사용률 조사                              | 시스템의 공간 사용률을 이해하려면 [테이블 크기](sql-data-warehouse-tables-overview.md#table-size-queries) 를 참조하세요. |
| 테이블 관리 도움말                                    | 테이블 관리에 대한 도움이 필요한 경우 [테이블 개요](sql-data-warehouse-tables-overview.md) 문서를 참조하세요.  이 문서에는 [테이블 데이터 유형](sql-data-warehouse-tables-data-types.md), [테이블 배포](sql-data-warehouse-tables-distribute.md), [테이블 인덱싱](sql-data-warehouse-tables-index.md),  [테이블 분할](sql-data-warehouse-tables-partition.md), [테이블 통계 유지 관리](sql-data-warehouse-tables-statistics.md) 및 [임시 테이블](sql-data-warehouse-tables-temporary.md)과 같이 좀 더 자세한 항목으로 연결되는 링크가 포함되어 있습니다. |
| Azure 포털에서 투명 데이터 암호화(TDE) 진행률 표시줄이 업데이트되지 않음 | [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)을 통해 TDE의 상태를 볼 수 있습니다. |

## <a name="differences-from-sql-database"></a>SQL Database와의 차이점

| 문제                                 | 해결 방법                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| 지원되지 않는 SQL Database 기능     | [지원되지 않는 테이블 기능](sql-data-warehouse-tables-overview.md#unsupported-table-features)을 참조하세요. |
| 지원되지 않는 SQL Database 데이터 형식   | [지원되지 않는 데이터 형식](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)을 참조하세요.        |
| DELETE 및 UPDATE 제한 사항         | [UPDATE 해결](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [DELETE 해결](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) 및 [CTAS를 사용하여 지원되지 않는 UPDATE 및 DELETE 구문 해결](sql-data-warehouse-develop-ctas.md)을 참조하세요. |
| MERGE 문이 지원되지 않음      | [MERGE 해결 방법](sql-data-warehouse-develop-ctas.md#replace-merge-statements)을 참조하세요.                  |
| 저장 프로시저 제한 사항          | 저장 프로시저의 몇 가지 제한을 이해하려면 [저장 프로시저 제한 사항](sql-data-warehouse-develop-stored-procedures.md#limitations) 을 참조하세요. |
| UDF가 SELECT 문을 지원하지 않음 | 이 문제가 UDF의 현재 제한 사항입니다.  지원되는 구문에 대해서는 [CREATE FUNCTION](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) 을 참조하세요. |

## <a name="next-steps"></a>다음 단계

문제 해결 방법을 찾는 데 도움이 필요한 경우 다음과 같은 리소스를 사용해 보세요.

* [블로그](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [기능 요청](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [지원 티켓 만들기](sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [스택 오버플로우 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
