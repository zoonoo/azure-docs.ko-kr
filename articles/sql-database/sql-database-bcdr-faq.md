<properties 
   pageTitle="SQL 데이터베이스 비즈니스 연속성 FAQ" 
   description="Azure SQL 데이터베이스를 사용한 비즈니스 연속성 및 재해 복구에 대한 기본 기능과 선택적 기능에 대해 고객이 자주 묻는 질문과 이에 대한 대답입니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/14/2015"
   ms.author="elfish"/>

# 비즈니스 연속성 FAQ

## 1\. 서비스 계층을 다운그레이드/업그레이드할 때 복원 지점 보존 기간은 어떻게 변경됩니까?
낮은 성능 계층으로 다운그레이드 하면 복원 지점의 보존 기간은 현재 데이터베이스 성능 계층의 보존 기간으로 즉시 단축됩니다.

데이터베이스 서비스 계층을 업그레이드 한 경우 보존 기간은 데이터베이스를 업그레이드한 후에만 확장이 시작됩니다.

예를 들어 데이터베이스가 P1에서 S3로 다운그레이드한 경우 보존 기간은 35일에서 14일로 즉시 변경되고, 14일 이전의 복원 지점을 더는 사용할 수 없게 됩니다. 이후에 다시 P1으로 업그레이드할 경우 보존 기간은 14일에서 시작하여 35일까지 작성되기 시작합니다.

## 2\. 삭제된 데이터베이스의 보존 기간은 얼마나 깁니까? 
보존 기간은 데이터베이스가 존재했던 서비스 계층 또는 데이터베이스 존재 일 수 중 더 적은 일 수에 의해 결정됩니다.

## 3\. 삭제된 서버를 복원하려면 어떻게 해야 합니까?

현재는 삭제된 서버 복원에 대한 지원이 제공되지 않습니다.

## 4\. 데이터베이스 복원에는 얼마나 소요됩니까?

데이터베이스를 복원하는 데 걸리는 기간은 데이터베이스의 크기, 트랜잭션 로그의 수, 네트워크 대역폭과 같은 여러 요인에 따라 달라집니다. 대부분의 데이터베이스는 12 시간 내에 복원됩니다.

## 5\. 내 데이터베이스의 복원 지점 보존 기간을 변경할 수 있습니까?

번호

## 6\. 내 데이터베이스에 대해 사용 가능한 복원 지점을 찾으려면 어떻게 해야 합니까?

사용자 오류로 인한 복원의 경우 사용 가능한 가장 최근 복원 지점은 현재 시간입니다. 사용 가능한 복원 지점 중 가장 오래된 지점을 알아보려면 [Get Database](https://msdn.microsoft.com/library/dn505708.aspx)(*RecoveryPeriodStartDate*)를 사용하여 가장 오래된 복원 지점(지역에서 복제된 복원 지점 아님)을 찾을 수 있습니다.

중단 상태에서 복원하려면 [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx)(*LastAvailableBackupDate*)를 사용하여 가장 최근의 지역에서 복제된 복원 지점을 가져옵니다.

## 7\. 내 서버에서 대량으로 데이터베이스를 복원하려면 어떻게 해야 합니까?

대량 복원을 위한 기본 제공 기능은 없습니다. 이 작업은 [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) 스크립트를 사용하여 수행할 수 있습니다.

## 8\. 표준 지역에서 복제와 활성 지역 복제의 차이 무엇입니까?

표준 지역에서 복제의 경우 보조 데이터베이스를 읽을 수 없습니다. 이는 중단 상태 동안 장애 조치에만 사용할 수 있습니다.

활성 지역 복제의 경우 최대 4개까지, 모든 보조 데이터베이스를 읽을 수 있습니다.

## 9\. 표준 지역에서 복제 또는 활성 지역 복제를 사용하는 경우 복제 지연이란 무엇입니까?

지역에서 복제는 연속 복사를 사용합니다. 따라서 [sys.dm\_continuous\_copy\_status](https://msdn.microsoft.com/library/azure/dn741329.aspx) 동적 관리 뷰(DMV)를 사용하여 마지막 복제 시간 및 기타 정보를 얻을 수 있습니다.




 

<!---HONumber=August15_HO6-->