---
title: 데이터 암호화 문제 해결-Azure Database for PostgreSQL 단일 서버
description: Azure Database for PostgreSQL-단일 서버에서 데이터 암호화 문제를 해결 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: ee0a1ebe483dd4719fd1a84fec37906329116eba
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117902"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL에서 데이터 암호화 문제 해결-단일 서버

이 문서는 고객 관리 키를 사용 하 여 데이터 암호화를 사용 하 여 구성 하는 경우 Azure Database for PostgreSQL 단일 서버 배포에서 발생할 수 있는 일반적인 문제를 식별 하 고 해결 하는 데 도움이 됩니다.

## <a name="introduction"></a>소개

Azure Key Vault에서 고객이 관리 하는 키를 사용 하도록 데이터 암호화를 구성 하는 경우 서버에서 키에 대 한 지속적인 액세스가 필요 합니다. 서버 Azure Key Vault에서 고객이 관리 하는 키에 대 한 액세스 권한을 상실 하면 모든 연결을 거부 하 고, 적절 한 오류 메시지를 반환 하 고, Azure Portal에서 해당 상태를 ***액세스할*** 수 없음으로 변경 합니다.

Azure Database for PostgreSQL 서버에 액세스할 수 없는 경우 서버를 삭제 하 여 비용을 절감할 수 있습니다. 키 자격 증명 모음에 대 한 액세스가 복원 되 고 서버를 사용할 수 있을 때까지 서버에 대 한 다른 작업은 허용 되지 않습니다. 또한 `Yes` `No` 고객이 관리 하는 키로 암호화 된 경우 액세스할 수 없는 서버에서 (고객 관리)에서 (서비스 관리)로 데이터 암호화 옵션을 변경할 수 없습니다. 서버를 다시 액세스 하려면 키를 수동으로 다시 유효성을 다시 검사 해야 합니다. 이 작업은 고객이 관리 하는 키에 대 한 권한이 해지 되는 동안 무단 액세스 로부터 데이터를 보호 하는 데 필요 합니다.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>서버에 액세스할 수 없게 하는 일반적인 오류

다음 잘못 된 구성을 사용 하면 Azure Key Vault 키를 사용 하는 데이터 암호화에 대부분의 문제가 발생 합니다.

- 키 자격 증명 모음을 사용할 수 없거나 존재 하지 않습니다.
  - Key Vault를 실수로 삭제되었습니다.
  - 일시적인 네트워크 오류로 인해 Key Vault를 사용할 수 없습니다.

- 키 자격 증명 모음에 액세스할 수 있는 권한이 없거나 키가 없습니다.
  - 키가 만료 되었거나 실수로 삭제 되었거나 사용 하지 않도록 설정 되었습니다.
- Azure Database for PostgreSQL 인스턴스의 관리 id가 실수로 삭제 되었습니다.
  - Azure Database for PostgreSQL 인스턴스의 관리 id에 키 권한이 부족 합니다. 예를 들어 사용 권한에는 Get, Wrap 및 Wrap이 포함 되지 않습니다.
  - Azure Database for PostgreSQL 인스턴스에 대 한 관리 id 권한이 해지 되었거나 삭제 되었습니다.

## <a name="identify-and-resolve-common-errors"></a>일반적인 오류 식별 및 해결

### <a name="errors-on-the-key-vault"></a>키 자격 증명 모음에 대 한 오류

#### <a name="disabled-key-vault"></a>비활성화 된 key vault

- `AzureKeyVaultKeyDisabledMessage`
- **설명**: Azure Key Vault 키가 사용 하지 않도록 설정 되어 있으므로 서버에서 작업을 완료할 수 없습니다.

#### <a name="missing-key-vault-permissions"></a>누락 된 키 자격 증명 모음 권한

- `AzureKeyVaultMissingPermissionsMessage`
- **설명**: 서버에 Azure Key Vault 하는 데 필요한 가져오기, 래핑 및 래핑 해제 권한이 없습니다. ID를 사용 하 여 서비스 사용자에 게 누락 된 사용 권한을 부여 합니다.

### <a name="mitigation"></a>완화 방법

- 고객 관리 키가 키 자격 증명 모음에 있는지 확인 합니다.
- Key Vault를 식별하고 Azure Portal의 Key Vault로 이동합니다.
- 키 URI가 존재 하는 키를 식별 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL에서 고객 관리 키를 사용 하 여 데이터 암호화를 설정 하려면 Azure Portal을 사용 합니다.](howto-data-encryption-portal.md)
