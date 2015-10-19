<properties
   pageTitle="Powershell을 사용하여 SQL 데이터 웨어하우스 만들기 | Microsoft Azure"
   description="Powershell을 사용하여 SQL 데이터 웨어하우스 만들기"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/05/2015"
   ms.author="lodipalm"/>

# Powershell을 사용하여 SQL 데이터 웨어하우스 만들기

> [AZURE.NOTE]SQL 데이터 웨어하우스와 함께 Microsoft Azure Powershell을 사용하려면 버전 0.9.4 이상이 필요합니다. Powershell에서 (Get-Module Azure).Version을 실행하여 버전을 확인할 수 있습니다.

## Azure PowerShell cmdlet을 다운로드하여 실행합니다.
Powershell을 아직 설정하지 않은 경우 다음 작업을 수행할 수 있습니다.

1. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다.
2. 모듈을 실행하려면 시작 창에서 **Microsoft Azure PowerShell**을 입력합니다.
3. 아직 컴퓨터에 계정을 추가하지 않았다면 다음 cmdlet을 실행합니다. (자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법][]을 참조하세요.)

            Add-AzureAccount

4. 또한 ARM 모드에서 PowerShell을 실행해야 합니다. 다음 명령을 실행하여 이 모드로 전환할 수 있습니다.

            switch-azuremode AzureResourceManager

## SQL 데이터 웨어하우스 만들기
계정에 Powershell이 성공적으로 설정되었는지 확인한 후 다음을 실행하여 새 SQL 데이터 웨어하우스를 배포할 수 있습니다.

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

이 cmdlet에 대한 필요한 매개 변수는 다음과 같습니다.

 + **RequestedServiceObjectiveName**: "DWXXX" 형식의 요청 중인 DWU의 양입니다. 현재 지원되는 값은 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000입니다.
 + **DatabaseName**: 만들려는 SQL 데이터 웨어하우스의 이름입니다.
 + **ServerName**: 만들기에 사용하는 서버의 이름입니다(V12이어야 함).
 + **ResourceGroupName**: 사용 중인 리소스 그룹입니다. 구독에서 사용 가능한 리소스 그룹을 찾으려면 Get-AzureResource를 사용합니다.
 + **Edition**: SQL 데이터 웨어하우스를 만들 버전을 "DataWarehouse"로 설정해야 합니다. 

## 다음 단계
SQL 데이터 웨어하우스에서 프로비전을 완료한 후 [샘플 데이터를 로드][]하거나 [개발][], [로드][] 또는 [마이그레이션][] 방법을 확인할 수 있습니다.

SQL 데이터 웨어하우스를 프로그래밍 방식으로 관리하는 방법에 대한 자세한 내용은 [Powershell][] 또는 [REST API][] 설명서를 확인하세요.



<!--Image references-->

<!--Article references-->
[마이그레이션]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-overview-migrate/
[개발]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-overview-develop/
[로드]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-overview-load/
[샘플 데이터를 로드]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/ko-KR/library/azure/dn546722.aspx
[firewall rules]: https://azure.microsoft.com/ko-KR/documentation/articles/sql-database-configure-firewall-settings/
[Azure PowerShell을 설치 및 구성하는 방법]: powershell-install-configure.md

<!---HONumber=Oct15_HO2-->