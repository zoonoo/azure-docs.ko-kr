---
title: 고객 관리 키가 있는 데이터 암호화 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: 고객 관리 키가 있는 PostgreSQL 단일 서버 데이터 암호화용 Azure 데이터베이스는 미사용 데이터 보호를 위해 BYOK(사용자 고유의 키)를 가져올 수 있도록 합니다. 또한 조직은 키와 데이터의 관리에서 업무 분리를 구현할 수 있습니다.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 20e01e681c382e3c9c69f76c95a90f709f409d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297025"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>고객 관리 키가 있는 PostgreSQL 단일 서버 데이터 암호화용 Azure 데이터베이스

> [!NOTE]
> 이 때 이 기능을 사용하려면 액세스를 요청해야 합니다. 이렇게 하려면 에 AskAzureDBforPostgreSQL@service.microsoft.com문의하십시오.

PostgreSQL 단일 서버에 대한 Azure 데이터베이스에 대한 고객 관리 키가 있는 데이터 암호화를 사용하면 미사용 데이터 보호를 위해 BYOK(사용자 고유의 키)를 가져올 수 있습니다. 또한 조직은 키와 데이터의 관리에서 업무 분리를 구현할 수 있습니다. 고객 관리 암호화를 사용하면 키의 수명 주기, 키 사용 권한 및 키에 대한 작업 감사에 대한 책임이 있습니다.

PostgreSQL 단일 서버에 대한 Azure 데이터베이스에 대한 고객 관리 키가 있는 데이터 암호화는 서버 수준에서 설정됩니다. 지정된 서버의 경우 KEK(키 암호화 키)라고 하는 고객 관리 키가 서비스에서 사용되는 DEK(데이터 암호화 키)를 암호화하는 데 사용됩니다. KEK는 고객 소유 및 고객 관리 [Azure 키 볼트](../key-vault/key-Vault-secure-your-key-Vault.md) 인스턴스에 저장된 비대칭 키입니다. KEK(키 암호화 키) 및 DEK(데이터 암호화 키)는 이 문서의 후반부에서 자세히 설명합니다.

Key Vault는 클라우드 기반의 외부 키 관리 시스템입니다. 고가용성이며 FIPS 140-2 레벨 2 검증된 하드웨어 보안 모듈(HSM)으로 선택적으로 지원되는 RSA 암호화 키에 대한 확장 가능하고 안전한 스토리지를 제공합니다. 저장된 키에 대한 직접 액세스는 허용되지 않지만 인증된 엔터티에 암호화 및 암호 해독 서비스를 제공합니다. 키 볼트는 키를 생성하거나 가져오거나 [온-프레미스 HSM 장치에서 전송할](../key-vault/key-Vault-hsm-protected-keys.md)수 있습니다.

> [!NOTE]
> 이 기능은 PostgreSQL 단일 서버에 대한 Azure 데이터베이스가 "범용" 및 "메모리 최적화" 가격 책정 계층을 지원하는 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="benefits"></a>이점

PostgreSQL 단일 서버에 대한 Azure 데이터베이스에 대한 데이터 암호화는 다음과 같은 이점을 제공합니다.

* 키를 제거하고 데이터베이스에 액세스할 수 없게 만드는 기능으로 데이터 액세스가 사용자가 완전히 제어합니다. 
*    기업 정책에 부합하는 키 회전을 포함하여 키 수명 주기에 대한 모든 제어
*    Azure 키 볼트의 키 중앙 관리 및 구성
*    보안 담당자와 DBA 및 시스템 관리자 간의 업무 분리를 구현할 수 있는 기능

## <a name="terminology-and-description"></a>용어 및 설명

**DEK(데이터 암호화 키)**: 파티션 또는 데이터 블록을 암호화하는 데 사용되는 대칭 AES256 키입니다. 서로 다른 키로 각 데이터 블록을 암호화하면 암호 분석 공격이 더욱 어려워집니다. DEK에 대한 액세스는 특정 블록을 암호화하고 암호 해독하는 리소스 공급자 또는 애플리케이션 인스턴스에 필요합니다. DEK를 새 키로 바꾸면 연결된 블록의 데이터만 새 키로 다시 암호화해야 합니다.

**키 암호화 키(KEK)**: DEK를 암호화하는 데 사용되는 암호화 키입니다. 키 볼트를 떠나지 않는 KEK를 사용하면 DEK 자체가 암호화되고 제어될 수 있습니다. KEK에 액세스할 수 있는 엔터티는 DEK가 필요한 엔터티와 다를 수 있습니다. KEK는 DEK를 암호 해독하는 데 필요하므로 KEK는 실질적으로 KEK를 삭제함으로써 DEK를 효과적으로 삭제할 수 있는 단일 지점이 됩니다.

