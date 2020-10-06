---
title: Azure Portal를 사용 하 여 Azure Data Box에 대 한 고객 관리 키를 구성 합니다.
description: Azure Portal를 사용 하 여 Azure Data Box에 대 한 Azure Key Vault에서 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다. 고객 관리형 키를 사용하면 액세스 제어를 만들고, 회전하고, 비활성화하고, 철회할 수 있습니다.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 31147d534109e0d74d33d102075c69eeb703496e
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739938"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Azure Data Box Azure Key Vault에서 고객이 관리 하는 키 사용

Azure Data Box는 암호화 키를 통해 장치를 잠그는 데 사용 되는 장치 잠금 해제 키 (장치 암호 라고도 함)를 보호 합니다. 기본적으로 Data Box 주문의 장치 잠금 해제 키는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 장치 잠금 해제 키에 대 한 추가 제어를 위해 고객이 관리 하는 키를 제공할 수도 있습니다. 

그러려면 고객 관리형 키를 만들어 Azure Key Vault에 저장해야 합니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](../key-vault/general/overview.md)를 참조 하세요.

이 문서에서는 [Azure Portal](https://portal.azure.com/)의 Azure Data Box에서 고객이 관리 하는 키를 사용 하는 방법을 보여 줍니다. 이 문서는 Azure Data Box 장치와 Azure Data Box Heavy 장치에 모두 적용 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: order Azure Data Box](data-box-deploy-ordered.md)의 지침에 따라 Azure Data Box 순서를 만들었습니다.

2. 장치 잠금 해제 키를 보호 하는 데 사용할 수 있는 키가 있는 기존 Azure Key Vault 있습니다. Azure Portal을 사용하여 키 자격 증명 모음을 만드는 방법은 [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../key-vault/secrets/quick-create-portal.md)을 참조하세요.

    - **일시 삭제** 및 **제거 안 함** 은 기존 키 자격 증명 모음에 설정 됩니다. 이러한 속성은 기본적으로 사용하도록 설정되지 않습니다. 이러한 속성을 사용하려면 다음 문서 중 하나에서 **일시 삭제를 사용하도록 설정** 및 **제거 보호 활성화** 섹션을 참조하세요.

        - [PowerShell에서 일시 삭제를 사용하는 방법](../key-vault/general/soft-delete-powershell.md)
        - [CLI에서 일시 삭제를 사용하는 방법](../key-vault/general/soft-delete-cli.md)
    - 기존 키 자격 증명 모음에는 크기가 2048 이상인 RSA 키가 있어야 합니다. 키에 대 한 자세한 내용은 [Azure Key Vault 키 정보](../key-vault/keys/about-keys.md)를 참조 하세요.
    - Key vault는 데이터에 사용 되는 저장소 계정과 동일한 지역에 있어야 합니다. 여러 저장소 계정을 Azure Data Box 리소스와 연결할 수 있습니다.
    - 기존 키 자격 증명 모음이 없는 경우 다음 섹션에 설명 된 대로 인라인으로 만들 수도 있습니다.

## <a name="enable-keys"></a>키 사용

Azure Data Box에 대 한 고객 관리 키를 구성 하는 것은 선택 사항입니다. 기본적으로 Data Box는 Microsoft 관리 키를 사용 하 여 BitLocker 키를 보호 합니다. Azure Portal에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Data Box 순서에 대 한 **개요** 블레이드로 이동 합니다.

    ![Data Box 순서의 개요 블레이드](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. **설정 > 암호화**로 이동 합니다. **암호화 유형**에서 장치 잠금 해제 키를 보호 하는 방법을 선택할 수 있습니다. 기본적으로 Microsoft 관리 키는 장치 잠금 해제 암호를 보호 하는 데 사용 됩니다. 

    ![암호화 옵션 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. 암호화 유형을 **Customer 관리 키**로 선택 합니다. 고객 관리 키를 선택한 후 **key vault 및 키를 선택**합니다.

    ![고객 관리 키 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. **Azure Key Vault에서 키 선택** 블레이드에서 구독이 자동으로 채워집니다. **키 자격 증명 모음**의 드롭다운 목록에서 기존 키 자격 증명 모음을 선택할 수 있습니다.

    ![새 Azure Key Vault 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    **새로 만들기**를 선택하여 새로운 키 자격 증명 모음을 만들 수도 있습니다. **키 자격 증명 모음 만들기 블레이드**에서 리소스 그룹 및 주요 자격 증명 모음 이름을 입력합니다. **일시 삭제** 및 **보호 제거** 를 사용 하도록 설정 했는지 확인 합니다. 그 외에는 기본값을 적용합니다. **검토 + 만들기**를 선택합니다.

    ![새 Azure Key Vault 만들기 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. 키 자격 증명 모음과 연결된 정보를 검토하고**만들기**를 선택합니다. 키 자격 증명 모음 만들기가 완료될 때까지 몇 분 정도 기다립니다.

    ![Azure Key Vault 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. **Azure Key Vault에서 키 선택**에서 기존 키 자격 증명 모음의 키를 선택할 수 있습니다.

    ![Azure Key Vault 3에서 새 키 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. 새 키를 만들려는 경우 **새로 만들기** 를 선택 하 여 키를 만듭니다. RSA 키 크기는 2048 이상이어야 합니다.

    ![Azure Key Vault 4에서 새 키 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. 키 이름을 입력하고 나머지는 기본값을 적용한 다음, **만들기**를 선택합니다. 

    ![새 키 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. 키 자격 증명 모음에 키가 생성되었다는 알림이 표시됩니다. **버전**을 선택하고, **선택**을 클릭합니다.

    ![키 자격 증명 모음에 새로 생성된 키](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. **암호화 유형** 창에서 고객이 관리 하는 키에 대해 선택한 키 자격 증명 모음 및 키를 볼 수 있습니다.

    ![고객 관리 키에 대 한 키 및 키 자격 증명 모음](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. 키를 저장 합니다. 

    ![고객 관리 키 저장](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    키 URL이 **암호화 유형**아래에 표시 됩니다.

    ![고객이 관리 하는 키 URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Microsoft 관리 키를 사용 하지 않도록 설정 하 고 Data Box 주문의 모든 단계에서 고객 관리 키로 이동할 수 있습니다. 그러나 고객이 관리 하는 키를 만든 후에는 Microsoft에서 관리 하는 키로 다시 전환할 수 없습니다.

## <a name="troubleshoot-errors"></a>오류 문제 해결

고객이 관리 하는 키와 관련 된 오류가 표시 되 면 다음 표를 사용 하 여 문제를 해결 하십시오.

| 오류 코드| 오류 세부 정보| 복구 가능한가요?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| 고객 관리 키가 사용 하지 않도록 설정 되어 있으므로 암호를 가져올 수 없습니다.| 예, 키 버전을 사용 하도록 설정 합니다.|
| SsemUserErrorEncryptionKeyExpired| 고객 관리 키가 만료 되어 서 암호를 가져올 수 없습니다.| 예, 키 버전을 사용 하도록 설정 합니다.|
| SsemUserErrorKeyDetailsNotFound| 고객 관리 키를 찾을 수 없으므로 암호를 가져올 수 없습니다.| 키 자격 증명 모음을 삭제 한 경우 고객 관리 키를 복구할 수 없습니다.  키 자격 증명 모음을 다른 테 넌 트로 마이그레이션한 경우 [구독 이동 후에 주요 자격 증명 모음 테 넌 트 ID 변경](../key-vault/general/move-subscription.md)을 참조 하세요. 키 자격 증명 모음을 삭제 한 경우:<ol><li>예, 제거 보호 기간 내에 있는 경우 [키 자격 증명 모음 복구](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)의 단계에 따라 복구할 수 있습니다.</li><li>아니요, 제거 보호 기간이 지나면 복구할 수 없습니다.</li></ol><br>키 자격 증명 모음에서 테 넌 트 마이그레이션을 클러스터가 거쳤다 하는 경우, 예, 아래 단계 중 하나를 사용 하 여 복구할 수 있습니다. <ol><li>키 자격 증명 모음을 이전 테넌트로 되돌립니다.</li><li>`Identity = None`을 설정한 다음, 값을 다시 `Identity = SystemAssigned`로 설정합니다. 이렇게 하면 새 ID가 생성될 때 ID가 삭제되고 다시 생성됩니다. 키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 `Get`, `Wrap` 및 `Unwrap` 권한을 사용하도록 설정합니다.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| 고객 관리 키 액세스가 취소 되어 서 암호를 가져올 수 없습니다.| 예, 다음 사항을 확인하세요. <ol><li>키 자격 증명 모음의 액세스 정책에 여전히 MSI가 있습니다.</li><li>액세스 정책은 가져오기, 래핑, 래핑 해제에 대 한 권한을 제공 합니다.</li><li>키 자격 증명 모음이 방화벽 뒤에 있는 vNet에 있는 경우 **Microsoft 신뢰할 수 있는 서비스 허용** 이 사용 하도록 설정 되어 있는지 확인 합니다.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| 고객 관리 키에 대 한 연결 된 키 자격 증명 모음을 찾을 수 없으므로 암호를 가져올 수 없습니다. | 키 자격 증명 모음을 삭제 한 경우 고객 관리 키를 복구할 수 없습니다.  키 자격 증명 모음을 다른 테 넌 트로 마이그레이션한 경우 [구독 이동 후에 주요 자격 증명 모음 테 넌 트 ID 변경](../key-vault/general/move-subscription.md)을 참조 하세요. 키 자격 증명 모음을 삭제 한 경우:<ol><li>예, 제거 보호 기간 내에 있는 경우 [키 자격 증명 모음 복구](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)의 단계에 따라 복구할 수 있습니다.</li><li>아니요, 제거 보호 기간이 지나면 복구할 수 없습니다.</li></ol><br>키 자격 증명 모음에서 테 넌 트 마이그레이션을 클러스터가 거쳤다 하는 경우, 예, 아래 단계 중 하나를 사용 하 여 복구할 수 있습니다. <ol><li>키 자격 증명 모음을 이전 테넌트로 되돌립니다.</li><li>`Identity = None`을 설정한 다음, 값을 다시 `Identity = SystemAssigned`로 설정합니다. 이렇게 하면 새 ID가 생성될 때 ID가 삭제되고 다시 생성됩니다. 키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 `Get`, `Wrap` 및 `Unwrap` 권한을 사용하도록 설정합니다.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | 고객 관리 키를 찾을 수 없으므로 암호를 가져올 수 없습니다.| 예, 다음 사항을 확인하세요. <ol><li>키 자격 증명 모음의 액세스 정책에 여전히 MSI가 있습니다.</li><li>Id는 시스템 할당 유형입니다.</li><li>키 자격 증명 모음의 액세스 정책에서 id에 대 한 가져오기, 래핑 및 래핑 해제 권한을 사용 하도록 설정 합니다.</li></ol>|
| 일반 오류  | 암호를 가져올 수 없습니다.| 일반 오류입니다. Microsoft 지원에 문의 하 여 오류를 해결 하 고 다음 단계를 확인 합니다.|


## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란](../key-vault/general/overview.md)?
