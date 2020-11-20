---
title: Azure Portal를 사용 하 여 Azure Data Box에 대 한 고객 관리 키를 관리 합니다.
description: Azure Portal를 사용 하 여 Azure Data Box에 대 한 Azure Key Vault에서 고객이 관리 하는 키를 만들고 관리 하는 방법을 알아봅니다. 고객 관리 키를 사용 하 여 액세스 제어를 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있습니다.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: cd9f4ad6b6831b2b15c09b37edc569b3f2d247f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958210"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Azure Data Box Azure Key Vault에서 고객이 관리 하는 키 사용

Azure Data Box는 암호화 키를 통해 장치를 잠그는 데 사용 되는 장치 잠금 해제 키 (장치 암호 라고도 함)를 보호 합니다. 기본적으로이 암호화 키는 Microsoft에서 관리 하는 키입니다. 추가 제어를 위해 고객이 관리 하는 키를 사용할 수 있습니다.

고객 관리 키를 사용 하는 것은 장치의 데이터가 암호화 되는 방식에 영향을 주지 않습니다. 장치 잠금 해제 키를 암호화 하는 방법에만 영향을 줍니다.

주문 프로세스 전체에서이 제어 수준을 유지 하려면 주문을 만들 때 고객 관리 키를 사용 합니다. 자세한 내용은 [자습서: Order Azure Data Box](data-box-deploy-ordered.md)를 참조 하세요.

