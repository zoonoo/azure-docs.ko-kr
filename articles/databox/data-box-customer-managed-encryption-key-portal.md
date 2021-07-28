---
title: Azure Portal를 사용하여 Azure Data Box에 고객 관리형 키를 관리 합니다.
description: Azure Portal를 사용하여 Azure Data Box에 대한 Azure Key Vault에서 고객 관리형 키를 만들고 관리하는 방법을 알아봅니다. 고객 관리형 키를 사용하면 액세스 제어를 만들고, 회전하고, 비활성화하고, 철회할 수 있습니다.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: e6b588ddea5bf4b4c92e89d9cebb37b09b9a86af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791547"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Azure Data Box용 Azure Key Vault에서 고객 관리형 키 사용

Azure Data Box는 암호화 키를 통해 장치를 잠그는 데 사용되는 장치 잠금 해제 키(장치 암호라고도 함)를 보호합니다. 기본적으로 이 암호화 키는 Microsoft에서 관리하는 키입니다. 추가 제어를 위해 고객 관리형 키를 사용할 수 있습니다.

고객 관리형 키는 장치의 데이터가 암호화되는 방식에 영향을 주지 않습니다. 장치 잠금 해제 키를 암호화하는 방법에만 영향을 줍니다.

순서 프로세스 전체에서 이 제어 수준을 유지하려면 주문을 만들 때 고객 관리형 키를 사용합니다. 자세한 내용은 [자습서: Azure Data Box 주문](data-box-deploy-ordered.md)을 참조하세요.

