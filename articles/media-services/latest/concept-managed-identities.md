---
title: Media services를 사용 하 여 관리 되는 id 및 신뢰할 수 있는 저장소
description: Media Services는 관리 id와 함께 사용 하 여 신뢰할 수 있는 저장소를 사용 하도록 설정할 수 있습니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: a0ded2a1ad5cd590ab5715edebde2ab19e399e8d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686869"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Media services를 사용 하 여 관리 되는 id 및 신뢰할 수 있는 저장소

Media Services는 [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 와 함께 사용 하 여 신뢰할 수 있는 저장소를 사용 하도록 설정할 수 있습니다. Media Services 계정을 만드는 경우 저장소 계정과 연결 해야 합니다. Media Services는 시스템 인증을 사용 하 여 해당 저장소 계정에 액세스할 수 있습니다. Media Services Media Services 계정과 저장소 계정이 동일한 구독에 있는지 확인 하 고 연결을 추가 하는 사용자가 Azure Resource Manager RBAC를 사용 하 여 저장소 계정에 액세스 하는지 확인 합니다.

## <a name="trusted-storage"></a>신뢰할 수 있는 저장소

그러나 방화벽을 사용 하 여 저장소 계정을 보호 하려면 관리 되는 id 인증을 사용 해야 합니다. 이를 통해 Media Services는 신뢰할 수 있는 저장소 액세스를 통해 방화벽이 나 VNet 제한으로 구성 된 저장소 계정에 액세스할 수 있습니다.  신뢰할 수 있는 Microsoft 서비스에 대 한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)을 참조 하세요.

## <a name="media-services-managed-identity-scenarios"></a>Media services 관리 id 시나리오

현재 관리 id를 Media Services와 함께 사용할 수 있는 두 가지 시나리오가 있습니다.

- Media Services 계정의 관리 id를 사용 하 여 저장소 계정에 액세스 합니다.

- Media Services 계정의 관리 id를 사용 하 여 고객 키에 액세스 하기 위한 Key Vault에 액세스 합니다.

다음 두 섹션에서는 두 시나리오의 차이점을 설명 합니다.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Media Services 계정의 관리 id를 사용 하 여 저장소 계정에 액세스

1. 관리 id를 사용 하 여 Media Services 계정을 만듭니다.
1. 소유 하 고 있는 저장소 계정에 대 한 관리 되는 id 사용자 액세스 권한을 부여 합니다.
1. 그런 다음 관리 되는 id를 사용 하 여 사용자 대신 저장소 계정에 액세스할 수 Media Services.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Media Services 계정의 관리 id를 사용 하 여 고객 키에 액세스 하기 위한 Key Vault 액세스

1. 관리 id를 사용 하 여 Media Services 계정을 만듭니다.
1. 소유 하 고 있는 Key Vault에 대 한 관리 되는 id 사용자 액세스 권한을 부여 합니다.
1. 고객 키 기반 계정 암호화를 사용 하도록 Media Services 계정을 구성 합니다.
1. Media Services는 관리 되는 id를 사용 하 여 사용자를 대신 하 여 Key Vault에 액세스 합니다.

고객 관리 키 및 Key Vault에 대 한 자세한 내용은를 [사용 하 여 사용자 고유의 키 (고객 관리 키) 가져오기](concept-use-customer-managed-keys-byok.md) 를 참조 하세요 Media Services

## <a name="tutorials"></a>자습서

이러한 자습서에는 위에서 언급 한 시나리오가 모두 포함 됩니다.

- [Azure Portal를 사용 하 여 고객 관리 키 또는 BYOK를 Media Services 사용 합니다.](tutorial-byok-portal.md)
- [Media Services REST API에서 고객이 관리 하는 키 또는 BYOK를 사용](tutorial-byok-postman.md)합니다.

## <a name="next-steps"></a>다음 단계

사용자 및 Azure 응용 프로그램에 대해 수행할 수 있는 관리 되는 id에 대 한 자세한 내용은 [AZURE AD 관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 참조 하세요.