이 문서에서는 [Azure Portal](https://portal.azure.com/)에서 기존 Data Box 주문에 대해 고객이 관리 하는 키를 사용 하도록 설정 하는 방법을 보여 줍니다. 현재 고객이 관리 하는 키에 대 한 주요 자격 증명 모음, 키, 버전 또는 id를 변경 하거나 Microsoft 관리 키를 사용 하 여 다시 전환 하는 방법에 대해 알아봅니다.

이 문서는 Azure Data Box 및 Azure Data Box Heavy 장치에 적용 됩니다.

## <a name="requirements"></a>요구 사항

Data Box 주문의 고객 관리 키는 다음 요구 사항을 충족 해야 합니다.

- 일시 삭제를 사용 하도록 설정 **하지** 않고 **일시 삭제** 를 사용 하는 Azure Key Vault에 키를 만들고 저장 해야 합니다. 자세한 내용은 [Azure Key Vault란?](../key-vault/general/overview.md)을 참조하세요. 주문을 만들거나 업데이트 하는 동안 키 자격 증명 모음 및 키를 만들 수 있습니다.

- 키는 2048 크기 이상의 RSA 키 여야 합니다.

## <a name="enable-key"></a>키 사용

Azure Portal의 기존 Data Box 주문에 대해 고객이 관리 하는 키를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Data Box 순서에 대 한 **개요** 화면으로 이동 합니다.

    ![Data Box 순서-1의 개요 화면](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. **설정 > 암호화** 로 이동 하 고 **고객 관리 키** 를 선택 합니다. 그런 다음 **키 및 키 자격 증명 모음 선택** 을 선택 합니다.

    ![고객 관리 키 암호화 옵션을 선택 합니다.](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   **Azure Key Vault에서 키 선택** 화면에 구독이 자동으로 채워집니다.

 3. **Key vault** 의 경우 드롭다운 목록에서 기존 key vault를 선택 하거나 **새로 만들기** 를 선택 하 여 새 key vault를 만듭니다.

     ![고객 관리 키를 선택할 때 주요 자격 증명 모음 옵션](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     새 key vault를 만들려면 **새 키** 자격 증명 모음 만들기 화면에 구독, 리소스 그룹, 주요 자격 증명 모음 이름 및 기타 정보를 입력 합니다. **복구 옵션** 에서 **일시 삭제** 및 **보호 제거** 를 사용 하도록 설정 했는지 확인 합니다. 그런 다음, **검토 + 만들기** 를 선택합니다.

      ![검토 및 만들기 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      주요 자격 증명 모음에 대 한 정보를 검토 하 고 **만들기** 를 선택 합니다. 키 자격 증명 모음 만들기가 완료 될 때까지 몇 분 정도 기다립니다.

       ![설정을 사용 하 여 Azure Key Vault 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. **Azure Key Vault에서 키 선택** 화면에서 키 자격 증명 모음에서 기존 키를 선택 하거나 새 키를 만들 수 있습니다.

    ![Azure Key Vault에서 키 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   새 키를 만들려면 **새로 만들기** 를 선택 합니다. RSA 키를 사용 해야 합니다. 크기는 2048 이상일 수 있습니다.

    ![Azure Key Vault에서 새 키 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    새 키의 이름을 입력 하 고 다른 기본값을 적용 한 다음 **만들기** 를 선택 합니다. 키 자격 증명 모음에 키가 생성 되었다는 알림이 표시 됩니다.

    ![새 키 이름](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. **버전** 의 경우 드롭다운 목록에서 기존 키 버전을 선택할 수 있습니다.

    ![새 키에 대 한 버전 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    새 키 버전을 생성 하려면 **새로 만들기** 를 선택 합니다.

    ![새 키 버전을 만들기 위한 대화 상자 열기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    새 키 버전에 대 한 설정을 선택 하 고 **만들기** 를 선택 합니다.

    ![새로운 키 버전 만들기](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. 주요 자격 증명 모음, 키 및 키 버전을 선택한 경우 **선택** 을 선택 합니다.

    ![Azure Key Vault 키](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    **암호화 유형** 설정에는 선택한 키 자격 증명 모음 및 키가 표시 됩니다.

    ![고객 관리 키에 대 한 키 및 키 자격 증명 모음](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. 이 리소스에 대 한 고객 관리 키를 관리 하는 데 사용할 id 유형을 선택 합니다. 주문 생성 중에 생성 된 **시스템 할당** id를 사용 하거나 사용자 할당 id를 선택할 수 있습니다.

    사용자 할당 id는 리소스에 대 한 액세스를 관리 하는 데 사용할 수 있는 독립적인 리소스입니다. 자세한 내용은 [관리 되는 id 유형](/azure/active-directory/managed-identities-azure-resources/overview)을 참조 하세요.

    ![Id 유형 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    사용자 id를 할당 하려면 **사용자 할당 됨** 을 선택 합니다. 그런 다음 **사용자 Id 선택** 을 선택 하 고 사용 하려는 관리 되는 id를 선택 합니다.

    ![사용할 id 선택](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    여기에서는 새 사용자 id를 만들 수 없습니다. 계정을 만드는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 사용자 할당 관리 id에 역할 만들기, 나열, 삭제 또는 할당](/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)을 참조 하세요.

    선택한 사용자 id가 **암호화 유형** 설정에 표시 됩니다.

    ![암호화 유형 설정에 표시 되는 선택 된 사용자 id](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. **저장** 을 선택 하 여 업데이트 된 **암호화 유형** 설정을 저장 합니다.

     ![고객 관리 키 저장](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    키 URL이 **암호화 유형** 아래에 표시 됩니다.

    ![고객이 관리 하는 키 URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>키 변경

현재 사용 중인 고객이 관리 하는 키의 키 자격 증명 모음, 키 및/또는 키 버전을 변경 하려면 다음 단계를 수행 합니다.

1. **개요** 화면에서 Data Box **설정**  >  **암호화** 로 이동 하 고 **키 변경** 을 클릭 합니다.

    ![고객 관리 키-1을 사용 하는 Data Box 주문의 개요 화면](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. **다른 키 자격 증명 모음 및 키 선택을** 선택 합니다.

    ![Data Box 순서의 개요 화면-다른 키 및 키 자격 증명 모음 옵션을 선택 합니다.](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. **키 자격 증명 모음에서 키 선택** 화면은 구독을 표시 하지만 주요 자격 증명 모음, 키 또는 키 버전은 표시 하지 않습니다. 다음과 같은 변경 작업을 수행할 수 있습니다.

   - 동일한 키 자격 증명 모음에서 다른 키를 선택 합니다. 키와 버전을 선택 하기 전에 키 자격 증명 모음을 선택 해야 합니다.

   - 다른 키 자격 증명 모음을 선택 하 고 새 키를 할당 합니다.

   - 현재 키에 대 한 버전을 변경 합니다.
   
    변경을 완료 한 후 **선택** 을 선택 합니다.

    ![암호화 옵션 선택-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. **저장** 을 선택합니다.

    ![업데이트 된 암호화 설정 저장-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Id 변경

이러한 순서로 고객 관리 키에 대 한 액세스를 관리 하는 데 사용 되는 id를 변경 하려면 다음 단계를 수행 합니다.

1. 완료 된 Data Box 순서의 **개요** 화면에서 **설정**  >  **암호화** 로 이동 합니다.

2. 다음 변경 중 하나를 수행 합니다.

     - 다른 사용자 id를 변경 하려면 **다른 사용자 Id 선택** 을 클릭 합니다. 그런 다음 화면 오른쪽의 패널에서 다른 id를 선택 하 고 **선택** 을 선택 합니다.

       ![고객 관리 키에 대 한 사용자 할당 id를 변경 하는 옵션](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - 주문 생성 중에 생성 된 시스템 할당 id로 전환 하려면 **id 유형 선택을 선택** 하 여 **시스템에 할당** 된 id를 선택 합니다.

     ![고객 관리 키에 대 한 시스템 할당으로 변경 하기 위한 옵션](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. **저장** 을 선택합니다.

    ![업데이트 된 암호화 설정 저장-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Microsoft 관리 키 사용

고객 관리 키를 사용 하 여 Microsoft 관리 키로 변경 하려면 다음 단계를 수행 합니다.

1. 완료 된 Data Box 순서의 **개요** 화면에서 **설정**  >  **암호화** 로 이동 합니다.

2. **유형을 선택** 하 여 **Microsoft 관리 키** 를 선택 합니다.

    ![Data Box 순서-5의 개요 화면](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. **저장** 을 선택합니다.

    ![Microsoft 관리 키에 대 한 업데이트 된 암호화 설정 저장](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>오류 문제 해결

고객이 관리 하는 키와 관련 된 오류가 표시 되 면 다음 표를 사용 하 여 문제를 해결 하십시오.

| 오류 코드| 오류 세부 정보| 복구 가능한가요?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| 고객 관리 키가 사용 하지 않도록 설정 되어 있으므로 암호를 가져올 수 없습니다.| 예, 키 버전을 사용 하도록 설정 합니다.|
| SsemUserErrorEncryptionKeyExpired| 고객 관리 키가 만료 되어 서 암호를 가져올 수 없습니다.| 예, 키 버전을 사용 하도록 설정 합니다.|
| SsemUserErrorKeyDetailsNotFound| 고객 관리 키를 찾을 수 없으므로 암호를 가져올 수 없습니다.| 키 자격 증명 모음을 삭제 한 경우 고객 관리 키를 복구할 수 없습니다.  키 자격 증명 모음을 다른 테 넌 트로 마이그레이션한 경우 [구독 이동 후에 주요 자격 증명 모음 테 넌 트 ID 변경](../key-vault/general/move-subscription.md)을 참조 하세요. 키 자격 증명 모음을 삭제 한 경우:<ol><li>예, 제거 보호 기간 내에 있는 경우 [키 자격 증명 모음 복구](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)의 단계에 따라 복구할 수 있습니다.</li><li>아니요, 제거 보호 기간이 지나면 복구할 수 없습니다.</li></ol><br>키 자격 증명 모음에서 테 넌 트 마이그레이션을 클러스터가 거쳤다 하는 경우, 예, 아래 단계 중 하나를 사용 하 여 복구할 수 있습니다. <ol><li>키 자격 증명 모음을 이전 테넌트로 되돌립니다.</li><li>`Identity = None`을 설정한 다음, 값을 다시 `Identity = SystemAssigned`로 설정합니다. 이렇게 하면 새 ID가 생성될 때 ID가 삭제되고 다시 생성됩니다. 키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 `Get`, `Wrap` 및 `Unwrap` 권한을 사용하도록 설정합니다.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | 고객 관리 키를 적용 했지만 키 액세스 권한이 부여 되지 않았거나, 방화벽이 사용 하도록 설정 되어 있어 키 자격 증명 모음에 액세스할 수 없습니다. | 선택한 id를 key vault에 추가 하 여 고객 관리 키에 액세스할 수 있도록 합니다. Key vault에서 방화벽을 사용 하도록 설정한 경우 시스템 할당 id로 전환 하 고 고객 관리 키를 추가 합니다. 자세한 내용은 [키를 사용 하도록 설정](#enable-key)하는 방법을 참조 하세요. |
| SsemUserErrorKeyVaultDetailsNotFound| 고객 관리 키에 대 한 연결 된 키 자격 증명 모음을 찾을 수 없으므로 암호를 가져올 수 없습니다. | 키 자격 증명 모음을 삭제 한 경우 고객 관리 키를 복구할 수 없습니다.  키 자격 증명 모음을 다른 테 넌 트로 마이그레이션한 경우 [구독 이동 후에 주요 자격 증명 모음 테 넌 트 ID 변경](../key-vault/general/move-subscription.md)을 참조 하세요. 키 자격 증명 모음을 삭제 한 경우:<ol><li>예, 제거 보호 기간 내에 있는 경우 [키 자격 증명 모음 복구](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)의 단계에 따라 복구할 수 있습니다.</li><li>아니요, 제거 보호 기간이 지나면 복구할 수 없습니다.</li></ol><br>키 자격 증명 모음에서 테 넌 트 마이그레이션을 클러스터가 거쳤다 하는 경우, 예, 아래 단계 중 하나를 사용 하 여 복구할 수 있습니다. <ol><li>키 자격 증명 모음을 이전 테넌트로 되돌립니다.</li><li>`Identity = None`을 설정한 다음, 값을 다시 `Identity = SystemAssigned`로 설정합니다. 이렇게 하면 새 ID가 생성될 때 ID가 삭제되고 다시 생성됩니다. 키 자격 증명 모음의 액세스 정책에서 새 ID에 대한 `Get`, `Wrap` 및 `Unwrap` 권한을 사용하도록 설정합니다.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | 고객 관리 키를 찾을 수 없으므로 암호를 가져올 수 없습니다.| 예, 다음 사항을 확인하세요. <ol><li>키 자격 증명 모음의 액세스 정책에 여전히 MSI가 있습니다.</li><li>Id는 시스템 할당 유형입니다.</li><li>키 자격 증명 모음의 액세스 정책에서 id에 대 한 가져오기, 래핑 및 래핑 해제 권한을 사용 하도록 설정 합니다.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | 추가할 수 있는 사용자 할당 id의 총 수에 대 한 제한에 도달 하 여 새 사용자 할당 Id를 추가 하지 못했습니다. | 사용자 id가 적을수록 작업을 다시 시도 하거나 리소스에서 일부 사용자 할당 id를 제거한 후 다시 시도 하세요. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | 관리 id 액세스 작업이 실패 했습니다. <br> 참고: 구독이 다른 테 넌 트로 이동 되는 시나리오에 대 한 것입니다. 고객은 id를 새 테 넌 트로 수동으로 이동 해야 합니다. 자세한 내용은 PFA 메일을 참조 하세요. | 선택한 id를 구독이 있는 새 테 넌 트로 이동 하세요. 자세한 내용은 [키를 사용 하도록 설정](#enable-key)하는 방법을 참조 하세요. |
| SsemUserErrorKekUserIdentityNotFound | 고객 관리 키를 적용 했지만 해당 키에 대 한 액세스 권한이 있는 사용자 할당 id를 active directory에서 찾을 수 없습니다. <br> 참고: Azure에서 사용자 id를 삭제 하는 경우입니다.| 고객 관리 키에 대 한 액세스를 사용 하도록 설정 하려면 키 자격 증명 모음에 선택한 다른 사용자 할당 id를 추가 하세요. 자세한 내용은 [키를 사용 하도록 설정](#enable-key)하는 방법을 참조 하세요. |
| SsemUserErrorUserAssignedIdentityAbsent | 고객 관리 키를 찾을 수 없으므로 암호를 가져올 수 없습니다. | 고객 관리 키에 액세스할 수 없습니다. 키와 연결 된 사용자 할당 Id (UAI)가 삭제 되었거나 UAI 형식이 변경 되었습니다. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | 관리 id 액세스 작업이 실패 했습니다. <br> 참고: 구독이 다른 테 넌 트로 이동 되는 시나리오에 대 한 것입니다. 고객은 id를 새 테 넌 트로 수동으로 이동 해야 합니다. 자세한 내용은 PFA 메일을 참조 하세요. | 고객 관리 키에 대 한 액세스를 사용 하도록 설정 하려면 키 자격 증명 모음에 선택한 다른 사용자 할당 id를 추가 하세요. 자세한 내용은 [키를 사용 하도록 설정](#enable-key)하는 방법을 참조 하세요.|
| SsemUserErrorKeyVaultBadRequestException | 고객 관리 키를 적용 했지만 키 액세스 권한이 부여 되지 않았거나, 방화벽이 사용 하도록 설정 되어 있어 키 자격 증명 모음에 액세스할 수 없습니다. | 선택한 id를 key vault에 추가 하 여 고객 관리 키에 액세스할 수 있도록 합니다. Key vault에서 방화벽을 사용 하도록 설정한 경우 시스템 할당 id로 전환 하 고 고객 관리 키를 추가 합니다. 자세한 내용은 [키를 사용 하도록 설정](#enable-key)하는 방법을 참조 하세요. |
| 일반 오류  | 암호를 가져올 수 없습니다.| 일반 오류입니다. Microsoft 지원에 문의 하 여 오류를 해결 하 고 다음 단계를 확인 합니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../key-vault/general/overview.md)
- [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../key-vault/secrets/quick-create-portal.md)
