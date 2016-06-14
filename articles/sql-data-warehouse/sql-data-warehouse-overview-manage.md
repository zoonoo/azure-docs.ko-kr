<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 데이터베이스 관리 | Microsoft Azure"
   description="SQL 데이터 웨어하우스 데이터베이스 관리 개요. 관리 도구, DWU 및 성능 확장, 쿼리 성능 문제 해결, 보안 정책 설정, 데이터 손상 또는 지역적 중단으로부터 데이터베이스 복원 등이 포함되어 있습니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/04/2016"
   ms.author="barbkess;sonyama;"/>

# Azure SQL 데이터 웨어하우스의 데이터베이스 관리

SQL 데이터 웨어하우스는 데이터베이스 관리의 여러 측면을 자동화합니다. 예를 들어 성능을 조정하려는 경우 적정 수준의 계산 리소스를 조정하고 해당 비용만 지불하기만 하면 SQL 데이터 웨어하우스에서 모든 확장 및 축소 작업을 수행합니다.

성능 요구 사항을 식별하고 오래 실행되는 쿼리의 문제를 해결하기 위해 워크로드를 모니터링해야 합니다. 또한 사용자의 권한과 로그인을 관리하기 위해 몇 가지 보안 작업도 수행해야 합니다.

이 개요에서는 SQL 데이터 웨어하우스 관리에 대한 이러한 측면에 대해 다룹니다.

- 관리 도구
- 계산 조정
- 일지 중지 및 다시 시작
- 성능 모범 사례
- 쿼리 모니터링
- 보안
- 백업 및 복원

## 관리 도구

다양한 도구를 사용하여 SQL 데이터 웨어하우스의 데이터베이스를 관리할 수 있습니다. 데이터베이스를 관리하면서 수행해야 하는 각 작업 유형의 도구 기본 설정을 개발해야 합니다.

### Azure 포털
[Azure 포털][]은 데이터베이스를 생성, 업데이트, 삭제하고 데이터베이스 리소스를 모니터링할 수 있는 웹 기반 포털입니다. Azure를 방금 시작했거나 관리하는 데이터웨어하우스 데이터베이스 수가 적거나 신속하게 작업을 수행해야 하는 경우에 매우 유용한 도구입니다.

Azure 포털을 시작하려면 [SQL 데이터 웨어하우스 만들기(Azure 포털)][]를 참조하세요.

### Visual Studio에서 SQL Server 데이터 도구
Visual Studio의 [SSDT(SQL Server Data Tools)][]를 사용하여 데이터베이스를 연결, 관리, 개발할 수 있습니다. Visual Studio 또는 다른 IDE(통합 개발 환경)에 익숙한 응용 프로그램 개발자라면 Visual Studio에서 SSDT를 사용해 보세요.

SSDT에는 SQL 데이터 웨어하우스 데이터베이스에 대해 스크립트를 시각화, 연결 및 실행할 수 있게 해주는 SQL 서버 탐색기가 포함됩니다. SQL 데이터 웨어하우스에 빠르게 연결하려면 Azure 클래식 포털에서 데이터베이스 세부 정보를 볼 때 명령 모음에서 **Visual Studio에서 열기** 단추를 클릭하기만 하면 됩니다.

Visual Studio에서 SSDT를 시작하려면 [Visual Studio로 Azure SQL 데이터 웨어하우스에 연결][]을 참조하세요.

### 명령줄 도구
명령줄은 워크로드 자동화에 적합합니다. PowerShell 및 sqlcmd는 프로세스를 자동화하는 데 적합한 두 가지 방법입니다. 이들 도구는 필수 작업을 스크립트 및 자동화할 수 있는 프로덕션 환경에서 많은 수의 논리 서버를 관리하고 리소스 변경 사항을 배포할 때 권장합니다.

### 동적 관리 뷰 

