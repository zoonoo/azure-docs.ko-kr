---
title: 미사용 데이터의 Language Understanding 서비스 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리하는 암호화 키를 제공하며 이를 통해 CMK(고객 관리형 키)라고 하는 사용자 고유의 키를 사용하여 Cognitive Services 구독을 관리할 수 있습니다. 이 문서에서는 LUIS(Language Understanding)에 대한 미사용 데이터 암호화에 대해 다루고 CMK를 사용하고 관리하는 방법을 설명합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 2dcfff005eaaac034f5fed13b6d4d18e20d2afae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524487"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>미사용 데이터의 Language Understanding 서비스 암호화

Language Understanding 서비스는 데이터가 클라우드에 유지될 때 데이터를 자동으로 암호화합니다. Language Understanding 서비스 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다.

## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 규격 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화 및 암호 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 자동으로 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK(고객 관리형 키)라고 하는 사용자 고유의 키를 사용하여 구독을 관리하는 옵션도 있습니다. CMK를 사용하면 훨씬 더 유연하게 액세스 제어를 만들고, 순환하고, 사용하지 않도록 설정하고, 취소할 수 있습니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용하는 고객 관리형 키

사용자 고유의 키를 사용하여 구독을 관리하는 옵션도 있습니다. BYOK(Bring Your Own Key)라고도 하는 CMK(고객 관리형 키) 사용하면 훨씬 더 유연하게 액세스 제어를 만들고, 회전하고, 사용하지 않도록 설정하고, 취소할 수 있습니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

고객 관리형 키를 저장하려면 Azure Key Vault를 사용해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. Cognitive Services 리소스 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure AD(Azure Active Directory) 테넌트에 있어야 하지만 서로 다른 구독에 있을 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../../key-vault/general/overview.md)을 참조하세요.

### <a name="customer-managed-keys-for-language-understanding"></a>Language Understanding에 대한 고객 관리형 키

