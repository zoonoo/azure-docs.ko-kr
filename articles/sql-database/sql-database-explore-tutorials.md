---        
title: "Azure SQL Database 자습서 탐색 | Microsoft Docs"
description: "SQL 데이터베이스 기능에 대해 알아보기"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 04c0fd7f-d260-4e43-a4f0-41cdcd5e3786
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 9b09383350284c8f1cd3e384c802c25c962b1226
ms.openlocfilehash: 1fe15e7ad3667d42995cd487c793fae496216fca
ms.lasthandoff: 02/16/2017

        
---
 
# <a name="explore-azure-sql-database-tutorials"></a>Azure SQL 데이터베이스 자습서 탐색
아래 테이블의 링크를 통해 나열된 각 기능 영역의 개요 및 각 영역에 대한 간단한 단계별 시작 자습서로 이동합니다. 

## <a name="create-servers-databases-and-server-level-firewall-rules"></a>서버, 데이터베이스 및 서버 수준 방화벽 규칙 만들기
다음 자습서에서는 서버, 데이터베이스 및 서버 수준 방화벽 규칙을 만들어 서버와 데이터베이스를 연결하고 쿼리하는 방법을 알아봅니다.

| 자습서 | 설명 |
| --- | --- | 
| [첫 Azure SQL Database](sql-database-get-started.md) | 이 빠른 시작 자습서를 완료하면 샘플 데이터베이스와 빈 데이터베이스가 Azure 리소스 그룹에서 실행되며 논리 서버에 연결됩니다. 또한 지정된 두 개의 IP 주소에서 서버 수준 보안 주체가 서버에 로그인할 수 있도록 구성된 두 개의 서버 수준 방화벽 규칙도 갖게 됩니다. 마지막으로, Azure Portal에서 데이터베이스를 쿼리하는 방법과 SQL Server Management Studio를 사용하여 연결하고 쿼리하는 방법을 알아봅니다. |
| [PowerShell을 사용하여 Azure SQL Database 프로비전 및 액세스](sql-database-get-started-powershell.md) | 이 자습서를 완료하면 샘플 데이터베이스와 빈 데이터베이스가 Azure 리소스 그룹에서 실행되며 논리 서버에 연결됩니다. 또한 서버 수준 보안 주체가 지정된 IP 주소(또는 IP 주소 범위)에서 서버에 로그인할 수 있도록 구성된 서버 수준 방화벽 규칙을 갖게 됩니다. |
| [C#을 사용하여 .NET용 SQL Database 라이브러리로 SQL Database 만들기](sql-database-get-started-csharp.md)| 이 자습서에서는 C#을 사용하여 SQL Database 서버, 방화벽 규칙 및 SQL Database를 만듭니다. C# 응용 프로그램을 인증하는 데 필요한 Active Directory(AD) 응용 프로그램 및 서비스 주체를 만들 수도 있습니다. |
|  | |

## <a name="backups-long-term-retention-and-database-recovery"></a>백업, 장기 보존 및 데이터베이스 복구
다음 자습서에서는 [데이터베이스 백업](sql-database-automated-backups.md), [장기 백업 보존](sql-database-long-term-retention.md) 및 [백업을 사용한 데이터베이스 복구](sql-database-recovery-using-backups.md) 사용법에 대해 알아봅니다.

| 자습서 | 설명 |
| --- | --- | 
| [Azure Portal을 사용하여 백업 및 복원](sql-database-get-started-backup-recovery-portal.md) | 이 자습서에서는 Azure Portal을 사용하여 백업을 확인하고, 특정 시점으로 복구하고, 장기 백업 보존을 구성하며, Azure Recovery Services 자격 증명 모음의 백업으로부터 복구하는 방법을 알아봅니다.
| [PowerShell을 사용하여 백업 및 복원](sql-database-get-started-backup-recovery-powershell.md) | 이 자습서에서는 PowerShell을 사용하여 백업을 확인하고, 특정 시점으로 복구하고, 장기 백업 보존을 구성하며, Azure Recovery Services 자격 증명 모음의 백업으로부터 복구하는 방법을 알아봅니다.
|  | |

## <a name="sharded-databases"></a>분할된 데이터베이스
다음 자습서에서는 [분할된 데이터베이스 맵 관리자를 사용하여 데이터베이스를 확장](sql-database-elastic-scale-shard-map-management.md)하는 방법을 알아봅니다.

| 자습서 | 설명 |
| --- | --- | 
| [분할된 데이터베이스 응용 프로그램 만들기](sql-database-elastic-scale-get-started.md) |이 자습서에서는 간단한 분할된 응용 프로그램을 사용하여 탄력적 데이터베이스 도구의 기능을 사용하는 방법을 알아봅니다. |
| [분할/병합 서비스 배포](sql-database-elastic-scale-configure-deploy-split-and-merge.md) |이 자습서에서는 분할된 데이터베이스 간에 데이터를 이동하는 방법을 알아봅니다. |
|  | |

## <a name="elastic-database-jobs"></a>탄력적 데이터베이스 작업
다음 자습서에서는 [탄력적 데이터베이스 작업](sql-database-elastic-jobs-overview.md)을 사용하는 방법을 알아봅니다.

| 자습서 | 설명 |
| --- | --- | 
| [Azure SQL 데이터베이스 탄력적 작업 시작](sql-database-elastic-jobs-getting-started.md) |이 자습서에서는 관계형 데이터베이스 그룹을 관리하는 작업을 만들고 관리하는 방법을 알아봅니다. |
|  | |

## <a name="elastic-queries"></a>탄력적 쿼리
다음 자습서에서는 [탄력적 쿼리](sql-database-elastic-query-overview.md)를 실행하는 방법을 알아봅니다.

| 자습서 | 설명 |
| --- | --- | 
| [탄력적 쿼리 만들기)](sql-database-elastic-query-getting-started-vertical.md) | 이 자습서에서는 단일 연결 지점을 사용하여 여러 데이터베이스를 확장하는 T-SQL 쿼리를 실행하는 방법을 알아봅니다. |
| [규모가 확장된 클라우드 데이터베이스에 대한 보고](sql-database-elastic-query-getting-started.md) |이 자습서에서는 수평 분할된(분할) 데이터베이스의 모든 데이터베이스에서 보고서를 만드는 방법을 알아봅니다. |
| [여러 스키마를 사용하여 클라우드 데이터베이스에서 쿼리](sql-database-elastic-query-vertical-partitioning.md) | 이 자습서에서는 여러 스키마를 사용하여 여러 데이터베이스를 확장하는 T-SQL 쿼리를 실행하는 방법을 알아봅니다 |
| [확장된 클라우드 데이터베이스에서 보고](sql-database-elastic-query-horizontal-partitioning.md) |이 자습서에서는 분할된 데이터베이스의 모든 데이터베이스를 망라하는 보고서를 만드는 방법을 알아봅니다. |
|  | |

