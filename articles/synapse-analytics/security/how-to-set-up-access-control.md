---
title: Synapse 작업 영역에 대 한 액세스 제어를 설정 하는 방법
description: 이 문서에서는 Azure 역할, Synapse 역할, SQL 사용 권한 및 Git 권한을 사용 하 여 Synapse 작업 영역에 대 한 액세스를 제어 하는 방법을 설명 합니다.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 97f9d0e0037090a8c058eb6e2393451d975e79c6
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472265"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Synapse 작업 영역에 대 한 액세스 제어를 설정 하는 방법 

이 문서에서는 Azure 역할, Synapse 역할, SQL 사용 권한 및 Git 권한을 사용 하 여 Synapse 작업 영역에 대 한 액세스를 제어 하는 방법을 설명 합니다.   

이 가이드에서는 작업 영역을 설정 하 고 많은 Synapse 프로젝트에 적합 한 기본 액세스 제어 시스템을 구성 합니다.  그런 다음 필요한 세부적인 제어를 위한 고급 옵션을 설명 합니다.  

조직의 역할 및 가상 사용자와 정렬 된 보안 그룹을 사용 하 여 Synapse access control을 단순화할 수 있습니다.  액세스를 관리 하려면 보안 그룹에서 사용자를 추가 하 고 제거 하기만 하면 됩니다.

이 연습을 시작 하기 전에 [Synapse access control 개요](./synapse-workspace-access-control-overview.md) 를 읽고 Synapse에서 사용 하는 액세스 제어 메커니즘을 숙지 합니다.   

## <a name="access-control-mechanisms"></a>액세스 제어 메커니즘

> [!NOTE]
> 이 가이드에서 수행 하는 방법은 여러 보안 그룹을 만든 다음 이러한 그룹에 역할을 할당 하는 것입니다. 그룹이 설정 된 후에는 작업 영역에 대 한 액세스를 제어 하기 위해 보안 그룹 내의 구성원 자격을 관리 하기만 하면 됩니다.

Synapse 작업 영역을 보호 하려면 다음 항목을 구성 하는 패턴을 따릅니다.

- **보안 그룹**-유사한 액세스 요구 사항을 가진 사용자를 그룹화 할 수 있습니다.
- **Azure 역할** 을 사용 하 여 SQL 풀을 만들고 관리할 수 있는 사용자를 제어 하 고, 풀 및 통합 런타임을 Apache Spark 하 고, ADLS Gen2 저장소에 액세스할 수 있습니다.
- **Synapse 역할**-게시 된 코드 아티팩트에 대 한 액세스를 제어 하 고, Apache Spark 계산 리소스 및 통합 런타임을 사용 합니다. 
- Sql 풀에 대 한 관리 및 데이터 평면 액세스를 제어 하는 **sql 권한**. 
- Git **권한**-작업 영역에 대 한 git 지원을 구성 하는 경우 소스 제어에서 코드 아티팩트에 액세스할 수 있는 사용자를 제어 합니다. 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Synapse 작업 영역을 보호하는 단계

이 문서에서는 지침을 단순화하기 위해 표준 이름을 사용합니다. 사용자가 선택한 이름으로 바꿉니다.

|설정 | 표준 이름 | Description |
| :------ | :-------------- | :---------- |
| **Synapse 작업 영역** | `workspace1` |  Synapse 작업 영역의 이름입니다. |
| **ADLSGEN2 계정** | `storage1` | 작업 영역에 사용할 ADLS 계정입니다. |
| **컨테이너** | `container1` | 작업 영역에서 기본적으로 사용하게 될 STG1의 컨테이너입니다. |
| **Active Directory 테넌트** | `contoso` | Active Directory 테넌트 이름입니다.|
||||

## <a name="step-1-set-up-security-groups"></a>1단계: 보안 그룹 설정

