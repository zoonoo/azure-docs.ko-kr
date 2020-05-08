---
title: Azure Portal를 사용 하 여 Import/Export 서비스에 대 한 고객 관리 키를 구성 합니다.
description: Azure Portal를 사용 하 여 Azure Import/Export 서비스에 대 한 Azure Key Vault에서 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다. 고객 관리 키를 사용 하 여 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있습니다.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 71426d131cdd46b176c387a31e3dc2ca66ae3761
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871167"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Import/Export 서비스에 대 한 Azure Key Vault에서 고객이 관리 하는 키 사용

Azure Import/Export는 암호화 키를 통해 드라이브를 잠그는 데 사용 되는 BitLocker 키를 보호 합니다. 기본적으로 BitLocker 키는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대 한 추가 제어를 위해 고객이 관리 하는 키를 제공할 수도 있습니다.

고객 관리 키를 만들어 Azure Key Vault에 저장 해야 합니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 항목](../../key-vault/general/overview.md) 을 참조 하세요.

이 문서에서는 [Azure Portal](https://portal.azure.com/)의 가져오기/내보내기 서비스에서 고객 관리 키를 사용 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.

1. 의 지침에 따라 가져오기 또는 내보내기 작업을 만들었습니다.

    - [Blob에 대 한 가져오기 작업을 만듭니다](storage-import-export-data-to-blobs.md).
    - [파일에 대 한 가져오기 작업을 만듭니다](storage-import-export-data-to-files.md).
    - [Blob에 대 한 내보내기 작업 만들기](storage-import-export-data-from-blobs.md)

2. BitLocker 키를 보호 하는 데 사용할 수 있는 키가 있는 기존 Azure Key Vault 있습니다. Azure Portal를 사용 하 여 주요 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](../../key-vault/secrets/quick-create-portal.md)을 참조 하세요.

    - **일시 삭제** 및 **제거 안 함** 은 기존 Key Vault에 설정 됩니다. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않습니다. 이러한 속성을 사용 하도록 설정 하려면 다음 문서 중 하나에서 **일시 삭제 사용** 및 **제거 방지** 사용 이라는 섹션을 참조 하세요.

        - [PowerShell에서 일시 삭제를 사용 하는 방법](../../key-vault/general/soft-delete-powershell.md)
        - [CLI에서 일시 삭제를 사용 하는 방법](../../key-vault/general/soft-delete-cli.md)입니다.
    - 기존 키 자격 증명 모음에는 2048 크기 이상의 RSA 키가 있어야 합니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)를 참조 하세요.
    - Key vault는 데이터에 대 한 저장소 계정과 동일한 지역에 있어야 합니다.  
    - 기존 Azure Key Vault 없는 경우 다음 섹션에 설명 된 대로 인라인으로 만들 수도 있습니다.

## <a name="enable-keys"></a>키 사용