SQL 데이터 웨어하우스는 주로 DMV를 사용하여 관리합니다. 포털에 표시되는 거의 모든 정보가 DMV에 의존합니다. SQL 데이터 웨어하우스 DMV 목록을 보려면 [SQL 데이터 웨어하우스 시스템 뷰][]를 참조하세요.

시작하려면 [sqlcmd를 사용하여 연결 및 쿼리][] 및 [데이터베이스 만들기(PowerShell)][]를 참조하세요.

## 계산 조정

SQL 데이터 웨어하우스에서 CPU의 계산 리소스, 메모리, I/O 대역폭을 늘리거나 줄여 성능을 빠르게 확장 또는 축소할 수 있습니다. 성능을 조정할 경우 SQL 데이터 웨어하우스에서 데이터베이스에 할당하는 DWU(데이터 웨어하우스 단위) 수를 조정하기만 하면 됩니다. SQL 데이터 웨어하우스가 빠르게 변경하고 하드웨어 또는 소프트웨어의 모든 기본 변경 사항을 처리합니다.

DWU 확장에 대한 자세한 내용은 [성능 조정][]을 참조하십시오.

##  일지 중지 및 다시 시작

비용을 절약하기 위해 필요에 따라 계산 리소스를 일지 중지 및 다시 시작할 수 있습니다. 예를 들어, 밤 시간과 주말에 데이터베이스를 사용하지 않으려면 해당 시간에 일시 중지했다가 주간에 다시 시작할 수 있습니다. 데이터베이스 일시 중지 중에는 DWU 요금이 부과되지 않습니다.

자세한 내용은 [계산 일시 중지][] 및 [계산 다시 시작][]을 참조하세요.

## 성능 모범 사례

새 기술을 시작할 경우 처음부터 가장 적절한 팁과 요령을 발견하면 많은 시간을 절약할 수 있습니다. Microsoft의 다양한 항목에 모범 사례가 나와 있습니다.

워크로드를 개발할 때 가장 중요한 고려 사항의 요약 문서를 보려면 [SQL 데이터 웨어하우스 모범 사례][]를 참조하세요.

## 쿼리 모니터링

쿼리가 너무 오래 실행될 때 무엇이 문제인지 확실히 파악할 수 없는 경우가 있습니다. SQL 데이터 웨어하우스에는 어느 쿼리가 너무 오래 실행되는지 확인하는 데 사용하는 동적 관리 뷰가 있습니다.

오래 실행되는 쿼리를 찾으려면 [DMV를 사용하여 워크로드 모니터링][]을 참조하세요.

## 보안

안전한 시스템을 유지하려면 모든 종류의 무단 액세스로부터 보호하고 경계를 유지해야 합니다. 보안 시스템은 허가된 IP 주소만 연결할 수 있도록 하는 방화벽을 구현하고 사용자 자격 증명을 올바르게 인증해야 합니다. 사용자가 데이터베이스에 연결한 후에는 최소한의 작업만 수행할 수 있는 권한만 가져야 합니다. 데이터 보호를 위해 암호화를 사용할 수 있습니다. 또한 의심스러운 활동이 있는지 여부를 추적할 수 있도록 감사와 추적 기능을 갖추는 것도 중요합니다.

보안 관리에 대한 자세한 내용은 [보안 개요][]를 참조하세요.

## 백업 및 복원

데이터베이스를 복구하는 방법은 두 가지입니다. 데이터베이스에서 일부 데이터가 손상되었거나 실수를 한 경우 데이터베이스 스냅숏을 복원할 수 있습니다. 지역에 정전 또는 재해가 발생하여 지역 중 한 곳을 이용할 수 없게 된 경우 다른 지역에 데이터베이스를 다시 만들 수 있습니다.

SQL 데이터 웨어하우스가 정기적 간격으로 데이터베이스를 자동으로 백업합니다. 백업 일정 및 보존 정책은 [높은 안정성][]을 참조하세요.

### 지역 중복 저장소

