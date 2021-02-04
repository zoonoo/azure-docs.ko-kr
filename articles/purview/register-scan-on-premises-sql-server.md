---
title: 온-프레미스 SQL server 등록 및 검사
description: 이 자습서에서는 자체 호스팅 IR을 사용 하 여 온-프레미스 SQL server를 검색 하는 방법을 설명 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 26012b23a10f560158e3ba3919e12f5c15759189
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539318"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>온-프레미스 SQL server 등록 및 검사

이 문서에서는 부서의 범위에 SQL server 데이터 원본을 등록 하 고 검색을 설정 하는 방법을 설명 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SQL server 온-프레미스 데이터 원본은 다음과 같은 기능을 지원 합니다.

- 온-프레미스 네트워크 또는 Azure VM에 설치 된 SQL server에서 메타 데이터 및 분류를 캡처하기 위한 **전체 및 증분 검색**

- ADF 복사/데이터 흐름 작업을 위한 데이터 자산 간 **계보**

SQL server 온-프레미스 데이터 원본은 다음을 지원 합니다.

- sql server 2019의 모든 SQL 버전에서 SQL server 2000로 다시

- 인증 방법: SQL 인증

### <a name="known-limitations"></a>알려진 제한 사항

Azure 부서의 범위는 SQL Server의 [뷰](/sql/relational-databases/views/views) 검색을 지원 하지 않습니다.

## <a name="prerequisites"></a>전제 조건

- 데이터 원본을 등록 하기 전에 Azure 부서의 범위 계정을 만듭니다. 부서의 범위 계정을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.

- [자체 호스팅 integration runtime](manage-integration-runtimes.md) 을 설정 하 여 데이터 원본을 검색 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

SQL server 온-프레미스에 대 한 인증을 설정 하는 방법은 한 가지 뿐입니다.

- SQL 인증

### <a name="sql-authentication"></a>SQL 인증

SQL 계정에는 **master** 데이터베이스에 대 한 액세스 권한이 있어야 합니다. 가 `sys.databases` master 데이터베이스에 있기 때문입니다. 부서의 범위 스캐너는 `sys.databases` 서버에서 모든 SQL 데이터베이스를 찾기 위해 열거 해야 합니다.

#### <a name="using-an-existing-server-administrator"></a>기존 서버 관리자 사용

기존 sa (서버 관리자) 사용자를 사용 하 여 온-프레미스 SQL server를 검색할 계획인 경우 다음을 확인 합니다.

1. `sa` 는 Windows 인증 계정이 아닙니다.

2. 사용할 서버 수준 로그인에는 public 및 sysadmin의 서버 역할이 있어야 합니다. 서버에 연결 하 고, SSMS (SQL Server Management Studio)로 이동 하 고, 보안으로 이동 하 고, 사용 하려는 로그인을 선택 하 고, **속성** 을 마우스 오른쪽 단추로 클릭 한 다음 **서버 역할** 을 선택 하 여이를 확인할 수 있습니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="서버 수준 로그인입니다.":::

#### <a name="creating-a-new-login-and-user"></a>새 로그인 및 사용자 만들기

SQL server를 검색할 수 있는 새 로그인 및 사용자를 만들려면 다음 단계를 수행 합니다.

1. SSMS (SQL Server Management Studio)로 이동 하 여 서버에 연결 하 고 보안으로 이동한 후 로그인을 마우스 오른쪽 단추로 클릭 하 고 새 로그인을 만듭니다. SQL 인증을 선택 해야 합니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="새 로그인 및 사용자를 만듭니다.":::

2. 왼쪽 탐색 영역에서 서버 역할을 선택 하 고 공용 역할이 할당 되었는지 확인 합니다.

3. 왼쪽 탐색 영역에서 사용자 매핑을 선택 하 고 맵에서 모든 데이터베이스를 선택 하 고 데이터베이스 역할: **db_datareader** 을 선택 합니다.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="사용자 매핑.":::

4. 확인을 클릭하여 저장합니다.

5. 마우스 오른쪽 단추로 클릭 하 고 **속성** 을 선택 하 여 만든 사용자로 다시 이동 합니다. 새 암호를 입력 하 고 확인 합니다. ' 기존 암호 지정 '을 선택 하 고 이전 암호를 입력 합니다. **새 로그인을 만드는 즉시 암호를 변경 해야 합니다.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="암호를 변경 합니다.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>부서의 범위에서 SQL 로그인 암호를 key vault에 저장 하 고 자격 증명 만들기

1. Azure portal1에서 주요 자격 증명 모음으로 이동 합니다. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택 하 고 SQL server 로그인의 *암호로* **이름과** **값** 을 입력 합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 **사용자 이름** 및 **암호** 를 사용하여 검사를 설정하기 위한 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

## <a name="register-a-sql-server-data-source"></a>SQL server 데이터 원본 등록

1. Purview 계정으로 이동합니다.

1. 왼쪽 탐색의 원본 및 스캔에서 **Integration** runtime을 선택 합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정 되지 않은 경우 온-프레미스 네트워크에 대 한 액세스 권한이 있는 온-프레미스 또는 Azure VM에서 검색을 위한 자체 호스팅 통합 런타임을 만들려면 [여기](manage-integration-runtimes.md) 에 설명 된 단계를 따르세요.

1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.

1. **등록** 을 선택합니다.

1. **SQL server** 를 선택한 다음 **계속**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="SQL 데이터 원본을 설정 합니다.":::

5. 친숙 한 이름 및 서버 끝점을 입력 한 다음 **마침** 을 선택 하 여 데이터 원본을 등록 합니다. 예를 들어 SQL server FQDN이 **foobar.database.windows.net** 인 경우 서버 끝점으로 *foobar* 을 입력 합니다.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
