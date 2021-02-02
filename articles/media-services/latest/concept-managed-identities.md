---
title: 관리 ID
description: Media Services은 Azure 관리 되는 Id와 함께 사용할 수 있습니다.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258442"
---
# <a name="managed-identities"></a>관리 ID

개발자는 일반적으로 서로 다른 서비스 간의 통신을 보호하기 위해 비밀과 자격 증명을 관리하는 데 어려움을 겪고 있습니다. Azure에서 관리 ID를 사용하면 개발자가 Azure AD에서 Azure 리소스에 대한 ID를 제공하고 이를 사용하여 Azure AD(Azure Active Directory) 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다.

현재 관리 Id를 Media Services와 함께 사용할 수 있는 두 가지 시나리오가 있습니다.

- Media Services 계정의 관리 id를 사용 하 여 저장소 계정에 액세스 합니다.

- Media Services 계정의 관리 id를 사용 하 여 고객 키에 액세스 하기 위한 Key Vault에 액세스 합니다.

다음 두 섹션에서는 두 시나리오의 단계를 설명 합니다.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Media Services 계정의 관리 id를 사용 하 여 저장소 계정에 액세스

1. 관리 id를 사용 하 여 Media Services 계정을 만듭니다.
1. 소유 하 고 있는 저장소 계정에 대 한 관리 되는 id 사용자 액세스 권한을 부여 합니다.
1. 그런 다음 관리 되는 id를 사용 하 여 사용자 대신 저장소 계정에 액세스할 수 Media Services.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Media Services 계정의 관리 id를 사용 하 여 고객 키에 액세스 하기 위한 Key Vault 액세스

1. 관리 id를 사용 하 여 Media Services 계정을 만듭니다.
1. 소유 하 고 있는 Key Vault에 대 한 관리 되는 id 사용자 액세스 권한을 부여 합니다.
1. 고객 키 기반 계정 암호화를 사용 하도록 Media Services 계정을 구성 합니다.
1. Media Services는 관리 되는 id를 사용 하 여 사용자를 대신 하 여 Key Vault에 액세스 합니다.

고객 관리 키 및 Key Vault에 대 한 자세한 내용은를 [사용 하 여 사용자 고유의 키 (고객 관리 키) 가져오기](concept-use-customer-managed-keys-byok.md) 를 참조 하세요 Media Services

## <a name="tutorials"></a>자습서

이러한 자습서에는 위에서 언급 한 시나리오가 모두 포함 됩니다.

- [Azure Portal을 사용하여 Media Services에서 고객 관리형 키 또는 BYOK 사용](tutorial-byok-portal.md)
- [Media Services REST API에서 고객이 관리 하는 키 또는 BYOK를 사용](tutorial-byok-postman.md)합니다.

## <a name="next-steps"></a>다음 단계

사용자 및 Azure 응용 프로그램에 대해 수행할 수 있는 관리 되는 id에 대 한 자세한 내용은 [AZURE AD 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요.
