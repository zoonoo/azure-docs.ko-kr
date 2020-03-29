---
title: 미사용 데이터의 언어 이해 서비스 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 언어 이해 서비스 암호화.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372435"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>미사용 데이터의 언어 이해 서비스 암호화

언어 이해 서비스는 클라우드에 유지될 때 데이터를 자동으로 암호화합니다. 언어 이해 서비스 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다.

## <a name="about-cognitive-services-encryption"></a>코그너티브 서비스 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 준수 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화되고 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 나 응용 프로그램을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 자신의 키로 구독을 관리하는 옵션도 있습니다. CMK(고객 관리 키)는 액세스 제어를 생성, 회전, 비활성화 및 해지할 수 있는 유연성을 제공합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure 키 볼트를 사용할 수 있는 고객 관리 키

자신의 키로 구독을 관리하는 옵션도 있습니다. BYOK(사용자 고유의 키 가져오기)라고도 하는 CMK(고객 관리 키)는 액세스 제어를 생성, 회전, 비활성화 및 해지할 수 있는 유연성을 제공합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 저장해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다. 코그너티브 서비스 리소스와 키 자격 증명 모음은 동일한 리전과 동일한 Azure Active Directory(Azure AD) 테넌트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

### <a name="customer-managed-keys-for-language-understanding"></a>언어 이해를 위한 고객 관리 키