>[!Note] 
>미리 보기 중에는 Synapse **SYNAPSE SQL 관리자** 및 **Synapse Apache Spark 관리자** 역할에 매핑되는 보안 그룹을 만드는 것이 좋습니다.  더욱 세분화 된 Synapse RBAC 역할 및 범위를 도입 하 여 이제 이러한 새 기능을 사용 하 여 작업 영역에 대 한 액세스를 제어 하는 것이 좋습니다.  이러한 새 역할 및 범위는 더 많은 구성 유연성을 제공 하며, 개발자가 분석 응용 프로그램을 만드는 데 SQL과 Spark를 함께 사용 하는 경우가 많으며 전체 작업 영역 보다는 특정 리소스에 대 한 액세스 권한을 부여 받아야 하는 것을 인식 합니다. Synapse RBAC에 [대해 자세히 알아보세요](./synapse-workspace-synapse-rbac.md) .

작업 영역에 대해 다음과 같은 보안 그룹을 만듭니다.

- **`workspace1_SynapseAdministrators`**-작업 영역을 완벽 하 게 제어 해야 하는 사용자  처음에는이 보안 그룹에 자신을 추가 합니다.
- **`workspace1_SynapseContributors`**-서비스에 코드를 개발, 디버그 및 게시 해야 하는 개발자를 위한 것입니다.   
- **`workspace1_SynapseComputeOperators`**-Apache Spark 풀 및 통합 런타임을 관리 하 고 모니터링 해야 하는 사용자를 위해
- **`workspace1_SynapseCredentialUsers`**-작업 영역 MSI (관리 서비스 id) 자격 증명을 사용 하 여 오케스트레이션 파이프라인을 디버깅 및 실행 하 고 파이프라인 실행을 취소 해야 하는 사용자를 위해 사용 됩니다.   

작업 영역 범위에서 잠시 후에 이러한 그룹에 Synapse 역할을 할당 합니다.  

또한 다음 보안 그룹을 만듭니다. 
- **`workspace1_SQLAdmins`** 작업 영역의 SQL 풀 내에서 SQL Active Directory 관리 권한이 필요한 사용자를 위한 그룹입니다. 

`workspace1_SQLAdmins`만든 그룹은 sql 풀에서 sql 사용 권한을 구성할 때 사용 됩니다. 

기본 설정의 경우에는 이러한 다섯 가지 그룹으로 충분 합니다. 나중에 보안 그룹을 추가 하 여 보다 전문화 된 액세스 권한이 필요한 사용자를 처리 하거나 사용자에 게 특정 리소스에만 액세스 권한을 부여할 수 있습니다.

> [!NOTE]
>- [이 문서](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)에서 보안 그룹을 만드는 방법을 알아보세요.
>- [이 문서](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)에서 다른 보안 그룹에서 보안 그룹을 추가하는 방법을 알아보세요.

>[!Tip]
>개별 Synapse 사용자는 Azure Portal에서 Azure Active Directory를 사용 하 여 그룹 멤버 자격을 확인 하 고 해당 그룹 멤버 자격을 통해 부여 된 역할을 확인할 수 있습니다.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>2 단계: ADLS Gen2 storage 계정 준비

Synapse 작업 영역에는 다음에 대 한 기본 저장소 컨테이너가 사용 됩니다.
  - Spark 테이블의 지원 데이터 파일 저장
  - Spark 작업의 실행 로그
  - 설치 하도록 선택한 라이브러리 관리

저장소에 대 한 다음 정보를 확인 합니다.

- 작업 영역에 사용할 ADLS Gen2 계정입니다. 이 문서에서는이 `storage1` 를 호출 합니다. `storage1` 작업 영역에 대 한 "기본" 저장소 계정으로 간주 됩니다.
- `workspace1`Synapse 작업 영역이 기본적으로 사용 하는 내의 컨테이너입니다. 이 문서에서는이 `container1` 를 호출 합니다. 

