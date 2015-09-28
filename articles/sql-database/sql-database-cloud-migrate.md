<properties
   pageTitle="Azure SQL 데이터베이스로 데이터베이스 마이그레이션"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 배포, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 데이터베이스 내보내기, 마이그레이션 마법사"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="09/02/2015"
   ms.author="carlrab"/>

# Azure SQL 데이터베이스로 데이터베이스 마이그레이션

Azure SQL 데이터베이스 V12는 SQL Server 2014 이후와 엔진 호환성이 거의 완벽에 가깝습니다. 이와 같이 SQL Server 2005 이상의 온-프레미스 인스턴스에서 Azure SQL 데이터베이스로 대부분의 데이터베이스를 마이그레이션하는 작업은 훨씬 더 간단 합니다. 대부분의 데이터베이스에서 마이그레이션은 스키마에 변화가 있거나 응용 프로그램의 엔지니어링을 다시 하는 과정이 적거나 없는 경우 간단한 스키마 및 데이터 이동을 위한 몇 가지 작업만 필요합니다. 데이터베이스를 변경할 필요가 있을 경우 이러한 변경의 범위도 더 작습니다.

설계상 SQL Server의 서버 범위 기능은 Azure SQL 데이터베이스 V12에서 지원되지 않습니다. 이러한 기능에 의존하는 데이터베이스 및 응용 프로그램은 마이그레이션될 수 있기 전에 일부 엔지니어링을 다시 해야 합니다. Azure SQL 데이터베이스 V12는 온-프레미스 SQL Server 데이터베이스와의 호환성이 향상되었지만 마이그레이션은 여전히 신중하게 계획하고 실행해야 합니다. 특히 더 크고 복잡한 데이터베이스라면 더 신경 써야 합니다.

## 호환성 확인
온-프레미스 SQL Server 데이터베이스가 Azure SQL 데이터베이스 V12와 호환되는지 확인하려면 아래 옵션 #1에서 설명하는 두 방법 중 하나를 사용하여 마이그레이션을 시작할 수 있습니다. 스키마 유효성 검사 루틴이 비호환성을 검색할 수 있는지 확인하고 아래 옵션 #2에서 설명한 것처럼 Visual Studio에서 SQL Server Data Tools을 사용하여 호환성의 유효성을 검사할 수 있습니다. 온-프레미스 SQL Server 데이터베이스에 호환성 문제가 있으면 Visual Studio 또는 SQL Server Management Studio에서 SQL Server 데이터 도구를 사용하여 환성 문제를 짚어 보고 해결할 수 있습니다.

## 마이그레이션 방법
호환 가능한 온-프레미스 SQL Server 데이터베이스를 Azure SQL 데이터베이스 V12로 마이그레이션하는 메서드가 많이 있습니다.

