---
title: "PowerShell을 사용하여 Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경 | Microsoft Docs"
description: "Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경에서는 PowerShell을 사용하여 SQL 데이터베이스 규모 확장 또는 축소 방법을 보여 줍니다. PowerShell을 사용하여 Azure SQL 데이터베이스의 가격 책정 계층 변경."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6f016c17-b048-4968-b82b-d2dcec954e54
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aea3bcbd6ac73a05b00b7b79b2dc47bf06d67f6f


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>PowerShell을 사용하여 SQL 데이터베이스의 서비스 계층 및 성능 수준(가격 책정 계층) 변경
> [!div class="op_single_selector"]
> * [쉬운 테이블](sql-database-scale-up.md)
> * [**PowerShell**](sql-database-scale-up-powershell.md)
> 
> 

서비스 계층 및 성능 수준은 SQL 데이터베이스에 사용할 수 있는 기능 및 리소스를 설명하고 응용 프로그램이 변경될 필요에 따라 업데이트될 수 있습니다. 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.

서비스 계층 및/또는 데이터베이스의 성능 수준을 변경하는 작업은 새 성능 수준에서 원본 데이터베이스의 복제본을 만들고 연결을 복제본으로 전환합니다. 이 프로세스 중에 데이터가 손실되지는 않지만 복제본으로 전환할 경우 잠깐 동안 데이터베이스에 대한 연결이 비활성화되므로 비행의 일부 트랜잭션이 롤백될 수 있습니다. 하지만 평균 4초 이내 또 사례의 99% 이상에서 30초 미만으로 이 창이 달라집니다. 현재 연결에서 비행의 트랜잭션 숫자가 있는 경우에 특히 이 창을 더 이상 유지될 수 없습니다.  

전체 확장 프로세스 기간은 변경 전후 데이터베이스의 크기 및 서비스 계층에 따라 달라집니다. 예를 들어 표준 서비스 계층 내에서 변경되고 있는 250GB 데이터베이스는 6시간 내에 완료되어야 합니다. 프리미엄 서비스 계층 내의 성능 수준을 변경하고 있는 동일한 크기의 데이터베이스의 경우 3시간 이내에 완료되어야 합니다.

* 데이터베이스를 다운그레이드하려면 데이터베이스가 대상 서비스 계층의 최대 허용 크기보다 작아야 합니다. 
* [지역에서 복제](sql-database-geo-replication-portal.md) 를 사용할 수 있는 데이터베이스를 업그레이드하는 경우에는 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 성능 계층으로 업그레이드해야 합니다.
* 프리미엄 서비스 계층에서 다운그레이드하는 경우는 먼저 모든 지역에서 복제 관계를 종료해야 합니다. [가동 중단에서 복구](sql-database-disaster-recovery.md) 토픽에 설명된 단계에 따라 주 데이터베이스와 활성 보조 데이터베이스 간의 복제 프로세스를 중지할 수 있습니다.
* 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. 다운그레이드하는 경우 지정 시간으로 복원하는 기능을 잃게 되거나 백업 보존 기간이 단축될 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 백업 및 복원](sql-database-business-continuity.md)을 참조하세요.
* 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

**이 문서를 완료하려면 다음이 필요합니다.**

* Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 계정** 을 클릭하고 되돌아와 이 문서를 완료합니다.
* Azure SQL 데이터베이스입니다. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)문서의 단계에 따라 만듭니다.
* Azure PowerShell.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>SQL 데이터베이스의 서비스 계층 및 성능 수준 변경
[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) cmdlet를 실행하고 **-RequestedServiceObjectiveName**을 원하는 가격 책정 계층의 성능 수준(예: *S0*, *S1*, *S2*, *S3*, *P1*, *P2*...)으로 설정합니다.

```
$ResourceGroupName = "resourceGroupName"

$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```






## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>SQL 데이터베이스의 서비스 계층 및 성능 수준을 변경하는 샘플 PowerShell 스크립트
```{variables}```를 원하는 값으로 바꿉니다(중괄호를 포함하지 않음).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```



## <a name="next-steps"></a>다음 단계
* [규모 확장 및 감축](sql-database-elastic-scale-get-started.md)
* [SSMS를 사용하여 SQL 데이터베이스에 연결 및 쿼리하기](sql-database-connect-query-ssms.md)
* [Azure SQL 데이터베이스 내보내기](sql-database-export-powershell.md)

## <a name="additional-resources"></a>추가 리소스
* [비즈니스 연속성 개요](sql-database-business-continuity.md)
* [SQL 데이터베이스 설명서](http://azure.microsoft.com/documentation/services/sql-database/)
* [Azure SQL Database cmdlet](http://msdn.microsoft.com/library/azure/mt574084https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx.aspx)




<!--HONumber=Nov16_HO3-->


