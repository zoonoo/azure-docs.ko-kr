---
author: baanders
description: Azure Digital Twins 설정에서 역할 요구 사항에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407479"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>필수 조건: 사용 권한 요구 사항

이 문서의 모든 단계를 완료 하려면 Azure 구독에서 소유자로 분류 해야 합니다. 

Cloud Shell에서 다음 명령을 실행 하 여 사용 권한 수준을 확인할 수 있습니다.

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

소유자 인 경우 `roleDefinitionName` 출력의 값은 *소유자*입니다.

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Az role 대입문 list 명령의 출력을 보여 주는 Cloud Shell 창":::

값이 *참가자* 또는 *소유자*가 아닌 다른 것으로 확인 되 면 다음 방법 중 하나를 진행할 수 있습니다.
* 구독 소유자에 게 문의 하 고 소유자에 게 문의 하 여 사용자 대신이 문서의 단계를 완료 하도록 요청 하세요.
* 구독 소유자 또는 구독에 대 한 사용자 액세스 관리자 역할을 가진 사용자에 게 연락 하 고 사용자가 직접 계속할 수 있는 권한을 부여 하도록 구독 소유자에 게 권한을 부여 하도록 요청 합니다. 이 방법이 적절 한지 여부는 조직 및 조직 내 사용자의 역할에 따라 달라 집니다.