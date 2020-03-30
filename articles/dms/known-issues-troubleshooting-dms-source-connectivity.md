---
title: 원본 데이터베이스를 연결하는 문제
titleSuffix: Azure Database Migration Service
description: Azure 데이터베이스 마이그레이션 서비스를 원본 데이터베이스에 연결하는 것과 관련된 알려진 문제/오류를 해결하는 방법에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297092"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>원본 데이터베이스에 연결할 때 DMS 오류 문제 해결

다음 문서에서는 Azure 데이터베이스 마이그레이션 서비스(DMS)를 원본 데이터베이스에 연결할 때 발생할 수 있는 잠재적인 문제를 해결하는 방법에 대해 자세히 설명합니다. 아래각 섹션은 특정 유형의 소스 데이터베이스와 관련이 있으며, 연결 문제를 해결하는 방법에 대한 세부 정보 및 링크와 함께 발생할 수 있는 오류를 나열합니다.

## <a name="sql-server"></a>SQL Server

원본 SQL Server 데이터베이스에 연결하는 것과 관련된 잠재적인 문제와 이를 해결하는 방법은 다음 표에 제공됩니다.

| Error         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| SQL 연결이 실패했습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바른지, SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인합니다.<br> | 서비스가 원본 서버를 찾을 수 없는 경우 이 오류가 발생합니다. 이 문제를 해결하려면 [동적 포트 또는 명명된 인스턴스를 사용할 때 원본 SQL Server에 연결하는 오류](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)문서를 참조하십시오. |
| **오류 53** - SQL 연결이 실패했습니다. (또한 오류 코드 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | 서비스가 원본 서버에 연결할 수 없는 경우 이 오류가 발생합니다. 이 문제를 해결하려면 다음 리소스를 참조한 다음 다시 시도하십시오. <br><br>  [연결 문제 해결을 위한 대화형 사용자 가이드](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [SQL Server를 Azure SQL Database로 마이그레이션하기 위한 필수 구성 요소](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [SQL Server를 Azure SQL Database 관리 인스턴스로 마이그레이션하기 위한 필수 구성 조건](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **오류 18456** - 로그인에 실패했습니다.<br> | 이 오류는 제공된 T-SQL 자격 증명을 사용하여 서비스가 원본 데이터베이스에 연결할 수 없는 경우에 발생합니다. 문제를 해결하려면 입력한 자격 증명을 확인합니다. [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) 참조하거나 이 표 아래 의 메모에 나열된 문제 해결 문서를 참조한 다음 다시 시도할 수 있습니다. |
| 잘못 된 계정{0}이름 값 ' 제공. 계정 이름에 대한 예상 형식은 도메인 이름\사용자 이름입니다.<br> | 이 오류는 사용자가 Windows 인증을 선택하지만 잘못된 형식으로 사용자 이름을 제공하는 경우에 발생합니다. 이 문제를 해결하려면 Windows 인증에 대한 올바른 형식으로 사용자 이름을 제공하거나 **SQL 인증을**선택합니다. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

원본 AWS RDS MySQL 데이터베이스에 연결하는 것과 관련된 잠재적인 문제와 이를 해결하는 방법은 다음 표에 제공됩니다.

| Error         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 [2003]**[HY000] - 연결이 실패했습니다. 오류 [HY000] [MySQL][ODBC x.x(w) 드라이버] '{서버}'에서 MySQL 서버에 연결할 수 없습니다(10060) | MySQL ODBC 드라이버가 원본 서버에 연결할 수 없는 경우 이 오류가 발생합니다. 이 문제를 해결하려면 이 표 아래의 메모에 나열된 문제 해결 문서를 참조한 다음 다시 시도하십시오.<br> |
| **오류 [2005]**[HY000] - 연결이 실패했습니다. 오류 [HY000] [MySQL][ODBC x.x(w) 드라이버] 알 수 없는 MySQL 서버 호스트 '{서버}' | 서비스가 RDS에서 소스 호스트를 찾을 수 없는 경우 이 오류가 발생합니다. 나열된 원본이 없거나 RDS 인프라에 문제가 있기 때문일 수 있습니다. 이 문제를 해결하려면 이 표 아래의 메모에 나열된 문제 해결 문서를 참조한 다음 다시 시도하십시오.<br> |
| **오류 [1045]**[HY000] - 연결이 실패했습니다. 오류 [HY000] [MySQL][ODBC x.x(w) 드라이버] 사용자 '{사용자}'@'{서버}'에 대한 액세스가 거부됨(암호 사용: YES) | MySQL ODBC 드라이버가 잘못된 자격 증명으로 인해 원본 서버에 연결할 수 없는 경우 이 오류가 발생합니다. 입력한 자격 증명을 확인합니다. 문제가 계속되면 원본 컴퓨터에 올바른 자격 증명이 있는지 확인합니다. 본체에서 암호를 재설정해야 할 수 있습니다. 그래도 문제가 발생하면 이 표 아래 의 메모에 나열된 문제 해결 문서를 참조한 다음 다시 시도하십시오.<br> |
| **오류 [9002]**[HY000] - 연결이 실패했습니다. 오류 [HY000] [MySQL][ODBC x.x(w) 드라이버] 연결 문자열이 맞지 않을 수 있습니다. 포털에서 참조를 확인합니다.| 이 오류는 연결 문자열 문제로 인해 연결이 실패하는 경우에 발생합니다. 제공된 연결 문자열이 유효한지 확인합니다. 이 문제를 해결하려면 이 표 아래의 메모에 나열된 문제 해결 문서를 참조한 다음 다시 시도하십시오.<br> |
| **이진 로깅 오류입니다. 변수 binlog_format 값 '{value}'가 있습니다. '행'으로 변경하십시오.** | 이진 로깅에 오류가 있는 경우 이 오류가 발생합니다. 변수 binlog_format 잘못된 값을 가있습니다. 이 문제를 해결하려면 매개 변수 그룹의 binlog_format 'ROW'로 변경한 다음 인스턴스를 다시 부팅합니다. 자세한 내용은 [바이너리 로깅 옵션 및 변수](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) 또는 [AWS RDS MySQL 데이터베이스 로그 파일 문서를](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)참조하십시오.<br> |

> [!NOTE]
> 원본 AWS RDS MySQL 데이터베이스에 연결하는 문제와 관련된 문제 해결에 대한 자세한 내용은 다음 리소스를 참조하십시오.
> * [Amazon RDS 연결 문제 해결](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Amazon RDS 데이터베이스 인스턴스에 연결하는 문제를 해결하려면 어떻게 해야 합니까?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS 포스트그레SQL

원본 AWS RDS PostgreSQL 데이터베이스에 연결하는 것과 관련된 잠재적인 문제와 이를 해결하는 방법은 다음 표에 제공됩니다.

| Error         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 [101]**[08001] - 연결이 실패했습니다. 오류 [08001] 시간 시간이 만료되었습니다. | Postgres 드라이버가 원본 서버에 연결할 수 없는 경우 이 오류가 발생합니다. 이 문제를 해결하려면 이 표 아래의 메모에 나열된 문제 해결 문서를 참조한 다음 다시 시도하십시오. |
| **오류: 매개 변수 wal_level 값 '{value}'가 있습니다. 복제를 허용하려면 '논리적'으로 변경하십시오.** | 이 오류는 매개 변수 wal_level 잘못된 값을 가지고 있는 경우에 발생합니다. 이 문제를 해결하려면 매개 변수 그룹의 rds.logical_replication 1로 변경한 다음 인스턴스를 재부팅합니다. 자세한 내용은 [Amazon RDS에서](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)DMS 또는 [PostgreSQL을 사용하여 Azure PostgreSQL로 마이그레이션하기 위한 필수 구성 조건을](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) 참조하십시오. |

> [!NOTE]
> 원본 AWS RDS PostgreSQL 데이터베이스에 연결하는 문제와 관련된 문제 해결에 대한 자세한 내용은 다음 리소스를 참조하십시오.
> * [Amazon RDS 연결 문제 해결](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Amazon RDS 데이터베이스 인스턴스에 연결하는 문제를 해결하려면 어떻게 해야 합니까?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL 서버

원본 AWS RDS SQL Server 데이터베이스에 연결하는 것과 관련된 잠재적인 문제와 이를 해결하는 방법은 다음 표에 제공됩니다.

| Error         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 53** - SQL 연결이 실패했습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바른지, SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인합니다. (공급자: 명명된 파이프 공급자, 오류: 40 - SQL Server에 대한 연결을 열 수 없습니다. | 서비스가 원본 서버에 연결할 수 없는 경우 이 오류가 발생합니다. 이 문제를 해결하려면 이 표 아래의 메모에 나열된 문제 해결 문서를 참조한 다음 다시 시도하십시오. |
| **오류 18456** - 로그인에 실패했습니다. 사용자 '{사용자}'에 대한 로그인 실패 | 이 오류는 서비스가 제공된 T-SQL 자격 증명을 사용하여 원본 데이터베이스에 연결할 수 없는 경우에 발생합니다. 문제를 해결하려면 입력한 자격 증명을 확인합니다. [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) 참조하거나 이 표 아래 의 메모에 나열된 문제 해결 문서를 참조하고 다시 시도할 수 있습니다. |
| **오류 87** - 연결 문자열이 잘못되었습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바른지, SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인합니다. (공급자: SQL 네트워크 인터페이스, 오류: 25 - 연결 문자열이 잘못되었습니다) | 이 오류는 서비스가 잘못된 연결 문자열로 인해 원본 서버에 연결할 수 없는 경우에 발생합니다. 문제를 해결하려면 제공된 연결 문자열을 확인합니다. 문제가 지속되면 이 표 아래의 메모에 나열된 문제 해결 문서를 참조한 다음 다시 시도하십시오. |
| **오류 - 서버 인증서를 신뢰할 수 없습니다.** 서버에 성공적으로 연결되었지만 로그인 중 오류가 발생했습니다. (공급자: SSL 공급자, 오류: 0 - 인증서 체인을 신뢰할 수 없는 기관에서 발급했습니다.) | 이 오류는 사용된 인증서를 신뢰할 수 없는 경우에 발생합니다. 이 문제를 해결하려면 신뢰할 수 있는 인증서를 찾은 다음 서버에서 사용하도록 설정해야 합니다. 또는 연결하는 동안 인증서 신뢰 옵션을 선택할 수 있습니다. 사용된 인증서에 익숙하고 신뢰할 수 있는 경우에만 이 작업을 수행합니다. <br> 자체 서명된 인증서를 사용하여 암호화된 TLS 연결은 강력한 보안을 제공하지 않으므로 중간자 공격에 취약합니다. 프로덕션 환경이나 인터넷에 연결된 서버에서 자체 서명된 인증서를 사용하여 TLS에 의존하지 마십시오. <br> 자세한 내용은 Microsoft SQL Server DB 인스턴스 또는 [자습서와 함께 SSL 사용:](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) [DMS를 사용 하 여 RDS SQL 서버를 Azure로 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)합니다. |
| **오류 300** - 사용자에게 필요한 권한이 없습니다. 뷰 서버 상태 권한 개체 '{서버}', 데이터베이스 '{데이터베이스}'에서 거부되었습니다. | 이 오류는 사용자가 마이그레이션을 수행할 수 있는 권한이 없는 경우에 발생 합니다. 이 문제를 해결하려면 [GRANT 서버 권한 - 거래-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) 또는 [자습서: DMS를 사용하여 RDS SQL Server를 Azure로 마이그레이션하여](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) 자세한 내용을 확인하십시오. |

> [!NOTE]
> 원본 AWS RDS SQL Server 연결과 관련된 문제 해결에 대한 자세한 내용은 다음 리소스를 참조하십시오.
>
> * [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)(SQL Server에 대한 연결 오류 해결)
> * [Amazon RDS 데이터베이스 인스턴스에 연결하는 문제를 해결하려면 어떻게 해야 합니까?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>알려진 문제

* [Azure SQL Database로의 온라인 마이그레이션으로 알려진 문제/마이그레이션 제한](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [MySQL용 Azure 데이터베이스로의 온라인 마이그레이션으로 알려진 문제/마이그레이션 제한](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [PostgreSQL용 Azure 데이터베이스로의 온라인 마이그레이션으로 알려진 문제/마이그레이션 제한](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>다음 단계

* [문서 보기 Azure 데이터베이스 마이그레이션 서비스 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* 문서 보기 [Azure 포털을 사용 하 여 MySQL에 대 한 Azure 데이터베이스에서 서버 매개 변수를 구성 하는 방법](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* [Azure 데이터베이스 마이그레이션 서비스를 사용하기 위한 필수 구성 조건의](https://docs.microsoft.com/azure/dms/pre-reqs)문서 개요를 봅니다.
* Azure [데이터베이스 마이그레이션 서비스 사용에 대한 FAQ를](https://docs.microsoft.com/azure/dms/faq)참조하십시오.
