---
title: 고객 관리 키를 사용 하 여 단일 서버 데이터 암호화 Azure Database for PostgreSQL
description: 고객 관리 키를 사용 하 여 단일 서버 데이터 암호화 Azure Database for PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: df2504b7e65a0087c9a8e20d94c596a19494069c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903939"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-customer-managed-key"></a>고객 관리 키를 사용 하 여 단일 서버 데이터 암호화 Azure Database for PostgreSQL

> [!NOTE]
> 지금은이 기능을 사용 하기 위해 액세스를 요청 해야 합니다. 이렇게 하려면 AskAzureDBforPostgreSQL@service.microsoft.com에 문의 하세요.

고객 관리 키를 사용 하 여 단일 서버 데이터 암호화를 Azure Database for PostgreSQL 하 여 미사용 데이터 보호를 위해 Bring Your Own Key (BYOK) 할 수 있으며, 조직에서 키 및 데이터 관리의 의무 분리를 구현할 수 있습니다. 고객이 관리 하는 암호화를 사용 하 여 키의 수명 주기 (키 생성, 업로드, 회전, 삭제), 키 사용 권한 및 키에 대 한 작업 감사에 대 한 모든 제어를 담당 합니다.

단일 서버 Azure Database for PostgreSQL에 대해 서버 수준에서 데이터 암호화가 설정 됩니다. 이러한 형태의 데이터 암호화를 사용 하면 키가 DEK (데이터베이스 암호화 키)를 암호화 하는 데 사용 됩니다 .이 키는 고객 소유의 [AKV (고객 Azure Key Vault 관리)](https://docs.microsoft.com/azure/key-Vault/key-Vault-secure-your-key-Vault)및 클라우드 기반 외부 키 관리 시스템에 저장 된 고객이 관리 하는 비대칭 키입니다. AKV는 항상 사용 가능 하며 FIPS 140-2 수준 2 유효성 검사 된 Hsm (하드웨어 보안 모듈)에 의해 선택적으로 지원 되는 RSA 암호화 키에 대 한 확장 가능한 보안 저장소를 제공 합니다. 저장 된 키에 대 한 직접 액세스를 허용 하지 않지만 권한 있는 엔터티에 대 한 키를 사용 하 여 암호화/암호 해독 서비스를 제공 합니다. 키는 Key Vault에서 생성 되거나 [온-프레미스 HSM 장치에서 Key Vault으로 전송 되거나 전송](https://docs.microsoft.com/azure/key-Vault/key-Vault-hsm-protected-keys)될 수 있습니다.

> [!NOTE]
> 이 기능은 Azure Database for PostgreSQL 단일 서버가 범용 및 메모리 액세스에 최적화 된 가격 책정 계층을 지 원하는 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="benefits"></a>혜택

Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화는 다음과 같은 이점을 제공 합니다.
* 암호화 키에 대 한 투명도 및 세부적인 제어 및 관리 향상 
* Azure Key Vault에서 호스트 하는 중앙 관리 및 키 구성 
* 조직 내의 키 및 데이터 관리에서 의무 분리를 구현 하는 기능
* 조직 내의 데이터 관리에서 키 관리를 분리 하 여 관리자가 키 액세스 권한을 취소 하 여 암호화 된 데이터베이스에 액세스할 수 없도록 할 수 있습니다 Key Vault 
* Microsoft에서 암호화 키를 보거나 추출할 수 없도록 설계 되었기 때문에 최종 고객 으로부터 더 높은 신뢰를 Azure Key Vault.

## <a name="terminology-and-description"></a>용어 및 설명

**DEK(데이터 암호화 키)** - 파티션이나 데이터 블록을 암호화하는 데 사용되는 대칭 AES256 키입니다. 서로 다른 키로 각 데이터 블록을 암호화하면 암호 분석 공격이 더욱 어려워집니다. DEK에 대한 액세스는 특정 블록을 암호화하고 암호 해독하는 리소스 공급자 또는 애플리케이션 인스턴스에 필요합니다. DEK가 새 키로 대체되면 연결된 블록의 데이터만 새 키로 다시 암호화해야 합니다.

**KEK (키 암호화 키)** -데이터 암호화 키를 암호화 하는 데 사용 되는 암호화 키입니다. Key Vault 유지 하지 않는 키 암호화 키를 사용 하 여 데이터 암호화 키 자체를 암호화 하 고 제어할 수 있습니다. KEK에 액세스할 수 있는 엔터티는 DEK가 필요한 엔터티와 다를 수 있습니다. KEK는 DEK를 암호 해독하는 데 필요하므로 KEK는 실질적으로 KEK를 삭제함으로써 DEK를 효과적으로 삭제할 수 있는 단일 지점이 됩니다.

키 암호화 키로 암호화 된 데이터 암호화 키는 별도로 저장 되며 키 암호화 키에 대 한 액세스 권한이 있는 엔터티만 이러한 데이터 암호화 키의 암호를 해독할 수 있습니다. 자세한 내용은 [미사용 암호화의 보안](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)을 참조 하세요.

## <a name="how-data-encryption-with-customer-managed-key-works"></a>고객 관리 키로 데이터 암호화가 작동 하는 방식

![사용자 고유의 키 개요 가져오기](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

PostgreSQL 서버에서 DEK의 암호화를 위해 AKV에 저장 된 고객 관리 키를 사용할 수 있도록 하려면 Key Vault 관리자가 고유 id를 사용 하 여 서버에 대 한 다음 액세스 권한을 부여 해야 합니다.
* **get** -Key Vault에 있는 키의 공용 파트 및 속성을 검색 합니다.
* **wrapKey** -dek를 보호 (암호화) 할 수 있음
* **unwrapKey** -dek를 보호 해제 (암호 해독) 할 수 있음

Key Vault 관리자는 [Key Vault 감사 이벤트에 대 한 로깅을 사용 하도록 설정](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-Vault)하 여 나중에 감사할 수 있습니다.

서버에서 Key Vault에 저장 된 고객 관리 키를 사용 하도록 구성 된 경우 서버는 암호화를 위해 DEK를 Key Vault으로 보냅니다. Key Vault은 사용자 데이터베이스에 저장 된 암호화 된 DEK를 반환 합니다. 마찬가지로, 필요한 경우 서버는 암호 해독을 위해 보호 된 DEK를 Key Vault 보냅니다. 로깅 기능을 사용 하는 경우 감사자는 Azure Monitor을 사용 하 여 Key Vault AuditEvent 로그를 검토할 수 있습니다.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화를 구성 하기 위한 요구 사항

### <a name="requirements-for-configuring-akv"></a>AKV 구성에 대 한 요구 사항

* Key Vault 및 Azure Database for PostgreSQL 단일 서버는 동일한 Azure Active Directory (AAD) 테 넌 트에 속해야 합니다. 교차 테 넌 트 Key Vault 및 서버 상호 작용은 지원 되지 않습니다. 나중에 리소스를 이동 하려면 데이터 암호화를 다시 구성 해야 합니다. 리소스 이동에 대해 자세히 알아보세요.
* Key Vault에서 일시 삭제 기능을 사용 하도록 설정 하 여 데이터 손실 실수로 인 한 키 또는 Key Vault 삭제를 방지 해야 합니다. 일시 삭제 된 리소스는 고객이 복구 하거나 제거 하지 않는 한 90 일간 보존 됩니다. 복구 및 제거 작업에는 Key Vault 액세스 정책에 연결 된 고유한 사용 권한이 있습니다. 일시 삭제 기능은 기본적으로 해제 되어 있으며 Powershell 또는 CLI를 통해 사용 하도록 설정할 수 있습니다. Azure Portal를 통해 사용 하도록 설정할 수 없습니다.
* 고유한 관리 id를 사용 하 여 **get, wrapKey, unwrapKey** 권한을 사용 하 여 Key Vault에 대 한 단일 서버 액세스를 Azure Database for PostgreSQL에 부여 합니다. Azure Portal를 사용 하는 경우 PostgreSQL 단일 서버에서 데이터 암호화를 사용 하도록 설정 하면 고유 id가 자동으로 생성 됩니다. Azure Portal를 사용 하는 경우 자세한 단계별 지침은 [PostgreSQL 단일 서버에 대 한 데이터 암호화 구성](howto-data-encryption-portal.md) 을 참조 하세요.

* AKV에서 방화벽을 사용 하는 경우 *신뢰할 수 있는 Microsoft 서비스에서 방화벽을 우회 하도록 허용*옵션을 사용 하도록 설정 해야 합니다.

### <a name="requirements-for-configuring-customer-managed-key"></a>고객 관리 키를 구성 하기 위한 요구 사항
* DEK를 암호화 하는 데 사용 되는 고객 관리 키는 비대칭, RSA 2028만 가능 합니다.
* 키 활성화 날짜 (설정 된 경우)는 과거 날짜와 시간 이어야 합니다. 만료 날짜 (설정 된 경우)는 미래의 날짜 및 시간 이어야 합니다.
* 키는 *사용* 상태 여야 합니다.

* Key Vault에 기존 키를 가져오는 경우 지원 되는 파일 형식 (`.pfx`, `.byok`, `.backup`)에 제공 해야 합니다.

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>고객 관리 키를 사용 하 여 데이터 암호화를 사용 하는 경우 권장 사항

### <a name="recommendation-for-configuring-akv"></a>AKV 구성에 대 한 권장 사항

* Key Vault에 대 한 리소스 잠금을 설정 하 여이 중요 한 리소스를 삭제할 수 있는 사용자를 제어 하 고 실수로 또는 무단 삭제를 방지할 수 있습니다. 리소스 잠금에 대 한 자세한 정보.
* 모든 암호화 키에 대 한 감사 및 보고 사용: Key Vault 다른 보안 정보 및 이벤트 관리 도구에 쉽게 삽입할 수 있는 로그를 제공 합니다. Azure Monitor Log Analytics는 이미 통합 된 서비스의 한 예입니다.

* Key Vault 및 Azure Database for PostgreSQL 단일 서버가 동일한 지역에 있는지 확인 하 여 DEK 래핑/래핑 해제 작업에 더 빠르게 액세스할 수 있도록 합니다. 

### <a name="recommendation-for-configuring-customer-managed-key"></a>고객 관리 키 구성에 대 한 권장 사항

* 고객 관리 키 (KEK)의 복사본을 안전한 장소에 보관 하거나 에스크로 서비스로 에스크로 하세요.

* 키가 Key Vault에서 생성 되는 경우 처음으로 AKV의 키를 사용 하기 전에 키 백업을 만듭니다. 백업은 Azure Key Vault 으로만 복원할 수 있습니다. [AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) 명령에 대해 자세히 알아보세요. 

## <a name="inaccessible-customer-managed-key-condition"></a>고객 관리 키 조건에 액세스할 수 없음

AKV (Azure Key Vault)에서 고객 관리 키를 사용 하 여 데이터 암호화를 구성한 경우 서버를 온라인 상태로 유지 하려면이 키에 대 한 지속적인 액세스가 필요 합니다. 서버가 10 분 이내에 AKV에서 고객이 관리 하는 키에 액세스할 수 없는 경우 서버는 해당 오류 메시지와 함께 모든 연결을 거부 하기 시작 하 고 서버 상태를 **액세스할 수**없음으로 변경 합니다. 액세스할 수 없는 상태의 데이터베이스에 대해 허용 되는 유일한 작업은 삭제 하는 것입니다.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Azure Key Vault에서 실수로 인 한 키 액세스 해지 (AKV)

Key Vault에 대 한 충분 한 액세스 권한이 있는 사용자가 실수로 키에 대 한 서버 액세스를 사용 하지 않도록 설정할 수 있습니다.
* 서버에서 Key Vault의 get, wrapKey, unwrapKey 권한 취소
* 키 삭제
* Key Vault 삭제
* Key Vault의 방화벽 규칙 변경

* Azure Active Directory에서 서버의 관리 되는 id를 삭제 하는 중

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Key Vault에서 고객이 관리 하는 키 모니터링

데이터베이스 상태를 모니터링 하 고 TDE 보호기 액세스 손실에 대 한 경고를 사용 하도록 설정 하려면 다음 Azure 기능을 구성 합니다.

* [Azure Resource Health](../service-health/resource-health-overview.md) -고객 키에 대 한 액세스 권한이 손실 된 액세스 불가능 데이터베이스는 데이터베이스에 대 한 첫 번째 연결이 거부 된 후 "액세스할 수 없음"으로 표시 됩니다.
* [활동 로그](../service-health/alerts-activity-log-service-notifications.md) -고객이 관리 하는 Key Vault의 고객 키에 대 한 액세스가 실패 하는 경우 항목이 활동 로그에 추가 됩니다. 이러한 이벤트에 대 한 경고를 만들면 최대한 빨리 액세스를 복구할 수 있습니다.

* 사용자의 기본 설정에 따라 알림 및 경고를 보내도록 [작업 그룹](../azure-monitor/platform/action-groups.md) 을 정의할 수 있습니다 (예: 이메일/SMS/푸시/음성, 논리 앱, 웹 후크, Itsm 또는 Automation Runbook).

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Key Vault에서 고객의 관리 키를 사용 하 여 복원 및 복제

Key Vault에 저장 된 고객의 관리 키를 사용 하 여 Azure Database for PostgreSQL 단일 서버를 암호화 한 후에는 로컬 또는 지역 복원 작업을 수행 하거나 읽기 복제본을 통해 새로 생성 된 서버 복사본도 동일한 고객의으로 암호화 됩니다. 관리 되는 키입니다. 그러나 암호화를 위해 새로운 고객의 관리 키를 반영 하도록 변경할 수 있습니다. 고객 관리 키가 변경 되 면 서버의 이전 백업에서 최신 키를 사용 하기 시작 합니다.

복원 또는 복제본을 만드는 동안 고객이 관리 하는 데이터 암호화를 설정 하는 동안 문제를 방지 하려면 master 및 복원/복제 서버에서 다음 단계를 수행 하는 것이 중요 합니다.

* Master Azure Database for PostgreSQL 단일 서버에서 복원 또는 읽기 복제본 만들기 프로세스를 시작 합니다.
* 새로 만든 서버 (복원 된/복제본)는 고유 id에 아직 Azure Key Vault (AKV)에 대 한 권한이 부여 되지 않았기 때문에 액세스할 수 없는 상태로 유지 됩니다.
* 복원/복제 서버에서 데이터 암호화 설정에서 고객 관리 키의 유효성을 다시 검사 하 여 새로 만든 서버에 AKV에 저장 된 키에 대 한 줄 바꿈/래핑 해제 권한이 제공 되도록 합니다.

* 복원/복제 서버 뿐만 아니라 마스터에서 데이터 암호화가 유지 되도록 하려면 위의 단계를 모두 수행 해야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal](howto-data-encryption-portal.md)를 사용 하 여 Azure Database For PostgreSQL 단일 서버에 대해 고객이 관리 하는 키를 사용 하 여 데이터 암호화를 설정 하는 방법을 알아봅니다.
