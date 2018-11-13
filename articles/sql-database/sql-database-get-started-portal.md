---
title: 'Azure Portal: SQL Database 만들기 | Microsoft Docs'
description: Azure Portal에서 SQL Database 논리 서버, 서버 수준 방화벽 규칙 및 데이터베이스를 만들고 쿼리합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 66ee4ac8fe946696d6760891a086a672fa9fc412
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914604"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure SQL 데이터베이스 만들기

이 빠른 시작은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)을 사용하여 Azure에서 SQL Database를 만드는 방법을 설명합니다. Azure SQL Database는 클라우드에서 항상 사용 가능한 SQL Server Database를 실행하고 크기를 조정할 수 있는 “Database-as-a-Service” 제품입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 SQL 데이터베이스를 만들고 쿼리하여 시작하는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

  >[!NOTE]
  >이 자습서에서는 DTU 기반 구매 모델을 사용하지만 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)도 사용할 수 있습니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기

Azure SQL Database는 일련의 정의된 [계산 및 저장소 리소스](sql-database-service-tiers-dtu.md)를 사용하여 만들어집니다. 데이터베이스는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 및 [Azure SQL Database 논리 서버](sql-database-features.md)에서 만들어집니다.

다음 단계에 따라서 Adventure Works LT 샘플 데이터를 포함하는 SQL Database를 만듭니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

2. **새로 만들기** 페이지에서 **데이터베이스**를 선택하고 **새로 만들기** 페이지의 **SQL Database** 아래에서 **만들기**를 선택합니다.

   ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

3. 위의 이미지에 표시된 대로 다음과 같은 정보를 사용하여 SQL Database 형식을 작성합니다.   

   | 설정       | 제안 값 | 설명 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **데이터베이스 이름** | mySampleDatabase | 유효한 데이터베이스 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
   | **구독** | 사용자의 구독  | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
   | **리소스 그룹**  | myResourceGroup | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   | **원본 선택** | 샘플(AdventureWorksLT) | AdventureWorksLT 스키마 및 데이터를 새 데이터베이스에 로드 |

   > [!IMPORTANT]
   > 이 빠른 시작의 나머지 부분에 사용되기 때문에 이 양식에서 샘플 데이터베이스를 선택해야 합니다.
   >

4. **서버** 아래에서 **필요한 설정 구성**을 클릭하고, 다음 이미지에 표시된 대로 다음과 같은 정보를 사용하여 SQL Server(논리 서버) 양식을 작성합니다.   

   | 설정       | 제안 값 | 설명 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   | **서버 관리자 로그인** | 모든 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
   | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 포함해야 합니다. |
   | **구독** | 사용자의 구독 | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
   | **리소스 그룹** | myResourceGroup | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
   | **위치**: | 모든 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |

   > [!IMPORTANT]
   > 여기에 지정한 서버 관리자 로그인 및 암호는 이 빠른 시작의 뒷부분에 나오는 서버 및 데이터베이스에 로그인하는 데 필요합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.
   >  

   ![create database-server](./media/sql-database-get-started-portal/create-database-server.png)

5. 양식을 완료하는 경우 **선택**을 클릭합니다.

6. **가격 책정 계층**을 클릭하여 서비스 계층, DTU 수 및 저장소 크기를 지정합니다. 각 서비스 계층에 대해 사용할 수 있는 DTU 수 및 저장소 크기에 대한 옵션을 살펴봅니다.

   > [!IMPORTANT]
   > 현재 영국 북부, 미국 중서부, 영국 남부2, 중국 동부, USDoDCentral, 독일 중부, USDoDEast, US Gov 남서부, US Gov 중남부, 독일 북동부, 중국 북부, US Gov 동부를 제외한 모든 지역에서 1TB를 초과하는 저장소를 프리미엄 계층에 사용할 수 있습니다. 다른 지역에서 프리미엄 계층 저장소 크기는 1TB로 제한됩니다. [P11-P15 현재 제한 사항]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.  

7. 이 빠른 시작에서는 **Standard** 서비스 계층을 선택한 다음, 슬라이더를 사용하여 **10DTU(S0)** 및 **1GB** 저장소를 선택합니다.

   ![create database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. **추가 기능 저장소** 옵션을 사용하려면 미리 보기 약관에 동의합니다.

   > [!IMPORTANT]
   > 현재 미국 중서부, 중국 동부, USDoDCentral, USGov 아이오와, 독일 중부, USDoDEast, US Gov 남서부, US Gov 중남부, 독일 북동부, 중국 북부를 제외한 모든 지역에서 1TB를 초과하는 저장소를 프리미엄 계층에 사용할 수 있습니다. 다른 지역에서 프리미엄 계층 저장소 크기는 1TB로 제한됩니다. [P11-P15 현재 제한 사항]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.  

9. 서버 계층, DTU 수 및 저장소 크기를 선택한 후에 **적용**을 클릭합니다.  

10. 이제 SQL Database 양식을 완료했으므로 **만들기**를 클릭하여 데이터베이스를 프로비전합니다. 프로비전하는 데 몇 분이 걸립니다.

11. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.

     ![알림](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>SQL Database 쿼리

이제 Azure에서 샘플 데이터베이스를 만들었으므로 Azure Portal 내에서 기본 제공 쿼리 도구를 사용하여 데이터베이스에 연결하고 데이터를 쿼리할 수 있는지 확인해 보겠습니다.

1. 데이터베이스에 대한 SQL Database 페이지의 왼쪽 메뉴에서 **쿼리 편집기(미리 보기)**, **로그인**을 차례로 클릭합니다.

   ![로그인](./media/sql-database-get-started-portal/query-editor-login.png)

2. SQL 서버 인증을 선택하고, 필요한 로그인 정보를 제공한 다음, **확인**을 클릭하여 로그인합니다.

3. **ServerAdmin**으로 인증된 후에 쿼리 편집기 창에서 다음 쿼리를 입력합니다.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. **실행**을 클릭한 후 **결과** 창에서 쿼리 결과를 검토합니다.

   ![쿼리 편집기 결과](./media/sql-database-get-started-portal/query-editor-results.png)

5. **쿼리 편집기** 페이지를 닫고 **확인**을 클릭하여 저장하지 않은 편집 내용을 삭제합니다.

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)로 이동하여 다양한 방법으로 데이터베이스에 연결하고 쿼리하는 방법을 알아보려면 이러한 리소스를 저장합니다. 그러나 이 빠른 시작에서 만든 리소스를 삭제하려면 다음 단계를 사용합니다.


1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**과 **myResourceGroup**을 차례로 클릭합니다.
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에 **myResourceGroup**을 입력한 후 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- 이제 데이터베이스가 있으므로 온-프레미스 도구에서 서버 수준 방화벽 규칙을 만들어 이 데이터베이스에 연결해야 합니다. [서버 수준 방화벽 규칙 만들기](sql-database-get-started-portal-firewall.md)를 참조하세요.
- 서버 수준 방화벽 규칙을 만드는 경우 즐겨찾는 도구 또는 언어 중 하나를 사용하여 다음과 같이 [연결하고 쿼리](sql-database-connect-query.md)할 수 있습니다.
  - [SQL Server Management Studio를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)
  - [Azure Data Studio를 사용하여 연결 및 쿼리](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI를 사용하여 데이터베이스를 만들려면 [Azure CLI 샘플](sql-database-cli-samples.md)을 참조하세요.
- Azure PowerShell을 사용하여 데이터베이스를 만들려면 [Azure PowerShell 샘플](sql-database-powershell-samples.md)을 참조하세요.