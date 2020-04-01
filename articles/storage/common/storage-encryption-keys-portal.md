---
title: Azure 포털을 사용하여 고객 관리 키 구성
titleSuffix: Azure Storage
description: Azure 포털을 사용하여 Azure 저장소 암호화에 대한 Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 구성하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e7878cae7bc6aabf1db58bfd63338955b9e830d3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478246"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 키 자격 증명 모음을 사용하여 고객 관리 키 구성

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 Azure [포털을](https://portal.azure.com/)사용하여 고객 관리 키로 Azure 키 자격 증명 모음을 구성하는 방법을 보여 주며 있습니다. Azure 포털을 사용하여 키 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure 포털을 사용하여 Azure 키 자격 증명 모음에서 비밀 설정 및 검색을](../../key-vault/quick-create-portal.md)참조하세요.

## <a name="configure-azure-key-vault"></a>Azure Key Vault 구성

Azure Storage 암호화를 사용하여 고객 관리 키를 사용하려면 키 자격 증명 모음에 두 개의 속성인 **소프트 삭제** 및 제거 **안 식**의 두 가지 속성을 설정해야 합니다. 이러한 속성은 기본적으로 활성화되어 있지 않지만 새 키 또는 기존 키 자격 증명 모음에서 PowerShell 또는 Azure CLI를 사용하여 사용할 수 있습니다.

기존 키 자격 증명 모음에서 이러한 속성을 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나에서 **연무 및** **지우기 보호 활성화** 라는 섹션을 참조하십시오.

- [PowerShell에서 소프트 삭제를 사용하는 방법](../../key-vault/key-vault-soft-delete-powershell.md).
- [CLI와 소프트 삭제를 사용하는 방법](../../key-vault/key-vault-soft-delete-cli.md).

Azure 저장소 암호화를 통해 2048비트 RSA 및 RSA-HSM 키만 지원됩니다. 키에 대한 자세한 내용은 Azure [키 볼트 정보 키, 비밀 및 인증서의](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)키 자격 증명 모음 **키를** 참조하십시오.

## <a name="enable-customer-managed-keys"></a>고객 관리 키 사용

Azure 포털에서 고객 관리 키를 활성화하려면 다음 단계를 따르십시오.

1. 스토리지 계정으로 이동합니다.
1. 스토리지 계정에 대한 **설정** 블레이드에서 **암호화**를 클릭합니다. 다음 이미지와 같이 고객 관리 키 옵션을 **선택합니다.**

    ![암호화 옵션을 보여주는 포털 스크린샷](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>키 지정

고객 관리 키를 사용하도록 설정하면 저장소 계정과 연결할 키를 지정할 수 있습니다.

### <a name="specify-a-key-as-a-uri"></a>키를 URI로 지정

키를 URI로 지정하려면 다음 단계를 따르십시오.

1. Azure 포털에서 키 URI를 찾으려면 키 자격 증명 모음으로 이동한 다음 **키** 설정을 선택합니다. 원하는 키를 선택한 다음 키를 클릭하여 해당 버전을 봅니다. 해당 버전의 설정을 보려면 키 버전을 선택합니다.
1. URI를 제공하는 **키 식별자** 필드의 값을 복사합니다.

    ![키 볼트 키 URI를 보여주는 스크린샷](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. 저장소 계정의 **암호화** 설정에서 **주요 URI 입력** 옵션을 선택합니다.
1. 복사한 URI를 **키 URI** 필드에 붙여넣습니다.

   ![키 URI를 입력하는 방법을 보여주는 스크린샷](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. 키 자격 증명 모음이 포함된 구독을 지정합니다.
1. 변경 내용을 저장합니다.

### <a name="specify-a-key-from-a-key-vault"></a>Key Vault에서 키 지정

키 자격 증명 모음에서 키를 지정하려면 먼저 키가 포함된 키 자격 증명 모음이 있는지 확인합니다. 키 자격 증명 모음에서 키를 지정하려면 다음 단계를 따르십시오.

1. **Key Vault에서 선택** 옵션을 선택합니다.
1. 사용하려는 키가 포함된 키 자격 증명 모음을 선택합니다.
1. 키 자격 증명 모음에서 키를 선택합니다.

   ![고객 관리 키 옵션을 보여주는 스크린샷](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. 변경 내용을 저장합니다.

## <a name="update-the-key-version"></a>키 버전 업데이트

키의 새 버전을 만들 때 새 버전을 사용 하 여 저장소 계정을 업데이트 합니다. 다음 단계를 수행하세요.

1. 저장소 계정으로 이동하여 **암호화** 설정을 표시합니다.
1. 새 키 버전에 대한 URI를 입력합니다. 또는 키 자격 증명 모음과 키를 다시 선택하여 버전을 업데이트할 수 있습니다.
1. 변경 내용을 저장합니다.

## <a name="use-a-different-key"></a>다른 키 사용

Azure Storage 암호화에 사용되는 키를 변경하려면 다음 단계를 따르십시오.

1. 저장소 계정으로 이동하여 **암호화** 설정을 표시합니다.
1. 새 키에 대한 URI를 입력합니다. 또는 키 자격 증명 모음을 선택하고 새 키를 선택할 수 있습니다.
1. 변경 내용을 저장합니다.

## <a name="disable-customer-managed-keys"></a>고객 관리 키 비활성화

고객 관리 키를 사용하지 않도록 설정하면 저장소 계정이 다시 한 번 Microsoft 관리 키로 암호화됩니다. 고객 관리 키를 사용하지 않도록 설정하려면 다음 단계를 따르세요.

1. 저장소 계정으로 이동하여 **암호화** 설정을 표시합니다.
1. 사용자 고유의 키 사용 설정 옆의 확인란을 선택 **취소합니다.**

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure 저장소 암호화](storage-service-encryption.md)
- [Azure 키 볼트란?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
