---
title: "Azure SQL Database 보안 | Microsoft Docs"
description: "Azure SQL Database 보안 기술 및 기능에 대해 자세히 알아봅니다."
services: sql-database
documentationcenter: 
author: DRediske
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-secure, mvc
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/07/2017
ms.author: daredis
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 34815f5b716a38f957392d8955f924eeb6fe621e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---
# <a name="secure-your-azure-sql-database"></a>Azure SQL Database 보안

SQL Database는 방화벽 규칙, 사용자에게 ID 확인을 요구하는 인증 메커니즘 및 역할 기반 멤버 자격과 권한을 통한 데이터 인증을 사용하여 데이터베이스에 대한 액세스를 제한할 뿐만 아니라 행 수준 보안과 동적 데이터 마스킹을 사용하여 데이터베이스에 대한 액세스도 제한함으로써 데이터를 보호합니다.

몇 가지 간단한 단계만 거치면 악의적인 사용자 또는 무단 액세스로부터 데이터베이스를 보호하는 기능을 크게 향상시킬 수 있습니다. 이 자습서에서는 다음에 대해 알아봅니다. 

> [!div class="checklist"]
> * 서버 및 데이터베이스에 대한 방화벽 규칙 설정
> * 보안 연결 문자열을 사용하여 데이터베이스에 연결
> * 사용자 액세스 관리
> * 암호화로 데이터 보호
> * SQL Database 감사 사용
> * SQL Database 위협 감지 사용

이 자습서를 완료하려면 Excel 및 최신 버전의 SSMS([SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx))가 설치되어 있는지 확인합니다.


## <a name="set-up-firewall-rules-for-your-database"></a>데이터베이스에 대한 방화벽 규칙 설정

Azure에서 SQL Database는 방화벽으로 보호됩니다. 기본적으로 다른 Azure 서비스의 연결을 제외하고 서버와 서버 내부의 데이터베이스에 대한 모든 연결은 거부됩니다. 가장 안전한 구성은 ‘Azure 서비스에 대한 액세스 허용’을 [끄기]로 설정하는 것입니다. Azure VM 또는 클라우드 서비스에서 데이터베이스에 연결해야 하는 경우 [예약된 IP](../virtual-network/virtual-networks-reserved-public-ip.md)를 만들고 방화벽을 통해 예약된 IP 주소 액세스만 허용해야 합니다. 

