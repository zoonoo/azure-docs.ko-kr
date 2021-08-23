---
title: 원본 데이터베이스 연결 문제
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 원본 데이터베이스에 연결하는 것과 관련된 알려진 문제/오류를 해결하는 방법에 관해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: e3dcc8f6bba6830eec9f20732da9e42f03d5b28e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528190"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>원본 데이터베이스에 연결할 때 DMS 오류 문제 해결

다음 문서에서는 Azure DMS(Database Migration Service)를 원본 데이터베이스에 연결할 때 발생할 수 있는 잠재적인 문제를 해결하는 방법에 관해 자세히 설명합니다. 아래 각 섹션은 특정 형식의 원본 데이터베이스와 관련이 있으며, 발생할 수 있는 오류와 연결 문제 해결 방법에 관한 정보의 링크를 자세히 나열합니다.

## <a name="sql-server"></a>SQL Server

원본 SQL Server 데이터베이스에 연결하는 것과 관련된 잠재적인 문제 및 해결 방법에 관한 자세한 사항은 다음 표에 나와 있습니다.

| Error         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| SQL 연결에 실패했습니다. SQL Server에 연결하는 중에 네트워크 관련 오류 또는 인스턴스별 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인하세요.<br> | 이 오류는 서비스가 원본 서버를 찾을 수 없는 경우 발생합니다. 이 문제를 해결하려면 [동적 포트나 명명된 인스턴스를 사용할 때 원본 SQL Server에 연결하는 중 오류 발생](./known-issues-troubleshooting-dms.md#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance) 문서를 참조하세요. |
| **오류 53** - SQL 연결에 실패했습니다. (오류 코드 1, 2, 5, 53, 233, 258, 1225, 11001의 경우도 해당)<br><br> | 이 오류는 서비스가 원본 서버에 연결할 수 없는 경우 발생합니다. 문제를 해결하려면 다음 리소스를 참조한 후 다시 시도합니다. <br><br>  [연결 문제를 해결하기 위한 대화형 사용자 가이드](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [SQL Server를 Azure SQL Database로 마이그레이션하기 위한 필수 구성 요소](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) <br><br> [SQL Server를 Azure SQL Managed Instance로 마이그레이션하기 위한 필수 구성 요소](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) |
| **오류 18456** - 로그인에 실패했습니다.<br> | 이 오류는 서비스가 제공된 T-SQL 자격 증명을 사용하여 원본 데이터베이스에 연결할 수 없는 경우 발생합니다. 이 문제를 해결하려면 입력한 자격 증명을 확인합니다. [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)을 참조하거나 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다. |
| 잘못된 형식의 AccountName 값 ‘{0}’이(가) 제공되었습니다. AccountName의 예상 형식은 DomainName\UserName임<br> | 이 오류는 사용자가 Windows 인증을 선택했지만 잘못된 형식으로 사용자 이름을 제공하는 경우 발생합니다. 문제를 해결하려면 Windows 인증에 올바른 형식으로 사용자 이름을 제공하거나 **SQL 인증** 을 선택합니다. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

원본 AWS RDS MySQL 데이터베이스에 연결하는 것과 관련된 잠재적인 문제 및 해결 방법에 관한 자세한 사항은 다음 표에 나와 있습니다.

| Error         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 [2003]** [HY000] - 연결에 실패했습니다. 오류 [HY000] [MySQL][ODBC x.x(w) 드라이버] ‘{server}’(10060)의 MySQL 서버에 연결할 수 없음 | MySQL ODBC 드라이버가 원본 서버에 연결할 수 없는 경우 이 오류가 발생합니다. 문제를 해결하려면 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다.<br> |
| **오류 [2005]** [HY000] - 연결에 실패했습니다. 오류 [HY000] [MySQL][ODBC x.x(w) 드라이버] 알 수 없는 MySQL 서버 호스트 ‘{server}’ | 이 오류는 서비스가 RDS에서 원본 호스트를 찾을 수 없는 경우 발생합니다. 나열된 원본이 없거나 RDS 인프라에 문제가 있기 때문에 문제가 발생할 수 있습니다. 문제를 해결하려면 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다.<br> |
| **오류 [1045]** [HY000] - 연결에 실패했습니다. 오류 [HY000] [MySQL][ODBC x.x(w) 드라이버] ‘{user}’@’{server}’사용자의 액세스가 거부됨(암호: YES 사용) | 이 오류는 잘못된 자격 증명으로 인해 MySQL ODBC 드라이버가 원본 서버에 연결할 수 없는 경우 발생합니다. 입력한 자격 증명을 확인합니다. 문제가 지속되면 원본 컴퓨터에 올바른 자격 증명이 있는지 확인합니다. 콘솔에서 암호를 재설정해야 할 수도 있습니다. 여전히 문제가 발생하면 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다.<br> |
| **오류 [9002]** [HY000] - 연결에 실패했습니다. 오류 [HY000] [MySQL][ODBC x.x(w) 드라이버] 연결 문자열이 적절하지 않을 수 있습니다. 포털을 참조하세요.| 연결 문자열 문제 때문에 연결에 실패하는 경우 이 오류가 발생합니다. 제공된 연결 문자열이 유효한지 확인합니다. 문제를 해결하려면 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다.<br> |
| **이진 로깅에서 오류가 발생했습니다. binlog_format 변수의 값이 ‘{value}’입니다. ‘row’로 변경하세요.** | 이 오류는 이진 로깅에 오류가 있는 경우 발생합니다. binlog_format 변수에 잘못된 값이 있습니다. 문제를 해결하려면 매개 변수 그룹의 binlog_format을 ‘ROW’로 변경한 다음 인스턴스를 다시 부팅합니다. 자세한 내용은 [이진 로깅 옵션 및 변수](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) 또는 [AWS RDS MySQL 데이터베이스 로그 파일 문서](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)를 참조하세요.<br> |

> [!NOTE]
> 원본 AWS RDS MySQL 데이터베이스에 연결하는 것과 관련된 문제를 해결하는 방법에 관한 자세한 내용은 다음 리소스를 참조하세요.
> *    [Amazon RDS 연결 문제 해결](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> *    [Amazon RDS 데이터베이스 인스턴스 연결 문제를 해결하려면 어떻게 할까요?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

원본 AWS RDS PostgreSQL 데이터베이스에 연결하는 것과 관련된 잠재적인 문제 및 해결 방법에 관한 자세한 사항은 다음 표에 나와 있습니다.

| Error         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 [101]** [08001] - 연결에 실패했습니다. 오류 [08001] 시간 제한이 만료되었습니다. | Postgres 드라이버가 원본 서버에 연결할 수 없는 경우 이 오류가 발생합니다. 문제를 해결하려면 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다. |
| **오류: wal_level 매개 변수의 값이 ‘{value}’입니다. 복제를 허용하도록 ‘logical’로 변경하세요.** | 이 오류는 wal_level 매개 변수의 값이 잘못된 경우 발생합니다. 문제를 해결하려면 매개 변수 그룹의 rds.logical_replication을 1로 변경한 다음, 인스턴스를 다시 부팅합니다. 자세한 내용은 [DMS를 사용하여 Azure PostgreSQL로 마이그레이션하기 위한 필수 구성 요소](./tutorial-postgresql-azure-postgresql-online.md#prerequisites) 또는 [Amazon RDS의 PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)을 참조하세요. |

> [!NOTE]
> 원본 AWS RDS PostgreSQL 데이터베이스에 연결하는 것과 관련된 문제를 해결하는 방법에 관한 자세한 내용은 다음 리소스를 참조하세요.
> *    [Amazon RDS 연결 문제 해결](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> *    [Amazon RDS 데이터베이스 인스턴스 연결 문제를 해결하려면 어떻게 할까요?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

원본 AWS RDS SQL Server 데이터베이스에 연결하는 것과 관련된 잠재적인 문제 및 해결 방법에 관한 자세한 사항은 다음 표에 나와 있습니다.

| Error         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 53** - SQL 연결에 실패했습니다. SQL Server에 연결하는 중에 네트워크 관련 오류 또는 인스턴스별 오류가 발생했습니다. 서버를 찾을 수 없거나 서버에 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인하세요. (공급자: 명명된 파이프 공급자, 오류: 40 - SQL Server에 대한 연결을 열 수 없음) | 이 오류는 서비스가 원본 서버에 연결할 수 없는 경우 발생합니다. 문제를 해결하려면 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다. |
| **오류 18456** - 로그인에 실패했습니다. ‘{user}’ 사용자가 로그인에 실패함 | 이 오류는 서비스가 제공된 T-SQL 자격 증명을 사용하여 원본 데이터베이스에 연결할 수 없는 경우 발생합니다. 이 문제를 해결하려면 입력한 자격 증명을 확인합니다. [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)을 참조하거나 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다. |
| **오류 87** - 연결 문자열이 유효하지 않습니다. SQL Server에 연결하는 중에 네트워크 관련 오류 또는 인스턴스별 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인하세요. (공급자: SQL 네트워크 인터페이스, 오류: 25-연결 문자열이 유효하지 않음) | 이 오류는 잘못된 연결 문자열 때문에 서비스가 원본 서버에 연결할 수 없는 경우 발생합니다. 이 문제를 해결하려면 제공된 연결 문자열을 확인합니다. 문제가 지속되면 이 표 아래의 참고에 나열된 문제 해결 문서를 참조한 다음, 다시 시도합니다. |
| **오류 - 서버 인증서를 신뢰할 수 없습니다.** 서버에 성공적으로 연결되었지만 로그인 중 오류가 발생했습니다. (공급자: SSL 공급자, 오류: 0 - 인증서 체인이 신뢰할 수 없는 기관에서 발급되었습니다.) | 이 오류는 사용된 인증서를 신뢰할 수 없는 경우에 발생합니다. 문제를 해결하려면 신뢰할 수 있는 인증서를 찾은 다음, 서버에서 사용하도록 설정해야 합니다. 또는 연결하는 동안 신뢰 인증서 옵션을 선택할 수 있습니다. 이 작업은 사용된 인증서에 익숙하고 신뢰할 수 있는 경우에만 수행합니다. <br> 자체 서명된 인증서를 사용하여 암호화된 TLS 연결은 강력한 보안을 제공하지 않으며 중간자(man-in-the-middle) 공격에 취약합니다. 프로덕션 환경이나 인터넷에 연결된 서버에서는 자체 서명된 인증서를 사용한 TLS에 의존하지 마세요. <br> 자세한 내용은 [Microsoft SQL Server DB 인스턴스와 함께 SSL 사용](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) 또는 [자습서: DMS를 사용하여 RDS SQL Server를 Azure로 마이그레이션](./index.yml)을 참조하세요. |
| **오류 300** - 사용자에게 필요한 권한이 없습니다. ‘{server}’ 개체, ‘{database}’ 데이터베이스에서 VIEW SERVER STATE 권한이 거부됨 | 이 오류는 사용자에게 마이그레이션을 수행할 수 있는 권한이 없는 경우 발생합니다. 문제를 해결하려면 [GRANT 서버 권한 - Transact-SQL](/sql/t-sql/statements/grant-server-permissions-transact-sql) 또는 [자습서: DMS를 사용하여 RDS SQL Server를 Azure로 마이그레이션](./index.yml)을 참조하세요. |

> [!NOTE]
> 원본 AWS RDS SQL Server에 연결하는 것과 관련된 문제를 해결하는 방법에 관한 자세한 내용은 다음 리소스를 참조하세요.
>
> *    [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)(SQL Server에 대한 연결 오류 해결)
> * [Amazon RDS 데이터베이스 인스턴스 연결 문제를 해결하려면 어떻게 할까요?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>알려진 문제

* [Azure SQL Database로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항](./index.yml)
* [Azure Database for PostgreSQL로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>다음 단계

* [Azure Database Migration Service PowerShell](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0&preserve-view=true#data_migration)문서를 참조하세요.
* [Azure Portal을 사용하여 Azure Database for MySQL에서 서버 매개 변수 구성 방법](../mysql/howto-server-parameters.md) 문서를 참조하세요.
* [Azure Database Migration Service 사용을 위한 필수 구성 요소 개요](./pre-reqs.md) 문서를 참조하세요.
* [Azure Database Migration Service 사용에 대한 FAQ](./faq.yml)를 참조하세요.