가져오기/내보내기 서비스에 대 한 고객 관리 키를 구성 하는 것은 선택 사항입니다. 기본적으로 Import/Export 서비스는 Microsoft 관리 키를 사용 하 여 BitLocker 키를 보호 합니다. Azure Portal에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. 가져오기 작업에 대 한 **개요** 블레이드로 이동 합니다.
2. 오른쪽 창에서 **BitLocker 키를 암호화 하는 방법 선택**을 선택 합니다.

    ![암호화 옵션 선택](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. **암호화** 블레이드에서 장치 BitLocker 키를 보고 복사할 수 있습니다. **암호화 유형**에서 BitLocker 키를 보호 하는 방법을 선택할 수 있습니다. 기본적으로 Microsoft 관리 키가 사용 됩니다.

    ![BitLocker 키 보기](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 고객 관리 키를 지정 하는 옵션이 있습니다. 고객 관리 키를 선택한 후 **key vault 및 키를 선택**합니다.

    ![고객 관리 키 선택](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. **Azure Key Vault에서 키 선택** 블레이드에서 구독이 자동으로 채워집니다. **Key vault**의 경우 드롭다운 목록에서 기존 key vault를 선택할 수 있습니다.

    ![Azure Key Vault 선택 또는 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. **새로 만들기** 를 선택 하 여 새 키 자격 증명 모음을 만들 수도 있습니다. **키 자격 증명 모음 만들기 블레이드에서**리소스 그룹 및 주요 자격 증명 모음 이름을 입력 합니다. 다른 모든 기본값을 적용합니다. **검토 + 만들기**를 선택합니다.

    ![새 Azure Key Vault 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 주요 자격 증명 모음과 연결 된 정보를 검토 하 고 **만들기**를 선택 합니다. 키 자격 증명 모음 만들기가 완료 될 때까지 몇 분 정도 기다립니다.

    ![Azure Key Vault 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. **Azure Key Vault에서 선택 키**의 기존 키 자격 증명 모음에서 키를 선택할 수 있습니다.

9. 새 키 자격 증명 모음을 만든 경우 **새로 만들기** 를 선택 하 여 키를 만듭니다. RSA 키 크기는 2048 이상일 수 있습니다.

    ![Azure Key Vault에서 새 키 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    키 자격 증명 모음을 만들 때 일시 삭제 및 제거 보호를 사용 하도록 설정 하지 않으면 키 자격 증명 모음이 일시 삭제 및 제거 보호를 사용 하도록 업데이트 됩니다.

10. 키의 이름을 입력 하 고 다른 기본값을 적용 한 다음 **만들기**를 선택 합니다.

    ![새 키 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. **버전** 을 선택 하 고 **선택**을 선택 합니다. 키 자격 증명 모음에 키가 생성 되었다는 알림이 표시 됩니다.

    ![키 자격 증명 모음에 새 키가 만들어짐](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

**암호화** 블레이드에서 핵심 자격 증명 모음 및 고객 관리 키에 대해 선택 된 키를 볼 수 있습니다.

> [!IMPORTANT]
> 가져오기/내보내기 작업의 모든 단계에서 Microsoft 관리 키를 사용 하지 않도록 설정 하 고 고객 관리 키로 이동할 수 있습니다. 그러나 고객이 관리 하는 키를 만든 후에는 사용 하지 않도록 설정할 수 없습니다.

## <a name="troubleshoot-customer-managed-key-errors"></a>고객 관리 키 오류 문제 해결

고객 관리 키와 관련 된 오류가 표시 되 면 다음 표를 사용 하 여 문제를 해결 하십시오.

| 오류 코드     |설명     | 복구 가능한?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked 됨 | 고객 관리 키를 적용 했지만 키 액세스가 현재 취소 되어 있습니다. 자세한 내용은 [키 액세스를 사용 하도록 설정](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)하는 방법을 참조 하세요.                                                      | 예, 다음을 확인 합니다. <ol><li>키 자격 증명 모음에는 여전히 액세스 정책에 MSI가 있습니다.</li><li>액세스 정책은 가져오기, 래핑, 래핑 해제에 대 한 권한을 제공 합니다.</li><li>키 자격 증명 모음이 방화벽 뒤에 있는 vNet에 있는 경우 **Microsoft 신뢰할 수 있는 서비스 허용** 이 사용 하도록 설정 되어 있는지 확인 합니다.</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | 고객 관리 키를 적용 했지만 키를 사용할 수 없습니다. 자세한 내용은 [키를 사용 하도록 설정](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate)하는 방법을 참조 하세요.                                                                             | 예, 키 버전을 사용 하도록 설정 합니다.     |
| CmkErrorKeyNotFound      | 고객 관리 키를 적용 하 고 키와 연결 된 key vault를 찾을 수 없습니다.<br>Key vault를 삭제 한 경우에는 고객 관리 키를 복구할 수 없습니다.  키 자격 증명 모음을 다른 테 넌 트로 마이그레이션한 경우 [구독 이동 후에 주요 자격 증명 모음 테 넌 트 ID 변경](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)을 참조 하세요. |   키 자격 증명 모음을 삭제 한 경우:<ol><li>예 (제거 보호 기간이 면 [키 자격 증명 모음 복구](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)의 단계를 사용 합니다.</li><li>아니요 (제거 보호 기간을 초과 하는 경우).</li></ol><br>키 자격 증명 모음에서 테 넌 트 마이그레이션을 클러스터가 거쳤다 하는 경우, 예, 아래 단계 중 하나를 사용 하 여 복구할 수 있습니다. <ol><li>키 자격 증명 모음을 이전 테 넌 트로 다시 되돌립니다.</li><li>을 `Identity = None` 설정 하 고 값을 다시로 `Identity = SystemAssigned`설정 합니다. 새 id를 만든 후 id를 삭제 하 고 다시 만듭니다. 키 `Get`자격 `Wrap`증명 모음 `Unwrap` 액세스 정책에서, 및 사용 권한을 새 id로 사용 하도록 설정 합니다.</li></ol>|

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)이란?
