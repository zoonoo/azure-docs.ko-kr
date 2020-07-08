---
title: Azure Digital Twins 인스턴스 만들기
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 서비스의 인스턴스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4cac7a3f663d9ede966b8d6e5753c48629049dcd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057486"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

이 문서에서는 새 Azure Digital Twins 인스턴스를 설정 하는 기본 단계를 안내 합니다. 여기에는 인스턴스를 만들고 인스턴스에 대 한 [AAD (Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) ) 권한을 할당 하는 작업이 포함 됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

다음으로,이 방법에 사용할 새 Azure 리소스 그룹을 만듭니다. 그런 다음 해당 리소스 그룹 내에 **Azure Digital Twins의 새 인스턴스를 만들** 수 있습니다. 

또한 인스턴스의 이름을 제공 하 고 배포에 대 한 영역을 선택 해야 합니다. Azure Digital Twins를 지 원하는 지역을 확인 하려면 [지역별 제공 되는 azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 방문 하세요.

>[!NOTE]
> 새 인스턴스의 이름은 지역 내에서 고유 해야 합니다. 즉, 해당 지역의 다른 Azure 디지털 Twins 인스턴스가 이미 선택한 이름을 사용 하 고 있는 경우 다른 이름을 선택 해야 합니다.

다음 명령을 사용 하 여 리소스 그룹 및 인스턴스를 만듭니다.

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

이러한 명령의 결과는 사용자가 만든 리소스에 대 한 정보를 출력 하는 다음과 같습니다.

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="리소스 그룹 및 Azure Digital Twins 인스턴스를 성공적으로 만든 명령 창":::

출력에서 Azure Digital Twins 인스턴스의 *호스트*이름, *이름*및 *resourceGroup* 을 확인 합니다. 이러한 모든 키 값은 인증 및 관련 Azure 리소스를 설정 하기 위해 Azure Digital Twins 인스턴스 작업을 계속할 때 필요할 수 있습니다.

> [!TIP]
> 을 실행 하 여 언제 든 지 인스턴스의 모든 속성과 함께 이러한 속성을 볼 수 있습니다 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Azure Active Directory 권한 할당

Azure Digital Twins는 RBAC (역할 기반 액세스 제어)에 대해 [AAD (Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) )를 사용 합니다. 즉, Azure Digital Twins 인스턴스에 대 한 데이터 평면 호출을 수행 하기 전에 먼저 이러한 사용 권한을 가진 역할을 할당 해야 합니다.

클라이언트 응용 프로그램에서 Azure Digital Twins를 사용 하기 위해 클라이언트 앱이 Azure Digital Twins에 대해 인증할 수 있는지도 확인 해야 합니다. 이 작업은 [방법: 클라이언트 응용 프로그램 인증](how-to-authenticate-client.md)에서 읽을 수 있는 AAD (Azure Active Directory) 앱 등록을 설정 하 여 수행 합니다.

#### <a name="assign-yourself-a-role"></a>자신에 게 역할 할당

Azure 구독의 AAD 테 넌 트와 연결 된 전자 메일을 사용 하 여 Azure Digital Twins 인스턴스에서 자신에 대 한 역할 할당을 만듭니다. 

이 작업을 수행 하려면 Azure 구독에서 소유자로 분류 해야 합니다. 명령을 실행 하 `az role assignment list --assignee <your-Azure-email>` 고 출력에서 *Roledefinitionname* 값이 *Owner*인지 확인 하 여이를 확인할 수 있습니다. 값이 *참가자* 또는 *소유자*가 아닌 다른 것으로 확인 되 면 구독 관리자에 게 문의 하 여 역할을 승격 하기 위해 구독에서 사용 권한을 부여 하세요.

구독의 소유자는 다음 명령을 사용 하 여 사용자를 Azure Digital Twins 인스턴스의 소유자 역할에 할당할 수 있습니다.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

이 명령을 실행하면 사용자가 만든 역할 할당에 대한 정보가 출력됩니다.

> [!TIP]
> 대신 *400: BadRequest* 오류가 발생 하는 경우 다음 명령을 실행 하 여 사용자의 *ObjectID* 를 가져옵니다.
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> 그런 다음 사용자의 전자 메일 대신 사용자의 *개체 ID* 를 사용 하 여 역할 할당 명령을 반복 합니다.

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

인스턴스를 사용 하도록 클라이언트 앱을 설정 하 고 인증 하는 방법을 참조 하세요.
* [방법: 클라이언트 응용 프로그램 인증](how-to-authenticate-client.md)
