---
title: 'Azure Portal: 단일 데이터베이스 만들기 - Azure SQL Database | Microsoft Docs'
description: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스를 만들고 쿼리합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 016ad5f97c0c9ebbdd3dbc1156c38b4cc842fe9e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757243"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기

Azure SQL Database에서 데이터베이스를 만드는 가장 쉽고 빠른 방법은 [단일 데이터베이스](sql-database-single-database.md)를 만드는 것입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 단일 데이터베이스를 만들고 쿼리하는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).

이 빠른 시작에서 모든 단계의 경우 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-single-database"></a>단일 데이터베이스 만들기

단일 데이터베이스는 두 [구매 모델(sql-database-service-tiers.md) 중 하나를 사용하여 컴퓨팅, 메모리, IO 및 스토리지 리소스 세트가 정의됩니다. 단일 데이터베이스를 만들 때 지정된 Azure 지역의 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 내에서 데이터베이스를 관리 및 배치하기 위한 [SQL Database 서버](sql-database-servers.md)도 정의합니다.

AdventureWorksLT 샘플 데이터를 포함하는 단일 데이터베이스를 만들려면:

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
2. **데이터베이스**를 선택한 다음, **SQL Database**를 선택합니다.
3. **SQL Database 만들기** 형식에서 다음 값을 입력하거나 선택합니다.

   - **데이터베이스 이름**: *mySampleDatabase*를 입력합니다.
   - **구독**: 표시되지 않는 경우 올바른 구독을 드롭다운하고 선택합니다.
   - **리소스 그룹**: **새로 만들기**를 선택하고, *myResourceGroup*을 입력하고, **확인**을 선택합니다.
   - **원본 선택**: **샘플(AdventureWorksLT)** 을 드롭다운하고 선택합니다.

    >[!IMPORTANT]
    >이 데이터를 사용하는 Azure SQL Database 빠른 시작을 쉽게 따라 할 수 있도록 **샘플(AdventureWorksLT)** 데이터를 선택해야 합니다.
  
   ![단일 데이터베이스 만들기](./media/sql-database-get-started-portal/create-database-1.png)

4. **서버**에서 **새로 만들기**를 선택합니다.
5. **새 서버** 형식으로 다음 값을 입력하거나 선택합니다.

   - **서버 이름**: *mysqlserver*를 입력합니다.
   - **서버 관리자 로그인**: *azureuser*를 입력합니다.
   - **암호**: *Azure1234567*을 입력합니다.
   - **암호 확인**: 암호를 다시 입력합니다.
   - **위치**: 모든 유효한 위치를 드롭다운하고 선택합니다.  

   >[!IMPORTANT]
   >빠른 시작을 위해 서버 및 데이터베이스에 로그인할 수 있도록 서버 관리자 로그인 및 암호를 적어 둡니다. 로그인 또는 암호를 잊은 경우 **SQL 서버** 페이지에서 로그인 이름을 얻거나 암호를 다시 설정할 수 있습니다. **SQL 서버** 페이지를 열려면 데이터베이스를 만든 후 데이터베이스 **개요** 페이지에서 서버 이름을 선택합니다.

    ![서버 만들기](./media/sql-database-get-started-portal/create-database-server.png)

6. **선택**을 선택합니다.
7. **SQL Database** 양식의 **가격 책정 계층**을 선택합니다. 각 서비스 계층에 대해 사용할 수 있는 DTU 수 및 스토리지 크기를 살펴봅니다.

   >[!NOTE]
   >이 빠른 시작에서는 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 사용하지만 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)도 사용할 수 있습니다.
   >[!IMPORTANT]
   >현재 다음 지역을 제외한 모든 지역에서 프리미엄 계층의 스토리지 1TB 이상을 사용할 수 있습니다. 영국 북부, 미국 중서부, 영국 남부2, 중국 동부, USDoDCentral, 독일 중부, USDoDEast, US Gov 남서부, US Gov 중남부, 독일 북동부, 중국 북부 및 US Gov 동부. 이러한 지역에서 프리미엄 계층 저장소 최대 크기는 1TB로 제한됩니다. 자세한 내용은 [P11-P15 현재 제한 사항](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.  

8. 이 빠른 시작에서는 **Standard** 서비스 계층을 선택한 다음, 슬라이더를 사용하여 **10DTU(S0)** 및 **1**GB 스토리지를 선택합니다.
9. **적용**을 선택합니다.  

   ![가격 책정 선택](./media/sql-database-get-started-portal/create-database-s1.png)

10. **SQL Database** 양식에서 **만들기**를 선택하여 리소스 그룹, 서버 및 데이터베이스를 배포하고 프로비전합니다.

   배포에는 몇 분 정도 걸립니다. 배포 프로세스를 모니터링하려면 도구 모음에서 **알림**을 선택할 수 있습니다.

   ![알림](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-database"></a>데이터베이스 쿼리

데이터베이스를 만들었으니, 이제 Azure Portal에서 기본 제공 쿼리 도구를 사용하여 데이터베이스에 연결하고 데이터를 쿼리합니다.

1. 데이터베이스의 **SQL Database** 페이지에서 왼쪽 메뉴에 있는 **쿼리 편집기(미리 보기)** 를 선택합니다.

   ![쿼리 편집기에 로그인](./media/sql-database-get-started-portal/query-editor-login.png)

2. 사용자의 로그인 정보를 입력하고 **확인**을 선택합니다.
3. 다음 쿼리를 **쿼리 편집기** 창에 입력합니다.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. **실행**을 선택한 다음, **결과** 창에서 쿼리 결과를 검토합니다.

   ![쿼리 편집기 결과](./media/sql-database-get-started-portal/query-editor-results.png)

5. **쿼리 편집기** 창을 닫고, 저장하지 않은 편집 내용을 삭제하라는 메시지가 표시될 때 **확인**을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)를 진행하려면 이 리소스 그룹, 데이터베이스 서버 및 단일 데이터베이스를 그대로 유지합니다. 다음 단계에서는 다른 메서드를 사용하여 데이터베이스를 연결하고 쿼리하는 방법을 보여줍니다.

이러한 리소스 사용을 완료한 경우 다음과 같이 해당 리소스를 삭제할 수 있습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택한 다음, **myResourceGroup**을 선택합니다.
2. 리소스 그룹 페이지에서 **리소스 그룹 삭제**를 선택합니다.
3. 필드에 *myResourceGroup*을 입력한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 온-프레미스 또는 원격 도구에서 단일 데이터베이스에 연결하기 위한 서버 수준 방화벽 규칙을 만듭니다. 자세한 내용은 [서버 수준 방화벽 규칙 만들기](sql-database-server-level-firewall-rule.md)를 참조하세요.
- 서버 수준 방화벽 규칙을 만든 후 여러 다양한 도구 및 언어를 사용하여 데이터베이스를 [연결 및 쿼리](sql-database-connect-query.md)할 수 있습니다.
  - [SQL Server Management Studio를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)
  - [Azure Data Studio를 사용하여 연결 및 쿼리](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI를 사용하여 단일 데이터베이스를 만들려면 [Azure CLI 샘플](sql-database-cli-samples.md)을 참조하세요.
- Azure PowerShell을 사용하여 단일 데이터베이스를 만들려면 [Azure PowerShell 샘플](sql-database-powershell-samples.md)을 참조하세요.
