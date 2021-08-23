---
title: 일반적인 문제 - Azure Database Migration Service
description: Azure Database Migration Service 사용에 관련된 일반적으로 알려진 문제/오류를 해결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: e5c10b4830a1bba5ff4db07b81ee447e5d33b731
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537046"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>일반적인 Azure Database Migration Service 문제 및 오류 해결

이 문서에서는 Azure Database Migration Service 사용자가 겪을 수 있는 몇 가지 일반적인 문제와 오류에 대해 설명합니다. 이 문서에는 이러한 문제와 오류를 해결하는 방법에 대한 정보도 포함되어 있습니다.

> [!NOTE]
> 바이어스 없는 통신
>
> Microsoft는 다양하고 포용적인 환경을 지원합니다. 이 문서에는 _slave(슬레이브)_ 라는 단어에 대한 참조가 포함되어 있습니다. [바이어스 없는 통신을 위한 Microsoft 스타일 가이드](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)에서는 이 단어를 '배제(exclusionary)'라는 단어로 인식합니다. 이 단어는 현재 소프트웨어에 표시되는 단어이므로 일관성을 위해 이 문서에서 사용됩니다. 이 단어를 제거하도록 소프트웨어가 업데이트되면 이 문서도 이에 맞춰 업데이트됩니다.
>

## <a name="migration-activity-in-queued-state"></a>큐 대기 상태에서의 마이그레이션 작업

Azure Database Migration Service 프로젝트에서 새 작업을 만들 때 작업이 큐 대기 상태로 유지됩니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 문제는 Azure Database Migration Service 인스턴스가 현재 동시 실행되는 진행 중인 작업 용량의 최대치에 도달했을 때 발생합니다. 용량을 사용할 수 있게 될 때까지 새 작업은 모두 큐에 대기하게 됩니다. | Data Migration Service 인스턴스가 프로젝트 간에 작업을 실행하고 있는지 확인합니다. 실행을 위해 큐에 자동으로 추가되는 새 작업을 계속 만들 수 있습니다. 실행 중인 기존 작업이 완료되면 바로 다음에 큐 대기 중인 작업이 실행되기 시작하고 상태가 자동으로 실행 상태로 변경됩니다. 큐 대기 중인 작업의 마이그레이션을 시작하려고 추가 작업을 수행할 필요는 없습니다.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>마이그레이션 목적으로 선택한 데이터베이스의 최대 수

Azure SQL Database 또는 Azure SQL Managed Instance로 이동하기 위해 데이터베이스 마이그레이션 프로젝트에 작업을 만들 때 다음과 같은 오류가 발생합니다.

* **오류**: 마이그레이션 설정 유효성 검사 오류”, “errorDetail”:“‘데이터베이스’의 최대 개수 ‘4’개 이상의 개체를 마이그레이션하도록 선택했습니다.”

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 단일 마이그레이션 작업에 데이터베이스를 4개 이상 선택한 경우에 표시됩니다. 현재 각 마이그레이션 작업은 4개의 데이터베이스로 제한됩니다. | 각 마이그레이션 작업마다 4개 이하의 데이터베이스를 선택합니다. 4개 이상의 데이터베이스를 병렬로 마이그레이션해야 하는 경우 Azure Database Migration Service의 다른 인스턴스를 프로비저닝해야 합니다. 현재 각 구독은 최대 2개의 Azure Database Migration Service 인스턴스를 지원합니다.<br><br> |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Azure Database Migration Service를 중지하려 할 때 오류 발생

Azure Database Migration Service 인스턴스를 중지하면 다음과 같은 오류가 표시됩니다.

* **오류**: 서비스를 중지하지 못했습니다. 오류: {'error':{'code':'InvalidRequest','message':'하나 이상의 작업이 현재 실행 중입니다. 서비스를 중지하려면 작업이 완료될 때까지 기다리거나 해당 작업을 수동으로 중지한 후 다시 시도하십시오.'}}

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 중지하려는 서비스 인스턴스에 마이그레이션 프로젝트에서 아직 실행 중이거나 존재하는 작업이 포함되어 있을 때 표시됩니다. <br><br><br><br><br><br> | 중지하려는 Azure Database Migration Service 인스턴스에서 실행 중인 작업이 없는지 확인합니다. 서비스를 중지하기 전에 작업이나 프로젝트를 삭제할 수도 있습니다. 다음 단계에서는 실행 중인 모든 작업을 삭제하여 마이그레이션 서비스 인스턴스를 정리하기 위해 프로젝트를 제거하는 방법을 설명합니다.<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "\<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Azure Database Migration Service를 시작하려 할 때 오류 발생