이 문서에서는 [Azure Portal](https://portal.azure.com/)에서 기존 Data Box 주문에 대해 고객 관리형 키를 사용하도록 설정 하는 방법을 보여줍니다. 현재 고객 관리형 키에 대한 키 자격 증명 모음, 키, 버전 또는 ID를 변경하거나 Microsoft 관리 키를 사용 하여 다시 전환하는 방법에 대해 알아봅니다.

이 문서는 Azure Data Box 및 Azure Data Box Heavy 장치에 모두 적용됩니다.

## <a name="requirements"></a>요구 사항

Data Box 순서의 고객 관리형 키는 다음 요구 사항을 충족해야 합니다.

- **일시 삭제** 를 사용하는 Azure Key Vault에 키를 만들고 저장해야 하며 일시 삭제를 사용 하도록 **설정 하지 않아야** 합니다. 자세한 내용은 [Azure Key Vault란?](../key-vault/general/overview.md)을 참조하세요. 주문을 만들거나 업데이트하는 동안 키 자격 증명 모음 및 키를 만들 수 있습니다.

- 키는 2048 크기 이상의 RSA 키 여야 합니다.

## <a name="enable-key"></a>키 사용

Azure Portal의 기존 Data Box 주문에 대해 고객 관리형 키를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Data Box 순서에 대한 **개요** 화면으로 이동합니다.

    ![Data Box 순서-1의 개요 화면](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. **설정 > 암호화** 로 이동하고 **고객 관리형 키** 를 선택합니다. 그런 다음, **Key Vault 및 키 선택** 을 선택합니다.

    ![고객 관리형 키 암호화 옵션을 선택합니다.](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   **Azure Key Vault에서 키 선택** 화면에서 구독이 자동으로 채워집니다.

 3. **키 자격 증명 모음** 의 경우 드롭다운 목록에서 기존 키 자격 증명 모음을 선택하거나 **새로 만들기** 를 선택하여 새 증명 모음을 만듭니다.

     ![고객 관리형 키를 선택할 때 키 자격 증명 모음 옵션](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     새 키 자격 증명 모음을 만들려면 새 키 자격 증명 모음 **만들기** 화면에서 구독, 리소스 그룹, 키 자격 증명 모음 이름 및 기타 정보를 입력합니다. **복구 옵션** 에서 **일시 삭제** 및 **제거 보호가** 사용되도록 설정되어 있는지 확인합니다. 그런 다음, **검토 + 만들기** 를 선택합니다.

      ![Azure Key Vault 검토 및 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      키 자격 증명 모음에 대한 정보를 검토하고, **만들기** 를 선택합니다. 키 자격 증명 모음 만들기가 완료될 때까지 몇 분 정도 기다립니다.

       ![설정을 사용하여 Azure Key Vault 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. Azure Key Vault **키 선택** 화면에서 키 자격 증명 모음에서 기존 키를 선택하거나 새 키를 만들 수 있습니다.

    ![Azure Key Vault에서 키 선택 화면](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   새 키를 만들려면 **새로 만들기** 를 선택합니다. RSA 키를 사용해야 합니다. 크기는 2048 이상일 수 있습니다.

    ![Azure Key Vault에서 새 키 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    새 키에 대한 이름을 입력하고, 다른 기본값을 적용한 다음, **만들기** 를 선택합니다. 키가 키 자격 증명 모음에 만들어지면 알림이 표시됩니다.

    ![새 키의 이름](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. **버전** 의 경우, 드롭다운 목록에서 기존 키 버전을 선택할 수 있습니다.

    ![새 키의 버전 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    새 키 버전을 만들려면 **새로 만들기** 를 선택합니다.

    ![새 키 버전을 만들기 위한 대화 상자 열기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    새 키 버전에 대한 설정을 선택하고,**만들기** 를 선택합니다.

    ![새로운 키 버전 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. 키 자격 증명 모음, 키 및 키 버전을 선택한 경우 **선택** 을 선택합니다.

    ![Azure Key Vault의 키](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    **암호화 유형** 설정은 선택한 키 자격 증명 모음 및 키를 표시합니다.

    ![고객 관리형 키에 대한 키 및 키 자격 증명 모음](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. 이 리소스에 대한 고객 관리형 키를 관리하는 데 사용할 ID 유형을 선택합니다. 주문 생성 중에 생성된 **시스템 할당** ID를 사용하거나 사용자 할당 ID를 선택할 수 있습니다.

    사용자 할당 ID는 리소스에 대한 액세스를 관리하는 데 사용할 수 있는 독립 리소스입니다. 자세한 내용은 [관리 ID 유형](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

    ![ID 유형 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    사용자 ID를 할당하려면 **사용자 할당** 을 선택합니다. 그런 **다음, 사용자 ID 선택** 을 선택하고 사용할 관리 ID를 선택합니다.

    ![사용할 ID 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    여기서는 새 사용자 ID를 만들 수 없습니다. 만드는 방법을 알아보려면 [Azure Portal을 사용하여 사용자 할당 관리 ID에 역할 만들기, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)을 참조하세요.

    **암호화 유형** 설정에 표시된 선택한 사용자 ID

    ![암호화 유형 설정에 표시된 선택한 사용자 ID](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. **저장을** 선택하여 업데이트된 **암호화 유형** 설정을 저장합니다.

     ![고객 관리형 키 저장](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    키 URL은 암호화 **유형** 아래에 표시됩니다.

    ![고객 관리형 키 URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>키 변경

현재 사용 중인 고객 관리형 키의 키 자격 증명 모음, 키 및/또는 키 버전을 변경하려면 다음 단계를 수행합니다.

1. Data Box 주문에 대한 **개요** 화면에서 **설정** > **암호화** 로 이동하고 **키 변경** 을 클릭합니다.

    ![고객 관리형 키-1을 사용하는 Data Box 순서의 개요 화면](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. **다른 키 자격 증명 모음 및 키** 를 선택합니다.

    ![Data Box 순서의 개요 화면에서 다른 키 및 키 자격 증명 모음 옵션을 선택합니다.](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. **키 자격 증명 모음에서 키 선택** 화면은 구독을 표시하지만 주요 자격 증명 모음, 키 또는 키 버전은 표시하지 않습니다. 다음과 같은 변경 작업을 수행할 수 있습니다.

   - 동일한 키 자격 증명 모음에서 다른 키를 선택합니다. 키와 버전을 선택하기 전에 키 자격 증명 모음을 선택해야 합니다.

   - 다른 키 자격 증명 모음을 선택하고 새 키를 할당합니다.

   - 현재 키에 대한 버전을 변경합니다.
   
    변경을 완료한 후 **선택** 을 선택합니다.

    ![암호화 옵션 선택-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. **저장** 을 선택합니다.

    ![업데이트된 암호화 설정 저장-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>ID 변경

고객 관리형 키 액세스를 관리하는 데 사용 되는 ID를 변경하려면 다음 단계를 수행합니다.

1. 완료 된 Data Box 순서의 **개요** 화면에서 **설정** > **암호화** 로 이동합니다.

2. 다음 중 하나를 변경합니다.

     - 다른 사용자 ID를 변경하려면 **다른 사용자 ID 선택** 을 클릭합니다. 그런 다음 화면 오른쪽의 패널에서 다른 ID를 선택 하 고 **선택** 을 선택합니다.

       ![고객 관리형 키의 사용자 할당 ID를 변경하는 옵션](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - 주문 생성 중에 생성된 시스템 할당 ID로 전환하려면 **시스템에 할당** 된 ID를 **ID 유형 선택을 선택** 하여 선택합니다.

     ![고객 관리형 키에 시스템 할당으로 변경하기 위한 옵션](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. **저장** 을 선택합니다.

    ![업데이트된 암호화 설정 저장-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Microsoft 관리 키 사용

고객 관리형 키 사용에서 주문에 대한 Microsoft 관리형 키로 변경하려면 다음 단계를 수행합니다.

1. 완료 된 Data Box 순서의 **개요** 화면에서 **설정** > **암호화** 로 이동합니다.

2. **형식을 선택** 하여 **Microsoft 관리형 키** 를 선택합니다.

    ![Data Box 주문의 개요 화면 - 5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. **저장** 을 선택합니다.

    ![Microsoft 관리 키에 대한 업데이트된 암호화 설정 저장](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>오류 문제 해결

고객 관리형 키와 관련된 오류가 발생하면 다음 표를 사용하여 문제를 해결하세요.

| 오류 코드| 오류 세부 정보| 복구 가능한가요?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| 고객 관리형 키가 사용하지 않도록 설정되었으므로 암호를 가져올 수 없습니다.| 예, 키 버전을 사용하도록 설정하여 복구할 수 있습니다.|
| SsemUserErrorEncryptionKeyExpired| 고객 관리형 키가 만료되었으므로 암호를 가져올 수 없습니다.| 예, 키 버전을 사용하도록 설정하여 복구할 수 있습니다.|
| SsemUserErrorKeyDetailsNotFound| 고객 관리형 키를 찾을 수 없으므로 암호를 가져올 수 없습니다.| 키 자격 증명 모음을 삭제한 경우 고객 관리형 키를 복구할 수 없습니다.  키 자격 증명 모음을 다른 테넌트로 마이그레이션한 경우 [구독 이동 후 키 자격 증명 모음 테넌트 ID 변경](../key-vault/general/move-subscription.md)을 참조하세요. 키 자격 증명 모음을 삭제한 경우:<ol><li>예, 제거 보호 기간 내에 있는 경우 [키 자격 증명 모음 복구](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)의 단계에 따라 복구할 수 있습니다.</li><li>아니요, 제거 보호 기간이 지나면 복구할 수 없습니다.</li></ol><br>또는 키 자격 증명 모음이 다른 테넌트로 마이그레이션된 경우 아래 단계 중 하나를 사용하여 복구할 수 있습니다. <ol><li>키 자격 증명 모음을 이전 테넌트로 되돌립니다.</li><li>`Identity = None`을 설정한 다음, 값을 다시 `Identity = SystemAssigned`로 설정합니다. 이렇게 하면 새 ID가 생성될 때 ID가 삭제되고 다시 생성됩니다. 키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 `Get`, `Wrap` 및 `Unwrap` 권한을 사용하도록 설정합니다.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | 고객 관리형 키를 적용했지만, 키 액세스 권한이 부여되지 않았거나 철회되었거나, 방화벽이 사용하도록 설정되어 있어 키 자격 증명 모음에 액세스할 수 없습니다. | 선택한 ID를 키 자격 증명 모음에 추가하여 고객 관리형 키에 액세스할 수 있도록 합니다. 키 자격 증명 모음에서 방화벽을 사용하도록 설정한 경우 시스템 할당 ID로 전환한 다음, 고객 관리형 키를 추가합니다. 자세한 내용은 키를 [사용하도록 설정](#enable-key)하는 방법을 참조하세요. |
| SsemUserErrorKeyVaultDetailsNotFound| 고객 관리형 키에 연결된 키 자격 증명 모음을 찾을 수 없으므로 암호를 가져올 수 없습니다. | 키 자격 증명 모음을 삭제한 경우 고객 관리형 키를 복구할 수 없습니다.  키 자격 증명 모음을 다른 테넌트로 마이그레이션한 경우 [구독 이동 후 키 자격 증명 모음 테넌트 ID 변경](../key-vault/general/move-subscription.md)을 참조하세요. 키 자격 증명 모음을 삭제한 경우:<ol><li>예, 제거 보호 기간 내에 있는 경우 [키 자격 증명 모음 복구](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)의 단계에 따라 복구할 수 있습니다.</li><li>아니요, 제거 보호 기간이 지나면 복구할 수 없습니다.</li></ol><br>또는 키 자격 증명 모음이 다른 테넌트로 마이그레이션된 경우 아래 단계 중 하나를 사용하여 복구할 수 있습니다. <ol><li>키 자격 증명 모음을 이전 테넌트로 되돌립니다.</li><li>`Identity = None`을 설정한 다음, 값을 다시 `Identity = SystemAssigned`로 설정합니다. 이렇게 하면 새 ID가 생성될 때 ID가 삭제되고 다시 생성됩니다. 키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 `Get`, `Wrap` 및 `Unwrap` 권한을 사용하도록 설정합니다.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | 고객 관리형 키를 찾을 수 없으므로 암호를 가져올 수 없습니다.| 예, 다음 사항을 확인하세요. <ol><li>키 자격 증명 모음의 액세스 정책에 여전히 MSI가 있습니다.</li><li>ID는 시스템 할당 유형입니다.</li><li>키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 Enable Get, Wrap 및 Unwrap 권한을 사용하도록 설정합니다.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | 추가할 수 있는 사용자 할당 ID의 총 수 제한에 도달하여 새 사용자 할당 ID를 추가하지 못했습니다. | 사용자 ID가 적을 때 작업을 다시 시도하거나 리소스에서 일부 사용자 할당 ID를 제거한 후 다시 시도하세요. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | 관리 ID 액세스 작업이 실패했습니다. <br> 참고: 구독이 다른 테넌트로 이동 되는 시나리오에 대한 것입니다. 고객은 ID를 새 테넌트로 수동으로 이동해야 합니다. 자세한 내용은 PFA 메일을 참조하세요. | 선택한 ID를 구독이 있는 새 테넌트로 이동하세요. 자세한 내용은 키를 [사용하도록 설정](#enable-key)하는 방법을 참조하세요. |
| SsemUserErrorKekUserIdentityNotFound | 고객 관리형 키를 적용했지만 해당 키에 대한 액세스 권한이 있는 사용자 할당 ID를 active directory에서 찾을 수 없습니다. <br> 참고: 사용자 ID가 Azure에서 삭제되는 경우입니다.| 고객 관리형 키에 액세스할 수 있도록 키 자격 증명 모음에 선택한 다른 사용자 할당 ID를 추가해 보세요. 자세한 내용은 키를 [사용하도록 설정](#enable-key)하는 방법을 참조하세요. |
| SsemUserErrorUserAssignedIdentityAbsent | 고객 관리형 키를 찾을 수 없으므로 암호를 가져올 수 없습니다. | 고객 관리형 키에 액세스할 수 없습니다. 키와 연결된 UAI(사용자 할당 ID)가 삭제되었거나 UAI 유형이 변경되었습니다. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | 관리 ID 액세스 작업이 실패했습니다. <br> 참고: 구독이 다른 테넌트로 이동 되는 시나리오에 대한 것입니다. 고객은 ID를 새 테넌트로 수동으로 이동해야 합니다. 자세한 내용은 PFA 메일을 참조하세요. | 고객 관리형 키에 액세스할 수 있도록 키 자격 증명 모음에 선택한 다른 사용자 할당 ID를 추가해 보세요. 자세한 내용은 키를 [사용하도록 설정](#enable-key)하는 방법을 참조하세요.|
| SsemUserErrorKeyVaultBadRequestException | 고객 관리형 키를 적용했지만, 키 액세스 권한이 부여되지 않았거나 철회되었거나, 방화벽이 사용하도록 설정되어 있어 키 자격 증명 모음에 액세스할 수 없습니다. | 선택한 ID를 키 자격 증명 모음에 추가하여 고객 관리형 키에 액세스할 수 있도록 합니다. 키 자격 증명 모음에서 방화벽을 사용하도록 설정한 경우 시스템 할당 ID로 전환한 다음, 고객 관리형 키를 추가합니다. 자세한 내용은 키를 [사용하도록 설정](#enable-key)하는 방법을 참조하세요. |
| 일반 오류  | 암호를 가져올 수 없습니다.| 일반 오류입니다. Microsoft 지원팀에 문의하여 오류를 해결하고 다음 단계를 확인합니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../key-vault/general/overview.md)
- [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../key-vault/secrets/quick-create-portal.md)
