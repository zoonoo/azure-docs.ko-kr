<properties 
   pageTitle="Azure SQL 데이터베이스에 대한 가격 책정 계층 권장 사항" 
   description="Azure 포털에서 가격 책정 계층을 변경할 때 기존 Azure SQL 데이터베이스의 작업을 실행하는 데 가장 적합한 계층을 추천해 주는 가격 책정 계층 권장 사항이 제공됩니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="06/30/2015"
   ms.author="sstein"/>

# SQL 데이터베이스 가격 책정 계층 권장 사항

 Azure 포털에서 가격 책정 계층을 변경할 때 기존 Azure SQL 데이터베이스의 작업을 실행하는 데 가장 적합한 계층을 추천해 주는 가격 책정 계층 권장 사항이 제공됩니다.

> [AZURE.NOTE]가격 책정 계층 권장 사항은 Web 및 Business 데이터베이스에만 적용되며 [Azure 포털](https://portal.azure.com/)에서만 제공됩니다.


## 개요

Azure는 SQL 데이터베이스의 과거 리소스 사용량을 평가하여 현재 성능 및 기능 요구 사항을 분석합니다. 또한 허용 가능한 최소 서비스 계층은 데이터베이스 크기에 따라 결정되며 [비즈니스 연속성](https://msdn.microsoft.com/library/azure/hh852669.aspx) 기능이 사용됩니다.

이 정보를 분석하여 데이터베이스의 일반적인 작업을 실행하고 현재 기능 집합을 유지 관리하는 데 가장 적합한 서비스 계층 및 성능 수준을 추천해 줍니다.

- 이 서비스에서는 이전 15-30일의 데이터(리소스 사용량, 데이터베이스 크기 및 데이터베이스 활동)를 검사하여 사용된 리소스 양과 현재 사용 가능한 서비스 계층 및 성능 수준의 실제 제한을 비교합니다.
- 15초 간격으로 데이터를 분석하고 각 간격의 결과 집합은 해당 결과 집합의 작업을 처리하는 데 가장 적합한 기존 서비스 계층 및 성능 수준으로 분류됩니다.
- 이러한 15초 샘플을 더 큰 15-30일 분석으로 집계하여 과거 작업의 95%를 처리할 수 있는 최적의 서비스 계층 및 성능 수준을 추천해 줍니다.

### 추천

사용자의 데이터베이스 사용량에 따라 현재 2가지 범주의 권장 사항이 제공될 수 있습니다.


| 권장 사항 | 설명 |
| :--- | :--- |
| 업그레이드 | 새 계층으로 업그레이드합니다. |
| 사용할 수 없음 | 데이터베이스에 최소 작업량 또는 약 14일의 활동이 있어야 합니다. 데이터가 부족하여 유효한 권장 사항을 제공할 수 없습니다. |

## 가격 책정 계층 권장 사항 가져오기

기존 Web 또는 Business 데이터베이스를 선택하고 **가격 책정 계층** 타일을 클릭하여 가격 책정 계층 권장 사항을 가져옵니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **찾아보기**를 클릭합니다.
3. **찾아보기** 블레이드에서 **SQL 데이터베이스**를 클릭합니다.
4. **SQL 데이터베이스** 블레이드에서, 서비스에서 분석할 데이터베이스를 클릭합니다.

    ![데이터베이스 선택][1]

5. 데이터베이스 블레이드에서 **가격 책정 계층** 타일을 선택합니다.

    ![가격 책정 계층][2]


7. **가격 책정 계층** 타일을 클릭하면 **권장 가격 책정 계층** 블레이드가 표시됩니다. 여기서 제안된 계층을 클릭한 다음 **선택** 단추를 클릭하여 해당 계층으로 변경할 수 있습니다.

    ![미리 보기 등록][4]

8. 필요에 따라 **사용량 세부 정보 보기**를 클릭하여 **가격 책정 계층 권장 사항 세부 정보** 블레이드를 열고 데이터베이스에 권장되는 계층, 현재 계층과 권장 계층의 기능 비교, 과거 리소스 사용량 분석 그래프를 볼 수 있습니다.

    ![미리 보기 등록][5]



## 요약

가격 책정 계층 권장 사항은 각 SQL 데이터베이스에 대한 원격 분석 데이터를 수집하여 데이터베이스의 실제 성능 요구 사항 및 기능 요구 사항에 따라 최적의 서비스 계층/성능 수준 조합을 추천해 주는 자동화된 환경을 제공합니다. 가격 책정 계층 권장 사항을 보려면 데이터베이스 블레이드에서 **가격 책정 계층** 타일을 클릭합니다.



## 다음 단계

특정 데이터베이스의 세부 정보에 따라 일반적으로 업그레이드 또는 다운그레이드가 즉시 수행되지는 않습니다. 데이터베이스가 새 계층으로 전환되면 관리 포털에서 알림을 제공합니다. 또는 사용자가 SQL 데이터베이스 서버의 마스터 데이터베이스에서 [sys.dm_operation_status(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/dn270022.aspx) 보기를 쿼리하여 업그레이드 상태를 모니터링할 수 있습니다.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[2]: ./media/sql-database-service-tier-advisor/pricing-tier.png
[3]: ./media/sql-database-service-tier-advisor/preview-sign-up.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 

<!---HONumber=July15_HO3-->