KEK로 암호화된 DEK는 별도로 저장됩니다. KEK에 액세스할 수 있는 엔터티만 이러한 DEK의 암호를 해독할 수 있습니다. 자세한 내용은 [미사용 암호화의 보안을](../security/fundamentals/encryption-atrest.md)참조하십시오.

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>고객 관리 키로 데이터 암호화 작동 방식

![나만의 키 가져오기 개요를 보여주는 다이어그램](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

PostgreSQL 서버가 KEY Vault에 저장된 고객 관리 키를 사용하여 DEK 암호화를 위해 키 볼트 관리자가 서버에 다음과 같은 액세스 권한을 부여합니다.

* **get**: 키 자격 증명 모음에서 키의 공용 부품 및 속성을 검색합니다.
* **wrapKey**: DEK를 암호화할 수 있습니다.
* **언랩키**: DEK의 암호를 해독할 수 있습니다.

키 볼트 관리자는 [Key Vault 감사 이벤트의 로깅을 활성화하여](../azure-monitor/insights/azure-key-vault.md)나중에 감사할 수도 있습니다.

서버가 키 자격 증명 모음에 저장된 고객 관리 키를 사용하도록 구성되면 서버는 암호화를 위해 DEK를 키 자격 증명 모음으로 보냅니다. Key Vault는 사용자 데이터베이스에 저장된 암호화된 DEK를 반환합니다. 마찬가지로 필요한 경우 서버는 암호 해독을 위해 보호된 DEK를 키 자격 증명 모음으로 보냅니다. 감사관은 Azure Monitor를 사용하여 로깅이 활성화된 경우 키 볼트 감사 이벤트 로그를 검토할 수 있습니다.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>PostgreSQL 단일 서버에 대한 Azure 데이터베이스에 대한 데이터 암호화 구성 요구 사항

다음은 키 볼트 구성에 대한 요구 사항입니다.

* PostgreSQL 단일 서버에 대한 키 볼트 및 Azure 데이터베이스는 동일한 Azure Active Directory(Azure AD) 테넌트에 속해야 합니다. 테넌트 간 키 자격 증명 모음 및 서버 상호 작용은 지원되지 않습니다. 나중에 리소스를 이동하려면 데이터 암호화를 다시 구성해야 합니다.
* 실수로 키(또는 키 볼트)가 삭제되는 경우 데이터 손실을 방지하려면 키 자격 증명 모음에서 소프트 삭제 기능을 사용하도록 설정해야 합니다. 일시 삭제된 리소스는 사용자가 그 동안 복구하거나 제거하지 않는 한 90일 동안 유지됩니다. 복구 및 제거 작업에는 Key Vault 액세스 정책과 연결된 자체 사용 권한이 있습니다. 소프트 삭제 기능은 기본적으로 꺼져 있지만 PowerShell 또는 Azure CLI를 통해 활성화할 수 있습니다(Azure 포털을 통해 활성화할 수 없습니다).
* 고유 관리되는 ID를 사용하여 Get, wrapKey 및 래핑키 사용 권한을 사용하여 PostgreSQL 단일 서버 액세스 권한을 가져옵니다. Azure 포털에서 PostgreSQL 단일 서버에서 데이터 암호화를 사용하도록 설정하면 고유 ID가 자동으로 만들어집니다. Azure 포털을 사용할 때 자세한 단계별 지침은 [Azure 포털을 사용하여 PostgreSQL 단일 서버에 대한 Azure 데이터베이스에 대한 데이터 암호화를](howto-data-encryption-portal.md) 참조하십시오.

* 키 볼트가 있는 방화벽을 사용하는 경우 신뢰할 수 있는 Microsoft 서비스 로 인해 **방화벽을 우회할 수 있도록**허용 옵션을 사용하도록 설정해야 합니다.

다음은 고객 관리 키를 구성하기 위한 요구 사항입니다.

* DEK를 암호화하는 데 사용되는 고객 관리 키는 비대칭, RSA 2028일 뿐입니다.
* 키 활성화 날짜(설정된 경우)는 과거의 날짜및 시간여야 합니다. 만료 날짜(설정된 경우)는 미래의 날짜와 시간여야 합니다.
* 키는 *사용 가능한* 상태여야 합니다.
* 키 자격 증명 모음으로 기존 키를 가져오는 경우 지원되는 파일 형식`.pfx`(, `.byok` `.backup`) 으로 제공해야 합니다.

## <a name="recommendations"></a>권장 사항

고객 관리 키를 사용하여 데이터 암호화를 사용하는 경우 Key Vault를 구성하는 권장 사항은 다음과 같습니다.

* Key Vault에서 리소스 잠금을 설정하여 이 중요한 리소스를 삭제하고 우발적이거나 무단으로 삭제되는 것을 방지할 수 있는 사용자를 제어합니다.
* 모든 암호화 키에 대한 감사 및 보고를 활성화합니다. Key Vault는 다른 보안 정보 및 이벤트 관리 도구에 쉽게 삽입할 수 있는 로그를 제공합니다. Azure 모니터 로그 분석은 이미 통합된 서비스의 한 예입니다.

* PostgreSQL 단일 서버에 대한 키 볼트 및 Azure 데이터베이스가 동일한 지역에 있는지 확인하여 DEK 래핑 및 래핑 해제 작업에 대한 빠른 액세스를 보장합니다.

다음은 고객 관리 키를 구성하기 위한 권장 사항입니다.

* 고객 관리 키의 복사본을 안전한 장소에 보관하거나 에스크로 서비스로 에스크로합니다.

* Key Vault에서 키를 생성하는 경우 키를 처음 사용하기 전에 키 백업을 만듭니다. 백업은 키 볼트로만 복원할 수 있습니다. 백업 명령에 대한 자세한 내용은 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)를 참조하십시오.

