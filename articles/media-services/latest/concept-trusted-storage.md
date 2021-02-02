---
title: Media Services에 대 한 신뢰할 수 있는 저장소
description: 관리 되는 Id 인증을 사용 하면 Media Services는 신뢰할 수 있는 저장소 액세스를 통해 방화벽이 나 VNet 제한으로 구성 된 저장소 계정에 액세스할 수 있습니다.
keywords: 신뢰할 수 있는 저장소, 관리 되는 id
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408519"
---
# <a name="trusted-storage-for-media-services"></a>Media Services에 대 한 신뢰할 수 있는 저장소

Media Services 계정을 만드는 경우 저장소 계정과 연결 해야 합니다. Media Services는 시스템 인증을 사용 하 여 해당 저장소 계정에 액세스할 수 있습니다. Media Services Media Services 계정과 저장소 계정이 동일한 구독에 있는지 확인 하 고 연결을 추가 하는 사용자가 Azure Resource Manager RBAC를 사용 하 여 저장소 계정에 액세스 하는지 확인 합니다.

그러나 방화벽을 사용 하 여 저장소 계정을 보호 하 고 신뢰할 수 있는 저장소를 사용 하도록 설정 하려는 경우 [관리 되는 id](concept-managed-identities.md) 인증을 사용 해야 합니다. 이를 통해 Media Services는 신뢰할 수 있는 저장소 액세스를 통해 방화벽이 나 VNet 제한으로 구성 된 저장소 계정에 액세스할 수 있습니다.

관리 Id를 사용 하 여 신뢰할 수 있는 저장소를 만드는 방법을 이해 하려면 [관리 되는 id 및 Media Services](concept-managed-identities.md)을 참조 하세요.

고객 관리 키 및 Key Vault에 대 한 자세한 내용은를 [사용 하 여 사용자 고유의 키 (고객 관리 키) 가져오기](concept-use-customer-managed-keys-byok.md) 를 참조 하세요 Media Services

신뢰할 수 있는 Microsoft 서비스에 대 한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../../storage/common/storage-network-security.md#trusted-microsoft-services)을 참조 하세요.

## <a name="tutorials"></a>자습서

이러한 자습서에는 위에서 언급 한 시나리오가 모두 포함 됩니다.

- [Azure Portal을 사용하여 Media Services에서 고객 관리형 키 또는 BYOK 사용](tutorial-byok-portal.md)
- [Media Services REST API에서 고객이 관리 하는 키 또는 BYOK를 사용](tutorial-byok-postman.md)합니다.

## <a name="next-steps"></a>다음 단계

사용자 및 Azure 응용 프로그램에 대해 수행할 수 있는 관리 되는 id에 대 한 자세한 내용은 [AZURE AD 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요.