고객 관리 키를 사용할 수 있는 기능을 요청하려면 [LUIS 서비스 고객 관리 키 요청 양식을](https://aka.ms/cogsvc-cmk)작성하고 제출합니다. 요청 상태를 다시 듣는 데 는 영업일 기준으로 약 3~5일이 소요됩니다. 필요에 따라 대기열에 배치되고 공간이 사용 가능해지면 승인될 수 있습니다. LUIS에서 CMK를 사용할 수 있도록 승인되면 Azure 포털에서 새 언어 이해 리소스를 만들고 E0을 가격 책정 계층으로 선택해야 합니다. 새 SKU는 CMK를 제외하고 이미 사용 가능한 F0 SKU와 동일하게 작동합니다. 사용자는 F0에서 새로운 E0 SKU로 업그레이드할 수 없습니다.

E0 리소스는 작성 서비스에서만 사용할 수 있으며 E0 계층은 처음에는 미국 서부 지역에서만 지원됩니다.

![LUIS 구독 이미지](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>국가별 가용성

고객 관리 키는 현재 **미국 서부** 지역에서 사용할 수 있습니다.

### <a name="limitations"></a>제한 사항

기존/이전에 만든 응용 프로그램에서 E0 계층을 사용할 때 몇 가지 제한 사항이 있습니다.

* E0 리소스로의 마이그레이션이 차단됩니다. 사용자는 앱을 F0 리소스로만 마이그레이션할 수 있습니다. 기존 리소스를 F0으로 마이그레이션한 후 E0 계층에서 새 리소스를 만들 수 있습니다. 마이그레이션에 대한 자세한 내용은 [여기를](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)참조하십시오.  
* 응용 프로그램을 E0 리소스로 이동하거나 E0 리소스로 이동하면 차단됩니다. 이 제한에 대한 해결 방법은 기존 응용 프로그램을 내보내고 E0 리소스로 가져오는 것입니다.
* Bing 맞춤법 검사 기능은 지원되지 않습니다.
* 응용 프로그램이 E0인 경우 최종 사용자 트래픽 을 로깅할 수 없습니다.
* Azure Bot 서비스의 음성 프라이밍 기능은 E0 계층의 응용 프로그램에 대해 지원되지 않습니다. 이 기능은 CMK를 지원하지 않는 Azure 봇 서비스를 통해 사용할 수 있습니다.
* 포털의 음성 프라이밍 기능에는 Azure Blob 저장소가 필요합니다. 자세한 내용은 [자신의 저장소](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)가져오기를 참조하십시오.

### <a name="enable-customer-managed-keys"></a>고객 관리 키 사용

새 코그너티브 서비스 리소스는 항상 Microsoft에서 관리하는 키를 사용하여 암호화됩니다. 리소스를 만들 때 고객 관리 키를 사용하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장되며 키 자격 증명 모음에는 코그너티브 서비스 리소스와 연결된 관리되는 ID에 키 권한을 부여하는 액세스 정책이 제공되어야 합니다. CMK에 대한 가격 책정 계층을 사용하여 리소스를 만든 후에만 관리되는 ID를 사용할 수 있습니다.

코그너티브 서비스 암호화를 위해 Azure 키 볼트를 사용하여 고객 관리 키를 사용하는 방법을 알아보려면 다음을 참조하십시오.

- [Azure 포털에서 인지 서비스 암호화를 위한 키 자격 증명 모음으로 고객 관리 키 구성](../Encryption/cognitive-services-encryption-keys-portal.md)

고객 관리 키를 활성화하면 Azure AD의 기능인 관리ID가 할당된 시스템도 사용할 수 있습니다. 관리되는 ID를 사용할 수 있는 시스템이 활성화되면 이 리소스는 Azure Active Directory에 등록됩니다. 등록 후 관리되는 ID는 고객 관리 키 설정 중에 선택한 키 볼트에 대한 액세스 권한이 부여됩니다. [관리되는 ID에](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)대해 자세히 알아볼 수 있습니다.

> [!IMPORTANT]
> 관리되는 ID를 할당된 시스템에서 사용하지 않도록 설정하면 키 자격 증명 모음에 대한 액세스가 제거되고 고객 키로 암호화된 모든 데이터에 더 이상 액세스할 수 없습니다. 이 데이터에 의존하는 모든 기능은 작동을 중지합니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure 포털에서 고객 관리 키를 구성하면 관리되는 ID가 자동으로 처리됩니다. 이후에 구독, 리소스 그룹 또는 리소스를 한 Azure AD 디렉터리에서 다른 Azure AD 디렉터리로 이동하면 리소스와 연결된 관리되는 ID가 새 테넌트로 전송되지 않으므로 고객 관리 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 **FAQ의 Azure AD 디렉터리 간의 구독 전송** 및 Azure [리소스에 대한 관리ID에 대한 알려진 문제를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)참조하십시오.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure 키 볼트에 고객 관리 키 저장

고객 관리 키를 사용하려면 Azure Key Vault를 사용하여 키를 저장해야 합니다. 키 자격 증명 모음에서 **소프트 삭제** 및 삭제 **안 함을** 모두 사용하도록 설정해야 합니다.

크기 2048의 RSA 키만 코그너티브 서비스 암호화를 통해 지원됩니다. 키에 대한 자세한 내용은 Azure [키 볼트 정보 키, 비밀 및 인증서의](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)키 자격 증명 모음 **키를** 참조하십시오.

### <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리 키를 회전할 수 있습니다. 키가 회전되면 새 키 URI를 사용하려면 코그너티브 서비스 리소스를 업데이트해야 합니다. Azure 포털에서 키의 새 버전을 사용하도록 리소스를 업데이트하는 방법을 알아보려면 [Azure 포털을 사용하여 코그너티브 서비스에 대한 고객 관리 키 구성의](../Encryption/cognitive-services-encryption-keys-portal.md) **키 버전 업데이트** 섹션을 참조하십시오.

키를 회전해도 리소스의 데이터 재암호화가 트리거되지 않습니다. 사용자에게 필요한 추가 작업이 없습니다.

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대한 액세스 취소

고객 관리 키에 대한 액세스를 취소하려면 PowerShell 또는 Azure CLI를 사용합니다. 자세한 내용은 [Azure 키 볼트 PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 또는 Azure 키 볼트 [CLI를](https://docs.microsoft.com/cli/azure/keyvault)참조하십시오. 액세스를 취소하면 코그너티브 서비스에서 암호화 키에 액세스할 수 없기 때문에 코그너티브 서비스 리소스의 모든 데이터에 대한 액세스를 효과적으로 차단합니다.

## <a name="next-steps"></a>다음 단계

* [LUIS 서비스 고객 관리 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure 키 볼트에 대해 자세히 알아보기](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
