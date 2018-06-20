---
title: Azure 스택 SQL 어댑터 리소스 공급자가 제공 하는 데이터베이스를 사용 하 여 | Microsoft Docs
description: 만들기 및 SQL 어댑터 리소스 공급자를 사용 하 여 사용자를 프로 비전 하는 SQL 데이터베이스를 관리 하는 방법
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: a82db16f2012672f6e2669f2fd8198b177f501f3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264185"
---
# <a name="create-sql-databases"></a>SQL 데이터베이스 만들기

만들 수 있으며 사용자 포털에서 데이터베이스 셀프 서비스 관리. Azure 스택 사용자 구독 SQL 데이터베이스 서비스를 포함 하는 제품에 필요 합니다.

1. 에 로그인 하 고 [Azure 스택](azure-stack-poc.md) 사용자 포털입니다.

2. 선택 **새 +** &gt; **데이터 + 저장소 "** &gt; **SQL Server 데이터베이스** &gt; **추가**합니다.

3. 아래 **Create Database**, 필요한 정보를 같은 입력 **데이터베이스 이름** 및 **최대 크기 (MB)** 합니다.

   >[!NOTE]
   >데이터베이스 크기는 최소한을 64MB로 늘릴 수 있어야 합니다. 데이터베이스를 배포 합니다.

   환경에 필요한 다른 설정을 구성 합니다.

4. 아래 **Create Database**선택, **SKU**합니다. 아래 **SKU 선택**를 데이터베이스에 대 한 SKU를 선택 합니다.

   ![데이터베이스 만들기](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >호스팅 서버는 Azure 스택에 추가 되 면 SKU는 할당 하는 있습니다. SKU의 서버를 호스팅하는 풀에서 데이터베이스가 만들어집니다.

5. 선택 **로그인**합니다.
6. 아래 **로그인을 선택**, 기존 로그인을 선택 또는 선택 **새 로그인을 만듭니다 +** 합니다.
7. 아래 **새 로그인**에 대 한 이름을 입력 **데이터베이스 로그인** 및 **암호**합니다.

   >[!NOTE]
   >이러한 설정은이 데이터베이스에만 액세스를 위해 만든 SQL 인증 자격 증명에 설명 합니다. 로그인 사용자 이름과 전역적으로 고유 해야 합니다. 동일한 SKU를 사용 하는 다른 데이터베이스에 대 한 로그인 설정을 다시 사용할 수 있습니다.

   ![새 데이터베이스 로그인 생성](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. 선택 **확인** 데이터베이스 배포를 완료 합니다.

아래 **Essentials**데이터베이스가 배포 된 후 표시 되는,를 메모해는 **연결 문자열**합니다. SQL Server 데이터베이스에 액세스 하는 모든 응용 프로그램에서이 문자열을 사용할 수 있습니다.

![연결 문자열 검색](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Alwayson 데이터베이스

기본적으로 데이터베이스에서 항상 다르게 처리 됩니다 보다 독립 실행형 서버 환경에서. 자세한 내용은 참조 [소개 SQL Server Always On 가용성 그룹에 Azure 가상 컴퓨터](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)합니다.

### <a name="verify-sql-always-on-databases"></a>SQL Alwayson 데이터베이스를 확인 합니다.

다음 화면 캡처 SQL Always On의 데이터베이스 상태를 확인 하려면 SQL Server Management Studio를 사용 하는 방법을 보여 줍니다.

![AlwaysOn 데이터베이스 상태](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On 데이터베이스는 Synchronized로 및 모든 SQL 인스턴스에서 사용할 수 있는 표시 하 고 가용성 그룹에 표시 해야 합니다. 이전 화면 캡처 데이터베이스 예는 newdb1 하 고 해당 상태는 **newdb1 (동기화)** 합니다.

### <a name="delete-an-alwayson-database"></a>AlwaysOn 데이터베이스를 삭제 합니다.

리소스 공급자에서 SQL AlwaysOn 데이터베이스를 삭제 하면 주 복제본 및 가용성 그룹에서 SQL 데이터베이스를 삭제 합니다.

그런 다음 SQL 다른 복제본에서 데이터베이스를 Restoring 상태로 전환 하 고 발생 하지 않는 한 데이터베이스를 삭제 하지 않습니다. 데이터베이스 삭제 되지 않은 경우 보조 복제본이 Not Synchronizing 상태로 이동 합니다.

## <a name="next-steps"></a>다음 단계

[SQL Server 리소스 공급자를 유지 관리](azure-stack-sql-resource-provider-maintain.md)