특정 IP 주소에서 연결을 허용하도록 서버에 대한 [SQL Database 서버 수준 방화벽 규칙](sql-database-firewall-configure.md)을 만들려면 다음 단계를 수행합니다. 

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
1. 왼쪽 메뉴에서 **SQL Database**를 클릭하고 **SQL Database** 페이지에서 방화벽 규칙을 구성할 데이터베이스를 클릭합니다. 데이터베이스에 대한 개요 페이지가 열리고 이 페이지에 정규화된 서버 이름(예: **mynewserver-20170313.database.windows.net**)이 표시되며 추가 구성을 위한 옵션도 제공됩니다.

      ![서버 방화벽 규칙](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. 이전 이미지에 표시된 대로 도구 모음에서 **서버 방화벽 설정**을 클릭합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다. 

3. 도구 모음에서 **클라이언트 IP 추가**를 클릭하여 포털에 연결된 컴퓨터의 공용 IP 주소를 추가하거나 수동으로 방화벽 규칙을 입력한 다음 **저장**을 클릭합니다.

      ![set server firewall rule](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. **확인**을 클릭한 후 **X**를 클릭하여 **방화벽 설정** 페이지를 닫습니다.

이제 지정된 IP 주소 또는 IP 주소 범위로 서버의 모든 데이터베이스에 연결할 수 있습니다.

> [!NOTE]
> SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결을 시도하는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 1433을 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다
>

동일한 논리 서버 내의 다른 데이터베이스에 대해 다른 방화벽 설정이 필요한 경우 각 데이터베이스에 대해 데이터베이스 수준 규칙을 만들어야 합니다. 데이터베이스 수준 방화벽 규칙은 Transact-SQL 문을 사용하는 경우에 한해 첫 번째 서버 수준 방화벽 규칙을 구성한 후에만 구성할 수 있습니다. 다음 단계에 따라 데이터베이스 관련 방화벽 규칙을 만듭니다.

1. 예를 들어 [SQL Server Management Studio](./sql-database-connect-query-ssms.md)를 사용하여 데이터베이스에 연결

2. 개체 탐색기에서 방화벽 규칙을 추가할 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.

3. 쿼리 창에서 다음 쿼리를 입력합니다.

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. 도구 모음에서 **실행**을 클릭하여 방화벽 규칙을 만듭니다.

## <a name="connect-to-your-database-using-a-secure-connection-string"></a>보안 연결 문자열을 사용하여 데이터베이스에 연결

클라이언트와 SQL Database 간에 암호화된 보안 연결을 보장하려면1) 암호화된 연결을 요청하고 2) 서버의 인증서를 신뢰하지 않도록 연결 문자열을 구성해야 합니다. 이렇게 하면 TLS(전송 계층 보안)를 사용하여 연결이 설정되고 메시지 가로채기(man-in-the-middle) 공격의 위험을 줄일 수 있습니다. 아래 스크린샷의 ADO.NET에 표시된 것처럼 Azure Portal에서 지원되는 클라이언트 드라이버의 SQL Database에 대해 올바르게 구성된 연결 문자열을 얻을 수 있습니다.

1. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다.

2. 데이터베이스의 **개요** 페이지에서 **데이터베이스 연결 문자열 표시**를 클릭합니다.

3. 전체 **ADO.NET** 연결 문자열을 검토합니다.

    ![ADO.NET 연결 문자열](./media/sql-database-security-tutorial/adonet-connection-string.png)


## <a name="user-management"></a>사용자 관리

사용자를 만들기 전에 먼저 Azure SQL Database가 지원하는 두 가지 인증 유형 중 하나를 선택해야 합니다. 

**SQL 인증**은 논리 서버 내 특정 데이터베이스의 컨텍스트 내에서만 유효한 로그인 및 사용자에 대해 사용자 이름 및 암호를 사용합니다. 

**Azure Active Directory 인증**은 Azure Active Directory에서 관리되는 ID를 사용합니다. 

[Azure Active Directory](./sql-database-aad-authentication.md)를 사용하여 SQL Database에 대해 인증을 수행하려면 채워진 Azure Active Directory가 있어야 진행할 수 있습니다.

SQL 인증을 사용하여 사용자를 만들려면 다음 단계를 수행합니다.

1. 서버 관리자 자격 증명(예: [SQL Server Management Studio](./sql-database-connect-query-ssms.md))을 사용하여 데이터베이스에 연결합니다.

2. 개체 탐색기에서 새 사용자를 추가할 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다. 선택한 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.

3. 쿼리 창에서 다음 쿼리를 입력합니다.

    ```sql
    CREATE USER ApplicationUserUse' WITH PASSWORD = 'YourStrongPassword1';
    ```

4. 도구 모음에서 **실행**을 클릭하여 사용자를 만듭니다.

