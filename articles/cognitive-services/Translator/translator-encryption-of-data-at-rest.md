---
title: 미사용 데이터의 번역기 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 번역기 암호화.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372417"
---
# <a name="translator-encryption-of-data-at-rest"></a>미사용 데이터의 번역기 암호화

번역기는 클라우드에 유지될 때 사용자 지정 번역 모델을 구축하기 위해 업로드하는 데이터를 자동으로 암호화하여 조직의 보안 및 규정 준수 목표를 충족합니다.

## <a name="about-cognitive-services-encryption"></a>코그너티브 서비스 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 준수 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화되고 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 나 응용 프로그램을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 고객 관리 키를 지원하는 가격 책정 계층을 사용하는 경우 다음 이미지와 같이 [Azure 포털의](https://portal.azure.com)암호화 섹션에서 리소스에 대한 **암호화** 설정을 볼 수 있습니다.

![암호화 설정 보기](../media/cognitive-services-encryption/encryptionblade.png)

Microsoft에서 관리하는 암호화 키만 지원하는 구독의 경우 **암호화** 섹션이 없습니다.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure 키 볼트를 사용할 수 있는 고객 관리 키

자신의 키로 구독을 관리하는 옵션도 있습니다. BYOK(사용자 고유의 키 가져오기)라고도 하는 CMK(고객 관리 키)는 액세스 제어를 생성, 회전, 비활성화 및 해지할 수 있는 유연성을 제공합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

> [!IMPORTANT]
> 고객 관리 키는 번역기 서비스의 모든 가격 책정 계층에서 사용할 수 있습니다. 고객 관리 키를 사용할 수 있는 기능을 요청하려면 [번역사 고객 관리 키 요청 양식을](https://aka.ms/cogsvc-cmk) 작성하고 제출하려면 요청 상태를 다시 듣는 데 영업일 약 3~5일이 소요됩니다. 필요에 따라 대기열에 배치되고 공간이 사용 가능해지면 승인될 수 있습니다. 번역기 서비스와 함께 CMK를 사용할 수 있도록 승인되면 새 번역기 리소스를 만들어야 합니다. 번역기 리소스가 만들어지면 Azure Key Vault를 사용하여 관리되는 ID를 설정할 수 있습니다.

다음 단계에 따라 변환기에 대한 고객 관리 키를 활성화할 수 있습니다.

1. 새 지역 번역기 텍스트 또는 지역 인지 서비스 리소스를 만듭니다. 전역 리소스에는 작동하지 않습니다.
2. Azure 포털에서 관리되는 ID를 사용하도록 설정하고 고객 관리 키 정보를 추가합니다.
3. 사용자 지정 번역기에서 새 작업 영역을 만들고 이 구독 정보를 연결합니다.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>고객 관리 키 사용

Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 저장해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다. 코그너티브 서비스 리소스와 키 자격 증명 모음은 동일한 리전과 동일한 Azure Active Directory(Azure AD) 테넌트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

새 코그너티브 서비스 리소스는 항상 Microsoft에서 관리하는 키를 사용하여 암호화됩니다. 리소스를 만들 때 고객 관리 키를 사용하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장되며 키 자격 증명 모음에는 코그너티브 서비스 리소스와 연결된 관리되는 ID에 키 권한을 부여하는 액세스 정책이 제공되어야 합니다. 관리되는 ID는 리소스가 만들어지는 즉시 사용할 수 있습니다.

코그너티브 서비스 암호화를 위해 Azure 키 볼트를 사용하여 고객 관리 키를 사용하는 방법을 알아보려면 다음을 참조하십시오.

- [Azure 포털에서 인지 서비스 암호화를 위한 키 자격 증명 모음으로 고객 관리 키 구성](../Encryption/cognitive-services-encryption-keys-portal.md)

고객 관리 키를 활성화하면 Azure AD의 기능인 관리ID가 할당된 시스템도 사용할 수 있습니다. 관리되는 ID를 사용할 수 있는 시스템이 활성화되면 이 리소스는 Azure Active Directory에 등록됩니다. 등록 후 관리되는 ID는 고객 관리 키 설정 중에 선택한 키 볼트에 대한 액세스 권한이 부여됩니다. [관리되는 ID에](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)대해 자세히 알아볼 수 있습니다.

> [!IMPORTANT]
> 관리되는 ID를 할당된 시스템에서 사용하지 않도록 설정하면 키 자격 증명 모음에 대한 액세스가 제거되고 고객 키로 암호화된 모든 데이터에 더 이상 액세스할 수 없습니다. 이 데이터에 의존하는 모든 기능은 작동을 중지합니다. 배포한 모든 모델도 배포취소됩니다. 업로드된 모든 데이터는 사용자 지정 번역기에서 삭제됩니다. 관리되는 ID를 다시 사용하도록 설정한 경우 자동으로 모델을 다시 배포하지 않습니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure 포털에서 고객 관리 키를 구성하면 관리되는 ID가 자동으로 처리됩니다. 이후에 구독, 리소스 그룹 또는 리소스를 한 Azure AD 디렉터리에서 다른 Azure AD 디렉터리로 이동하면 리소스와 연결된 관리되는 ID가 새 테넌트로 전송되지 않으므로 고객 관리 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 **FAQ의 Azure AD 디렉터리 간의 구독 전송** 및 Azure [리소스에 대한 관리ID에 대한 알려진 문제를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)참조하십시오.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure 키 볼트에 고객 관리 키 저장

고객 관리 키를 사용하려면 Azure Key Vault를 사용하여 키를 저장해야 합니다. 키 자격 증명 모음에서 **소프트 삭제** 및 삭제 **안 함을** 모두 사용하도록 설정해야 합니다.

크기 2048의 RSA 키만 코그너티브 서비스 암호화를 통해 지원됩니다. 키에 대한 자세한 내용은 Azure [키 볼트 정보 키, 비밀 및 인증서의](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)키 자격 증명 모음 **키를** 참조하십시오.

> [!NOTE]
> 전체 키 자격 증명 모음이 삭제되면 데이터가 더 이상 표시되지 않고 모든 모델이 배포되지 않습니다. 업로드된 모든 데이터는 사용자 지정 번역기에서 삭제됩니다. 

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대한 액세스 취소

고객 관리 키에 대한 액세스를 취소하려면 PowerShell 또는 Azure CLI를 사용합니다. 자세한 내용은 [Azure 키 볼트 PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 또는 Azure 키 볼트 [CLI를](https://docs.microsoft.com/cli/azure/keyvault)참조하십시오. 액세스를 취소하면 코그너티브 서비스 리소스의 모든 데이터에 대한 액세스가 효과적으로 차단되며 코그너티브 서비스에서 암호화 키에 액세스할 수 없기 때문에 모델이 배포되지 않습니다. 업로드된 모든 데이터는 사용자 지정 번역기에서도 삭제됩니다.


## <a name="next-steps"></a>다음 단계

* [Azure 키 볼트에 대해 자세히 알아보기](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