Azure Database Migration Service 인스턴스를 시작할 때 다음과 같은 오류가 표시됩니다.

* **오류**: 서비스를 시작하지 못했습니다. 오류: {‘errorDetail’:‘서비스를 시작하지 못했습니다. Microsoft 지원에 문의하세요.’}

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 이전 인스턴스가 내부적으로 실패한 경우 표시됩니다. 이 오류는 거의 발생하지 않으며 엔지니어링 팀이 이를 인식하고 있습니다. <br> | 시작할 수 없는 서비스 인스턴스를 삭제한 다음 새 서비스를 프로비저닝하여 바꿉니다. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>SQL을 Azure SQL DB 관리되는 인스턴스로 마이그레이션하는 동안 데이터베이스 복원 오류 발생

SQL Server에서 Azure SQL Managed Instance로 온라인 마이그레이션을 수행하는 경우 다음 오류가 발생하여 컷오버가 실패합니다.

* **오류**: 작업 ID ‘operationId’의 복원 작업에 실패했습니다. 코드 ‘AuthorizationFailed’, 메시지 ‘개체 ID가 ‘objectId’인 클라언트 ‘clientId’가 ‘/subscriptions/subscriptionId’ 범위에 대해 ‘Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read’ 작업을 수행할 권한이 없습니다.’.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 오류는 SQL Server에서 SQL Managed Instance로의 온라인 마이그레이션에 사용되는 애플리케이션 보안 주체가 구독에 대한 참가 권한이 없음을 나타냅니다. 현재 Managed Instance가 있는 특정 API 호출에는 복원 작업에 대한 구독 권한이 필요합니다. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 오류 메시지에서 사용할 수 있는 `Get-AzureADServicePrincipal` PowerShell cmdlet을 `-ObjectId`와 함께 사용하여 사용 중인 애플리케이션 ID의 표시 이름을 나열합니다.<br><br> 이 애플리케이션에 대한 사용 권한 유효성을 검사하고 구독 수준에서 [참가자 역할](../role-based-access-control/built-in-roles.md#contributor)이 있는지 확인합니다. <br><br> Azure Database Migration Service 엔지니어링 팀이 구독의 현재 참가자 역할에 필요한 액세스를 제한하기 위해 작업 중입니다. 참가자 역할을 사용할 수 없는 비즈니스 요구 사항이 있는 경우 Azure 지원에 문의하여 추가 도움을 요청하세요. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Azure Database Migration Service와 연결된 NIC를 삭제하는 동안 오류 발생

Azure Database Migration Service와 연결된 NIC(네트워크 인터페이스 카드)를 삭제하려고 하면 다음 오류와 함께 삭제 시도가 실패합니다.

* **오류**: NIC를 활용하는 DMS 서비스로 인해 Azure Database Migration Service에 연결된 NIC를 삭제할 수 없습니다

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 문제는 Azure Database Migration Service 인스턴스가 계속 존재해서 NIC를 사용하고 있을 때 발생합니다. <br><br><br><br><br><br><br><br> | 이 NIC를 삭제하려면 서비스에서 사용하는 NIC를 자동으로 삭제하는 DMS 서비스 인스턴스를 삭제합니다.<br><br> **주의**: 삭제 중인 Azure Database Migration Service 인스턴스에 실행 중인 작업이 없는지 확인합니다.<br><br> Azure Database Migration Service 인스턴스에 연결된 모든 프로젝트와 활동이 삭제된 후에는 서비스 인스턴스를 삭제할 수 있습니다. 서비스 인스턴스에서 사용하는 NIC는 서비스 삭제 과정에서 자동으로 정리됩니다. |

## <a name="connection-error-when-using-expressroute"></a>ExpressRoute를 사용할 때 발생하는 연결 오류

Azure Database Migration 서비스 프로젝트 마법사에서 원본에 연결하려고 할 때 원본이 ExpressRoute를 연결에 사용하는 경우 긴 제한 시간이 지난 후 연결이 실패합니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 사용할 경우 Azure Database Migration Service는 서비스와 연결된 Virtual Network 서브넷에서 3개의 서비스 엔드포인트를 프로비저닝 [해야](./tutorial-sql-server-to-azure-sql.md) 합니다.<br> -- Service Bus 엔드포인트<br> -- 스토리지 엔드포인트<br> -- 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트)<br><br><br><br><br> | 원본과 Azure Database Migration Service 사이의 ExpressRoute 연결에 필요한 서비스 엔드포인트를 [사용하도록 설정](./tutorial-sql-server-to-azure-sql.md)합니다. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Azure DB for MySQL로 MySQL 데이터베이스를 마이그레이션할 때 잠금 대기 시간 초과 오류가 발생합니다