5. 기본적으로 사용자는 데이터베이스에 연결할 수 있지만 데이터를 읽거나 쓰는 권한은 없습니다. 새로 만든 사용자에게 이러한 권한을 부여하려면 새 쿼리 창에서 다음 두 가지 명령을 실행합니다.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUserUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUserUser;
    ```

새로운 사용자 만들기와 같은 관리자 작업을 실행해야 하는 경우가 아니라면 데이터베이스 수준에서 관리자가 아닌 계정을 만들어서 데이터베이스에 연결하는 것이 가장 좋습니다. Azure Active Directory를 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure Active Directory 자습서](./sql-database-aad-authentication-configure.md)를 검토하세요.


## <a name="protect-your-data-with-encryption"></a>암호화로 데이터 보호

Azure SQL Database 투명한 데이터 암호화(TDE)는 암호화된 데이터베이스에 액세스하는 응용 프로그램을 변경하지 않고도 미사용 데이터를 자동으로 암호화합니다. 데이터베이스에 TDE를 사용하려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 

2. **투명한 데이터 암호화**를 클릭하여 TDE의 구성 페이지를 엽니다.

    ![투명한 데이터 암호화](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. **데이터 암호화**를 켜짐으로 설정하고 **저장**을 클릭합니다.

암호화 프로세스가 백그라운드에서 시작됩니다. [SQL Server Management Studio](./sql-database-connect-query-ssms.md)를 사용하여 `sys.dm_database_encryption_keys` 보기의 encryption_state 열을 쿼리하고 SQL Database에 연결하여 진행률을 모니터링할 수 있습니다.

## <a name="enable-sql-database-auditing"></a>SQL Database 감사 사용

Azure SQL Database 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 기록합니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 잠재적인 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다. SQL Database에 대한 기본 감사 정책을 만들려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다.

2. 설정 블레이드에서 **감사 및 위협 감지**를 선택합니다.

    ![감사 블레이드](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. 데이터베이스 감사 구성 블레이드에서 **서버의 설정 상속** 확인란을 선택하여 서버 설정에 따라 이 데이터베이스를 감사하도록 지정할 수 있습니다. 이 옵션을 선택하면 이 컨텍스트의 서버 감사 설정을 보거나 수정할 수 있는 **서버 감사 설정 보기** 링크가 표시됩니다.

    ![설정 상속](./media/sql-database-security-tutorial/auditing-get-started-server-inherit.png)

4. 서버 수준에서 지정된 형식과 다른 감사 형식(또는 위치)을 사용하는 것을 선호하는 경우 **서버의 감사 설정 상속** 옵션을 **선택 취소**하고, 감사를 **켜고**, **Blob** 감사 유형을 선택합니다.

    > 서버 Blob 감사가 활성화되면 구성된 데이터베이스 감사가 서버 Blob 감사와 나란히 존재하게 됩니다.

    ![감사 설정](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

5. **저장소 세부 정보** 를 선택하여 감사 로그 저장소 블레이드를 엽니다. 로그가 저장되는 Azure 저장소 계정을 선택하고, 이 기간 후 오래된 로그가 삭제되는 보존 기간을 선택한 다음 하단에서 **확인**을 클릭합니다. **팁:** 감사 보고서 서식 파일을 활용하려면 감사되는 모든 데이터베이스에 대해 동일한 저장소 계정을 사용합니다.

    ![탐색 창](./media/sql-database-security-tutorial/auditing-get-started-storage-details.png)

6. 감사 이벤트를 사용자 지정하려면 PowerShell 또는 REST API를 통해 다음 작업을 수행합니다. 자세한 내용은 [Automation(PowerShell/REST API)](#subheading-7) 섹션을 참조하세요.

7. **Save**를 클릭합니다.


## <a name="enable-sql-database-threat-detection"></a>SQL Database 위협 감지 사용

위협 감지는 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다. 사용자는 데이터베이스의 데이터를 액세스, 침범 또는 악용하려는 시도로 인해 의심스러운 이벤트가 발생했는지를 판단하기 위해서 SQL Database 감사를 사용하여 의심스러운 이벤트를 살펴볼 수 있습니다. 위협 감지는 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다.
예를 들어 위협 감지는 잠재적인 SQL 삽입 시도를 나타내는 비정상적인 데이터베이스 활동을 감지합니다. SQL 삽입은 데이터 기반 응용 프로그램 공격에 사용되는 인터넷 상의 일반적인 웹 응용 프로그램 보안 문제 중 하나입니다. 공격자는 데이터베이스의 데이터를 침범하거나 수정하기 위해 응용 프로그램의 취약성을 이용하여 악의적인 SQL 문을 응용 프로그램 항목 필드에 삽입합니다.

1. 모니터링할 SQL Database의 구성 블레이드로 이동합니다. 설정 블레이드에서 **감사 및 위협 감지**를 선택합니다.

    ![탐색 창](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. **감사 및 위협 감지** 구성 블레이드에서 감사를 **켜면** 위협 감지 설정이 표시됩니다.

3. 위협 감지를 **켭니다**.

4. 비정상적인 데이터베이스 활동이 감지되는 경우 보안 경고를 수신할 이메일 목록을 구성합니다.

5. **감사 및 위협 감지** 블레이드에서 **저장**을 클릭하여 새로운 또는 업데이트된 감사 및 위협 감지 정책을 저장합니다.

    ![탐색 창](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    비정상적인 데이터베이스 활동이 감지되면 이메일 알림을 받게 됩니다. 이메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 이벤트 시간을 포함하여 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 가능한 원인에 대한 정보와 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다. 다음 단계에서는 이러한 전자 메일을 받기 위해 수행해야 할 단계를 안내합니다.

    ![위협 감지 전자 메일](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. 전자 메일에서 **Azure SQL 감사 로그** 링크를 클릭하면 Azure Portal이 열리고 의심스러운 이벤트가 발생한 무렵의 시간에 해당하는 감사 레코드가 표시됩니다.

    ![감사 레코드](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. 의심스러운 데이터베이스 활동에 대한 세부 정보(예: SQL 문, 실패 원인, 클라이언트 IP)를 보려면 감사 레코드를 클릭합니다.

    ![레코드 세부 정보](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. 미리 구성된 Excel 템플릿을 열고 의심스러운 이벤트가 발생한 무렵의 시간에 대한 감사 로그를 가져와서 심층적인 분석을 실행하려면 감사 레코드 블레이드에서 **Excel에서 열기**를 클릭합니다.

    > [!NOTE]
    > Excel 2010 이상의 경우 파워 쿼리 및 **빠른 결합** 설정이 필요합니다.

    ![Excel에서 레코드 열기](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. **빠른 결합** 설정을 구성하려면 - **파워 쿼리** 리본 탭에서 **옵션**을 선택하여 옵션 대화 상자를 표시합니다. 개인 정보 섹션을 선택하고 두 번째 옵션 '개인 정보 보호 수준을 무시하고 잠재적으로 성능 향상'을 선택합니다.

    ![Excel 빠른 결합](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. SQL 감사 로그를 로드하려면, 설정 탭의 매개 변수가 바르게 설정되었는지 확인한 후 '데이터' 리본을 선택하고 '모두 새로 고침' 단추를 클릭합니다.

    ![Excel 매개 변수](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. **SQL 감사 로그** 시트에 결과가 표시되며 사용자는 이를 통해 감지된 비정상적인 활동을 심층적으로 분석하고 응용 프로그램의 보안 이벤트에 대한 영향을 완화시킬 수 있습니다.


## <a name="next-steps"></a>다음 단계
몇 가지 간단한 단계만 거치면 악의적인 사용자 또는 무단 액세스로부터 데이터베이스를 보호하는 기능을 크게 향상시킬 수 있습니다. 이 자습서에서는 다음에 대해 알아봅니다. 

> [!div class="checklist"]
> * 서버 및 데이터베이스에 대한 방화벽 규칙 설정
> * 보안 연결 문자열을 사용하여 데이터베이스에 연결
> * 사용자 액세스 관리
> * 암호화로 데이터 보호
> * SQL Database 감사 사용
> * SQL Database 위협 감지 사용

> [!div class="nextstepaction"]
>[SQL Database 성능 향상](sql-database-performance-tutorial.md)


