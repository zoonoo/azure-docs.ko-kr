---
title: 알려진 Azure Database Migration Service 사용과 관련 된 일반적인 문제/오류 문제 해결에 대 한 문서 | Microsoft Docs
description: 일반적인 알려진된 문제/오류 Azure Database Migration Service 사용과 관련 된 문제를 해결 하는 방법을 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: 1d639a8b1d5c7a5dd2b7bac7c5e020be7c8b1c50
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190953"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>일반적인 Azure Database Migration Service 문제 및 오류 문제 해결

이 문서에서는 몇 가지 일반적인 문제 및 Azure Database Migration Service 사용자 나올 수 있는 오류를 설명 합니다. 문서에는 이러한 문제 및 오류를 해결 하는 방법에 대 한 정보도 포함 됩니다.

## <a name="migration-activity-in-queued-state"></a>큐에 대기 중인된 상태에서 마이그레이션 작업

Azure Database Migration Service 프로젝트에서 새 활동을 만들 때 작업을 큐에 대기 중인된 상태로 유지 됩니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 문제는 Azure Database Migration Service 인스턴스가 동시에 실행 하는 진행 중인 작업에 대 한 최대 용량에 도달한 경우 발생 합니다. 새 작업에는 용량 사용 가능할 때까지 대기 중입니다. | 프로젝트에서 활동을 실행 하는 것이 인스턴스마다 데이터 마이그레이션 서비스의 유효성을 검사 합니다. 실행에 대 한 큐에 자동으로 추가 된 새 활동을 만드는 계속 수 있습니다. 기존 실행 중인 작업을 완료 하는 즉시 다음 큐에 대기 중인된 작업 실행을 시작 하 고 상태 변경 상태를 자동으로 실행 합니다. 큐에 대기 중인된 작업의 마이그레이션을 시작 하려면 추가 조치를 취할 필요가 없습니다.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>마이그레이션을 위해 선택한 데이터베이스의 최대 수

관리 되는 인스턴스를 Azure SQL Database 또는 Azure SQL Database로 데이터베이스 마이그레이션 프로젝트에 대 한 활동을 작성 하는 다음 오류가 발생 합니다.

* **오류**: 마이그레이션 설정 유효성 검사 오류","errorDetail":"이상 최대 수 '4' '데이터베이스' 개체 선정 된 마이그레이션에 대 한 합니다."

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 단일 마이그레이션 작업에 대 한 4 개 이상의 데이터베이스를 선택한 경우 표시 됩니다. 현재, 각 마이그레이션 작업은 4 개의 데이터베이스가 제한 됩니다. | 마이그레이션 활동 당 4 개 이하의 데이터베이스를 선택 합니다. 동시에 4 개 이상의 데이터베이스를 마이그레이션하는 경우 Azure Database Migration Service의 다른 인스턴스를 프로 비전 합니다. 현재 각 구독은 최대 2 개의 Azure Database Migration Service 인스턴스를 지원합니다.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>복구 실패를 사용 하 여 Azure MySQL로 MySQL 마이그레이션 오류

를 마이그레이션하는 경우 MySQL에서 Azure Database를 Azure Database Migration Service를 사용 하 여 MySQL에 대 한 마이그레이션 작업 다음 오류로 인해 실패 합니다.

* **오류**: 데이터베이스 마이그레이션 오류-'TaskID' 작업 [n] 연속 복구 오류로 인해 일시 중단 되었습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 마이그레이션을 수행 하는 사용자에 게 ReplicationAdmin 역할과 및/또는 REPLICATION CLIENT, 복제 복제본 및 슈퍼 (MySQL 5.6.6 이전 버전)의 권한이 없으면이 오류가 발생할 수 있습니다.<br><br><br><br><br><br><br><br><br><br><br><br><br> | 있는지 확인 합니다 [필수 권한](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) 사용자 계정에 구성 된 정확 하 게 Azure Database for MySQL 인스턴스. 예를 들어 라는 'migrateuser' 필요한 권한이 있는 사용자를 만들려면 다음 단계를 따라야 수 있습니다.<br>1. CREATE USER migrateuser@'%' 식별 하 여 'secret'; <br>2. 'Migrateuser'@'%' 'secret';으로 식별 하려면 db_name.*에 모든 권한을 부여 합니다. 더 많은 데이터베이스에 대 한 액세스 권한을 부여 하려면이 단계를 반복 합니다. <br>3. 권한 부여 복제 슬레이브 *합니다.* 'migrateuser'@'%' 'secret';으로 식별 하려면<br>4. 권한 부여 복제 클라이언트 *합니다.* 'migrateuser'@'%' 'secret';으로 식별 하려면<br>5. 플러시 권한이 있습니다. |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Azure Database Migration Service를 중지 하려고 하는 동안 오류가 발생 했습니다.

