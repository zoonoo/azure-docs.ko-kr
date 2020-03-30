---
title: 일반적인 문제 - Azure 데이터베이스 마이그레이션 서비스
description: Azure 데이터베이스 마이그레이션 서비스 사용과 관련된 일반적인 알려진 문제/오류를 해결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649110"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>일반적인 Azure 데이터베이스 마이그레이션 서비스 문제 및 오류 문제 해결

이 문서에서는 Azure Database 마이그레이션 서비스 사용자가 발견할 수 있는 몇 가지 일반적인 문제와 오류를 설명합니다. 이 문서에는 이러한 문제 및 오류를 해결하는 방법에 대한 정보도 포함되어 있습니다.

## <a name="migration-activity-in-queued-state"></a>큐에 대기 된 상태의 마이그레이션 활동

Azure 데이터베이스 마이그레이션 서비스 프로젝트에서 새 활동을 만들 때 활동은 큐에 대기 된 상태로 유지됩니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 문제는 Azure Database 마이그레이션 서비스 인스턴스가 동시에 실행되는 진행 중인 작업에 대한 최대 용량에 도달하면 발생합니다. 용량을 사용할 수 있게 될 때까지 새 활동이 큐에 대기됩니다. | 데이터 마이그레이션 서비스 인스턴스가 프로젝트 전체에서 실행 중인 활동을 확인합니다. 실행을 위해 큐에 자동으로 추가되는 새 활동을 계속 만들 수 있습니다. 기존 실행 중인 활동이 완료되는 즉시 다음 큐에 대기중인 활동이 실행되기 시작하고 상태가 실행 중인 상태로 자동으로 변경됩니다. 큐에 대기된 활동의 마이그레이션을 시작하기 위해 추가 작업을 수행할 필요가 없습니다.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>마이그레이션을 위해 선택된 최대 데이터베이스 수

다음 오류는 Azure SQL Database 또는 Azure SQL Database 관리 인스턴스로 이동하기 위한 데이터베이스 마이그레이션 프로젝트에 대한 활동을 만들 때 발생합니다.

* **오류**: 마이그레이션 설정 유효성 검사 오류", "errorDetail":"마이그레이션을 위해 '데이터베이스'의 최대 숫자 '4' 개체이상이 선택되었습니다."

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 단일 마이그레이션 활동에 대해 4개 이상의 데이터베이스를 선택한 경우에 표시됩니다. 현재 각 마이그레이션 작업은 4개의 데이터베이스로 제한됩니다. | 마이그레이션 활동당 4개 이하의 데이터베이스를 선택합니다. 네 개 이상의 데이터베이스를 병렬로 마이그레이션해야 하는 경우 Azure 데이터베이스 마이그레이션 서비스의 다른 인스턴스를 프로비전합니다. 현재 각 구독은 최대 2개의 Azure 데이터베이스 마이그레이션 서비스 인스턴스를 지원합니다.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>복구 실패와 함께 Azure MySQL으로의 MySQL 마이그레이션 오류

Azure 데이터베이스 마이그레이션 서비스를 사용하여 MySQL에서 MySQL용 Azure 데이터베이스로 마이그레이션하면 다음 오류로 마이그레이션 작업이 실패합니다.

* **오류**: 데이터베이스 마이그레이션 오류 - [n] 연속 복구 오류로 인해 작업 'TaskID'가 일시 중단되었습니다.

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 마이그레이션을 수행하는 사용자가 ReplicationAdmin 역할 및/또는 복제 클라이언트, 복제 복제 및 SUPER(MySQL 5.6.6 이전 버전)의 권한이 없는 경우에 발생할 수 있습니다.<br><br><br><br><br><br><br><br><br><br><br><br><br> | 사용자 계정에 대한 [필수 권한은](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) MySQL 인스턴스용 Azure 데이터베이스에서 정확하게 구성되었는지 확인합니다. 예를 들어 다음 단계를 수행하여 필요한 권한이 있는 'migrateuser'라는 사용자를 만들 수 있습니다.<br>1. '비밀'로 식별된 사용자 migrateuser@%' 만들기 <br>2. db_name.*에 대한 모든 권한을 '비밀'로 식별된 '마이그레이션사용자'@'%'로 부여합니다. 이 단계를 반복하여 더 많은 데이터베이스에 대한 액세스 권한을 부여합니다. <br>3. 에 복제 슬레이브를 *부여합니다.* '비밀'로 식별된 '마이그레이션사용자'@'%'로;<br>4. 에 복제 클라이언트를 *부여합니다.* '비밀'로 식별된 '마이그레이션사용자'@'%'로;<br>5. 플러시 권한; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Azure 데이터베이스 마이그레이션 서비스를 중지하려고 할 때 오류

Azure 데이터베이스 마이그레이션 서비스 인스턴스를 중지할 때 다음과 같은 오류가 발생합니다.

