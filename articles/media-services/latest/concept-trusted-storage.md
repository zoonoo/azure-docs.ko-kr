---
title: Media Services를 위한 신뢰할 수 있는 스토리지
description: 관리 ID 인증을 사용하면 Media Services가 신뢰할 수 있는 스토리지 액세스를 통해 방화벽 또는 VNet 제한으로 구성된 스토리지 계정에 액세스할 수 있습니다.
keywords: 신뢰할 수 있는 스토리지, 관리 ID
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 87f52bef07644370c268168c984a452d5ae3f9bd
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279462"
---
# <a name="trusted-storage-for-media-services"></a>Media Services를 위한 신뢰할 수 있는 스토리지

Media Services 계정을 만드는 경우 스토리지 계정과 연결해야 합니다. Media Services는 시스템 인증 또는 관리 ID 인증을 사용하여 해당 스토리지 계정에 액세스할 수 있습니다. Media Services는 Media Services 계정과 스토리지 계정이 동일한 구독에 있는지 확인하고 연결을 추가하는 사용자가 Azure Resource Manager RBAC를 사용하여 스토리지 계정에 액세스하는지 확인합니다.

>[!NOTE]
>신뢰할 수 있는 스토리지는 API에서만 사용할 수 있으며 현재 Azure Portal에서는 사용할 수 없습니다.

## <a name="trusted-storage-with-a-firewall"></a>방화벽이 있는 신뢰할 수 있는 스토리지

그러나 방화벽을 사용하여 스토리지 계정을 보호하고 신뢰할 수 있는 스토리지를 사용하도록 설정하려면 [관리 ID](concept-managed-identities.md) 인증을 사용하는 것이 좋습니다. 이를 통해 Media Services는 신뢰할 수 있는 스토리지 액세스를 통해 방화벽 또는 VNet 제한으로 구성된 스토리지 계정에 액세스할 수 있습니다.

## <a name="tutorial"></a>자습서

[Media Services 신뢰할 수 있는 스토리지](security-trusted-storage-rest-tutorial.md) 자습서에서 신뢰할 수 있는 스토리지를 사용하도록 설정하는 방법에 대해 자세히 알아볼 수 있습니다.

> [!NOTE]
> Media Services에서 스토리지 계정을 읽고 쓸 수 있으려면 AMS Managed Identity Storage Blob 데이터 기여자 액세스 권한을 부여해야 합니다.  일반 기여자 역할을 부여하면 데이터 평면에 대한 올바른 권한이 활성화되지 않기 때문에 작동하지 않습니다.

## <a name="further-reading"></a>추가 정보

관리 ID를 사용하여 신뢰할 수 있는 스토리지를 만드는 방법을 알아보려면 [관리 ID 및 Media Services](concept-managed-identities.md)를 참조하세요.

신뢰할 수 있는 Microsoft Services에 대한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../../storage/common/storage-network-security.md#trusted-microsoft-services)을 참조하세요.

## <a name="next-steps"></a>다음 단계

관리 ID가 사용자 및 Azure 애플리케이션에 대해 수행할 수 있는 작업에 대한 자세한 내용은 [Azure AD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