## <a name="database-authentication-and-authorization"></a>데이터베이스 인증 및 권한 부여
다음 자습서에서는 [로그인 및 사용자 만들기 및 관리](sql-database-manage-logins.md)에 대해 알아봅니다.

| 자습서 | 설명 |
| --- | --- | --- |
| [SQL 인증 및 권한 부여](sql-database-control-access-sql-authentication-get-started.md) | 이 자습서에서는 SQL Server 인증을 사용하여 로그인과 사용자를 만들고 역할에 추가하고 권한을 부여하는 방법에 대해 알아봅니다. |
| [Azure AD 인증 및 권한 부여](sql-database-control-access-aad-authentication-get-started.md) | 이 자습서에서는 Azure Active Directory 인증을 사용하여 로그인과 사용자를 만드는 방법에 대해 알아봅니다. |
|  | |

## <a name="secure-and-protect-data"></a>데이터 보안 및 보호
다음 자습서에서는 [Azure SQL Database 데이터 보호](sql-database-security-overview.md)에 대해 알아봅니다.

| 자습서 | 설명 |
| --- | --- | 
| [상시 암호화를 사용하여 중요한 데이터 보호 ](sql-database-always-encrypted-azure-key-vault.md) |이 자습서에서는 상시 암호화 마법사를 사용하여 Azure SQL Database의 중요한 데이터를 보호합니다. |
|  | |