Azure Database Migration Service를 통해 MySQL 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션하면 다음과 같은 잠금 대기 시간 초과 오류가 발생하여 마이그레이션이 실패합니다.

* **오류**: 데이터베이스 마이그레이션 오류 - 파일을 로드하지 못했습니다 - 파일 ‘n’에 대한 로드 프로세스를 시작하지 못했습니다 RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 메시지: [MySQL][ODBC Driver][mysqld] 잠금 대기 시간이 초과되었습니다; 트랜잭션을 다시 시도하십시오

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 오류는 마이그레이션하는 동안 잠금 대기 시간 제한으로 인해 마이그레이션이 실패할 때 발생합니다. | 서버 매개 변수 **‘innodb_lock_wait_timeout’** 의 값을 늘립니다. 허용되는 가장 높은 값은 1073741824입니다. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>동적 포트 또는 명명된 인스턴스를 사용하는 경우 원본 SQL Server에 연결할 때 오류 발생

명명된 인스턴스나 동적 포트 중 하나에서 실행되는 SQL Server 원본에 Azure Database Migration Service를 연결하려고 하면 다음 오류가 발생하며 연결이 실패합니다.

* **오류**: -1 - SQL 연결에 실패했습니다. SQL Server에 연결하는 중에 네트워크 관련 오류 또는 인스턴스별 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인합니다. (공급자: SQL 네트워크 인터페이스, 오류: 26 - 지정된 서버/인스턴스 찾기 오류)

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 문제는 Azure Database Migration Service에 연결하고자 하는 원본 SQL Server 인스턴스가 동적 포트를 가지고 있거나 명명된 인스턴스를 사용하는 경우에 발생합니다. SQL Server Browser 서비스는 명명된 인스턴스에 들어오는 연결 또는 동적 포트를 사용할 때 UDP 포트 1434를 수신합니다. SQL Server 서비스가 다시 시작될 때마다 동적 포트가 변경될 수 있습니다. SQL Server 구성 관리자의 네트워크 구성을 통해 인스턴스에 할당된 동적 포트를 확인할 수 있습니다.<br><br><br> |Azure Database Migration Service가 UDP 포트 1434에서 원본 SQL Server Browser 서비스에 연결되는지, 또 해당되는 경우 동적 할당된 TCP 포트를 통해 SQL Server 인스턴스에 연결이 가능한지 확인하세요. |

## <a name="additional-known-issues"></a>추가적으로 알려진 문제

* [Azure SQL Database로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항](./index.yml)
* [Azure Database for PostgreSQL로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>다음 단계

* [Azure Database Migration Service PowerShell](/powershell/module/azurerm.datamigration#data_migration)문서를 참조하세요.
* [Azure Portal을 사용하여 Azure Database for MySQL에서 서버 매개 변수 구성 방법](../mysql/howto-server-parameters.md) 문서를 참조하세요.
* [Azure Database Migration Service 사용을 위한 필수 구성 요소 개요](./pre-reqs.md) 문서를 참조하세요.
* [Azure Database Migration Service 사용에 대한 FAQ](./faq.yml)를 참조하세요.