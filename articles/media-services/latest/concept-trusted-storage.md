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
ms.openlocfilehash: e8d21e57f9a844b3cc0538f4805780829a1350f4
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428591"
---
# <a name="trusted-storage-for-media-services"></a>Media Services에 대 한 신뢰할 수 있는 저장소

Media Services 계정을 만드는 경우 저장소 계정과 연결 해야 합니다. 시스템 인증 또는 관리 Id 인증을 사용 하 여 해당 저장소 계정에 액세스할 수 Media Services. Media Services Media Services 계정과 저장소 계정이 동일한 구독에 있는지 확인 하 고 연결을 추가 하는 사용자가 Azure Resource Manager RBAC를 사용 하 여 저장소 계정에 액세스 하는지 확인 합니다.

## <a name="trusted-storage-with-a-firewall"></a>방화벽이 있는 신뢰할 수 있는 저장소

그러나 방화벽을 사용 하 여 저장소 계정을 보호 하 고 신뢰할 수 있는 저장소를 사용 하려는 경우에는 [관리 되는 id](concept-managed-identities.md) 인증이 기본 설정 된 옵션입니다. 이를 통해 Media Services는 신뢰할 수 있는 저장소 액세스를 통해 방화벽이 나 VNet 제한으로 구성 된 저장소 계정에 액세스할 수 있습니다. 이를 통해 Media Services는 신뢰할 수 있는 저장소 액세스를 통해 방화벽이 나 VNet 제한으로 구성 된 저장소 계정에 액세스할 수 있습니다.

> [!NOTE]
> Media Services에서 저장소 계정에 대 한 읽기 및 쓰기를 위해 AMS 관리 Id 저장소 Blob 데이터 참가자 액세스 권한을 부여 해야 합니다.  일반 참가자 역할을 부여 하는 것은 데이터 평면에 대 한 올바른 권한을 사용 하지 않으므로 작동 하지 않습니다.

## <a name="further-reading"></a>추가 참고 자료

관리 Id를 사용 하 여 신뢰할 수 있는 저장소를 만드는 방법을 이해 하려면 [관리 되는 id 및 Media Services](concept-managed-identities.md)을 참조 하세요.

신뢰할 수 있는 Microsoft 서비스에 대 한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../../storage/common/storage-network-security.md#trusted-microsoft-services)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

사용자 및 Azure 응용 프로그램에 대해 수행할 수 있는 관리 되는 id에 대 한 자세한 내용은 [AZURE AD 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요.