- Azure Portal를 사용 하 여 다음 Azure 역할을 보안 그룹에 할당 합니다. `container1` 

  - **저장소 Blob 데이터 참가자** 역할 할당`workspace1_SynapseAdmins` 
  - **저장소 Blob 데이터 참가자** 역할 할당`workspace1_SynapseContributors`
  - **저장소 Blob 데이터 참가자** 역할 할당`workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3단계: Synapse 작업 영역 만들기 및 구성

Azure Portal에서 다음과 같이 Synapse 작업 영역을 만듭니다.

- 구독 선택
- Azure **소유자** 역할을 보유 하 고 있는 리소스 그룹을 선택 하거나 만듭니다.
- 작업 영역 이름 `workspace1`
- `storage1`저장소 계정에 대해 선택
- `container1`"파일 시스템"으로 사용 되는 컨테이너에 대해를 선택 합니다.
- Synapse Studio에서 WS1 열기
- Access Control **관리** 로 이동 하 여  >   다음과 같이 *작업 영역 범위* 에서 Synapse 역할을 보안 그룹에 할당 합니다.
  - **Synapse 관리자** 역할을 다음에 할당 합니다.`workspace1_SynapseAdministrators` 
  - **Synapse 참여자** 역할을에 할당 합니다.`workspace1_SynapseContributors` 
  - **Synapse 계산 연산자** 역할을 다음에 할당 합니다.`workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>4 단계: 기본 저장소 컨테이너에 작업 영역 MSI 액세스 권한 부여

파이프라인을 실행 하 고 시스템 작업을 수행 하려면 Synapse에서 작업 영역 관리 서비스 id (MSI)가 `container1` 기본 ADLS Gen2 계정에 액세스 해야 합니다.

- Azure 포털 열기
- 저장소 계정를 찾은 `storage1` 다음 `container1`
- **Access Control (IAM)** 를 사용 하 여 **저장소 Blob 데이터 참가자** 역할이 작업 영역 MSI에 할당 되었는지 확인 합니다.
  - 할당 되지 않은 경우 할당 합니다.
  - MSI의 이름이 작업 영역과 동일합니다. 이 문서에서는 `workspace1` 입니다.

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>5 단계: 작업 영역에서 Synapse 관리자에 게 Azure 참가자 역할 부여 

SQL 풀, Apache Spark 풀 및 통합 런타임을 만들려면 사용자의 작업 영역에 Azure 참가자 역할이 하나 이상 있어야 합니다. 또한 참가자 역할을 통해 이러한 사용자는 일시 중지 및 크기 조정을 포함 하 여 리소스를 관리할 수 있습니다. Azure Portal 또는 Synapse Studio를 사용 하 여 SQL 풀, Apache Spark 풀 및 통합 런타임을 만드는 경우 리소스 그룹 수준에서 Azure 참가자 역할이 필요 합니다. 

- Azure 포털 열기
- 작업 영역을 찾습니다. `workspace1`
- 에 **Azure 참가자** 역할을 할당 `workspace1` `workspace1_SynapseAdministrators` 합니다. 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>6 단계: SQL Active Directory 관리자 역할 할당

작업 영역 작성자는 작업 영역에 대 한 SQL Active Directory 관리자로 자동으로 설정 됩니다.  이 역할은 단일 사용자 또는 그룹에만 부여할 수 있습니다. 이 단계에서는 작업 영역의 SQL Active Directory 관리자를 보안 그룹에 할당 합니다 `workspace1_SQLAdmins` .  이 역할을 할당 하면 작업 영역의 모든 SQL 풀 및 데이터베이스에 대해 높은 권한의 관리자 권한이이 그룹에 부여 됩니다.   

- Azure 포털 열기
- `workspace1`로 이동
- **설정** 에서 **SQL Active Directory 관리자** 를 선택 합니다.
- **관리자 설정** 을 선택 하 고 다음을 선택 합니다.**`workspace1_SQLAdmins`**

>[!Note]
>6 단계는 선택 사항입니다.  `workspace1_SQLAdmins`그룹에 권한이 낮은 역할을 부여 하도록 선택할 수 있습니다. `db_owner`또는 다른 sql 역할을 할당 하려면 각 sql 데이터베이스에서 스크립트를 실행 해야 합니다. 

## <a name="step-7-grant-access-to-sql-pools"></a>7 단계: SQL 풀에 대 한 액세스 권한 부여

기본적으로 Synapse 관리자 역할이 할당 된 모든 사용자에 게는 `db_owner` 서버를 사용 하지 않는 sql 풀, ' 기본 제공 ' 및 모든 데이터베이스에 대 한 sql 역할도 할당 됩니다.