Azure Database Migration Service 인스턴스를 중지 하는 경우 다음 오류가 나타납니다.

* **오류**: 서비스 중지 하지 못했습니다. 오류: {'error': {'code': 'InvalidRequest', 'message':' 하나 이상의 작업이 현재 실행 중인 합니다. 서비스를 중지 하려면 작업 완료 또는 해당 작업을 수동으로 중지 및 다시 시도 될 때까지 기다립니다.'}}

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 마이그레이션 프로젝트에서 여전히 실행 중인지 또는 제공 하는 활동을 포함 하는 서비스 인스턴스를 중지 하려고 할 때 표시 됩니다. <br><br><br><br><br><br> | Azure Database Migration Service를 중지 하려는 인스턴스의 실행 동작이 없는 되는지 확인 합니다. 또한 서비스를 중지 하기 전에 작업 또는 프로젝트를 삭제할 수 있습니다. 다음 단계를 실행 중인 모든 작업을 삭제 하 여 migration service 인스턴스를 정리 하는 프로젝트를 제거 하는 방법을 보여 줍니다.<br>1. Install-module-AzureRM.DataMigration 이름 <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Azure Database Migration Service를 시작 하려고 하는 동안 오류가 발생 했습니다.

Azure Database Migration Service 인스턴스를 시작 하는 경우 다음 오류가 나타납니다.

* **오류**: 서비스가 시작 되지 않습니다. 오류: {'errorDetail': "서비스가 실패 했습니다 시작 하려면 Microsoft 지원에 문의 하세요"}

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 이전 인스턴스는 내부적으로 실패 한 경우에 표시 됩니다. 이 오류는 드물게 발생 하 고 엔지니어링 팀은 인식 합니다. <br> | 없습니다를 시작 하는 다음 바꾸거나 새로 프로 비전 서비스의 인스턴스를 삭제 합니다. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Azure SQL DB에 마이그레이션 SQL 인스턴스를 관리 하는 동안 데이터베이스를 복원 하는 오류

온라인 마이그레이션 인 SQL Server에서 Azure SQL Database 관리 되는 인스턴스를 수행 하면 컷 오버 실패 다음 오류가 발생 합니다.

* **오류**: 복원 작업 Id 'operationId' 작업에 실패 했습니다. 메시지 코드 'AuthorizationFailed', ' 'clientId' 개체 id가 'objectId' 인 클라이언트 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' 범위에 대해 작업을 수행할 수 없는 ' /subscriptions/ subscriptionId'. '.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 오류는 Azure SQL Database 관리 되는 인스턴스로 SQL Server에서 온라인 마이그레이션에 사용 되는 응용 프로그램 보안 주체는 구독에 대 한 권한이 참가 하지 않습니다 나타냅니다. 현재 관리 되는 인스턴스를 사용 하 여 특정 API 호출 복원 작업에 대 한 구독에 대 한이 권한이 필요합니다. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 사용 된 `Get-AzureADServicePrincipal` PowerShell cmdlet `-ObjectId` 오류 메시지를 사용 하 고 응용 프로그램 ID의 표시 이름 목록에서 사용할 수 있습니다.<br><br> 이 응용 프로그램에 사용 권한을 확인 하 고 확인 합니다 [참가자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 구독 수준입니다. <br><br> Azure Database Migration Service 엔지니어링 팀은 필요한 제한 하기 액세스에서 현재 구독에 역할 기여 합니다. 사용을 허용 하지 않는 비즈니스 요구 사항이 있는 경우 역할 참가, 추가 도움말에 대 한 Azure 지원에 문의 합니다. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Azure Database Migration Service와 연결 된 NIC를 삭제 하는 동안 오류가 발생 했습니다.

Azure Database Migration Service와 연결 된 네트워크 인터페이스 카드를 삭제 하려고 할 때이 오류와 함께 삭제 시도가 실패 합니다.

* **오류**: DMS 서비스가 NIC를 활용 하 여 Azure Database Migration Service에 연결 된 NIC를 삭제할 수 없습니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| Azure Database Migration Service 인스턴스가 여전히 존재할 수 있습니다 하는 경우이 문제가 발생 하 고 NIC를 사용 합니다. <br><br><br><br><br><br><br><br> | 이 NIC를 삭제 하려면 자동으로 서비스에서 사용 되는 NIC를 삭제 하는 DMS 서비스 인스턴스를 삭제 합니다.<br><br> **중요**: 실행 중인 활동이 없습니다. Azure Database Migration Service 인스턴스를 삭제 해야 합니다.<br><br> 모든 프로젝트 및 Azure Database Migration Service 인스턴스에 연결 된 작업 삭제 된 후에 서비스 인스턴스를 삭제할 수 있습니다. 서비스 인스턴스에서 사용 되는 NIC 서비스 삭제의 일환으로 자동으로 정리 됩니다. |

