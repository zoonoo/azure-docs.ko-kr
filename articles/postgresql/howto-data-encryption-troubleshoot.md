---
title: 데이터 암호화 문제 해결 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 데이터 암호화 문제 해결 방법에 대해 알아보십시오.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2902ff17ac14a48f1a11259339c2ab1bc4595980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299263"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>PostgreSQL용 Azure 데이터베이스에서 데이터 암호화 문제 해결 - 단일 서버

이 문서에서는 고객 관리 키를 사용하여 데이터 암호화로 구성할 때 PostgreSQL용 Azure Database의 단일 서버 배포에서 발생할 수 있는 일반적인 문제를 식별하고 해결하는 데 도움이 됩니다.

## <a name="introduction"></a>소개

Azure Key Vault에서 고객 관리 키를 사용하도록 데이터 암호화를 구성할 때 서버는 키에 대한 지속적인 액세스가 필요합니다. 서버가 Azure Key Vault에서 고객 관리 키에 액세스할 수 없게 되면 모든 연결을 거부하고 적절한 오류 메시지를 반환하며 Azure Portal에서 ***액세스할 수 없음으로*** 상태를 변경합니다.

PostgreSQL 서버에 액세스할 수 없는 Azure 데이터베이스가 더 이상 필요하지 않은 경우 삭제하여 비용이 발생하는 것을 중지할 수 있습니다. 키 자격 증명 모음에 대한 액세스가 복원되고 서버를 사용할 수 있는 때까지 서버의 다른 작업은 허용되지 않습니다. 또한 고객 관리 키로 암호화된 경우 `Yes`액세스 불가능한 서버의 데이터 암호화 옵션을 (고객 관리)에서 `No` (서비스 관리)로 변경할 수 없습니다. 서버에 다시 액세스하려면 키를 수동으로 다시 유효성을 검사해야 합니다. 이 작업은 고객 관리 키에 대한 사용 권한이 해지되는 동안 무단 액세스로부터 데이터를 보호하는 데 필요합니다.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>서버에 액세스할 수 없게 하는 일반적인 오류

다음 의 잘못 구성으로 인해 Azure Key Vault 키를 사용하는 데이터 암호화에 대부분의 문제가 발생합니다.

- 키 자격 증명 모음을 사용할 수 없거나 존재하지 않습니다.
  - Key Vault를 실수로 삭제되었습니다.
  - 일시적인 네트워크 오류로 인해 Key Vault를 사용할 수 없습니다.

- 키 자격 증명 모음에 액세스할 수 있는 권한이 없거나 키가 존재하지 않습니다.
  - 키가 만료되었거나 실수로 삭제되거나 비활성화되었습니다.
- PostgreSQL 인스턴스에 대한 Azure 데이터베이스의 관리되는 ID가 실수로 삭제되었습니다.
  - PostgreSQL 인스턴스에 대한 Azure 데이터베이스의 관리되는 ID에는 키 권한이 부족합니다. 예를 들어 권한에는 받기, 줄 바꿈 및 둘러싸기 해제가 포함되지 않습니다.
  - PostgreSQL 인스턴스에 대한 Azure 데이터베이스에 대한 관리되는 ID 사용 권한이 취소되거나 삭제되었습니다.

## <a name="identify-and-resolve-common-errors"></a>일반적인 오류 식별 및 해결

### <a name="errors-on-the-key-vault"></a>키 자격 증명 모음의 오류

#### <a name="disabled-key-vault"></a>비활성화된 키 자격 증명 모음

- `AzureKeyVaultKeyDisabledMessage`
- **설명**: Azure 키 볼트 키가 비활성화되어 있으므로 서버에서 작업을 완료할 수 없습니다.

#### <a name="missing-key-vault-permissions"></a>키 자격 증명 모음 권한이 누락되었습니다.

- `AzureKeyVaultMissingPermissionsMessage`
- **설명**: 서버에 Azure 키 자격 증명 모음에 필요한 Get, Wrap 및 둘러싸기 사용 권한이 없습니다. 누락된 권한을 서비스 주체에게 ID로 부여합니다.

### <a name="mitigation"></a>완화 방법

- 고객 관리 키가 키 자격 증명 모음에 있는지 확인합니다.
- Key Vault를 식별하고 Azure Portal의 Key Vault로 이동합니다.
- 키 URI가 존재하는 키를 식별해야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 포털을 사용하여 PostgreSQL용 Azure 데이터베이스에서 고객 관리 키로 데이터 암호화설정](howto-data-encryption-portal.md)
