---
title: 미사용 데이터의 Translator 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft에서는 CMK(고객 관리형 키)라고 하는 사용자 고유 키를 사용하여 Cognitive Services 구독을 관리할 수 있습니다. 이 문서에서는 Translator의 미사용 데이터 암호화와 CMK를 활성화하고 관리하는 방법을 설명합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ff7b9f86cebc3c2479105d2a52aa92a265f8a1b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524455"
---
# <a name="translator-encryption-of-data-at-rest"></a>미사용 데이터의 Translator 암호화

Translator는 사용자 지정 번역 모델을 빌드하기 위해 업로드하는 데이터를 자동으로 암호화하며, 클라우드로 지속적으로 업로드할 때 조직의 보안 및 규정 준수 목표를 충족하는 데 도움이 됩니다.

## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 규격 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화 및 암호 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 자동으로 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 고객 관리형 키를 지원하는 가격 책정 계층을 사용하는 경우 다음 이미지에 표시된 것처럼 [Azure Portal](https://portal.azure.com)의 **암호화** 섹션에서 리소스에 대한 암호화 설정을 확인할 수 있습니다.

![암호화 설정 보기](../media/cognitive-services-encryption/encryptionblade.png)

Microsoft 관리형 암호화 키만 지원하는 구독의 경우에는 **암호화** 섹션이 표시되지 않습니다.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용하는 고객 관리형 키

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK(고객 관리형 키)라고 하는 사용자 고유의 키를 사용하여 구독을 관리하는 옵션도 있습니다. CMK를 사용하면 훨씬 더 유연하게 액세스 제어를 만들고, 순환하고, 사용하지 않도록 설정하고, 취소할 수 있습니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다. CMK가 구독에 대해 구성된 경우 이중 암호화가 제공되어 두 번째 보안 계층을 제공하는 동시에 Azure Key Vault를 통해 암호화 키를 제어할 수 있습니다.

> [!IMPORTANT]
> 고객 관리형 키는 Translator 서비스의 모든 가격 책정 계층에서 사용할 수 있습니다. 고객 관리형 키를 사용하는 기능을 요청하려면 [Translator 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성하고 제출합니다. 요청 상태를 확인하는 데 약 3-5영업일이 소요됩니다. 요청에 따라 공간이 확보되면 큐에 배치되고 승인될 수 있습니다. Translator 서비스를 사용하는 CMK 사용이 승인된 후에는 새 Translator 리소스를 만들어야 합니다. Translator 리소스가 생성되면 Azure Key Vault를 사용하여 관리 ID를 설정할 수 있습니다.

Translator용 고객 관리형 키를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 새 지역 Translator 또는 지역 Cognitive Services 리소스를 만듭니다. 전역 리소스에서는 작동하지 않습니다.
2. Azure Portal에서 관리 ID를 사용하도록 설정하고 고객 관리형 키 정보를 추가합니다.
3. Custom Translator에서 새 작업 영역을 만들고 이 구독 정보를 연결합니다.

### <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용

고객 관리형 키를 저장하려면 Azure Key Vault를 사용해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. Cognitive Services 리소스 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure AD(Azure Active Directory) 테넌트에 있어야 하지만 서로 다른 구독에 있을 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../../key-vault/general/overview.md)을 참조하세요.

새 Cognitive Services 리소스는 항상 Microsoft 관리형 키를 사용하여 암호화됩니다. 리소스를 만들 때는 고객 관리형 키를 사용하도록 설정할 수 없습니다. 고객 관리형 키는 Azure Key Vault에 저장되며, 키 자격 증명 모음은 Cognitive Services 리소스와 연결된 관리 ID에 키 권한을 부여하는 액세스 정책을 사용하여 프로비전되어야 합니다. 관리 ID는 리소스가 생성되는 즉시 사용할 수 있습니다.

Cognitive Services 암호화를 위해 Azure Key Vault에서 고객 관리형 키를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Portal에서 Cognitive Services 암호화를 위해 키 자격 증명 모음을 사용하여 고객 관리형 키 구성](../Encryption/cognitive-services-encryption-keys-portal.md)

고객 관리형 키를 사용하도록 설정하면 Azure AD의 기능인 시스템 할당 관리 ID도 사용할 수 있습니다. 시스템 할당 관리 ID를 사용하도록 설정하면 이 리소스가 Azure Active Directory에 등록됩니다. 등록 후 관리 ID에는 고객 관리형 키를 설정하는 동안 선택된 키 자격 증명 모음에 대한 액세스 권한이 부여됩니다. 여기에서 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아볼 수 있습니다.

> [!IMPORTANT]
> 시스템 할당 관리 ID를 사용하지 않도록 설정하면 키 자격 증명 모음에 대한 액세스 권한이 제거되고 고객 키로 암호화된 데이터에 더 이상 액세스할 수 없게 됩니다. 이 데이터에 의존하는 기능은 작동하지 않습니다. 배포한 모든 모델의 배포도 취소됩니다. 업로드된 모든 데이터가 Custom Translator에서 삭제됩니다. 관리 ID를 다시 사용하도록 설정하면 모델을 자동으로 다시 배포하지 않습니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 자동 할당됩니다. 이후에 구독, 리소스 그룹 또는 리소스를 Azure AD 디렉터리 간에 이동하는 경우, 리소스와 연결된 관리 ID가 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 FAQ에서 **Azure AD 디렉터리 간 구독 전송** 및 [Azure 리소스에 대한 관리 ID의 알려진 문제](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)를 참조하세요.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에 고객 관리형 키 저장

고객 관리형 키를 사용하도록 설정하려면 Azure Key Vault를 사용하여 키를 저장해야 합니다. 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 둘 다 사용하도록 설정해야 합니다.

2048 크기의 RSA 키만 Cognitive Services 암호화에서 지원됩니다. 키에 대한 자세한 내용은 [Azure Key Vault 키, 비밀 및 인증서 정보](../../key-vault/general/about-keys-secrets-certificates.md)의 **Key Vault 키** 를 참조하세요.

> [!NOTE]
> 전체 키 자격 증명 모음이 삭제되면 데이터가 더 이상 표시되지 않으며 모든 모델의 배포가 취소될 것입니다. 업로드된 모든 데이터가 Custom Translator에서 삭제됩니다. 

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리형 키에 대한 액세스 권한 철회

고객 관리형 키에 대한 액세스 권한을 철회하려면 PowerShell 또는 Azure CLI를 사용합니다. 자세한 내용은 [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) 또는 [Azure Key Vault CLI](/cli/azure/keyvault)를 참조하세요. 액세스 권한을 철회하면 Cognitive Services에서 암호화 키에 액세스할 수 없으므로 Cognitive Services 계정의 모든 데이터에 대한 액세스가 효과적으로 차단되고 모델의 배포가 취소됩니다. 업로드된 모든 데이터도 Custom Translator에서 삭제됩니다.


## <a name="next-steps"></a>다음 단계

* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)