## <a name="inaccessible-customer-managed-key-condition"></a>액세스할 수 없는 고객 관리 키 조건

Key Vault에서 고객 관리 키로 데이터 암호화를 구성할 때 서버가 온라인 상태를 유지하려면 이 키에 대한 지속적인 액세스가 필요합니다. 서버가 Key Vault에서 고객 관리 키에 액세스할 수 없게 되면 서버는 10분 이내에 모든 연결을 거부하기 시작합니다. 서버는 해당 오류 메시지를 발행하고 서버 상태를 *액세스할 수 없음으로*변경합니다. 이 상태의 데이터베이스에서 허용되는 유일한 작업은 해당 데이터베이스를 삭제하는 것입니다.

### <a name="accidental-key-access-revocation-from-key-vault"></a>키 볼트에서 실수로 키 액세스 취소

Key Vault에 대한 충분한 액세스 권한을 가진 사람이 실수로 키에 대한 서버 액세스를 비활성화하는 경우:

* 서버에서 키 자격 증명 모음의 get, wrapKey 및 wrapKey 사용 권한을 해제합니다.
* 키 삭제.
* 키 자격 증명 모음을 삭제합니다.
* 키 볼트의 방화벽 규칙 변경

* Azure AD에서 서버의 관리되는 ID를 삭제합니다.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>키 볼트에서 고객 관리 키 모니터링

데이터베이스 상태를 모니터링하고 투명 한 데이터 암호화 보호자 액세스의 손실에 대 한 경고를 사용 하려면 다음 Azure 기능을 구성 합니다.

* [Azure 리소스 상태:](../service-health/resource-health-overview.md)데이터베이스에 대한 첫 번째 연결이 거부된 후 고객 키에 대한 액세스 권한을 잃은 액세스할 수 없는 데이터베이스는 "액세스할 수 없음"으로 표시됩니다.
* [활동 로그](../service-health/alerts-activity-log-service-notifications.md): 고객이 관리하는 Key Vault의 고객 키에 대한 액세스가 실패하면 활동 로그에 항목이 추가됩니다. 이러한 이벤트에 대한 경고를 만드는 경우 가능한 한 빨리 액세스를 복원할 수 있습니다.

* [작업 그룹](../azure-monitor/platform/action-groups.md): 기본 설정에 따라 알림 및 경고를 보내도록 정의합니다.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Key Vault에서 고객의 관리 키로 복원 및 복제

PostgreSQL 단일 서버에 대한 Azure 데이터베이스가 키 볼트에 저장된 고객의 관리 키로 암호화된 후 새로 생성된 서버 복사본도 암호화됩니다. 로컬 또는 지역 복원 작업을 통해 또는 읽기 복제본을 통해 이 새 복사본을 만들 수 있습니다. 그러나 암호화에 대한 새 고객의 관리 키를 반영하도록 복사본을 변경할 수 있습니다. 고객 관리 키가 변경되면 서버의 이전 백업이 최신 키를 사용하기 시작합니다.

복제본 을 만들 거나 읽는 동안 고객 관리 데이터 암호화를 설정하는 동안 문제를 방지하려면 마스터 및 복원/복제서버에서 다음 단계를 따르는 것이 중요합니다.

* PostgreSQL 단일 서버에 대한 마스터 Azure 데이터베이스에서 복원 또는 읽기 복제본 만들기 프로세스를 시작합니다.
* Key Vault에 대한 고유 ID가 아직 부여되지 않았기 때문에 새로 생성된 서버(복원/복제본)를 액세스할 수 없는 상태로 유지합니다.
* 복원/복제서버에서 데이터 암호화 설정에서 고객 관리 키를 다시 유효성을 검사합니다. 이렇게 하면 새로 만든 서버에 키 볼트에 저장된 키에 대한 랩핑 및 래핑 해제 권한이 부여됩니다.

## <a name="next-steps"></a>다음 단계

[Azure 포털을 사용하여 PostgreSQL 단일 서버에 대한 Azure 데이터베이스에 대한 고객 관리 키로 데이터 암호화를 설정하는](howto-data-encryption-portal.md)방법에 대해 알아봅니다.

