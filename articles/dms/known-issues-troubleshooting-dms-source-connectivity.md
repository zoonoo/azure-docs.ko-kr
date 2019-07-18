---
title: 알려진 원본 데이터베이스에 연결 하는 Azure Database Migration Service와 관련 된 문제/오류 문제 해결에 대 한 문서 | Microsoft Docs
description: 원본 데이터베이스에 연결 하는 Azure Database Migration Service와 관련 된 알려진된 문제/오류를 해결 하는 방법에 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462851"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>원본 데이터베이스에 연결할 때 DMS 오류 문제 해결

다음 문서는 Azure 데이터베이스 마이그레이션 서비스 (DMS) 원본 데이터베이스에 연결할 때 발생할 수 있는 잠재적인 문제를 해결 하는 방법에 대 한 정보를 제공 합니다. 아래의 각 섹션 특정 형식의 원본 데이터베이스와 관련이 오류 목록 함께 발생할 수 있는 세부 정보 및 연결 문제를 해결 하는 방법에 대 한 정보에 대 한 링크.

## <a name="sql-server"></a>SQL Server

원본 SQL Server 데이터베이스를 연결할와 관련 된 잠재적인 문제 및 해결 방법 다음 표에 제공 됩니다.

| 오류         | 원인 및 문제 해결 자세한 정보 |
| ------------- | ------------- |
| SQL 연결 하지 못했습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바른지, 고 해당 SQL Server 원격 연결을 허용 하도록 구성 되어 있는지 확인 합니다.<br> | 서비스는 원본 서버를 찾을 수 없는 경우이 오류가 발생 합니다. 문제를 해결 하려면 문서를 참조 [동적 포트를 사용 하는 경우 원본 SQL Server에 연결 하거나 명명 된 인스턴스 오류](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)합니다. |
| **오류 53** -SQL 연결에 실패 했습니다. (또한 오류에 대 한 1, 2, 5, 53, 233, 258, 1225, 코드 11001)<br><br> | 이 오류는 서비스는 원본 서버에 연결할 수 없는 경우에 발생 합니다. 문제를 해결 하기 위해 다음 리소스를 참조 하 고 다시 시도 하십시오. <br><br>  [연결 문제를 해결 하려면 대화형 사용자 가이드](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Azure SQL Database로 SQL Server로 마이그레이션에 대 한 필수 구성 요소](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Azure SQL Database 관리 되는 인스턴스로 SQL Server로 마이그레이션에 대 한 필수 구성 요소](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **오류 18456** -로그인이 실패 했습니다.<br> | 이 오류는 서비스 제공된 된 T-SQL 자격 증명을 사용 하 여 원본 데이터베이스에 연결할 수 없는 경우에 발생 합니다. 문제를 해결 하기 위해 입력 한 자격 증명을 확인 합니다. 또한 참조할 수 있습니다 [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) 또는 아래 참고에 나열 된 문제 해결 문서에 테이블을 다시 시도 합니다. |
| 잘못 된 형식의 AccountName 값 '{0}' 제공 합니다. 예상 AccountName 형식은 DomainName\UserName<br> | 사용자가 Windows 인증을 선택 했지만 잘못 된 형식에서 이름을 제공 하는 경우이 오류가 발생 합니다. 올바른 형식으로 사용자 이름 선택 또는 Windows 인증에 대 한 제공 하거나 문제를 해결 하기 위해 **SQL 인증**합니다. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

원본 AWS RDS MySQL 데이터베이스를 연결할와 관련 된 잠재적인 문제 및 해결 방법 다음 표에 제공 됩니다.

| 오류         | 원인 및 문제 해결 자세한 정보 |
| ------------- | ------------- |
| **오류 [2003]** [HY000]-연결에 실패 했습니다. 오류 [HY000] [MySQL] [ODBC x.x(w) driver] '{server}' (10060)에서 MySQL 서버에 연결할 수 없습니다 | 이 오류는 MySQL ODBC 드라이버는 원본 서버에 연결할 수 없는 경우에 발생 합니다. 문제를 해결 하기 위해이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 합니다.<br> |
| **오류 [2005]** [HY000]-연결에 실패 했습니다. 오류 [HY000] [MySQL] [ODBC x.x(w) driver] 알 수 없는 MySQL server 호스트 '{server}' | 서비스에서 rds. 원본 호스트를 찾을 수 없는 경우이 오류가 발생 나열 된 원본 존재 하지 않거나 RDS 인프라에 문제가 있기 때문에 문제 일 수 있습니다. 문제를 해결 하기 위해이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 합니다.<br> |
| **오류 [1045]** [HY000]-연결에 실패 했습니다. 오류 [HY000] [MySQL] [ODBC x.x(w) driver] 액세스 사용자 '{user}'@'{server}'에 대 한 거부 (암호를 사용 하 여: 아니요) | 이 오류는 MySQL ODBC 드라이버는 잘못 된 자격 증명으로 인해 원본 서버에 연결할 수 없는 경우에 발생 합니다. 입력 한 자격 증명을 확인 합니다. 문제가 계속 되 면 해당 원본 컴퓨터에 올바른 자격 증명을 확인 합니다. 콘솔에서 암호를 다시 설정 해야 합니다. 문제를 계속 발생 하면이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 합니다.<br> |
| **오류 [9002]** [HY000]-연결에 실패 했습니다. 오류 [HY000] [MySQL] [ODBC x.x(w) driver] 연결 문자열 않을 합니다. 참조에 대 한 포털을 방문 합니다.| 이 오류는 연결 문자열을 사용 하 여 문제로 인해 연결이 실패 하는 경우에 발생 합니다. 제공한 연결 문자열이 올바른지 확인 합니다. 문제를 해결 하기 위해이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 합니다.<br> |
| **이진 로깅에 오류가 있습니다. 변수 binlog_format에 값 '{value}'. '행'으로 변경 하십시오.** | 이진 로깅;에서 오류가 발생 하는 경우이 오류가 발생 변수 binlog_format 잘못 된 값이 있습니다. 문제를 해결 하기 위해 '행,' 매개 변수 그룹에서 binlog_format 변경 및 다음 인스턴스를 다시 부팅 합니다. 자세한 내용은를 참조 하세요 [이진 로깅 옵션 변수와](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) 또는 [AWS RDS MySQL 데이터베이스 로그 파일 설명서](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)합니다.<br> |

> [!NOTE]
> 원본 AWS RDS MySQL 데이터베이스 연결과 관련 된 문제를 해결 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 합니다.
> * [Amazon RDS 연결 문제 해결](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [내 Amazon RDS 데이터베이스 인스턴스에 연결 하는 문제를 해결 하는 방법](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

원본 AWS RDS PostgreSQL 데이터베이스를 연결할와 관련 된 잠재적인 문제 및 해결 방법 다음 표에 제공 됩니다.

| 오류         | 원인 및 문제 해결 자세한 정보 |
| ------------- | ------------- |
| **오류 [101]** [08001]-연결에 실패 했습니다. 오류 [08001] 제한 시간 만료 되었습니다. | 이 오류는 Postgres 드라이버 원본 서버에 연결할 수 없는 경우에 발생 합니다. 문제를 해결 하기 위해이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 합니다. |
| **오류: 매개 변수 wal_level에 값 '{value}'. 복제를 허용 하도록 'logical'으로 변경 하세요.** | 매개 변수 wal_level에 잘못 된 값이 있는 경우이 오류가 발생 합니다. 문제를 해결 하기 위해 매개 변수 그룹에서 rds.logical_replication 1로 변경 하 고 인스턴스를 다시 부팅 합니다. 자세한 내용은를 참조 하세요 [DMS를 사용 하 여 Azure PostgreSQL로 마이그레이션하기 위한 필수 조건](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) 하거나 [Amazon RDS에서 PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)합니다. |

> [!NOTE]
> 원본 AWS RDS PostgreSQL 데이터베이스 연결과 관련 된 문제를 해결 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.
> * [Amazon RDS 연결 문제 해결](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [내 Amazon RDS 데이터베이스 인스턴스에 연결 하는 문제를 해결 하는 방법](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

원본 AWS RDS SQL Server 데이터베이스를 연결할와 관련 된 잠재적인 문제 및 해결 방법 다음 표에 제공 됩니다.

| 오류         | 원인 및 문제 해결 자세한 정보 |
| ------------- | ------------- |
| **오류 53** -SQL 연결에 실패 했습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없습니다 않았거나 액세스할 수 있습니다. 인스턴스 이름이 올바른지, 고 해당 SQL Server 원격 연결을 허용 하도록 구성 되어 있는지 확인 합니다. (공급자: 명명 된 파이프 공급자, 오류: 40-SQL Server에 연결을 열지 못했습니다. | 이 오류는 서비스는 원본 서버에 연결할 수 없는 경우에 발생 합니다. 문제를 해결 하기 위해이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 합니다. |
| **오류 18456** -로그인이 실패 했습니다. 사용자 '{user}'에 대 한 로그인 하지 못했습니다. | 이 오류는 서비스 제공 T-SQL 자격 증명을 사용 하 여 원본 데이터베이스에 연결할 수 없는 경우에 발생 합니다. 문제를 해결 하기 위해 입력 한 자격 증명을 확인 합니다. 또한 참조할 수 있습니다 [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) 또는 아래 참고에 나열 된 문제 해결 문서에 테이블을 다시 시도 합니다. |
| **오류 87** -연결 문자열이 잘못 되었습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바른지, 고 해당 SQL Server 원격 연결을 허용 하도록 구성 되어 있는지 확인 합니다. (공급자: SQL 네트워크 인터페이스, 오류: 25-연결 문자열이 잘못 되었습니다.) | 이 오류는 서비스는 잘못 된 연결 문자열 때문에 원본 서버에 연결할 수 없는 경우 발생 합니다. 문제를 해결 하기 위해 제공 된 연결 문자열을 확인 합니다. 문제가 지속 되 면이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 합니다. |
| **오류-서버 인증서를 신뢰할 수 없습니다.** 성공적으로 연결 된 서버와 이지만 로그인 프로세스 중에 오류가 발생 합니다. (공급자: SSL 공급자, 오류: 0-인증서 체인이 기관에서 발급 되었습니다는 신뢰할 수 없는 합니다.) | 이 오류는 사용 된 인증서는 신뢰할 수 없는 경우에 발생 합니다. 문제를 해결 하려면 서버에서 사용 하도록 설정 하 고 신뢰할 수 있는 인증서를 찾을 수 해야 합니다. 또는 연결 하는 동안 인증서 신뢰 옵션을 선택할 수 있습니다. 사용 된 인증서를 사용 하 여 친숙 하 고 있습니다. 신뢰할 수 하는 경우에이 작업을 수행 합니다. <br> 자체 서명 된 인증서를 사용 하 여 암호화 된 SSL 연결은 강력한 보안을 제공 하지 않습니다. 즉 중간자 개입 공격에 취약 하 합니다. 인터넷에 연결 된 서버 또는 프로덕션 환경에서 자체 서명 된 인증서를 사용 하 여 SSL에서 사용 하지 마십시오. <br> 자세한 내용은를 참조 하세요 [Microsoft SQL Server DB 인스턴스를 사용 하 여 SSL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) 또는 [자습서: DMS를 사용 하 여 Azure로 RDS SQL Server 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)합니다. |
| **오류 300** -사용자가 필요한 권한이 없습니다. 개체 '{server}', '{데이터베이스}' 데이터베이스에 대해 VIEW SERVER STATE 권한이 거부 되었습니다. | 사용자 마이그레이션을 수행할 수 있는 권한이 없는 경우이 오류가 발생 합니다. 문제를 해결 하려면 참조 [GRANT 서버 사용 권한-Transact SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) 또는 [자습서: DMS를 사용 하 여 Azure로 RDS SQL Server 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) 대 한 자세한 내용은 합니다. |

> [!NOTE]
> 원본 AWS RDS SQL Server 연결과 관련 된 문제를 해결 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.
>
> * [SQL Server 연결 오류 해결](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [내 Amazon RDS 데이터베이스 인스턴스에 연결 하는 문제를 해결 하는 방법](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>알려진 문제

* [Azure SQL Database로 온라인 마이그레이션 사용 하 여 알려진된 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [MySQL 용 Azure Database로 온라인 마이그레이션 사용 하 여 알려진된 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [PostgreSQL 용 Azure Database로 온라인 마이그레이션 사용 하 여 알려진된 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>다음 단계

* 문서를 볼 [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)합니다.
* 문서를 볼 [Azure portal을 사용 하 여 MySQL 용 Azure Database에서 서버 매개 변수를 구성 하는 방법을](https://docs.microsoft.com/azure/mysql/howto-server-parameters)합니다.
* 문서를 볼 [Azure Database Migration Service 사용에 대 한 필수 구성 요소 개요](https://docs.microsoft.com/azure/dms/pre-reqs)합니다.
* 참조 된 [Azure Database Migration Service 사용에 대 한 FAQ](https://docs.microsoft.com/azure/dms/faq)합니다.