* **오류**: 서비스가 중지되지 못했습니다. 오류: {'error':{'code':'InvalidRequest','message':'하나 이상의 작업이 현재 실행 중입니다. 서비스를 중지하려면 활동이 완료될 때까지 기다리거나 수동으로 해당 활동을 중지한 다음 다시 시도하십시오.'}}

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 중지하려는 서비스 인스턴스에 마이그레이션 프로젝트에 아직 실행 중이거나 현재 있는 활동이 포함될 때 표시됩니다. <br><br><br><br><br><br> | 중지하려는 Azure 데이터베이스 마이그레이션 서비스의 인스턴스에서 실행 중인 활동이 없는지 확인합니다. 서비스를 중지하기 전에 활동 이나 프로젝트를 삭제할 수도 있습니다. 다음 단계는 실행 중인 모든 작업을 삭제하여 마이그레이션 서비스 인스턴스를 정리하는 프로젝트를 제거하는 방법을 보여 줍니다.<br>1. 설치 모듈 -이름 AzureRM.DataMigration <br>2. 로그인-AzureRm계정 <br>3. 선택 -AzureRm구독 -구독 이름 "하위\<이름>" <br> 4. 제거-AzureRmDataMigrationProject-이름 \<프로젝트 이름> \<-ResourceGroupName rgName> -ServiceName \<서비스 이름> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Azure 데이터베이스 마이그레이션 서비스를 시작하려고 할 때 오류

Azure 데이터베이스 마이그레이션 서비스 인스턴스를 시작할 때 다음과 같은 오류가 발생합니다.