다른 사용자를 위한 SQL 풀 및 작업 영역 MSI에 대 한 액세스는 SQL 권한을 사용 하 여 제어 됩니다.  SQL 사용 권한을 할당 하려면 sql 스크립트가 생성 된 후 각 SQL 데이터베이스에서 실행 되어야 합니다.  이러한 스크립트를 실행 해야 하는 세 가지 사례가 있습니다.
1. 다른 사용자에 게 서버를 사용 하지 않는 SQL 풀, ' 기본 제공 ' 및 데이터베이스의 데이터베이스에 대 한 액세스 권한 부여
2. 전용 풀 데이터베이스에 대 한 사용자 액세스 권한 부여
3. Sql 풀 데이터베이스에 작업 영역 MSI 액세스 권한을 부여 하 여 SQL 풀 액세스가 필요한 파이프라인이 성공적으로 실행 될 수 있도록 합니다.

예제 SQL 스크립트는 아래에 포함 되어 있습니다.

전용 SQL 풀 데이터베이스에 대 한 액세스 권한을 부여 하려면 작업 영역 작성자 또는 그룹의 모든 구성원에서 스크립트를 실행할 수 있습니다 `workspace1_SQLAdmins` .  

서버를 사용 하지 않는 SQL 풀 ' 기본 제공 '에 대 한 액세스 권한을 부여 하려면 그룹 또는 그룹의 모든 구성원이 스크립트를 실행할 수 있습니다 `workspace1_SQLAdmins`  `workspace1_SynapseAdministrators` . 

