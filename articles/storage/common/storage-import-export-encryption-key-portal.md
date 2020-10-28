---
title: Azure Portal을 사용하여 Import/Export 서비스의 고객 관리형 키 구성
description: Azure Portal에서 Azure Key Vault를 사용하여 Azure Import/Export 서비스의 고객 관리형 키를 구성하는 방법을 알아봅니다. 고객 관리형 키를 사용하면 액세스 제어를 만들고, 회전하고, 비활성화하고, 철회할 수 있습니다.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 4362b579b7f01570a2b5fd072bf53ad495797cd8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783779"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Azure Key Vault에서 Import/Export 서비스에 고객 관리형 키 사용

Azure Import/Export 서비스는 암호화 키를 통해 드라이브를 잠그는 데 사용되는 BitLocker 키를 보호합니다. 기본적으로 BitLocker 키는 Microsoft 관리형 키를 사용하여 암호화됩니다. 암호화 키에 대한 추가 제어를 원한다면 고객 관리형 키를 제공할 수도 있습니다.

그러려면 고객 관리형 키를 만들어 Azure Key Vault에 저장해야 합니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../../key-vault/general/overview.md)

이 문서에서는 [Azure Portal](https://portal.azure.com/)에서 Import/Export 서비스에 고객 관리형 키를 사용하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.

1. 다음 문서의 지침에 따라 가져오기 또는 내보내기 작업을 만들었습니다.

    - [BLOB에 대한 가져오기 작업 만들기](storage-import-export-data-to-blobs.md)
    - [파일에 대한 가져오기 작업 만들기](storage-import-export-data-to-files.md)
    - [BLOB에 대한 내보내기 작업 만들기](storage-import-export-data-from-blobs.md)

2. BitLocker 키를 보호하는 데 사용할 수 있는 키가 들어 있는 기존 Azure Key Vault가 있습니다. Azure Portal을 사용하여 키 자격 증명 모음을 만드는 방법은 [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../../key-vault/secrets/quick-create-portal.md)을 참조하세요.

    - **일시 삭제** 및 **제거 안 함** 은 기존 Key Vault에서 설정됩니다. 이러한 속성은 기본적으로 사용하도록 설정되지 않습니다. 이러한 속성을 사용하려면 다음 문서 중 하나에서 **일시 삭제를 사용하도록 설정** 및 **제거 보호 활성화** 섹션을 참조하세요.

        - [PowerShell에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)
        - [CLI에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)
    - 기존 키 자격 증명 모음에는 크기가 2048 이상인 RSA 키가 있어야 합니다. 키에 대 한 자세한 내용은 [키](../../key-vault/keys/about-keys.md)정보를 참조 하세요.
    - 키 자격 증명 모음은 데이터의 스토리지 계정과 같은 지역에 있어야 합니다.  
    - 기존 Azure Key Vault가 없는 경우 다음 섹션에 설명된 대로 인라인으로 만들 수 있습니다.

## <a name="enable-keys"></a>키 사용

Import/Export 서비스의 고객 관리형 키를 구성하는 것은 선택 사항입니다. 기본적으로 Import/Export 서비스는 Microsoft 관리형 키를 사용하여 BitLocker 키를 보호합니다. Azure Portal에서 고객 관리형 키를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 가져오기 작업의 **개요** 블레이드로 이동합니다.
2. 오른쪽 창에서 **BitLocker 키의 암호화 방식** 을 선택합니다.

    ![암호화 옵션 선택](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. **암호화** 블레이드에서 디바이스 BitLocker 키를 살펴보고 복사할 수 있습니다. **암호화 유형** 에서 BitLocker 키를 보호하는 방법을 선택할 수 있습니다. 기본적으로 Microsoft 관리형 키가 사용됩니다.

    ![BitLocker 키 보기](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 고객 관리형 키를 지정하는 옵션이 있습니다. 고객 관리형 키를 선택한 후에는 **키 자격 증명 모음 및 키를 선택** 합니다.

    ![고객 관리형 키 선택](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. **Azure Key Vault에서 키 선택** 블레이드에서 구독이 자동으로 채워집니다. **키 자격 증명 모음** 의 드롭다운 목록에서 기존 키 자격 증명 모음을 선택할 수 있습니다.

    ![Azure Key Vault 선택 또는 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. **새로 만들기** 를 선택하여 새로운 키 자격 증명 모음을 만들 수도 있습니다. **키 자격 증명 모음 만들기 블레이드** 에서 리소스 그룹 및 주요 자격 증명 모음 이름을 입력합니다. 그 외에는 기본값을 적용합니다. **검토 + 만들기** 를 선택합니다.

    ![새 Azure Key Vault 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 키 자격 증명 모음과 연결된 정보를 검토하고 **만들기** 를 선택합니다. 키 자격 증명 모음 만들기가 완료될 때까지 몇 분 정도 기다립니다.

    ![Azure Key Vault 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. **Azure Key Vault에서 키 선택** 에서 기존 키 자격 증명 모음의 키를 선택할 수 있습니다.

9. 키 자격 증명 모음을 새로 만든 경우 **새로 만들기** 를 선택하여 키를 만듭니다. RSA 키 크기는 2048 이상이어야 합니다.

    ![Azure Key Vault에서 새 키 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    키 자격 증명 모음을 만들 때 일시 삭제 및 제거 보호를 사용하도록 설정하지 않으면 키 자격 증명 모음이 일시 삭제 및 제거 보호를 사용하도록 업데이트됩니다.

10. 키 이름을 입력하고 나머지는 기본값을 적용한 다음, **만들기** 를 선택합니다.

    ![새 키 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. **버전** 을 선택하고, **선택** 을 클릭합니다. 키 자격 증명 모음에 키가 생성되었다는 알림이 표시됩니다.

    ![키 자격 증명 모음에 새로 생성된 키](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

**암호화** 블레이드에서 고객 관리형 키에 대해 선택한 키 자격 증명 모음 및 키를 볼 수 있습니다.

> [!IMPORTANT]
> 가져오기/내보내기 작업의 모든 단계에서 Microsoft 관리형 키를 사용하지 않도록 설정하고 고객 관리형 키로 이동할 수 있습니다. 그러나 고객 관리형 키를 만든 후에는 사용하지 않도록 설정할 수 없습니다.

## <a name="troubleshoot-customer-managed-key-errors"></a>고객 관리형 키 오류 해결

고객 관리형 키와 관련된 오류가 발생하면 다음 표를 사용하여 문제를 해결하세요.

| 오류 코드     |세부 정보     | 복구 가능한가요?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | 고객 관리형 키에 대한 액세스가 철회되었습니다.                                                       | 예, 다음 사항을 확인하세요. <ol><li>키 자격 증명 모음의 액세스 정책에 여전히 MSI가 있습니다.</li><li>액세스 정책에서 가져오기, 래핑 및 래핑 해제 권한이 설정되었습니다.</li><li>키 자격 증명 모음이 방화벽의 보호를 받는 VNet에 있는 경우 **Microsoft에서 신뢰하는 서비스 허용** 이 설정되었는지 확인합니다.</li><li>API를 사용하여 작업 리소스의 MSI가 `None`으로 초기화되었는지 확인합니다.<br>초기화되었다면 이 값을 다시 `Identity = SystemAssigned`로 설정합니다. 이렇게 하면 작업 리소스의 ID가 다시 생성됩니다.<br>새 ID를 만든 후에는 키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 `Get`, `Wrap` 및 `Unwrap` 권한을 사용하도록 설정합니다.</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | 고객 관리형 키가 사용하지 않도록 설정되었습니다.                                         | 예, 키 버전을 사용하도록 설정하여 복구할 수 있습니다.     |
| CmkErrorKeyNotFound      | 고객 관리형 키를 찾을 수 없습니다. | 예, 키가 삭제되었지만 여전히 제거 기간 내에 있는 경우 [키 자격 증명 모음 키 제거 실행 취소](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)를 사용하여 복구할 수 있습니다.<br>또는 <ol><li>예, 고객이 키를 백업해 둔 경우 키를 복원하면 됩니다.</li><li>아니요, 복구할 수 없습니다.</li></ol>
| CmkErrorVaultNotFound |고객 관리형 키의 키 자격 증명 모음을 찾을 수 없습니다. |   키 자격 증명 모음이 삭제된 경우:<ol><li>예, 제거 보호 기간 내에 있는 경우 [키 자격 증명 모음 복구](../../key-vault/general/soft-delete-overview.md#key-vault-recovery)의 단계에 따라 복구할 수 있습니다.</li><li>아니요, 제거 보호 기간이 지나면 복구할 수 없습니다.</li></ol><br>또는 키 자격 증명 모음이 다른 테넌트로 마이그레이션된 경우 아래 단계 중 하나를 사용하여 복구할 수 있습니다.<ol><li>키 자격 증명 모음을 이전 테넌트로 되돌립니다.</li><li>`Identity = None`을 설정한 다음, 값을 다시 `Identity = SystemAssigned`로 설정합니다. 이렇게 하면 새 ID가 생성될 때 ID가 삭제되고 다시 생성됩니다. 키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 `Get`, `Wrap` 및 `Unwrap` 권한을 사용하도록 설정합니다.</li></ol>|

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란](../../key-vault/general/overview.md)?