---
title: Azure 포털을 사용하여 가져오기/내보내기 서비스에 대한 고객 관리 키 구성
description: Azure 포털을 사용하여 Azure 가져오기/내보내기 서비스에 대한 Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 구성하는 방법에 대해 알아봅니다. 고객 관리 키를 사용하면 액세스 컨트롤을 생성, 회전, 비활성화 및 해지할 수 있습니다.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ddcb47bfe8ba2b77efd8ff0aed52f1412107f0c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456501"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>가져오기/내보내기 서비스에 Azure 키 자격 증명 모음에서 고객 관리 키 사용

Azure 가져오기/내보내기는 암호화 키를 통해 드라이브를 잠그는 데 사용되는 BitLocker 키를 보호합니다. 기본적으로 BitLocker 키는 Microsoft에서 관리하는 키로 암호화됩니다. 암호화 키를 추가로 제어하려면 고객 관리 키를 제공할 수도 있습니다.

고객 관리 키는 Azure 키 자격 증명 모음에 만들고 저장해야 합니다. Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](../../key-vault/general/overview.md)

이 문서에서는 [Azure 포털에서](https://portal.azure.com/)가져오기/내보내기 서비스와 함께 고객 관리 키를 사용하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.

1. 다음의 지침에 따라 가져오기 또는 내보내기 작업을 만들었습니다.

    - [Blob에 대한 가져오기 작업을 만듭니다.](storage-import-export-data-to-blobs.md)
    - [파일에 대한 가져오기 작업을 만듭니다.](storage-import-export-data-to-files.md)
    - [Blob에 대한 내보내기 작업 만들기](storage-import-export-data-from-blobs.md)

2. BitLocker 키를 보호하는 데 사용할 수 있는 키가 있는 기존 Azure 키 볼트가 있습니다. Azure 포털을 사용하여 키 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure 포털을 사용하여 Azure 키 자격 증명 모음에서 비밀 설정 및 검색을](../../key-vault/secrets/quick-create-portal.md)참조하세요.

    - **기존** 키 볼트에 일시 삭제 및 **제거 금지가** 설정되어 있습니다. 이러한 속성은 기본적으로 활성화되지 않습니다. 이러한 속성을 사용하려면 다음 문서 중 하나에서 **일시 삭제 및** **지우기 보호 활성화** 라는 섹션을 참조하십시오.

        - [PowerShell에서 소프트 삭제를 사용하는 방법](../../key-vault/general/soft-delete-powershell.md).
        - [CLI와 소프트 삭제를 사용하는 방법](../../key-vault/general/soft-delete-cli.md).
    - 기존 키 자격 증명 모음에는 2048 크기 이상의 RSA 키가 있어야 합니다. 키에 대한 자세한 내용은 Azure [키 볼트 정보 키, 암호 및 인증서의](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)키 자격 증명 모음 **키를** 참조하십시오.
    - 키 자격 증명 모음은 데이터의 저장소 계정과 동일한 지역에 있어야 합니다.  
    - 기존 Azure 키 볼트가 없는 경우 다음 섹션에 설명된 대로 인라인을 만들 수도 있습니다.

## <a name="enable-keys"></a>키 사용

가져오기/내보내기 서비스에 대한 고객 관리 키 를 구성하는 것은 선택 사항입니다. 기본적으로 가져오기/내보내기 서비스는 Microsoft 관리 키를 사용하여 BitLocker 키를 보호합니다. Azure 포털에서 고객 관리 키를 활성화하려면 다음 단계를 따르십시오.