고객 관리형 키를 사용하는 기능을 요청하려면  [LUIS Service 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성하여 제출합니다. 요청 상태에 대한 의견을 받는 데는 영업일 기준 약 3~5일이 소요됩니다. 요청에 따라 공간이 확보되면 큐에 배치되고 승인될 수 있습니다. LUIS와 함께 CMK를 사용하도록 승인되면 Azure Portal에서 새 Language Understanding 리소스를 만들고 해당 가격 책정 계층으로 E0를 선택해야 합니다. 새 SKU는 CMK를 제외하고 이미 사용할 수 있는 F0 SKU와 동일하게 작동합니다. 사용자는 F0에서 새 E0 SKU로 업그레이드할 수 없습니다.

![LUIS 구독 이미지](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>제한 사항

기존/이전에 만든 애플리케이션에서 E0 계층을 사용할 때 몇 가지 제한 사항이 있습니다.

* E0 리소스로의 마이그레이션이 차단됩니다. 사용자는 앱을 F0 리소스로만 마이그레이션할 수 있습니다. 기존 리소스를 F0로 마이그레이션한 후에는 E0 계층에서 새 리소스를 만들 수 있습니다. [마이그레이션](./luis-migration-authoring.md)에 대해 자세히 알아보세요.  
* E0 리소스로 또는 E0 리소스에서의 애플리케이션 이동이 차단됩니다. 이러한 제한 사항에 대한 해결 방법은 기존 애플리케이션을 내보내고 이를 E0 리소스로 가져오는 것입니다.
* Bing 맞춤법 검사 기능은 지원되지 않습니다.
* 애플리케이션이 E0인 경우 최종 사용자 트래픽 로깅을 사용할 수 없습니다.
* Azure Bot Service의 음성 초기화 기능은 E0 계층의 애플리케이션에 대해 지원되지 않습니다. 이 기능은 CMK를 지원하지 않는 Azure Bot Service를 통해 사용할 수 있습니다.
* 포털에서 음성 초기화 기능을 사용하려면 Azure Blob Storage가 필요합니다. 자세한 내용은 [사용자 고유의 스토리지 가져오기](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)를 참조하세요.

### <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용

새 Cognitive Services 리소스는 항상 Microsoft 관리형 키를 사용하여 암호화됩니다. 리소스를 만들 때는 고객 관리형 키를 사용하도록 설정할 수 없습니다. 고객 관리형 키는 Azure Key Vault에 저장되며, 키 자격 증명 모음은 Cognitive Services 리소스와 연결된 관리 ID에 키 권한을 부여하는 액세스 정책을 사용하여 프로비전되어야 합니다. 관리 ID는 CMK에 대한 가격 책정 계층을 사용하여 리소스를 만든 후에만 사용할 수 있습니다.

Cognitive Services 암호화를 위해 Azure Key Vault에서 고객 관리형 키를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Portal에서 Cognitive Services 암호화를 위해 키 자격 증명 모음을 사용하여 고객 관리형 키 구성](../Encryption/cognitive-services-encryption-keys-portal.md)

고객 관리형 키를 사용하도록 설정하면 Azure AD의 기능인 시스템 할당 관리 ID도 사용할 수 있습니다. 시스템 할당 관리 ID를 사용하도록 설정하면 이 리소스가 Azure Active Directory에 등록됩니다. 등록한 후 관리 ID에는 고객 관리형 키를 설정하는 동안 선택된 키 자격 증명 모음에 대한 액세스 권한이 부여됩니다. 여기에서 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아볼 수 있습니다.

> [!IMPORTANT]
> 시스템 할당 관리 ID를 사용하지 않도록 설정하면 키 자격 증명 모음에 대한 액세스 권한이 제거되고 고객 키로 암호화된 데이터에는 더 이상 액세스할 수 없게 됩니다. 이 데이터에 따라 달라지는 기능은 작동하지 않습니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 리소스를 Azure AD 디렉터리 간에 이동하는 경우, 리소스와 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 FAQ에서 **Azure AD 디렉터리 간 구독 전송** 및 [Azure 리소스에 대한 관리 ID의 알려진 문제](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)를 참조하세요.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에 고객 관리형 키 저장

고객 관리형 키를 사용하도록 설정하려면 Azure Key Vault를 사용하여 키를 저장해야 합니다. 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 둘 다 사용하도록 설정해야 합니다.

2048 크기의 RSA 키만 Cognitive Services 암호화에서 지원됩니다. 키에 대한 자세한 내용은 [Azure Key Vault 키, 비밀 및 인증서 정보](../../key-vault/general/about-keys-secrets-certificates.md)의 **키 자격 증명 모음 키** 를 참조하세요.

### <a name="rotate-customer-managed-keys"></a>고객 관리형 키 순환

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리형 키를 순환할 수 있습니다. 키를 순환하는 경우 새 키 URI를 사용하도록 Cognitive Services 리소스를 업데이트해야 합니다. Azure Portal에서 새 버전의 키를 사용하도록 리소스를 업데이트하는 방법을 알아보려면 [Azure Portal을 사용하여 Cognitive Services에 대한 고객 관리형 키 구성](../Encryption/cognitive-services-encryption-keys-portal.md)의 **키 버전 업데이트** 섹션을 참조하세요.

키를 순환해도 리소스에서 데이터의 재암호화가 트리거되지 않습니다. 사용자의 추가 작업은 필요하지 않습니다.

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리형 키에 대한 액세스 취소

고객 관리형 키에 대한 액세스를 취소하려면 PowerShell 또는 Azure CLI를 사용합니다. 자세한 내용은 [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) 또는 [Azure Key Vault CLI](/cli/azure/keyvault)를 참조하세요. 액세스를 취소하면 Cognitive Services에서 암호화 키에 액세스할 수 없으므로 Cognitive Services 계정의 모든 데이터에 대한 액세스가 효과적으로 차단됩니다.

## <a name="next-steps"></a>다음 단계

* [LUIS Service 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)
