---
title: Azure Database for PostgreSQL 백업
description: 장기 보존을 통한 Azure Database for PostgreSQL 백업에 대 한 자세한 내용 (미리 보기)
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: 3c326ff197f18333812438719908daced2b268bb
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173587"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>장기 보존을 사용 하는 Azure Database for PostgreSQL 백업 (미리 보기)

Azure Backup 및 Azure 데이터베이스 서비스는 최대 10 년 동안 백업을 유지 하는 Azure Database for PostgreSQL 서버용 엔터프라이즈급 백업 솔루션을 구축 하는 데 함께 제공 됩니다.

장기 보존 외에도 솔루션은 아래에 나열 된 것과 같은 다양 한 기능을 제공 합니다.

- Azure RBAC (역할 기반 access control)를 사용 하 여 데이터베이스에 대 한 Azure Active Directory 및 관리 서비스 ID (MSI) 인증을 사용 합니다.
- 고객이 개별 데이터베이스 수준에서 예약 및 주문형 백업을 제어 했습니다.
- 데이터베이스 수준은 모든 Postgres server로 또는 blob 저장소에 직접 복원 됩니다.
- 장기 보존.
- 모든 작업 및 작업에 대 한 중앙 모니터링.
- 백업은 별도의 보안 및 장애 도메인에 저장 됩니다. 따라서 원본 서버가 손상 되거나 심지어 종료 되더라도 [백업 자격 증명 모음](backup-vault-overview.md)에서 백업이 안전 하 게 유지 됩니다.
- **Pg_dump** 를 사용 하면 데이터베이스 버전 간에 복원 하거나 백업의 일부만 복원할 수 있으므로 복원에 유연성을 높일 수 있습니다.

이 솔루션은 독립적으로 또는 최대 35 일의 보존을 제공 하는 Azure PostgreSQL에서 제공 하는 기본 백업 솔루션 외에도 사용할 수 있습니다. 기본 솔루션은 최신 백업에서 복구 하려는 경우와 같은 운영 복구에 적합 합니다. Azure Backup 솔루션을 사용 하면 규정 준수 요구와 보다 세분화 된 백업 및 복원 기능을 사용할 수 있습니다.

## <a name="support-matrix"></a>지원 매트릭스

|지원  |세부 정보  |
|---------|---------|
|지원되는 배포   |  Azure Database for PostgreSQL 독립 실행형 단일 서버     |
|지원되는 Azure 지역 |  미국 동부, 미국 동부 2, 미국 중부, 미국 서 부 |
|지원 되는 Azure PostgreSQL 버전    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>기능 고려 사항 및 제한 사항

- 모든 작업은 Azure Portal 에서만 지원 됩니다.
- 최대 데이터베이스 크기에 대 한 권장 제한은 400 GB입니다.
- 지역 간 백업은 지원 되지 않습니다. 즉, Azure PostgreSQL 서버를 다른 지역의 자격 증명 모음에 백업할 수 없습니다. 마찬가지로 자격 증명 모음과 동일한 지역 내에 있는 서버로만 백업을 복원할 수 있습니다.
- 복원 시 데이터만 복구 됩니다. "역할"이 복원 되지 않습니다.
- 미리 보기에서는 테스트 환경 에서만 솔루션을 실행 하는 것이 좋습니다.

## <a name="backup-process"></a>백업 프로세스

1. 이 솔루션은 **pg_dump** 를 사용 하 여 Azure PostgreSQL 데이터베이스의 백업을 수행 합니다.

2. 백업 하려는 Azure PostgreSQL 데이터베이스를 지정 하면 서비스는 서버 및 데이터베이스에서 백업 작업을 수행할 수 있는 올바른 권한 집합 및 액세스 권한이 있는지 확인 합니다.

3. Azure Backup는 백업 확장이 설치 된 작업자 역할을 회전 합니다. 이 확장은 Postgres server와 통신 합니다.

4. 이 확장은 코디네이터와 Azure Postgres 플러그 인으로 구성 됩니다. 코디네이터는 백업, 백업, 복원 등의 다양 한 작업에 대 한 워크플로를 트리거할 책임이 있지만,이 플러그 인은 실제 데이터 흐름을 담당 합니다.
  
5. 선택한 데이터베이스에 대 한 보호 구성을 트리거한 후 백업 서비스는 백업 일정 및 기타 정책 세부 정보를 사용 하 여 코디네이터를 설정 합니다.

6. 예약 된 시간에 코디네이터는 플러그 인과 통신 하 고 **pg_dump**를 사용 하 여 Postgres 서버에서 백업 데이터 스트리밍을 시작 합니다.

