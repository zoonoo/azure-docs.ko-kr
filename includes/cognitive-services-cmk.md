---
title: 파일 포함
description: 포함 파일
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372409"
---
### <a name="enable-customer-managed-keys"></a>고객이 관리 하는 키 사용

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성할 수 있습니다. Cognitive Services 리소스 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)를 참조 하세요.

새 Cognitive Services 리소스는 항상 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 리소스를 만들 때 고객 관리 키를 사용 하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장 되며, 키 자격 증명 모음은 Cognitive Services 리소스와 연결 된 관리 되는 id에 대 한 키 사용 권한을 부여 하는 액세스 정책을 사용 하 여 프로 비전 되어야 합니다. 관리 id는 CMK에 대 한 가격 책정 계층을 사용 하 여 리소스를 만든 후에만 사용할 수 있습니다.

Cognitive Services 암호화를 위해 Azure Key Vault에서 고객이 관리 하는 키를 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Portal에서 Cognitive Services 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

고객 관리 키를 사용 하도록 설정 하면 Azure AD의 기능인 시스템 할당 관리 id도 사용할 수 있습니다. 시스템 할당 관리 id를 사용 하도록 설정 하면이 리소스가 Azure Active Directory 등록 됩니다. 등록 한 후 관리 되는 id에는 고객이 관리 하는 키를 설정 하는 동안 선택 된 Key Vault에 대 한 액세스 권한이 부여 됩니다. [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에 대해 자세히 알아볼 수 있습니다.

> [!IMPORTANT]
> 시스템 할당 관리 id를 사용 하지 않도록 설정 하면 키 자격 증명 모음에 대 한 액세스가 제거 되 고 고객 키로 암호화 된 데이터에 더 이상 액세스할 수 없게 됩니다. 이 데이터에 의존 하는 기능은 작동 하지 않습니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에서 고객이 관리 하는 키를 구성 하는 경우 관리 id는 내부적으로 자동으로 할당 됩니다. 이후에 Azure AD 디렉터리 간에 구독, 리소스 그룹 또는 리소스를 이동 하는 경우 해당 리소스와 연결 된 관리 id는 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 Faq에서 **AZURE AD 디렉터리 간 구독 전송** [및 azure 리소스에 대 한 관리 id의 알려진 문제](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)를 참조 하세요.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객이 관리 하는 키 저장

고객이 관리 하는 키를 사용 하도록 설정 하려면 Azure Key Vault를 사용 하 여 키를 저장 해야 합니다. 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 모두 사용 하도록 설정 해야 합니다.

2048 크기의 RSA 키만 Cognitive Services 암호화에서 지원 됩니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)정보를 참조 하세요.

### <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객이 관리 하는 키를 회전할 수 있습니다. 키를 회전 하는 경우 새 키 URI를 사용 하도록 Cognitive Services 리소스를 업데이트 해야 합니다. Azure Portal에서 새 버전의 키를 사용 하도록 리소스를 업데이트 하는 방법을 알아보려면 [Azure Portal를 사용 하 여 Cognitive Services에 대 한 고객 관리 키 구성](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)의 **키 버전 업데이트** 섹션을 참조 하세요.

키를 회전 해도 리소스의 데이터를 다시 암호화 하는 것은 트리거되지 않습니다. 사용자에 게 필요한 추가 작업은 없습니다.

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대 한 액세스 취소

고객 관리 키에 대 한 액세스를 취소 하려면 PowerShell 또는 Azure CLI를 사용 합니다. 자세한 내용은 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 또는 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조 하세요. Cognitive Services에서 암호화 키에 액세스할 수 없으므로 액세스를 취소 하면 Cognitive Services 리소스의 모든 데이터에 대 한 액세스가 효과적으로 차단 됩니다.


