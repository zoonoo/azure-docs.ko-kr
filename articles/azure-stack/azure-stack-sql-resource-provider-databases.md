---
title: Azure Stack에서 SQL 어댑터 리소스 공급자가 제공 하는 데이터베이스를 사용 하 여 | Microsoft Docs
description: SQL 어댑터 리소스 공급자를 사용 하 여 프로 비전 하는 SQL database 만들기 및 관리 하는 방법
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: bbb4f72e029a442362b792f3cdb4731e9563687b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244830"
---
# <a name="create-sql-databases"></a>SQL 데이터베이스 만들기

만들 하 고 사용자 포털에서 셀프 서비스 데이터베이스를 관리할 수 있습니다. Azure Stack 사용자는 SQL database 서비스를 포함 하는 제품을 사용 하 여 구독을 해야 합니다.

1. 에 로그인 합니다 [Azure Stack](azure-stack-poc.md) 사용자 포털입니다.

2. 선택 **+ 새로 만들기** &gt; **데이터 + 저장소** &gt; **SQL Server Database** &gt; **추가**합니다.

3. 아래 **Create Database**와 같은 필요한 정보를 입력 **데이터베이스 이름** 하 고 **최대 크기 (mb)** 합니다.

   >[!NOTE]
   >데이터베이스 크기 이상 64MB로 늘릴 수 있어야 합니다. 데이터베이스를 배포 합니다.

   사용자 환경에 대 한 필요에 따라 다른 설정을 구성 합니다.

4. 아래 **Create Database**를 선택 **SKU**합니다. 아래 **SKU 선택**, 데이터베이스에 대 한 SKU를 선택 합니다.

   ![데이터베이스 만들기](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >호스팅 서버는 Azure Stack에 추가 되 면 SKU가 할당 됩니다. 호스팅 서버 SKU에서 풀의 데이터베이스가 만들어집니다.

5. 선택 **로그인**합니다.
6. 아래 **로그인 선택**, 기존 로그인을 선택 또는 선택 **+ 새 로그인 만들기**합니다.
7. 아래 **새 로그인**에 대 한 이름을 입력 **데이터베이스 로그인** 와 **암호**합니다.

   >[!NOTE]
   >이러한 설정은이 데이터베이스에 대 한 액세스에 대해 만든 SQL 인증 자격 증명에 설명 합니다. 로그인 사용자 이름을 전역적으로 고유 해야 합니다. 동일한 SKU를 사용 하는 다른 데이터베이스에 대 한 로그인 설정을 다시 사용할 수 있습니다.

   ![새 데이터베이스 로그인 만들기](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. 선택 **확인** 데이터베이스 배포를 완료 합니다.

아래 **Essentials**데이터베이스를 배포한 후 나와 있는, 메모를 **연결 문자열**합니다. SQL Server 데이터베이스에 액세스 해야 하는 모든 응용 프로그램에서이 문자열을 사용할 수 있습니다.

![연결 문자열 검색](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On 데이터베이스

기본적으로 Always On 데이터베이스 다르게 처리 됩니다 보다 독립 실행형 서버 환경에서. 자세한 내용은 [Introducing SQL Server Always On 가용성 그룹 Azure virtual machines에서](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)합니다.

### <a name="verify-sql-always-on-databases"></a>SQL Always On 데이터베이스를 확인 합니다.

다음 화면 캡처 SQL Always on 데이터베이스 상태를 검색할 SQL Server Management Studio를 사용 하는 방법을 보여 줍니다.

![AlwaysOn 데이터베이스 상태](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On 데이터베이스는 모든 SQL 인스턴스에서 사용할 수 있습니다 및 Synchronized로 표시 하 고 가용성 그룹에 표시 해야 합니다. 이전 화면 캡처에 있는 데이터베이스 예제 newdb1 이며 상태가 **newdb1 (동기화)** 합니다.

### <a name="delete-an-alwayson-database"></a>AlwaysOn 데이터베이스를 삭제 합니다.

리소스 공급자에서 SQL AlwaysOn 데이터베이스를 삭제 하면 주 복제본에서 가용성 그룹에서 SQL 데이터베이스를 삭제 합니다.

그런 다음 SQL 다른 복제본에서 데이터베이스를 Restoring 상태로 전환 하 고 발생 하지 않는 한 데이터베이스를 삭제 하지 않습니다. 데이터베이스 삭제 되지 않은 경우 보조 복제본이 Not Synchronizing 상태로 이동 합니다.

## <a name="next-steps"></a>다음 단계

[SQL Server 리소스 공급자를 유지 관리](azure-stack-sql-resource-provider-maintain.md)