## <a name="develop"></a>개발
다음 자습서에서는 응용 프로그램 및 데이터베이스 개발에 대해 알아봅니다.

| 자습서 | 설명 |
| --- | --- | 
| [Excel을 사용하여 보고서 만들기](sql-database-connect-excel.md) |이 자습서에서는 클라우드에서 SQL 데이터베이스에 Excel을 연결하여 데이터를 가져오고 데이터베이스의 값을 기반으로 테이블 및 차트를 만드는 방법에 대해 알아봅니다. |
| [SQL Server를 사용하여 앱 빌드](https://www.microsoft.com/sql-server/developer-get-started/) |이 자습서에서는 SQL Server를 사용하여 응용 프로그램을 빌드하는 방법을 알아봅니다. |
| [임시 테이블](sql-database-temporal-tables.md) | 이 자습서에서는 임시 테이블에 대해 알아봅니다.
| [탄력적 도구에서 엔터티 프레임워크 사용](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) |이 자습서에서는 Elastic Database 도구와 통합하는 데 필요한 Entity Framework 응용 프로그램의 변경 내용을 알아봅니다. |
| [메모리 내 OLTP 채택](sql-database-in-memory-oltp-migration.md) | 이 자습서에서는 [메모리 내 OLTP](sql-database-in-memory.md)를 사용하여 트랜잭션 처리 성능을 개선하는 방법을 알아봅니다. |
| [새 데이터베이스에 대한 Code First](https://msdn.microsoft.com/data/jj193542.aspx) | 이 자습서에서는 Code First 개발에 대해 알아봅니다.
| [Tailspin 설문 조사 샘플 응용 프로그램](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) | 이 자습서에서는 Tailspon 설문 조사 샘플 응용 프로그램으로 작업합니다. |
| [Contoso 클리닉 데모 응용 프로그램](https://github.com/Microsoft/azure-sql-security-sample) | 이 자습서에서는 Contoso 클리닉 데모 응용 프로그램을 사용하여 작업합니다. |
|  | |

## <a name="data-sync"></a>데이터 동기화
이 자습서에서는 [데이터 동기화](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)에 대해 알아봅니다.

| 자습서 | 설명 |
| --- | --- | 
| [Azure SQL 데이터 동기화 시작(미리 보기)](sql-database-get-started-sql-data-sync.md) |이 자습서에서는 Azure 클래식 포털을 사용하여 Azure SQL 데이터 동기화의 기본 사항에 대해 알아봅니다. |
|  | |

## <a name="monitor-and-tune"></a>모니터링 및 튜닝
다음 자습서에서는 모니터링 및 튜닝 방법을 알아봅니다.
| 자습서 | 설명 |
| --- | --- | 
| [PowerShell을 사용하여 탄력적 풀 원격 분석](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)| 이 자습서에서는 PowerShell을 사용하여 탄력적 풀 원격 분석 수집에 대해 알아봅니다. |
| [SaaS에 대한 탄력적 풀 사용자 지정 대시보드](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) | 이 자습서에서는 탄력적 풀을 모니터링하기 위한 사용자 지정 대시보드를 만드는 방법을 알아봅니다. |
| [이벤트 파일 대상에 확장된 이벤트 캡처](sql-database-xevent-code-event-file.md)| 이 자습서에서는 이벤트 대상 파일에 확장된 이벤트를 캡처하는 방법을 알아봅니다.|
| [링 버퍼 대상으로 확장된 이벤트를 캡처](sql-database-xevent-code-ring-buffer.md)| 이 자습서에서는 코드 링 버퍼에 확장된 이벤트를 캡처하는 방법을 알아봅니다.|
|  | |