> [!TIP]
> 작업 영역 수준에서 sysadmin 역할을 사용자에 게 할당할 수 있는 [작업 영역 범위 권한](#workspace-scoped-permission) 섹션을 제외 하 고 **각** sql 풀에 대해 사용자 액세스 권한을 부여 하려면 아래 단계를 실행 해야 합니다.

### <a name="step-71-serverless-sql-pool-built-in"></a>7.1 단계: 서버를 사용 하지 않는 SQL 풀, 기본 제공

이 섹션에는 서버를 사용 하지 않는 SQL 풀 ' 기본 제공 '의 모든 데이터베이스 또는 특정 데이터베이스에 액세스할 수 있는 권한을 사용자에 게 부여 하는 방법을 보여 주는 스크립트 예제가 있습니다.

> [!NOTE]
> 스크립트 예제에서 *alias* 를 액세스 권한이 부여 된 사용자 또는 그룹의 별칭으로 바꾸고, 사용 중인 회사 도메인에 *도메인* 을 사용 합니다.

#### <a name="database-scoped-permission"></a>데이터베이스 범위 권한

서버를 사용 하지 않는 **단일** SQL 데이터베이스에 사용자에 대 한 액세스 권한을 부여 하려면 다음 예제의 단계를 따르세요.

1. 로그인 만들기

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. 사용자 만들기

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. 지정된 역할의 구성원에 사용자 추가

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>작업 영역 범위 권한

작업 영역에서 서버를 사용 하지 않는 **모든** SQL 풀에 대 한 모든 권한을 부여 하려면 다음 예제의 스크립트를 사용 합니다.

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>7.2 단계: 전용 SQL 풀

**단일** 전용 sql 풀 데이터베이스에 대 한 액세스 권한을 부여 하려면 Synapse SQL 스크립트 편집기에서 다음 단계를 수행 합니다.

1. 대상 데이터베이스에서 다음 명령을 실행 하 여 데이터베이스에서 사용자를 만듭니다. *연결* 대상 드롭다운을 사용 하 여 선택 합니다.

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. 다음과 같이 사용자에게 데이터베이스에 액세스할 수 있는 역할을 부여합니다.

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* 및 *db_datawriter* 는 *db_owner* 권한을 부여 하는 것이 바람직하지 않은 경우 읽기/쓰기 권한에 대해 작동할 수 있습니다.
> Spark 사용자가 Spark에서 또는 SQL 풀에서 직접 읽거나 쓸 수 있도록 하려면 *db_owner* 권한이 필요 합니다.

사용자를 만든 후 쿼리를 실행 하 여 서버를 사용 하지 않는 SQL 풀에서 저장소 계정을 쿼리할 수 있는지 확인 합니다.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>7.3 단계: Synapse 파이프라인 실행에 대 한 SQL 액세스 제어

### <a name="workspace-managed-identity"></a>작업 영역 관리 id

> [!IMPORTANT]
> SQL 풀을 참조 하는 데이터 집합 또는 작업을 포함 하는 파이프라인을 성공적으로 실행 하려면 작업 영역 id에 SQL 풀에 대 한 액세스 권한을 부여 해야 합니다.

각 SQL 풀에서 다음 명령을 실행 하 여 작업 영역 관리 시스템 id가 SQL 풀 데이터베이스에서 파이프라인을 실행할 수 있도록 합니다.  

>[!note]
>아래 스크립트에서 전용 SQL 풀 데이터베이스용으로 databasename은 풀 이름과 동일 합니다.  서버를 사용 하지 않는 SQL 풀 ' 기본 제공 '의 데이터베이스의 경우 databasename은 데이터베이스의 이름입니다.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

동일한 SQL 풀에서 다음 스크립트를 실행하여 이 권한을 제거할 수 있습니다.

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>8 단계: 보안 그룹에 사용자 추가

액세스 제어 시스템의 초기 구성이 완료 되었습니다.

액세스를 관리 하기 위해 사용자가 설정한 보안 그룹에 사용자를 추가 하 고 제거할 수 있습니다.  Synapse 역할에 사용자를 수동으로 할당할 수는 있지만, 이렇게 하면 사용 권한이 일관 되 게 구성 되지 않습니다. 수동으로 할당하지 말고, 보안 그룹에 사용자를 추가 또는 제거하기만 하세요.

## <a name="step-9-network-security"></a>9 단계: 네트워크 보안

작업 영역을 보호 하는 마지막 단계로 다음을 사용 하 여 네트워크 액세스를 보호 해야 합니다.
- [작업 영역 방화벽](./synapse-workspace-ip-firewall.md)
- [관리형 가상 네트워크](./synapse-workspace-managed-vnet.md) 
- [전용 끝점](./synapse-workspace-managed-private-endpoints.md)
- [Private Link](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>10 단계: 완료

작업 영역이 완전히 구성되었으며 보안이 설정되었습니다.

## <a name="supporting-more-advanced-scenarios"></a>고급 시나리오 지원

이 가이드에서는 기본 액세스 제어 시스템을 설정 하는 방법을 중점적으로 설명 합니다. 추가 보안 그룹을 만들고 이러한 그룹을 더 구체적인 범위에서 보다 세부적인 역할에 할당 하 여 고급 시나리오를 지원할 수 있습니다. 다음과 같은 경우를 고려해 보세요.

CI/CD를 비롯 한 고급 개발 시나리오를 위해 작업 영역에 대 한 **Git 지원 사용** .  Git 모드에서 Git 사용 권한은 사용자가 작업 분기에 대 한 변경 내용을 커밋할 수 있는지 여부를 결정 합니다.  서비스에 게시는 공동 작업 분기에서 발생 합니다.  작업 분기에서 업데이트를 개발 하 고 디버깅 해야 하지만 라이브 서비스에 변경 내용을 게시할 필요가 없는 개발자를 위한 보안 그룹을 만드는 것이 좋습니다.

특정 리소스에 대 한 **개발자 액세스를 제한** 합니다.  특정 리소스에만 액세스 해야 하는 개발자를 위해 더욱 세분화 되는 보안 그룹을 추가로 만들 수 있습니다.  이러한 그룹에 특정 Spark 풀, 통합 런타임 또는 자격 증명으로 범위가 한정 된 적절 한 Synapse 역할을 할당 합니다.

**연산자가 코드 아티팩트에 액세스** 하지 못하도록 제한 합니다.  Synapse 계산 리소스의 작동 상태를 모니터링 해야 하는 운영자를 위한 보안 그룹을 만들고, 코드에 액세스 하거나 서비스에 업데이트를 게시 하지 않아도 되는 로그를 확인 합니다. 이러한 그룹에 특정 Spark 풀 및 통합 런타임에 범위가 지정 된 계산 연산자 역할을 할당 합니다.  

## <a name="next-steps"></a>다음 단계

[SYNAPSE RBAC 역할 할당을 관리 하는 방법](./how-to-manage-synapse-rbac-role-assignments.md) 에 대해 알아봅니다. [Synapse 작업 영역](../quickstart-create-workspace.md) 만들기
