---
title: 관리 ID
description: Media Services는 Azure 관리 ID와 함께 사용할 수 있습니다.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 0bbfb54d6ba7483e96633bdf05bb580e5517d216
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277745"
---
# <a name="managed-identities"></a>관리 ID

개발자는 일반적으로 서로 다른 서비스 간의 통신을 보호하기 위해 비밀과 자격 증명을 관리하는 데 어려움을 겪고 있습니다. Azure에서 관리 ID를 사용하면 개발자가 Azure AD에서 Azure 리소스에 대한 ID를 제공하고 이를 사용하여 Azure AD(Azure Active Directory) 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다.

현재 관리 ID를 Media Services와 함께 사용할 수 있는 두 가지 시나리오가 있습니다.

- Media Services 계정의 관리 ID를 사용하여 스토리지 계정에 액세스합니다.

- Media Services 계정의 관리 ID를 사용해 Key Vault에 액세스하여 고객 키에 액세스합니다.

다음 두 섹션에서는 두 시나리오의 단계를 설명합니다.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Media Services 계정의 관리 ID를 사용하여 스토리지 계정에 액세스합니다.

1. 관리 ID를 사용하여 Media Services 계정을 만듭니다.
1. 소유한 스토리지 계정에 대한 관리 ID 보안 주체 액세스 권한을 부여합니다.
1. 그러면 Media Services는 관리 ID를 사용하여 사용자를 대신해 스토리지 계정에 액세스할 수 있습니다.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Media Services 계정의 관리 ID를 사용해 Key Vault에 액세스하여 고객 키에 액세스합니다.

1. 관리 ID를 사용하여 Media Services 계정을 만듭니다.
1. 소유한 Key Vault에 대한 관리 ID 보안 주체 액세스 권한을 부여합니다.
1. 고객 키 기반 계정 암호화를 사용하도록 Media Services 계정을 구성합니다.
1. Media Services는 관리 ID를 사용하여 사용자를 대신해 Key Vault에 액세스합니다.

고객 관리형 키 및 Key Vault에 대한 자세한 내용은 [Media Services를 사용하여 자체 키(고객 관리형 키) 가져오기](concept-use-customer-managed-keys-byok.md)를 참조하세요.

## <a name="tutorials"></a>자습서

이러한 자습서에는 위에서 언급한 시나리오가 모두 포함됩니다.

- [Azure Portal을 사용하여 Media Services에서 고객 관리형 키 또는 BYOK 사용](security-customer-managed-keys-portal-tutorial.md)
- [Media Services REST API에서 고객 관리형 키 또는 BYOK를 사용](security-customer-managed-keys-rest-postman-tutorial.md)합니다.

## <a name="next-steps"></a>다음 단계

관리 ID가 사용자 및 Azure 애플리케이션에 대해 수행할 수 있는 작업에 대한 자세한 내용은 [Azure AD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
