---
author: baanders
description: Azure Digital Twins 설정에서 역할 요구 사항에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832354"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>필수 조건: 사용 권한 요구 사항

이 문서의 모든 단계를 완료 하려면 Azure 구독에서 소유자로 분류 해야 합니다. 

Cloud Shell에서 다음 명령을 실행 하 여 사용 권한 수준을 확인할 수 있습니다.

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> 이 명령을 실행할 때 CLI **가 graph 데이터베이스에서 사용자 또는 서비스 주체를 찾을 수**없다는 오류를 반환 하는 경우:
>
> 이 문서의 나머지 부분에서는 전자 메일 대신 사용자의 *개체 ID* 를 사용 합니다. 이는 [MSAs (개인 Microsoft 계정)](https://account.microsoft.com/account)의 사용자에 게 발생할 수 있습니다. 
>
> [Azure Active Directory 사용자의 Azure Portal 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 를 사용 하 여 사용자 계정을 선택 하 고 세부 정보를 엽니다. 사용자의 *ObjectID*를 복사 합니다.
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="' 개체 ID ' 필드에서 GUID를 강조 표시 하 Azure Portal의 사용자 페이지 보기" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> 그런 다음 사용자의 전자 메일 대신 사용자의 *개체 ID* 를 사용 하 여 역할 할당 목록 명령을 반복 합니다.

역할 할당 목록 명령을 실행 한 후 소유자 인 경우 `roleDefinitionName` 출력의 값은 *소유자*입니다.

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Az role 대입문 list 명령의 출력을 보여 주는 Cloud Shell 창":::

값이 *참가자* 또는 *소유자*가 아닌 다른 것으로 확인 되 면 다음 방법 중 하나를 진행할 수 있습니다.
* 구독 소유자에 게 문의 하 고 소유자에 게 문의 하 여 사용자 대신이 문서의 단계를 완료 하도록 요청 하세요.
* 구독 소유자 또는 구독에 대 한 사용자 액세스 관리자 역할을 가진 사용자에 게 연락 하 고 사용자가 직접 계속할 수 있는 권한을 부여 하도록 구독 소유자에 게 권한을 부여 하도록 요청 합니다. 이 방법이 적절 한지 여부는 조직 및 조직 내 사용자의 역할에 따라 달라 집니다.