SQL 데이터 웨어하우스는 계산과 저장소를 분리하기 때문에, 모든 데이터는 지리적 중복 Azure 저장소(RA-GRS)로 직접 기록됩니다. 지역 중복 저장소는 기본 지역에서 수백 마일 떨어져 있는 보조 영역에 데이터를 복제합니다. 기본 및 보조 지역에서 데이터는 별도 오류 도메인 및 업그레이드 도메인에 걸쳐 각각 세 번씩 복제됩니다. 이렇게 하면 영역 중 하나를 사용할 수 없게 하는 완전한 지역 가동 중단 또는 재해 경우더라도 데이터를 지속적으로 사용할 수 있습니다. 읽기 액세스 지역 중복 저장소에 대한 자세한 정보는 [Azure 저장소 중복 옵션][]을 참조하세요.

### 데이터베이스 복원

데이터베이스 복원은 데이터베이스를 이전 시점으로 복원하도록 설계되었습니다. SQL 데이터 웨어하우스 서비스는 적어도 8시간마다 자동 저장소 스냅숏을 사용하여 모든 데이터베이스를 보호하고 7일 동안 복원 지점의 불연속 집합 제공을 유지합니다. 이러한 백업은 RA-GRS Azure 저장소에 저장되므로 기본적으로 지리적으로 중복됩니다. 자동 백업 및 복원 기능은 추가 비용 없이 제공되며 실수로 손상 또는 삭제되지 않도록 데이터베이스를 보호하는 데 비용이 들지 않으며 별도의 관리 방법도 없습니다.

데이터베이스 복원에 대한 자세한 내용은 [스냅숏에서 복원][]을 참조하세요.

### 지역 복원

지역 복원은 중단 이벤트로 인해 사용할 수 없는 경우 데이터베이스를 복구하도록 설계되었습니다. 새 Azure 지역에서 새 데이터베이스를 만들려면 지역 중복 백업에서 데이터베이스를 복원하도록 지원을 요청할 수 있습니다. 백업이 지리적으로 중복되기 때문에 가동 중단으로 인해 데이터베이스에 액세스할 수 없는 경우더라도 데이터베이스를 복구하는 데 사용할 수 있습니다. 지역 복원 기능은 추가 비용 없이 제공됩니다.

지역 복원을 사용하려면 [스냅숏에서 지역 복원][]을 참조하세요.

## 다음 단계
좋은 데이터베이스 디자인 원칙을 사용할 경우 SQL 데이터 웨어하우스를 더 손쉽게 관리할 수 있습니다. 자세한 내용은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Azure 저장소 중복 옵션]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[SQL 데이터 웨어하우스 만들기(Azure 포털)]: sql-data-warehouse-get-started-provision.md
[데이터베이스 만들기(PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Visual Studio로 Azure SQL 데이터 웨어하우스에 연결]: sql-data-warehouse-get-started-connect.md
[sqlcmd를 사용하여 연결 및 쿼리]: sql-data-warehouse-get-started-connect-sqlcmd.md
[개발 개요]: sql-data-warehouse-overview-development.md
[스냅숏에서 지역 복원]: sql-data-warehouse-backup-and-geo-restore-from-snapshot.md
[높은 안정성]: sql-data-warehouse-overview-expectations.md#high-reliability
[DMV를 사용하여 워크로드 모니터링]: sql-data-warehouse-manage-monitor.md
[계산 일시 중지]: sql-data-warehouse-overview-scalability.md#pause-compute-bk
[스냅숏에서 복원]: sql-data-warehouse-backup-and-restore-from-snapshot.md
[계산 다시 시작]: sql-data-warehouse-overview-scalability.md#resume-compute-performance-bk
[성능 조정]: sql-data-warehouse-overview-scalability.md#scale-performance-bk
[보안 개요]: sql-data-warehouse-overview-security.md
[SQL 데이터 웨어하우스 모범 사례]: sql-data-warehouse-best-practices.md
[SQL 데이터 웨어하우스 시스템 뷰]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SSDT(SQL Server Data Tools)]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure 포털]: http://portal.azure.com/

<!---HONumber=AcomDC_0601_2016-->