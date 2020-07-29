---
author: baanders
description: Azure Digital Twins 설정에 스타터 정보에 대 한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: dcc616dd8d30bfadb751df9ba24d71d72675428a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371460"
---
>[!NOTE]
>이러한 작업은 Azure 구독에 대 한 *소유자* 역할이 있는 사용자가 완료 하기 위한 것입니다. 일부 부분은이 상승 된 권한 없이도 완료할 수 있지만 소유자의 협력은 사용 가능한 인스턴스를 완전히 설정 해야 합니다. 이에 대 한 자세한 내용은 아래의 [*필수 조건: 필수 권한*](#prerequisites-permission-requirements) 섹션을 참조 하세요.

새 Azure Digital Twins 인스턴스에 대 한 전체 설치는 다음 세 부분으로 구성 됩니다.
1. **인스턴스 만들기**
2. **사용자의 액세스 권한 설정**: 관리 작업을 수행 하려면 azure 사용자에 게 인스턴스에 대 한 *Azure Digital twins 소유자 (미리 보기)* 역할이 있어야 합니다. 이 단계에서는 사용자에 게이 역할을 할당 하거나 (Azure 구독의 소유자 인 경우) 구독에 대 한 소유자를 가져와서 사용자에 게 할당 합니다.
3. **클라이언트 응용 프로그램에 대 한 액세스 권한 설정**: 인스턴스와 상호 작용 하는 데 사용 하는 클라이언트 응용 프로그램을 작성 하는 것이 일반적입니다. 해당 클라이언트 앱이 Azure Digital Twins에 액세스 하려면 클라이언트 응용 프로그램이 인스턴스에 인증 하는 데 사용할 [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) 에서 *앱 등록* 을 설정 해야 합니다.

계속 하려면 Azure 구독이 필요 합니다. [여기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)에서 무료로 하나를 설정할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>필수 조건: 사용 권한 요구 사항

이 문서의 모든 단계를 완료 하려면 Azure 구독에서 소유자로 분류 해야 합니다. 

Cloud Shell에서 다음 명령을 실행 하 여 사용 권한 수준을 확인할 수 있습니다.

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

소유자 인 경우 `roleDefinitionName` 출력의 값은 *소유자*입니다.

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Az role 대입문 list 명령의 출력을 보여 주는 Cloud Shell 창":::

값이 *참가자* 또는 *소유자*가 아닌 다른 것으로 확인 되 면 다음 방법 중 하나를 진행할 수 있습니다.
* 구독 소유자에 게 문의 하 고 소유자에 게 문의 하 여 사용자 대신이 문서의 단계를 완료 하도록 요청 하세요.
* 구독 소유자 또는 구독에 대 한 사용자 액세스 관리자 역할을 가진 사용자에 게 연락 하 고 사용자가 직접 계속할 수 있는 권한을 부여 하도록 구독 소유자에 게 권한을 부여 하도록 요청 합니다. 이 방법이 적절 한지 여부는 조직 및 조직 내 사용자의 역할에 따라 달라 집니다.
