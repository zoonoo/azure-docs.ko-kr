---
title: 투명 데이터 암호화(포털)
description: Azure 시냅스 분석의 투명 데이터 암호화(TDE)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 669a02bb45ef61d1e66d719850defcfaa19b838d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350545"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>투명한 데이터 암호화(TDE) 시작
> [!div class="op_single_selector"]
> * [보안 개요](sql-data-warehouse-overview-manage-security.md)
> * [인증](sql-data-warehouse-authentication.md)
> * [암호화(포털)](sql-data-warehouse-encryption-tde.md)
> * [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>필요한 권한
TDE(투명한 데이터 암호화)를 사용하려면 관리자 또는 dbmanager 역할의 멤버여야 합니다.

## <a name="enabling-encryption"></a>암호화 설정
TDE를 사용하려면 아래 단계를 따르십시오.

1. [Azure 포털](https://portal.azure.com)
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. 투명 **데이터 암호화** ![옵션 포털 설정 선택](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. **에서** 포털 ![설정 설정 설정 선택](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. 포털 설정 **저장**
   ![선택](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>암호화 비활성화
TDE를 사용하지 않도록 설정하려면 아래 단계를 따르십시오.

1. [Azure 포털](https://portal.azure.com)
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. 투명 **데이터 암호화** ![옵션 포털 설정 선택](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. 포털 설정 ![ **끄기** 선택](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. 포털 **저장**
   ![설정 저장 2 선택](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>암호화 DMV
다음 DMV로 암호화를 확인할 수 있습니다.

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
