---
title: Azure Database for MySQL 문제 해결을 위한 데이터 암호화
description: Azure Database for MySQL에 대 한 데이터 암호화 문제를 해결 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4b517a463ec949d804798787ad4b35b53145a4a8
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371554"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 고객 관리 키를 사용 하 여 데이터 암호화 문제 해결
이 문서에서는 고객이 관리 하는 키를 사용 하 여 데이터 암호화로 구성 된 Azure Database for MySQL에서 발생 하는 일반적인 문제/오류를 식별 하 고 해결 하는 방법을 설명 합니다.

## <a name="introduction"></a>소개
데이터 암호화가 Azure Key Vault에서 고객 관리 키를 사용 하도록 구성 된 경우 서버를 계속 사용 하려면이 키에 대 한 지속적인 액세스가 필요 합니다. 서버가 Azure Key Vault에서 고객이 관리 하는 키에 대 한 액세스 권한을 상실 하면 서버에서 해당 오류 메시지와 함께 모든 연결 거부를 시작 하 고 Azure Portal에서 ***액세스할*** 수 없음으로 상태를 변경 합니다.

액세스할 수 없는 Azure Database for MySQL 서버가 더 이상 필요 하지 않은 경우 즉시 삭제 하 여 비용 발생을 중지할 수 있습니다. Azure 주요 자격 증명 모음에 대 한 액세스가 복원 되 고 서버를 다시 사용할 수 있을 때까지 서버에 대 한 다른 모든 작업은 허용 되지 않습니다. 서버를 고객 관리로 암호화 하는 동안에는 서버가 액세스할 수 없는 상태에서 ' 예 ' (고객 관리)에서 ' 아니요 ' (서비스 관리)로 데이터 암호화 옵션을 변경할 수 없습니다. 서버를 사용 가능 하 게 설정 하려면 수동으로 키의 유효성을 다시 검사 해야 합니다. 이는 고객이 관리 하는 키에 대 한 권한이 해지 된 동안 무단 액세스 로부터 데이터를 보호 하는 데 필요 합니다.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>서버에 액세스할 수 없게 하는 일반적인 오류

Azure Key Vault에서 데이터 암호화를 사용할 때 발생 하는 대부분의 문제는 다음과 같은 잘못 된 항목 중 하나로 인해 발생 합니다.

Key Vault를 사용할 수 없거나 존재하지 않습니다.

* Key Vault를 실수로 삭제되었습니다.
* 일시적인 네트워크 오류로 인해 Key Vault를 사용할 수 없습니다.

Key Vault 또는 키에 액세스할 수 있는 권한이 없습니다.

* 키가 실수로 삭제되었거나, 사용하지 않도록 설정되었거나, 키가 만료되었습니다.
* Azure Database for MySQL 인스턴스 관리 id가 실수로 삭제 되었습니다.
* 키에 대 한 Azure Database for MySQL 서버 관리 id에 부여 된 사용 권한이 충분 하지 않습니다 (가져오기, 래핑 및 래핑 해제를 포함 하지 않음).
* Azure Database for MySQL server 인스턴스 관리 id에 대 한 사용 권한이 취소 되었습니다.

## <a name="identify-and-resolve-common-errors"></a>일반적인 오류 식별 및 해결
### <a name="errors-on-the-key-vault"></a>키 자격 증명 모음에 대 한 오류

#### <a name="disabled-key-vault"></a>비활성화 된 key vault
* AzureKeyVaultKeyDisabledMessage
* **설명** : Azure Key Vault 키가 사용 하지 않도록 설정 되어 있으므로 서버에서 작업을 완료할 수 없습니다.

#### <a name="missing-key-vault-permissions"></a>누락 된 키 자격 증명 모음 권한
* AzureKeyVaultMissingPermissionsMessage
* 서버에 Azure Key Vault 사용 권한에 대 한 필요한 가져오기, 래핑 및 래핑 해제 권한이 없습니다. ID를 사용 하 여 서비스 사용자에 게 누락 된 사용 권한을 부여 합니다.

### <a name="mitigation"></a>완화 방법
* 고객이 관리 하는 키가 Key Vault에 있는지 확인 합니다.
* Key Vault를 식별하고 Azure Portal의 Key Vault로 이동합니다.
* 키 URI로 식별되는 키가 있는지 확인합니다.


## <a name="next-steps"></a>다음 단계
[Azure Portal를 사용 하 여 MySQL 용 Azure database에 대 한 고객 관리 키를 사용 하 여 데이터 암호화를 설정](howto-data-encryption-portal.md)합니다.