## <a name="connection-error-when-using-expressroute"></a>ExpressRoute를 사용 하는 경우 연결 오류

Azure Database Migration service 프로젝트 마법사에서 원본에 연결 하려고 할 때 원본 연결에 대 한 ExpressRoute를 사용 하는 경우 장기적인된 제한 시간이 지난 후 연결 실패 합니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 사용 하는 경우 [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [필요](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) 서비스에 연결 된 가상 네트워크 서브넷에 세 개의 서비스 끝점을 프로 비전 합니다.<br> -Service Bus 끝점<br> -저장소 끝점<br> -대상 데이터베이스 끝점 (예: SQL 끝점, Cosmos DB 끝점)<br><br><br><br><br> | [사용 하도록 설정](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) 원본 및 Azure Database Migration Service 간의 ExpressRoute 연결에 대 한 필수 서비스 끝점입니다. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Azure MySQL로 MySQL 데이터베이스를 마이그레이션할 때 시간 초과 오류

Azure Database Migration Service를 통해 MySQL 인스턴스에 대 한 Azure Database를 MySQL 데이터베이스를 마이그레이션하면 마이그레이션 제한 시간 오류로 실패 합니다.

* **오류**: 데이터베이스 마이그레이션 오류-파일을 로드 하지 못했습니다 파일에 대 한 로드 프로세스를 시작 하지 못했습니다 ' n ' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 메시지: [MySQL] [Odbc], [mysqld] 잠금 대기 시간이 초과 되었습니다. 트랜잭션 다시 시도 하세요.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 오류는 마이그레이션하는 동안 잠금 대기 시간 초과 인해 마이그레이션이 실패 하면 발생 합니다. | 서버 매개 변수 값을 늘리는 것이 좋습니다 **'innodb_lock_wait_timeout'** 합니다. 최대 허용된 값은 1073741824 합니다. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>동적 포트를 사용 하는 경우 원본 SQL Server에 연결 하거나 명명 된 인스턴스 오류

Azure Database Migration Service 명명 된 인스턴스 또는 동적 포트에서 실행 되는 SQL Server 원본에 연결 하려고 할 때이 오류를 사용 하 여 연결 실패 합니다.

* **오류**:-1-SQL 연결에 실패 했습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server 원격 연결을 허용 하도록 구성 되어 있는지 확인 합니다. (공급자: SQL 네트워크 인터페이스, 오류: 26-지정 된 서버/인스턴스 찾기 오류)

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 문제는 Azure Database Migration Service에 연결 하려고 하는 원본 SQL Server 인스턴스는 동적 포트가 또는 명명된 된 인스턴스를 사용 하는 경우에 발생 합니다. SQL Server Browser 서비스를 동적 포트를 사용 하는 경우 명명된 된 인스턴스 또는 들어오는 연결에 대 한 UDP 포트 1434로 수신 합니다. 동적 포트에는 SQL Server 서비스가 다시 시작 될 때마다를 변경 될 수 있습니다. SQL Server 구성 관리자에서 네트워크 구성을 통해 인스턴스에 할당 된 동적 포트를 확인할 수 있습니다.<br><br><br> |Azure Database Migration Service 원본 UDP 포트 1434에서 SQL Server Browser 서비스 및 해당 하는 경우 동적으로 할당 된 TCP 포트를 통해 SQL Server 인스턴스를 연결할 수 있는지 확인 합니다. |

## <a name="additional-known-issues"></a>기타 알려진된 문제

* [Azure SQL Database로 온라인 마이그레이션 사용 하 여 알려진된 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [MySQL 용 Azure Database로 온라인 마이그레이션 사용 하 여 알려진된 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [PostgreSQL 용 Azure Database로 온라인 마이그레이션 사용 하 여 알려진된 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>다음 단계

* 문서를 볼 [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)합니다.
* 문서를 볼 [Azure portal을 사용 하 여 MySQL 용 Azure Database에서 서버 매개 변수를 구성 하는 방법을](https://docs.microsoft.com/azure/mysql/howto-server-parameters)합니다.
* 문서를 볼 [Azure Database Migration Service 사용에 대 한 필수 구성 요소 개요](https://docs.microsoft.com/azure/dms/pre-reqs)합니다.
* 참조 된 [Azure Database Migration Service 사용에 대 한 FAQ](https://docs.microsoft.com/azure/dms/faq)합니다.