- 작거나 보통인 데이터베이스의 경우 호환 가능한 SQL Server 2005 이후의 데이터베이스를 마이그레이션하는 작업은 배포 데이터베이스를 SQL Server Management Studio의 Microsoft Azure 데이터베이스 마법사에 실행하는 것처럼 간단하여 연결에 어려움을 주지 않습니다.(연결이 안되거나 낮은 대역폭 또는 시간 초과 문제)
- 중대형 데이터베이스 또는 연결에 어려움이 있는 경우 SQL Server Management Studio를 사용하여 데이터 및 스키마를 BACPAC 파일로 내보낸 다음(로컬 또는 Azure blob에 저장) BACPAC 파일을 Azure SQL 인스턴스로 가져올 수 있습니다. 또한 Azure blob에서 BACPAC를 저장하는 경우 Azure 포털 내에서 BACPAC 파일을 가져올 수 있습니다. BACPAC 파일에 대한 자세한 내용은 [데이터 계층 응용 프로그램](https://msdn.microsoft.com/library/ee210546.aspx)을 참조하세요.
- 더 큰 데이터베이스의 경우 스키마와 데이터를 개별적으로 마이그레이션하여 최상의 성능을 얻습니다. SQL Server Management Studio 또는 Visual Studio를 사용하여 스키마를 데이터베이스 프로젝트로 추출하고 Azure SQL 데이터베이스를 만들기 위해 스키마를 배포할 수 있습니다. BCP를 사용하여 데이터를 추출한다음 병렬 스트림을 사용하여 Azure SQL 데이터베이스로 데이터를 가져오는 데 BCP를 사용할 수 있습니다. 대규모의 복잡한 데이터베이스를 마이그레이션하면 선택하는 방법에 관계 없이 많은 시간이 걸립니다.

### 옵션 1
******SQL Server Management Studio를 사용하여 호환 가능한 데이터베이스 마이그레이션 ***

SQL Server Management Studio는 호환 가능한 온-프레미스 SQL Server 데이터베이스를 Azure SQL 데이터베이스에 마이그레이션하기 위한 두 가지 메서드를 제공합니다. Microsoft Azure SQL 데이터베이스 마법사에 데이터베이스 배포를 사용하거나 데이터베이스를 BACPAC 파일에 내보내면 가져와서 새 Azure SQL 데이터베이스를 만들 수 있습니다. 마법사는 Azure SQL 데이터베이스 V12 호환성의 유효성을 검사하고 BACPAC 파일에 스키마 및 데이터를 추출한 다음 지정된 Azure SQL 데이터베이스 인스턴스로 가져옵니다. 이 옵션을 사용하려면 [SSMS 사용](sql-database-migrate-ssms.md)을 참조하세요.

### 옵션 2
***Visual Studio를 사용하여 오프라인으로 데이터베이스 스키마를 업데이트하고 SQL Server Management Studio를 사용하여 배포***

온-프레미스 SQL Server 데이터베이스가 호환 되지 않거나 호환되는지 확인하려면 분석을 위해 Visual Studio 데이터베이스 프로젝트에 데이터베이스 스키마를 가져올 수 있습니다. 분석하려면 대한 대상 플랫폼을 SQL 데이터베이스 V12로 프로젝트로 지정하고 프로젝트를 빌드합니다. 빌드에 성공한 경우 데이터베이스가 호환됩니다. 빌드가 실패한 경우 Visual Studio ("SSDT")용 SQL Server Data Tools에서 오류를 해결할 수 있습니다. 프로젝트가 성공적으로 빌드되면 원본 데이터베이스의 복사본으로 다시 게시할 수 있으며 다음 SSDT에서 데이터 비교 기능을 사용하여 원본 데이터베이스에서 Azure SQL V12 호환 가능한 데이터베이스로 데이터를 복사할 수 있습니다. 업데이트된 이 데이터베이스를 옵션 1을 사용하여 Azure SQL 데이터베이스로 배포합니다. 스키마만 마이그레이션해야 할 경우 Visual Studio에서 Azure SQL 데이터베이스로 직접 스키마를 게시할 수 있습니다. 데이터베이스 스키마가 마이그레이션 마법사 하나로 처리될 수 있는 것보다 많은 변경 내용이 필요한 경우 이 메서드를 사용합니다. 이 옵션을 사용하려면 [Visual Studio 사용](sql-database-migrate-visualstudio-ssdt.md)을 참조하세요.

## 사용할 옵션 결정
- 데이터베이스를 변경하지 않고 마이그레이션할 수 있는 경우 옵션 1을 사용하는 것이 빠르고 쉽습니다. 확실하지 않은 부분이 있다면 옵션 1에 설명된 대로 데이터베이스에서 스키마 전용 BACPAC를 내보내는 것으로 시작합니다. 오류 없이 내보내기에 성공하면 옵션 1을 사용하여 해당 데이터와 데이터베이스를 마이그레이션할 수 있습니다.  
- 옵션 #1을 내보내는 동안 오류가 발생하면 옵션 #2을 사용하고 마이그레이션 마법사 및 수동으로 적용된 스키마 변경 내용을 조합하여 Visual Studio에서 데이터베이스 스키마 오프라인을 수정합니다. 그런 다음 원본 데이터베이스의 복사본을 원래 자리에 업데이트하고 옵션 1을 사용하여 Azure로 마이그레이션합니다.

## 마이그레이션 도구
사용되는 도구에는 SSMS(SQL Server Management Studio), Visual Studio의 SQL Server 도구(VS, SSDT), 및 Azure 포털이 포함됩니다.

> [AZURE.IMPORTANT]이전 버전의 도구는 Azure SQL 데이터베이스 V12와 호환되지 않으므로 클라이언트 도구를 최신 버전으로 설치해야 합니다.

### SSMS(SQL Server Management Studio)
SSMS를 사용하여 호환되는 데이터베이스를 Azure SQL 데이터베이스로 직접 배포하거나 데이터베이스의 논리적 백업을 BACPAC로 내보낼 수 있습니다. 그런 다음 SSMS를 사용하여 가져와 새로운 Azure SQL 데이터베이스를 만들 수 있습니다.

[최신 버전의 SSMS 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)

### Visual Studio의 SQL Server 도구(VS, SSDT)
Visual Studio의 SQL Server 도구를 사용하여 스키마의 각 개체에 대한 Transact-SQL 파일 집합을 구성하는 데이터베이스 프로젝트를 만들고 관리할 수 있습니다. 프로젝트는 데이터베이스 또는 스크립트 파일에서 가져올 수 있습니다. 프로젝트가 생성되면 Azure SQL 데이터베이스 v12로 가져온 다음 빌드하여 스키마 호환성을 확인할 수 있습니다. 오류를 클릭하면 해당하는 Transact-SQL 파일이 열리므로 편집하여 오류를 수정할 수 있습니다. 모든 오류가 해결되면 프로젝트를 게시할 수 있습니다. 프로젝트는 SQL 데이터베이스로 직접 게시하여 빈 데이터베이스를 만들거나 다시 원본(복사본) SQL Server 데이터베이스로 게시하여 스키마를 업데이트할 수 있습니다. 이렇게 하면 위에서 설명한 대로 SSMS를 사용하여 데이터베이스를 배포할 수 있습니다.

Visual Studio 2013 업데이트 4 이상이 포함된 [최신 Visual Studio용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)를 사용하세요.

## 비교
| 옵션 1 | 옵션 2 |
| ------------ | ------------ | ------------ |
| Azure SQL 데이터베이스에 호환되는 데이터베이스 배포 | 데이터베이스를 바로 업데이트하여 Azure SQL 데이터베이스로 배포 |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![오프라인 편집](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| SSMS 사용 | VS 및 SSMS 사용 |
|스키마 호환이 필요한 간단한 프로세스입니다. 스키마는 변경되지 않고 마이그레이션됩니다. | 스키마는 Visual Studio에서 데이터베이스 프로젝트로 가져옵니다. Visual Studio의 SSDT 도구를 사용하여 추가 업데이트가 수행되고 최종 스키마가 원래 자리의 데이터베이스를 업데이트하는 데 사용됩니다. |
| 항상 전체 데이터베이스를 배포하거나 내보냅니다. | 마이그레이션에 포함된 개체를 완전하게 제어합니다. |
| 오류가 있는 경우 출력 변경을 위한 프로비전이 없습니다. 원본 스키마가 호환되어야 합니다. | 사용 가능한 Visual Studio용 SSDT의 전체 기능. 스키마는 오프라인으로 변경됩니다. | Azure에서 응용 프로그램 유효성 검사가 수행됩니다. 스키마 변경 없이 마이그레이션되는 경우 최소화해야 합니다. | 데이터베이스를 Azure에 배포하기 전에 SQL Server에서 응용 프로그램 유효성 검사를 수행할 수 있습니다. |
| 간단히 하나 또는 두 단계의 프로세스로 쉽게 구성할 수 있습니다. | 더 복잡한 여러 단계의 프로세스(스키마만 배포하는 경우에 더 간단)입니다. |

<!---HONumber=Sept15_HO3-->