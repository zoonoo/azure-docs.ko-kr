---
title: 사용자 고유의 키 (고객 관리 키) 가져오기
description: Media Services를 사용 하 여 고객 관리 키 (즉, 고유한 키 가져오기)를 사용할 수 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4564e28f76aebe7f708c2b6f68903fe67bcefe26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698861"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Media Services를 사용 하 여 사용자 고유의 키 (고객 관리 키) 가져오기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

BYOK (Bring Your Own Key)는 고객이 워크 로드를 클라우드로 이동 하는 데 도움이 되는 Azure wide 이니셔티브입니다. 고객 관리 키를 통해 고객은 업계 규정 준수 규정을 준수 하 고 서비스의 테 넌 트 격리를 개선할 수 있습니다. 고객에 게 암호화 키에 대 한 제어 권한을 부여 하는 것은 Microsoft 서비스에서 불필요 한 액세스를 최소화 하 고 안심 하는 방법입니다.

## <a name="keys-and-key-management"></a>키 및 키 관리

Media Services 2020-05-01 API를 사용 하는 경우 Media Services에 고유한 키를 사용할 수 있습니다. Media Services 소유의 시스템 키로 암호화 된 모든 계정에 대해 기본 계정 키가 생성 됩니다. 사용자 고유의 키를 사용 하는 경우 계정 키는 키로 암호화 됩니다. 콘텐츠 키는 계정 키에 의해 암호화 됩니다. JobInputHttp url 및 대칭 토큰 유효성 검사 키도 암호화 됩니다.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="고객 관리 키가 시스템 관리 키를 대체 합니다.":::

Media Services는 Media Services 계정의 관리 Id를 사용 하 여 사용자가 소유한 Key Vault에서 키를 읽습니다. Media Services을 사용 하려면 Key Vault 계정과 동일한 지역에 있고 일시 삭제 및 제거 보호를 사용 하도록 설정 해야 합니다.

키는 2048, 3072 또는 4096 RSA 키 일 수 있으며 HSM 및 소프트웨어 키가 모두 지원 됩니다.

> [!NOTE]
> EC 키는 지원 되지 않습니다.

키 이름 및 키 버전을 지정 하거나 키 이름만 지정할 수 있습니다. 키 이름만 사용 하는 경우 Media Services는 최신 키 버전을 사용 합니다. 새 버전의 고객 키가 자동으로 검색 되 고 계정 키가 다시 암호화 됩니다.

> [!WARNING]
> Media Services은 고객 키에 대 한 액세스를 모니터링 합니다. 사용자 키에 액세스할 수 없는 경우 (예: 키가 삭제 되었거나 Key Vault 삭제 되었거나 액세스 권한이 제거 된 경우) Media Services는 계정을 고객 키에 액세스할 수 없는 상태로 전환 합니다 (사실상 계정을 사용 하지 않도록 설정 함). 그러나이 상태에서는 계정을 삭제할 수 있습니다. 유일 하 게 지원 되는 작업은 account GET, LIST 및 DELETE입니다. 다른 모든 요청 (인코딩, 스트리밍 등)은 계정 키에 대 한 액세스가 복원 될 때까지 실패 합니다.

## <a name="double-encryption"></a>이중 암호화

Media Services는 자동으로 이중 암호화를 지원 합니다. 미사용 데이터의 경우 첫 번째 암호화 계층은 계정 설정에 따라 고객 관리 키 또는 Microsoft 관리 키를 사용 합니다 `AccountEncryption` .  휴지 상태의 데이터에 대 한 두 번째 암호화 계층은 Microsoft에서 관리 하는 별도의 키를 사용 하 여 자동으로 제공 됩니다. 이중 암호화에 대해 자세히 알아보려면 [Azure 이중 암호화](../../security/fundamentals/double-encryption.md)를 참조 하세요.

> [!NOTE]
> Media Services 계정에서 이중 암호화가 자동으로 설정 됩니다. 그러나 저장소 계정에서 별도로 고객 관리 키 및 이중 암호화를 구성 해야 합니다. [을 참조 하십시오.](../../storage/common/storage-service-encryption.md)

## <a name="tutorials"></a>자습서

- [Azure Portal을 사용하여 Media Services에서 고객 관리형 키 또는 BYOK 사용](tutorial-byok-portal.md)
- [Media Services REST API에서 고객이 관리 하는 키 또는 BYOK를 사용](tutorial-byok-postman.md)합니다.

## <a name="next-steps"></a>다음 단계

[Media Services 동적 암호화를 사용 하 여 콘텐츠 보호](content-protection-overview.md)