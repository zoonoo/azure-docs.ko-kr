---
title: 데이터 암호화 문제 해결 - Azure Database for MySQL
description: Azure Database for MySQL에서 데이터 암호화 문제를 해결하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 3697130dd0d623a71158121c572aba7a52be28c7
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642169"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 데이터 암호화 문제 해결

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 고객 관리형 키를 사용하는 데이터 암호화로 구성할 경우 Azure Database for MySQL에서 발생할 수 있는 일반적인 문제를 식별하고 해결하는 방법을 설명합니다.

## <a name="introduction"></a>소개

Azure Key Vault에서 고객 관리형 키를 사용하도록 데이터 암호화를 구성하는 경우 서버가 지속적으로 키에 액세스할 수 있어야 합니다. 서버가 Azure Key Vault의 고객 관리형 키에 액세스할 수 없게 되면 모든 연결을 거부하고 적절한 오류 메시지를 반환하며, Azure Portal에서 해당 상태를 ***액세스할 수 없음*** 으로 변경합니다.

액세스할 수 없는 Azure Database for MySQL 서버가 더 이상 필요하지 않은 경우 서버를 삭제하여 비용 발생을 중지할 수 있습니다. 키 자격 증명 모음에 대한 액세스가 복원되고 서버를 사용할 수 있을 때까지 서버에 대한 다른 작업은 허용되지 않습니다. 또한 고객 관리형 키로 암호화된 경우 액세스할 수 없는 서버에서 데이터 암호화 옵션을 `Yes`(고객 관리형)에서 `No`(서비스 관리형)로 변경할 수 없습니다. 서버가 다시 액세스할 수 있으려면 키의 유효성을 수동으로 재검사해야 합니다. 이 작업은 고객 관리형 키에 대한 권한이 철회된 상태에서 무단 액세스로부터 데이터를 보호하는 데 필요합니다.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>서버가 액세스할 수 없게 되는 일반적인 오류

Azure Key Vault 키를 사용하는 데이터 암호화와 관련된 대부분의 문제는 다음과 같은 잘못된 구성으로 인해 발생합니다.

- 키 자격 증명 모음을 사용할 수 없거나 존재하지 않는 경우:
  - Key Vault를 실수로 삭제되었습니다.
  - 일시적인 네트워크 오류로 인해 Key Vault를 사용할 수 없습니다.

- 키 자격 증명 모음 또는 키에 액세스할 수 있는 권한이 없는 경우:
  - 키가 만료되었거나 실수로 삭제되었거나 사용하지 않도록 설정되었습니다.
  - Azure Database for MySQL 인스턴스의 관리 ID가 실수로 삭제되었습니다.
  - Azure Database for MySQL 인스턴스의 관리 ID에 충분한 키 권한이 없습니다. 예를 들어 얻기, 래핑, 래핑 해제 권한이 없습니다.
  - Azure Database for MySQL 인스턴스에 대한 관리 ID 권한이 철회되었거나 삭제되었습니다.

## <a name="identify-and-resolve-common-errors"></a>일반적인 오류 식별 및 해결

### <a name="errors-on-the-key-vault"></a>키 자격 증명 모음 오류

#### <a name="disabled-key-vault"></a>비활성화된 키 자격 증명 모음

- `AzureKeyVaultKeyDisabledMessage`
- **설명**: Azure Key Vault 키가 비활성화되어 있으므로 서버에서 작업을 완료할 수 없습니다.

#### <a name="missing-key-vault-permissions"></a>키 자격 증명 모음 권한 누락

- `AzureKeyVaultMissingPermissionsMessage`
- **설명**: 서버에 Azure Key Vault에 대해 필요한 Get, Wrap 및 Unwrap 권한이 없습니다. ID를 사용하여 서비스 주체에게 누락된 사용 권한을 부여합니다.

### <a name="mitigation"></a>완화 방법

- 고객 관리형 키가 키 자격 증명 모음에 있는지 확인합니다.
- Key Vault를 식별하고 Azure Portal의 Key Vault로 이동합니다.
- 키 URI가 존재하는 키를 식별하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 Azure Database for MySQL에서 고객 관리형 키로 데이터 암호화 설정](howto-data-encryption-portal.md)
