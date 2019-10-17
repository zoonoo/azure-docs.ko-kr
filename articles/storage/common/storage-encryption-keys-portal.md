---
title: Azure Portal에서 Azure Storage 암호화를 위해 고객이 관리 하는 키 구성
description: Azure Portal를 사용 하 여 Azure Storage 암호화를 위해 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다. 고객 관리 키를 사용 하 여 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 74ca6f15baeeb9fe8adad4bda80e313a4b4cf03a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376235"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Azure Portal에서 Azure Storage 암호화를 위해 고객이 관리 하는 키 구성

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 [Azure Portal](https://portal.azure.com/)를 사용 하 여 고객이 관리 하는 키로 key vault를 구성 하는 방법을 보여 줍니다. Azure Portal를 사용 하 여 주요 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](../../key-vault/quick-create-portal.md)을 참조 하세요. 

> [!IMPORTANT]
> Azure Storage 암호화와 함께 고객 관리 키를 사용 하려면 키 자격 증명 모음에 대해 두 개의 속성을 설정 해야 합니다. **일시 삭제** 하 고 **제거 하지**않아야 합니다. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않습니다. 이러한 속성을 사용 하도록 설정 하려면 PowerShell 또는 Azure CLI 중 하나를 사용 합니다.
> RSA 키와 키 크기 2048만 지원 됩니다.

## <a name="enable-customer-managed-keys"></a>고객이 관리 하는 키 사용

Azure Portal에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. 스토리지 계정으로 이동합니다.
1. 스토리지 계정에 대한 **설정** 블레이드에서 **암호화**를 클릭합니다. 다음 그림과 같이 **사용자 고유 키 사용** 옵션을 선택합니다.

    ![암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>키 지정

고객이 관리 하는 키를 사용 하도록 설정 하면 저장소 계정에 연결할 키를 지정할 수 있습니다.

### <a name="specify-a-key-as-a-uri"></a>키를 URI로 지정

키를 URI로 지정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 키 URI를 찾으려면 키 자격 증명 모음으로 이동 하 고 **키 설정을 선택** 합니다. 원하는 키를 선택한 다음 키를 클릭 하 여 해당 설정을 봅니다. URI를 제공 하는 **키 식별자** 필드의 값을 복사 합니다.

    ![주요 자격 증명 모음 키 URI를 보여 주는 스크린샷](media/storage-encryption-keys-portal/key-uri-portal.png)

1. 저장소 계정에 대 한 **암호화** 설정에서 **키 URI 입력** 옵션을 선택 합니다.
1. **키 URI** 필드에 URI를 지정합니다.

   ![키 URI를 입력 하는 방법을 보여 주는 스크린샷](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Key Vault에서 키 지정

키 자격 증명 모음에서 키를 지정 하려면 먼저 키가 포함 된 key vault가 있는지 확인 합니다. 키 자격 증명 모음에서 키를 지정 하려면 다음 단계를 수행 합니다.

1. **Key Vault에서 선택** 옵션을 선택합니다.
2. 사용하려는 키가 포함된 Key Vault를 선택합니다.
3. Key Vault에서 키를 선택합니다.

   ![고객 관리 키 옵션을 보여 주는 스크린샷](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>키 버전 업데이트

새 버전의 키를 만드는 경우 새 버전을 사용 하도록 저장소 계정을 업데이트 해야 합니다. 다음 단계를 수행하세요.

1. 저장소 계정으로 이동 하 여 **암호화** 설정을 표시 합니다.
1. 새 키 버전에 대 한 URI를 지정 합니다. 또는 키 자격 증명 모음을 다시 선택 하 고 키를 다시 선택 하 여 버전을 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [휴지 상태의 데이터에 대 한 암호화 Azure Storage](storage-service-encryption.md)
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)이란?