* **오류**: 서비스가 시작되지 않습니다. 오류: {'errorDetail':'서비스가 시작되지 않아 Microsoft 지원팀에 문의하십시오.}

| 원인         | 해결 방법 |
| ------------- | ------------- |
| 이 오류는 이전 인스턴스가 내부적으로 실패했을 때 표시됩니다. 이 오류는 거의 발생하지 않으며 엔지니어링 팀은 이를 알고 있습니다. <br> | 시작할 수 없는 서비스의 인스턴스를 삭제한 다음 새 인스턴스를 프로비전하여 대체합니다. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>AZURE SQL DB 관리 인스턴스로 SQL을 마이그레이션하는 동안 데이터베이스 복원 오류

SQL Server에서 Azure SQL Database 관리 인스턴스로의 온라인 마이그레이션을 수행하면 다음과 같은 오류로 인해 컷오버가 실패합니다.

* **오류**: Id 'operationId'에 대해 복원 작업이 실패했습니다. 코드 '권한 부여 실패', 개체 ID 'objectId'가 있는 클라이언트 'clientId'는 범위 '/구독/구독Id'를 통해 'Microsoft.Sql/위치/managedDatabaseRestoreAzureAsyncOperation/read' 작업을 수행할 수 있는 권한이 없습니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 오류는 SQL Server에서 Azure SQL Database 관리 인스턴스로의 온라인 마이그레이션에 사용되는 응용 프로그램 주체가 구독에 대한 기여 권한이 없는 것을 나타냅니다. 현재 관리형 인스턴스를 사용 하 여 특정 API 호출 복원 작업에 대 한 구독에 이 권한이 필요 합니다. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 오류 `Get-AzureADServicePrincipal` 메시지에서 `-ObjectId` 사용할 수 있는 PowerShell cmdlet을 사용하여 사용 중인 응용 프로그램 ID의 표시 이름을 나열합니다.<br><br> 이 응용 프로그램에 대한 사용 권한을 확인하고 구독 수준에서 [기여자 역할이](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 있는지 확인합니다. <br><br> Azure 데이터베이스 마이그레이션 서비스 엔지니어링 팀은 구독에 대한 현재 기여 역할에서 필요한 액세스를 제한하기 위해 노력하고 있습니다. 기여 역할 사용을 허용하지 않는 비즈니스 요구 사항이 있는 경우 Azure 지원에 문의하여 추가 도움을 요청하십시오. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Azure 데이터베이스 마이그레이션 서비스와 연결된 NIC를 삭제할 때 오류가 발생했습니다.

Azure 데이터베이스 마이그레이션 서비스와 연결된 네트워크 인터페이스 카드를 삭제하려고 하면 이 오류로 삭제 시도가 실패합니다.

* **오류**: NIC를 사용하는 DMS 서비스로 인해 Azure 데이터베이스 마이그레이션 서비스에 연결된 NIC를 삭제할 수 없습니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 문제는 Azure 데이터베이스 마이그레이션 서비스 인스턴스가 여전히 존재하고 NIC를 사용할 수 있는 경우에 발생합니다. <br><br><br><br><br><br><br><br> | 이 NIC를 삭제하려면 서비스에서 사용하는 NIC를 자동으로 삭제하는 DMS 서비스 인스턴스를 삭제합니다.<br><br> **중요**: 삭제되는 Azure 데이터베이스 마이그레이션 서비스 인스턴스에 실행 중인 활동이 없는지 확인합니다.<br><br> Azure Database 마이그레이션 서비스 인스턴스와 연결된 모든 프로젝트 및 활동이 삭제된 후 서비스 인스턴스를 삭제할 수 있습니다. 서비스 인스턴스에서 사용하는 NIC는 서비스 삭제의 일부로 자동으로 정리됩니다. |

## <a name="connection-error-when-using-expressroute"></a>ExpressRoute를 사용할 때 발생하는 연결 오류

Azure Database Migration 서비스 프로젝트 마법사에서 원본에 연결하려고 할 때 원본이 ExpressRoute를 연결에 사용하는 경우 긴 제한 시간이 지난 후 연결이 실패합니다.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| [ExpressRoute를](https://azure.microsoft.com/services/expressroute/)사용하는 경우 Azure 데이터베이스 마이그레이션 서비스는 서비스와 연결된 가상 네트워크 서브넷에서 세 개의 서비스 끝점을 [프로비전해야 합니다.](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)<br> - 서비스 버스 엔드포인트<br> -- 스토리지 엔드포인트<br> -- 대상 데이터베이스 끝점(예: SQL 끝점, 코스모스 DB 끝점)<br><br><br><br><br> | 원본과 Azure 데이터베이스 마이그레이션 서비스 간의 ExpressRoute 연결에 필요한 서비스 끝점을 [사용하도록 설정합니다.](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>MySQL에 대 한 Azure DB에 MySQL 데이터베이스를 마이그레이션할 때 대기 시간 시간 오류 잠금

Azure 데이터베이스 마이그레이션 서비스를 통해 MySQL 인스턴스에 대 한 Azure 데이터베이스로 MySQL 데이터베이스를 마이그레이션 하는 경우 다음 잠금 대기 시간 시간 오류와 함께 마이그레이션실패 합니다.

* **오류**: 데이터베이스 마이그레이션 오류 - 파일 로드 실패 - 파일 'n'RetCode SQL_ERROR에 대한 로드 프로세스를 시작하지 못했습니다. 트랜잭션을 다시 시작해 보십시오.

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 오류는 마이그레이션 하는 동안 잠금 대기 시간 때문에 마이그레이션에 실패 할 때 발생 합니다. | 서버 매개 변수 **'innodb_lock_wait_timeout'의**값을 늘리는 것이 좋습니다. 가장 높은 허용 값은 1073741824입니다. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>동적 포트 또는 명명된 인스턴스를 사용할 때 원본 SQL Server에 연결하는 오류

Azure 데이터베이스 마이그레이션 서비스를 명명된 인스턴스 또는 동적 포트에서 실행되는 SQL Server 원본에 연결하려고 하면 이 오류로 연결이 실패합니다.

* **오류**: -1 - SQL 연결이 실패했습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바르고 SQL Server가 원격 연결을 허용하도록 구성되어 있는지 확인하십시오. (공급자: SQL 네트워크 인터페이스, 오류: 26-지정된 서버/인스턴스를 찾는 동안 오류가 발생했습니다)

| 원인         | 해결 방법    |
| ------------- | ------------- |
| 이 문제는 Azure Database 마이그레이션 서비스에서 연결하려고 시도하는 원본 SQL Server 인스턴스에 동적 포트가 있거나 명명된 인스턴스를 사용하는 경우에 발생합니다. SQL Server 브라우저 서비스는 UDP 포트 1434를 수신하여 명명된 인스턴스에 대한 연결을 수신하거나 동적 포트를 사용하는 경우 수신합니다. 동적 포트는 SQL Server 서비스를 다시 시작할 때마다 변경될 수 있습니다. SQL Server 구성 관리자의 네트워크 구성을 통해 인스턴스에 할당된 동적 포트를 확인할 수 있습니다.<br><br><br> |Azure 데이터베이스 마이그레이션 서비스가 UDP 포트 1434의 원본 SQL Server 브라우저 서비스와 동적으로 할당된 TCP 포트를 통해 SQL Server 인스턴스에 연결할 수 있는지 확인합니다. |

## <a name="additional-known-issues"></a>알려진 추가 문제

* [Azure SQL Database로의 온라인 마이그레이션으로 알려진 문제/마이그레이션 제한](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [MySQL용 Azure 데이터베이스로의 온라인 마이그레이션으로 알려진 문제/마이그레이션 제한](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [PostgreSQL용 Azure 데이터베이스로의 온라인 마이그레이션으로 알려진 문제/마이그레이션 제한](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>다음 단계

* [문서 보기 Azure 데이터베이스 마이그레이션 서비스 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* 문서 보기 [Azure 포털을 사용 하 여 MySQL에 대 한 Azure 데이터베이스에서 서버 매개 변수를 구성 하는 방법](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* [Azure 데이터베이스 마이그레이션 서비스를 사용하기 위한 필수 구성 조건의](https://docs.microsoft.com/azure/dms/pre-reqs)문서 개요를 봅니다.
* Azure [데이터베이스 마이그레이션 서비스 사용에 대한 FAQ를](https://docs.microsoft.com/azure/dms/faq)참조하십시오.
