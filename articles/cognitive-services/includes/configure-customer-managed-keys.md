---
title: Azure Portal를 사용 하 여 고객 관리 키 구성
titleSuffix: Cognitive Services
description: Azure Portal를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다. 고객 관리형 키를 사용하면 액세스 제어를 만들고, 회전하고, 비활성화하고, 철회할 수 있습니다.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 63cfe7968ec88ed75dfe23e8a3d34ac2649f6776
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307828"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용하는 고객 관리형 키

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. Cognitive Services 리소스 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)를 참조 하세요.

새 Cognitive Services 리소스가 만들어지면 항상 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 리소스를 만들 때 고객 관리 키를 사용 하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장 되며, 키 자격 증명 모음은 Cognitive Services 리소스와 연결 된 관리 되는 id에 대 한 키 사용 권한을 부여 하는 액세스 정책을 사용 하 여 프로 비전 되어야 합니다. 관리 id는 CMK에 필요한 가격 책정 계층을 사용 하 여 리소스를 만든 후에만 사용할 수 있습니다.

고객 관리 키를 사용 하도록 설정 하면 Azure AD의 기능인 시스템 할당 [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)도 사용할 수 있습니다. 시스템 할당 관리 id를 사용 하도록 설정 하면이 리소스가 Azure Active Directory 등록 됩니다. 등록 한 후 관리 되는 id에는 고객이 관리 하는 키를 설정 하는 동안 선택 된 Key Vault에 대 한 액세스 권한이 부여 됩니다. 

> [!IMPORTANT]
> 시스템 할당 관리 id를 사용 하지 않도록 설정 하면 키 자격 증명 모음에 대 한 액세스가 제거 되 고 고객 키로 암호화 된 데이터에 더 이상 액세스할 수 없게 됩니다. 이 데이터에 의존 하는 기능은 작동 하지 않습니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에서 고객이 관리 하는 키를 구성 하는 경우 관리 id는 내부적으로 자동으로 할당 됩니다. 이후에 Azure AD 디렉터리 간에 구독, 리소스 그룹 또는 리소스를 이동 하는 경우 해당 리소스와 연결 된 관리 id는 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 Faq에서 **AZURE AD 디렉터리 간 구독 전송** [및 azure 리소스에 대 한 관리 id의 알려진 문제](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)를 참조 하세요.  

## <a name="configure-azure-key-vault"></a>Azure Key Vault 구성

고객 관리 키를 사용 하려면 키 자격 증명 모음에 두 가지 속성을 설정 해야 합니다. **일시 삭제** 하 고 **제거 하지 마십시오**. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않지만 PowerShell 또는 기존 키 자격 증명 모음에서 Azure CLI를 사용 하 여 사용 하도록 설정할 수 있습니다.

> [!IMPORTANT]
> **일시 삭제** 및 **제거 안 함** 속성을 사용 하도록 설정 되어 있지 않고 키를 삭제 한 경우 인식 서비스 리소스의 데이터를 복구할 수 없습니다.

기존 키 자격 증명 모음에서 이러한 속성을 사용하려면 다음 문서 중 하나에서 **일시 삭제를 사용하도록 설정** 및 **제거 보호 활성화** 섹션을 참조하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)
- [CLI에서 일시 삭제를 사용하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)

2048 크기의 RSA 키만 Azure Storage 암호화에서 지원 됩니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)정보를 참조 하세요.

## <a name="enable-customer-managed-keys-for-your-resource"></a>리소스에 대해 고객이 관리 하는 키 사용

Azure Portal에서 고객 관리형 키를 사용하도록 설정하려면 다음 단계를 수행합니다.

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

## <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객이 관리 하는 키를 회전할 수 있습니다. 키를 회전 하는 경우 새 키 URI를 사용 하도록 Cognitive Services 리소스를 업데이트 해야 합니다. Azure Portal에서 새 버전의 키를 사용 하도록 리소스를 업데이트 하는 방법을 알아보려면 [키 버전 업데이트](#update-the-key-version)를 참조 하세요.

키를 회전 해도 리소스의 데이터를 다시 암호화 하는 것은 트리거되지 않습니다. 사용자에 게 필요한 추가 작업은 없습니다.

## <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대 한 액세스 취소

고객 관리 키에 대 한 액세스를 취소 하려면 PowerShell 또는 Azure CLI를 사용 합니다. 자세한 내용은 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 또는 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조 하세요. Cognitive Services에서 암호화 키에 액세스할 수 없으므로 액세스를 취소 하면 Cognitive Services 리소스의 모든 데이터에 대 한 액세스가 효과적으로 차단 됩니다.

## <a name="disable-customer-managed-keys"></a>고객 관리 키 사용 안 함

고객 관리 키를 사용 하지 않도록 설정 하면 Cognitive Services 리소스가 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 고객 관리 키를 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

1. Cognitive Services 리소스로 이동 하 여 **암호화** 설정을 표시 합니다.
1. 사용자의 **키 사용** 설정 옆에 있는 확인란의 선택을 취소 합니다.