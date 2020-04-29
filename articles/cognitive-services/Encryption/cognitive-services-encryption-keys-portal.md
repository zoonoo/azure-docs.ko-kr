---
title: Azure Portal를 사용 하 여 고객 관리 키 구성
titleSuffix: Cognitive Services
description: Azure Portal를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다. 고객 관리 키를 사용 하 여 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있습니다.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455260"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Key Vault으로 고객 관리 키 구성

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성할 수 있습니다. Cognitive Services 리소스 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)를 참조 하세요.

이 문서에서는 [Azure Portal](https://portal.azure.com/)를 사용 하 여 고객 관리 키를 사용 하 여 Azure Key Vault를 구성 하는 방법을 보여 줍니다. Azure Portal를 사용 하 여 주요 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](../../key-vault/secrets/quick-create-portal.md)을 참조 하세요.

## <a name="configure-azure-key-vault"></a>Azure Key Vault 구성

고객 관리 키를 사용 하려면 키 자격 증명 모음에 대해 두 개의 속성을 설정 해야 합니다. **일시 삭제** 하 고 **제거 하지 마십시오**. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않지만 PowerShell 또는 기존 키 자격 증명 모음에서 Azure CLI를 사용 하 여 사용 하도록 설정할 수 있습니다.

> [!IMPORTANT]
> **일시 삭제** 및 **제거 안 함** 속성을 사용 하도록 설정 되어 있지 않고 키를 삭제 한 경우 인식 서비스 리소스의 데이터를 복구할 수 없습니다.

기존 키 자격 증명 모음에서 이러한 속성을 사용 하도록 설정 하는 방법을 알아보려면 다음 문서 중 하나에서 **일시 삭제 사용** 및 **제거 방지** 사용 섹션을 참조 하세요.

- [PowerShell에서 일시 삭제를 사용 하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)
- [CLI에서 일시 삭제를 사용 하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)입니다.

2048 크기의 RSA 키만 Azure Storage 암호화에서 지원 됩니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)정보를 참조 하세요.

## <a name="enable-customer-managed-keys"></a>고객이 관리 하는 키 사용

Azure Portal에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Cognitive Services 리소스로 이동 합니다.
1. Cognitive Services 리소스에 대 한 **설정** 블레이드에서 **암호화**를 클릭 합니다. 다음 그림에 표시 된 것 처럼 **고객 관리 키** 옵션을 선택 합니다.

    ![고객 관리 키를 선택 하는 방법을 보여 주는 스크린샷](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>키 지정

고객이 관리 하는 키를 사용 하도록 설정 하면 Cognitive Services 리소스와 연결할 키를 지정할 수 있습니다.

### <a name="specify-a-key-as-a-uri"></a>키를 URI로 지정

키를 URI로 지정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 키 URI를 찾으려면 키 자격 증명 모음으로 이동 하 고 **키 설정을 선택** 합니다. 원하는 키를 선택한 다음 키를 클릭 하 여 해당 버전을 확인 합니다. 해당 버전에 대 한 설정을 보려면 키 버전을 선택 합니다.
1. URI를 제공 하는 **키 식별자** 필드의 값을 복사 합니다.

    ![주요 자격 증명 모음 키 URI를 보여 주는 스크린샷](../media/cognitive-services-encryption/key-uri-portal.png)

1. 저장소 계정에 대 한 **암호화** 설정에서 **키 URI 입력** 옵션을 선택 합니다.
1. 복사한 URI를 **키 uri** 필드에 붙여넣습니다.

   ![키 URI를 입력 하는 방법을 보여 주는 스크린샷](../media/cognitive-services-encryption/ssecmk2.png)

1. 키 자격 증명 모음을 포함 하는 구독을 지정 합니다.
1. 변경 내용을 저장합니다.

### <a name="specify-a-key-from-a-key-vault"></a>Key Vault에서 키 지정

키 자격 증명 모음에서 키를 지정 하려면 먼저 키가 포함 된 key vault가 있는지 확인 합니다. 키 자격 증명 모음에서 키를 지정 하려면 다음 단계를 수행 합니다.

1. **Key Vault에서 선택** 옵션을 선택합니다.
1. 사용할 키를 포함 하는 키 자격 증명 모음을 선택 합니다.
1. 키 자격 증명 모음에서 키를 선택 합니다.

   ![고객 관리 키 옵션을 보여 주는 스크린샷](../media/cognitive-services-encryption/ssecmk3.png)

1. 변경 내용을 저장합니다.

## <a name="update-the-key-version"></a>키 버전 업데이트

새 버전의 키를 만드는 경우 새 버전을 사용 하도록 Cognitive Services 리소스를 업데이트 합니다. 다음 단계를 수행하세요.

1. Cognitive Services 리소스로 이동 하 여 **암호화** 설정을 표시 합니다.
1. 새 키 버전의 URI를 입력 합니다. 또는 키 자격 증명 모음을 다시 선택 하 고 키를 다시 선택 하 여 버전을 업데이트할 수 있습니다.
1. 변경 내용을 저장합니다.

## <a name="use-a-different-key"></a>다른 키 사용

암호화에 사용 되는 키를 변경 하려면 다음 단계를 수행 합니다.

1. Cognitive Services 리소스로 이동 하 여 **암호화** 설정을 표시 합니다.
1. 새 키에 대 한 URI를 입력 합니다. 또는 키 자격 증명 모음을 선택 하 고 새 키를 선택할 수 있습니다.
1. 변경 내용을 저장합니다.

## <a name="disable-customer-managed-keys"></a>고객 관리 키 사용 안 함

고객 관리 키를 사용 하지 않도록 설정 하면 Cognitive Services 리소스가 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 고객 관리 키를 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

1. Cognitive Services 리소스로 이동 하 여 **암호화** 설정을 표시 합니다.
1. 사용자의 **키 사용** 설정 옆에 있는 확인란의 선택을 취소 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)이란?
* [Cognitive Services 고객이 관리 하는 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [미사용 데이터의 Face 서비스 암호화](../Face/face-encryption-of-data-at-rest.md)
* [휴지 상태의 데이터 암호화 QnA Maker](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [휴지 상태의 데이터에 대 한 서비스 암호화 Language Understanding](../LUIS/luis-encryption-of-data-at-rest.md)
* [휴지 상태의 데이터 암호화 Content Moderator](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [미사용 데이터의 변환기 암호화](../translator/translator-encryption-of-data-at-rest.md)
* [휴지 상태의 데이터 암호화 Personalizer](../personalizer/personalizer-encryption-of-data-at-rest.md)