1. 가져오기 작업에 대한 **개요** 블레이드로 이동합니다.
2. 오른쪽 창에서 **BitLocker 키가 암호화되는 방법 선택을**선택합니다.

    ![암호화 옵션 선택](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. **암호화** 블레이드에서 장치 BitLocker 키를 보고 복사할 수 있습니다. **암호화 유형에서**BitLocker 키를 보호하는 방법을 선택할 수 있습니다. 기본적으로 Microsoft 관리 키가 사용됩니다.

    ![비트락커 키 보기](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 고객 관리 키를 지정하는 옵션이 있습니다. 고객 관리 키를 선택한 후 **키 자격 증명 모음 및 키를 선택합니다.**

    ![고객 관리 키 선택](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Azure **키 볼트 블레이드에서 선택 키에서** 구독이 자동으로 채워집니다. **키 자격 증명 모음의**경우 드롭다운 목록에서 기존 키 자격 증명 모음을 선택할 수 있습니다.

    ![Azure 키 자격 증명 모음 선택 또는 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. 새 키 자격 증명 모음을 만들려면 **새 만들기를** 선택할 수도 있습니다. 키 **자격 증명 모음 만들기 블레이드에서**리소스 그룹과 키 자격 증명 모음 이름을 입력합니다. 다른 모든 기본값을 적용합니다. **검토 + 만들기**를 선택합니다.

    ![새 Azure 키 볼트 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 키 자격 증명 모음과 연결된 정보를 검토하고 **에서 만들기를**선택합니다. 키 자격 증명 모음 생성이 완료될 때까지 몇 분 정도 기다립니다.

    ![Azure Key Vault 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Azure **키 자격 증명 모음에서 선택 키에서**기존 키 자격 증명 모음에서 키를 선택할 수 있습니다.

9. 새 키 자격 증명 모음을 만든 경우 **새 키 만들기를** 선택합니다. RSA 키 크기는 2048 이상일 수 있습니다.

    ![Azure 키 볼트에서 새 키 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    키 자격 증명 모음을 만들 때 소프트 삭제 및 제거 보호가 활성화되지 않으면 키 자격 증명 모음이 소프트 삭제 및 제거 보호가 사용하도록 업데이트됩니다.

10. 키 이름을 제공하고 다른 기본값을 수락한 다음 **을**선택합니다.

    ![새 키 만들기](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. **버전을** 선택한 다음 **을 선택합니다.** 키 자격 증명 모음에 키가 생성된다는 알림이 전송됩니다.

    ![키 자격 증명 모음에서 만든 새 키](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

**암호화** 블레이드에서 키 자격 증명 모음과 고객 관리 키에 대해 선택한 키를 볼 수 있습니다.

## <a name="disable-keys"></a>키 사용 안 함

Microsoft 관리 키를 사용하지 않도록 설정하고 가져오기/내보내기 작업의 모든 단계에서 고객 관리 키로만 이동할 수 있습니다. 그러나 고객 관리 키를 만든 후에는 비활성화할 수 없습니다.

## <a name="troubleshoot-customer-managed-key-errors"></a>고객 관리 키 오류 문제 해결

고객 관리 키와 관련된 오류가 발생하면 다음 표를 사용하여 문제를 해결합니다.

| 오류 코드     |세부 정보     | 복구?    |
|----------------|------------|-----------------|
| CmkError액세스다시호출 | 고객 관리 키를 적용했지만 키 액세스는 현재 해지되었습니다. 자세한 내용은 [키 액세스 활성화](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)방법을 참조하세요.                                                      | 예, 다음을 확인하십시오. <ol><li>키 자격 증명 모음에는 액세스 정책에 MSI가 여전히 있습니다.</li><li>액세스 정책은 받기, 줄 바꿈, 줄 바꿈 해제에 대한 권한을 제공합니다.</li><li>키 자격 증명 모음이 방화벽 뒤의 vNet에 있는 경우 **Microsoft 신뢰할 수 있는 서비스 허용이** 활성화되어 있는지 확인합니다.</li></ol>                                                                                            |
| CmkError 사용 안 함      | 고객 관리 키를 적용했지만 키가 비활성화되었습니다. 자세한 내용은 키 사용 방법을 [참조하세요.](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate)                                                                             | 예. 키 버전을 사용하도록 설정하여     |
| CmkErrorNot발견      | 고객 관리 키를 적용했지만 키를 찾을 수 없습니다. <br>보존 기간 이후에 키가 삭제되고 제거된 경우 키를 복구할 수 없습니다. 키를 백업한 경우 키를 복원하여 이 문제를 해결할 수 있습니다. | 아니요, 키가 삭제되었으며 보존 기간 이후에 제거되었습니다. <br>예. 고객이 키를 백업하고 복원하는 경우에만 가능합니다.  |
| CmkErrorVaultNot발견되지 않음 | 고객이 관리키를 적용했지만 키와 연결된 키 자격 증명 모음을 찾을 수 없습니다.<br>키 자격 증명 모음을 삭제한 경우 고객 관리 키를 복구할 수 없습니다.  키 자격 증명 모음을 다른 테넌트로 마이그레이션한 경우 [구독 이동 후 키 자격 증명 모음 테넌트 ID 변경을](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)참조하세요. |   아니요, 고객이 키 자격 증명 모음을 삭제한 경우<br> 예. 키 자격 증명 모음에서 테넌트 마이그레이션을 받은 경우 다음 중 하나를 수행합니다. <ol><li>키 자격 증명 모음을 이전 테넌트로 다시 이동합니다.</li><li>ID = 없음을 설정한 다음 Id = SystemAssigned으로 다시 설정하면 ID가 삭제되고 다시 생성됩니다.</li></ol>|

## <a name="next-steps"></a>다음 단계

- [Azure 키 볼트란?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