7. 플러그 인은 데이터를 백업 자격 증명 모음으로 직접 전송 하 여 스테이징 위치에 대 한 필요성을 제거 합니다. 데이터는 Microsoft 관리 키를 사용 하 여 암호화 되 고 저장소 계정의 Azure Backup 서비스에 의해 저장 됩니다.

8. 데이터 전송이 완료 되 면 코디네이터는 백업 서비스를 사용 하 여 커밋을 확인 합니다.

    ![백업 프로세스](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Azure PostgreSQL 데이터베이스에서 백업 구성

여러 Azure PostgreSQL 서버에서 여러 데이터베이스에 백업을 구성할 수 있습니다. 서비스에서 Postgres 서버를 백업 하는 데 필요한 [필수 구성 요소 권한이](#prerequisite-permissions-for-configure-backup-and-restore) 이미 구성 되어 있는지 확인 합니다.

다음 지침은 Azure Backup를 사용 하 여 Azure PostgreSQL 데이터베이스에서 백업을 구성 하는 단계별 가이드입니다.

1. 프로세스를 시작 하는 방법에는 다음 두 가지가 있습니다.

    1. [Backup 센터](backup-center-overview.md)  ->  **개요**  ->  **백업**으로 이동 합니다.

        ![Backup Center로 이동](./media/backup-azure-database-postgresql/backup-center.png)

        **시작: 백업 구성**에서 **Datasource 유형을** **Azure Database for PostgreSQL**선택 합니다.

        ![시작: 백업 구성에서 데이터 원본 유형을 선택 합니다.](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. 또는 [백업 자격 증명 모음](backup-vault-overview.md)백업으로 바로 이동할 수 있습니다  ->  **Backup**.

        ![백업 자격 증명 모음으로 이동](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![백업 자격 증명 모음에서 백업 선택](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. **백업 구성**에서 Postgres 데이터베이스를 백업 하려는 **백업 자격 증명 모음** 을 선택 합니다. 자격 증명 모음 컨텍스트에 이미 있는 경우이 정보는 미리 채워집니다.

    ![백업 구성에서 백업 자격 증명 모음을 선택 합니다.](./media/backup-azure-database-postgresql/configure-backup.png)

1. **백업 정책을**선택 하거나 만듭니다.

    ![백업 정책 선택](./media/backup-azure-database-postgresql/backup-policy.png)

1. 백업할 리소스 또는 Postgres 데이터베이스를 선택 합니다.

    ![백업할 리소스 선택](./media/backup-azure-database-postgresql/select-resources.png)

1. 자격 증명 모음과 동일한 지역에 있는 경우 구독에 있는 모든 Azure PostgreSQL 서버 목록에서 선택할 수 있습니다. 서버 내의 데이터베이스 목록을 보려면 화살표를 확장 합니다.

    ![서버 선택](./media/backup-azure-database-postgresql/choose-servers.png)

1. 서비스는 선택한 데이터베이스에서 이러한 검사를 실행 하 여 자격 증명 모음에 선택한 Postgres 서버 및 데이터베이스를 백업할 수 있는 권한이 있는지 여부를 확인 합니다.
    1. 계속 하려면 모든 데이터베이스에 대 한 **백업 준비 상태** **를 읽어야 합니다** .
    1. 오류가 있는 경우 오류를 **수정** 하 고 선택에서 데이터베이스를 다시 **유효성** 을 검사 하거나 제거 합니다.

    ![수정할 유효성 검사 오류](./media/backup-azure-database-postgresql/validation-errors.png)

1. **검토 및 구성** 아래의 모든 세부 정보를 확인 하 고 **백업 구성** 을 선택 하 여 작업을 제출 합니다.

    ![검토 및 구성에서 세부 정보 확인](./media/backup-azure-database-postgresql/review-and-configure.png)

1. 트리거된 **백업 구성** 작업은 백업 인스턴스를 만듭니다. 백업 센터 또는 자격 증명 모음 보기의 [백업 인스턴스](backup-center-monitor-operate.md#backup-instances) 창에서 작업의 상태를 추적할 수 있습니다.

    ![백업 인스턴스](./media/backup-azure-database-postgresql/backup-instances.png)

1. 백업은 정책에 정의 된 백업 일정에 따라 트리거됩니다. 작업은 [백업 작업](backup-center-monitor-operate.md#backup-jobs)에서 추적할 수 있습니다. 현재는 지난 7 일간의 작업을 볼 수 있습니다.

    ![백업 작업](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>백업 정책 만들기

1. **백업 센터**  ->  **백업 정책**  ->  **추가**로 이동 합니다. 또는 **backup 자격 증명 모음**  ->  **백업 정책**  ->  **추가**로 이동할 수 있습니다.

    ![백업 정책 추가](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. 새 정책의 **이름을** 입력 합니다.

    ![정책 이름 입력](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. 백업 일정을 정의 합니다. 현재 **주간** 백업을 지원 합니다. 하나 이상의 요일에 백업을 예약할 수 있습니다.

    ![백업 일정 정의](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. **보존** 설정을 정의 합니다. 하나 이상의 보존 규칙을 추가할 수 있습니다. 각 보존 규칙은 특정 백업에 대 한 입력과 해당 백업에 대 한 데이터 저장소 및 보존 기간을 가정 합니다.

1. 백업을 **데이터** 저장소 (핫 계층) 또는 **보관 데이터 저장소** (미리 보기) 중 하나에 저장 하도록 선택할 수 있습니다. 두 개의 데이터 저장소에서 백업이 계층화 되는 경우를 정의 하는 **두 개의 계층화 옵션** 중에서 선택할 수 있습니다.

    - 백업과 보관 데이터 저장소 둘 다에서 백업 복사본을 사용 하려는 경우 **즉시** 복사를 선택 합니다.
    - 백업 데이터 저장소에서 만료 될 때 백업을 보관 데이터 저장소로 이동 하려는 경우 **에는 만료** 날짜를 이동 하도록 선택 합니다.

1. **기본 보존 규칙** 은 다른 보존 규칙이 없을 때 적용 되며 기본값은 3 개월입니다.

    - 보존 기간 범위는 **백업 데이터 저장소**에서 7 일에서 10 년 사이입니다.
    - 보존 기간은 **보관 데이터 저장소**에서 6 개월에서 10 년 사이입니다.

    ![보존 기간 편집](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>보존 규칙은 미리 결정 된 우선 순위 순서로 평가 됩니다. 우선 순위는 **연도별** 규칙의 가장 높은 우선 순위 이며, 그 다음에는 **월별** 및 **주간** 규칙이 적용 됩니다. 다른 규칙이 한정 되지 않은 경우 기본 보존 설정이 적용 됩니다. 예를 들어 동일한 복구 지점은 매주 수행 된 첫 번째 성공한 백업 뿐만 아니라 매월 수행 된 첫 번째 성공한 백업 일 수 있습니다. 그러나 월별 규칙 우선 순위가 주간 규칙의 우선 순위 보다 높기 때문에 매월 수행 된 첫 번째 성공한 백업에 해당 하는 보존은 적용 됩니다.

## <a name="restore"></a>복원

서비스에 대상 서버에 대 한 적절 한 사용 권한 집합이 있는 경우 동일한 구독 내의 Azure PostgreSQL 서버로 데이터베이스를 복원할 수 있습니다. 서비스에서 Postgres 서버를 백업 하는 데 필요한 [필수 구성 요소 권한이](#prerequisite-permissions-for-configure-backup-and-restore) 이미 구성 되어 있는지 확인 합니다.

이 단계별 가이드에 따라 복원을 트리거합니다.

1. 복원 프로세스를 시작 하는 방법에는 두 가지가 있습니다.
    1. [Backup 센터](backup-center-overview.md)  ->  **개요**  ->  **복원**으로 이동 합니다.

    ![백업 센터에서 복원 선택](./media/backup-azure-database-postgresql/backup-center-restore.png)

    **시작: 복원**에서 **데이터 원본 유형을** **Azure Database for PostgreSQL**선택 합니다. **백업 인스턴스**를 선택 합니다.

    ![시작: 복원에서 Datasource 유형 선택](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. 또는 **backup 자격 증명 모음**  ->  **백업 인스턴스로**바로 이동할 수 있습니다. 복원 하려는 데이터베이스에 해당 하는 **백업 인스턴스** 를 선택 합니다.

    ![복원에 대 한 백업 인스턴스](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![백업 인스턴스 목록](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![복원 선택](./media/backup-azure-database-postgresql/select-restore.png)

1. 선택한 백업 인스턴스에 대해 사용 가능한 모든 전체 백업 목록에서 **복구 지점을 선택** 합니다. 기본적으로 최신 복구 지점이 선택 됩니다.

    ![복구 지점 선택](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![복구 지점의 목록](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. **복원 매개 변수**를 입력 합니다. 이 시점에서 **데이터베이스** 복원 및 **파일로 복원**의 두 가지 복원 중에서 선택할 수 있습니다.

1. **데이터베이스로 복원**: 백업 데이터를 복원 하 여 대상 PostgreSQL 서버에 새 데이터베이스를 만듭니다.

    - 대상 서버는 원본 서버와 동일할 수 있습니다. 그러나 원본 데이터베이스를 덮어쓰는 것은 지원 되지 않습니다.
    - 서버는 모든 구독에서 선택할 수 있지만 자격 증명 모음과 동일한 지역에서 선택할 수 있습니다.
    - **검토 + 복원**을 선택 합니다. 이렇게 하면 유효성 검사를 트리거하여 서비스에 대상 서버에 대 한 적절 한 복원 권한이 있는지 확인 합니다.

    ![데이터베이스로 복원](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **파일로 복원**: 대상 저장소 계정 (blob)에 백업 파일을 덤프 합니다.

    - 저장소 계정은 모든 구독에서 선택할 수 있지만 자격 증명 모음과 동일한 지역에서 선택할 수 있습니다.
    - 선택한 저장소 계정에 대해 필터링 된 컨테이너 목록에서 대상 컨테이너를 선택 합니다.
    - **검토 + 복원**을 선택 합니다. 이렇게 하면 유효성 검사를 트리거하여 서비스에 대상 서버에 대 한 적절 한 복원 권한이 있는지 확인 합니다.

    ![파일로 복원](./media/backup-azure-database-postgresql/restore-as-files.png)

1. 정보를 검토 하 고 **복원**을 선택 합니다. 이렇게 하면 **백업 작업**에서 추적할 수 있는 해당 복원 작업이 트리거됩니다.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>백업 및 복원을 구성 하기 위한 필수 조건 권한

Azure Backup은 엄격한 보안 지침을 따릅니다. 네이티브 Azure 서비스인 경우에도 리소스에 대 한 사용 권한은 가정 하지 않으며 사용자가 명시적으로 지정 해야 합니다.  마찬가지로 데이터베이스에 연결 하기 위한 자격 증명은 저장 되지 않습니다. 이는 데이터를 보호 하는 데 중요 합니다. 대신 Azure Active Directory 인증을 사용 합니다.

[이 문서를 다운로드](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) 하 여 자동화 된 스크립트 및 관련 지침을 확인 하세요. 백업 및 복원을 위해 Azure PostgreSQL 서버에 적절 한 사용 권한 집합을 부여 합니다.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>백업 된 Azure PostgreSQL 데이터베이스 관리

**백업 인스턴스에서**수행할 수 있는 관리 작업은 다음과 같습니다.

### <a name="on-demand-backup"></a>주문형 백업

정책에 지정 된 일정에 맞지 않는 백업을 트리거하려면 지금 backup **instances**  ->  **backup**으로 이동 합니다.
연결 된 백업 정책에 정의 된 보존 규칙 목록에서 선택 합니다.

![지금 백업 트리거](./media/backup-azure-database-postgresql/backup-now.png)

![보존 규칙 목록에서 선택](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>보호 중지

백업 항목에 대 한 보호를 중지할 수 있습니다. 그러면 해당 백업 항목에 대 한 관련 복구 지점도 삭제 됩니다. 기존 복구 지점이 유지 되는 동안 보호를 중지 하는 옵션을 아직 제공 하지 않습니다.

![보호 중지](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>정책 변경

백업 인스턴스를 사용 하 여 연결 된 정책을 변경할 수 있습니다.

1. **백업 인스턴스**  ->  **변경 정책**을 선택 합니다.

    ![정책 변경](./media/backup-azure-database-postgresql/change-policy.png)

1. 데이터베이스에 적용 하려는 새 정책을 선택 합니다.

    ![정책 재할당](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 Azure Backup를 사용 하 여 Azure PostgreSQL 데이터베이스를 백업 하기 위한 문제 해결 정보를 제공 합니다.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

백업 하거나 복원할 PG 서버에서 백업 자격 증명 모음 MSI **읽기** 액세스 권한을 부여 합니다.

PostgreSQL 데이터베이스에 대 한 보안 연결을 설정 하기 위해 Azure Backup [관리 서비스 ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 인증 모델을 사용 합니다. 이는 백업 자격 증명 모음이 사용자에 게 명시적으로 권한이 부여 된 리소스에만 액세스할 수 있음을 의미 합니다.

시스템 MSI는 생성 시 자격 증명 모음에 자동으로 할당 됩니다. 이 자격 증명 모음 MSI에 데이터베이스를 백업 하려는 PostgreSQL 서버에 대 한 액세스 권한을 부여 해야 합니다.

단계:

1. Postgres server에서 **Access Control (IAM)** 창으로 이동 합니다.

    ![Access Control 창](./media/backup-azure-database-postgresql/access-control-pane.png)

1. **역할 할당 추가를**선택 합니다.

    ![역할 할당 추가](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 열리는 오른쪽 컨텍스트 창에서 다음을 입력 합니다.<br>

    **역할:** 익숙해야<br>
    **액세스 권한을 할당 합니다.** **백업 자격 증명 모음** 선택<br>
    드롭다운 목록에서 **백업 자격 증명 모음** 옵션을 찾을 수 없는 경우 **Azure AD 사용자, 그룹 또는 서비스 사용자 옵션** 을 선택 합니다.<br>

    ![직무 선택](./media/backup-azure-database-postgresql/select-role.png)

    **선택:** 이 서버 및 해당 데이터베이스를 백업 하려는 백업 자격 증명 모음 이름을 입력 합니다.<br>

    ![백업 자격 증명 모음 이름 입력](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Azure Active Directory를 사용 하 여 인증할 수 있는 데이터베이스 백업 사용자를 만듭니다.

PostgreSQL 서버에 대 한 Azure Active Directory 관리자가 없거나 Azure Active Directory를 사용 하 여 인증할 수 있는 백업 사용자가 없을 때이 오류가 발생할 수 있습니다.

단계:

OSS 서버에 Active Directory 관리자를 추가 합니다.

이 단계는 암호 대신 Azure Active Directory를 사용 하 여 인증할 수 있는 사용자를 통해 데이터베이스에 연결 하는 데 필요 합니다. Azure Database for PostgreSQL의 Azure AD 관리 사용자에 게 **azure_ad_admin**역할이 있습니다. **Azure_ad_admin** 역할만 azure ad를 사용 하 여 인증할 수 있는 새 데이터베이스 사용자를 만들 수 있습니다.

1. 서버 보기의 왼쪽 탐색 창에 있는 Active Directory 관리자 탭으로 이동 하 고 자신 (또는 다른 사용자)을 Active Directory 관리자로 추가 합니다.

    ![Active Directory 관리자 설정](./media/backup-azure-database-postgresql/set-admin.png)

1. AD 관리 사용자 설정을 **저장** 해야 합니다.

    ![Active Directory 관리 사용자 설정 저장](./media/backup-azure-database-postgresql/save-admin-setting.png)

권한 부여 단계를 완료 하기 위해 수행 해야 하는 단계 목록은 [이 문서](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) 를 참조 하세요.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

서버 보기에서 **Azure 서비스에 대 한 액세스 허용** 플래그를 사용 하 여 네트워크 시야를 설정 합니다. 서버 보기의 **연결 보안** 창에서 **Azure 서비스에 대 한 액세스 허용** 플래그를 **예**로 설정 합니다.

![Azure 서비스에 대한 액세스 허용](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>파일로 복원할 때 저장소 계정 컨테이너에 복원할 수 있는 권한

1. Azure Portal를 사용 하 여 백업 자격 증명 모음 MSI에 저장소 계정 컨테이너에 액세스할 수 있는 권한을 부여 합니다.
    1. **저장소 계정**  ->  **Access Control**  ->  **역할 할당 추가**로 이동 합니다.
    1. 백업 자격 증명 모음 MSI에 **저장소 Blob 데이터 참가자** 역할을 할당 합니다.

    ![저장소 Blob 데이터 참가자 역할 할당](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. 또는 Azure CLI [az role 대입문 create](/cli/azure/role/assignment) 명령을 사용 하 여 복원 하는 특정 컨테이너에 세부적인 사용 권한을 부여 합니다.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. 담당자 매개 변수를 자격 증명 모음 MSI의 **응용 프로그램 ID** 로 바꾸고 scope 매개 변수를 사용 하 여 특정 컨테이너를 참조 합니다.
    1. 자격 증명 모음 MSI의 **응용 프로그램 ID** 를 가져오려면 **응용 프로그램 종류**아래에서 **모든 응용 프로그램** 을 선택 합니다.

        ![모든 응용 프로그램 선택](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. 자격 증명 모음 이름을 검색 하 고 응용 프로그램 ID를 복사 합니다.

        ![자격 증명 모음 이름 검색](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>다음 단계

- [백업 자격 증명 모음 개요](backup-vault-overview.md)