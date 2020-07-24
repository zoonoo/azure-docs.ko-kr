---
title: 인스턴스 및 인증 설정 (수동)
titleSuffix: Azure Digital Twins
description: 적절 한 인증을 포함 하 여 Azure Digital Twins 서비스의 인스턴스를 설정 하는 방법을 참조 하세요. 수동 버전.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8d0e965360caab704bcf6c8f7d29e7bba421207e
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125890"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-manual"></a>Azure Digital Twins 인스턴스 및 인증 설정 (수동)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정 하는 등 **새 Azure Digital Twins 인스턴스를 설정**하는 단계에 대해 설명 합니다. 이 문서를 완료 한 후에는에 대 한 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스를 사용할 수 있습니다.

이 문서의 버전에서는 이러한 단계를 하나씩 수동으로 진행 합니다. 배포 스크립트 샘플을 사용 하 여 자동화 된 설치를 실행 하려면이 문서의 스크립팅된 버전 [*(방법: 인스턴스 및 인증 설정 (스크립팅된))*](how-to-set-up-instance-scripted.md)을 참조 하세요.

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

## <a name="set-up-cloud-shell-session"></a>Cloud Shell 세션 설정
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

이 섹션에서는 Cloud Shell 명령을 사용 하 여 **Azure Digital Twins의 새 인스턴스를 만듭니다** . 다음을 제공 해야 합니다.
* 배포할 리소스 그룹입니다. 기존 리소스 그룹이 아직 없는 경우 다음 명령을 사용 하 여 지금 만들 수 있습니다.
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 배포에 대 한 지역입니다. Azure Digital Twins를 지 원하는 지역을 확인 하려면 [*지역별 제공 되는 azure 제품*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 방문 하세요.
* 인스턴스의 이름입니다. 새 인스턴스의 이름은 지역 내에서 고유 해야 합니다. 즉, 해당 지역의 다른 Azure 디지털 Twins 인스턴스에서 이미 선택한 이름을 사용 하 고 있는 경우 다른 이름을 선택 하 라는 메시지가 표시 됩니다.

다음 명령에서 이러한 값을 사용 하 여 인스턴스를 만듭니다.

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success"></a>성공 확인

인스턴스가 성공적으로 만들어지면 Cloud Shell 결과가 다음과 같이 표시 되어 사용자가 만든 리소스에 대 한 정보를 출력 합니다.

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="리소스 그룹 및 Azure Digital Twins 인스턴스를 성공적으로 만든 명령 창":::

출력에서 Azure Digital Twins 인스턴스의 *호스트*이름, *이름*및 *resourceGroup* 을 확인 합니다. 이러한 모든 중요 한 값은 Azure Digital Twins 인스턴스 작업을 계속 하 여 인증 및 관련 Azure 리소스를 설정 하는 데 필요할 수 있습니다.

> [!TIP]
> 을 실행 하 여 언제 든 지 인스턴스의 모든 속성과 함께 이러한 속성을 볼 수 있습니다 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 그런 다음 적절 한 Azure 사용자에 게 관리 권한을 부여 합니다.

## <a name="set-up-your-users-access-permissions"></a>사용자의 액세스 권한 설정

Azure Digital Twins는 RBAC (역할 기반 액세스 제어)에 대 한 [Azure Active Directory (AZURE AD)](../active-directory/fundamentals/active-directory-whatis.md) 를 사용 합니다. 즉, 사용자가 Azure Digital Twins 인스턴스에 대 한 데이터 평면 호출을 수행할 수 있으려면 먼저 해당 사용자에 게 권한을 부여 하는 역할이 할당 되어야 합니다.

Azure Digital Twins의 경우이 역할은 _**Azure 디지털 쌍 소유자 (미리 보기)**_ 입니다. 역할 및 보안에 대 한 자세한 내용은 [*개념: Azure Digital Twins 솔루션의 보안*](concepts-security.md)을 참조 하세요.

이 섹션에서는 azure 구독의 azure AD 테 넌 트와 연결 된 전자 메일을 통해 Azure Digital Twins 인스턴스에서 사용자에 대 한 역할 할당을 만드는 방법을 보여 줍니다. Azure 구독에 대 한 사용자의 역할 및 권한에 따라이를 직접 설정 하거나 Azure Digital Twins 인스턴스를 관리 하는 다른 사용자를 대신 하 여 설정 합니다.

### <a name="assign-the-role"></a>역할 할당

사용자에 게 Azure Digital Twins 인스턴스를 관리할 수 있는 권한을 부여 하려면 인스턴스 내에서 _**Azure Digital Twins 소유자 (미리 보기)**_ 역할을 할당 해야 합니다. 

이 역할은와 다릅니다.
* 전체 Azure 구독에 대 한 *소유자* 역할입니다. *Azure 디지털 쌍 소유자 (미리 보기)* 는 Azure Digital twins 내에서 역할 이며이 개별 Azure 디지털 쌍 인스턴스로 범위가 지정 됩니다.
* Azure Digital Twins의 *소유자* 역할. 이는 두 가지 서로 다른 Azure 디지털 쌍 관리 역할이 며 *Azure Digital Twins 소유자 (미리 보기)* 는 미리 보기 중에 관리에 사용 해야 하는 역할입니다.

다음 명령을 사용 하 여 역할을 할당 합니다 (Azure 구독의 소유자가 실행 해야 함).

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

이 명령의 결과는 생성 된 역할 할당에 대 한 출력 된 정보입니다.

> [!TIP]
> 대신 *400: BadRequest* 오류가 발생 하는 경우 다음 명령을 실행 하 여 사용자의 *ObjectID* 를 가져옵니다.
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> 그런 다음 사용자의 전자 메일 대신 사용자의 *개체 ID* 를 사용 하 여 역할 할당 명령을 반복 합니다.

### <a name="verify-success"></a>성공 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되 고 관리 권한이 할당 됩니다. 다음으로 클라이언트 앱에 액세스할 수 있는 권한을 설정 합니다.

## <a name="set-up-access-permissions-for-client-applications"></a>클라이언트 응용 프로그램에 대 한 액세스 권한 설정

Azure Digital Twins 인스턴스를 설정 하 고 나면 사용자가 만든 클라이언트 응용 프로그램을 통해 해당 인스턴스와 상호 작용 하는 것이 일반적입니다. 이렇게 하려면 클라이언트 앱이 Azure Digital Twins에 대해 인증할 수 있는지 확인 해야 합니다. 클라이언트 앱에서 사용할 [Azure Active Directory (AZURE AD)](../active-directory/fundamentals/active-directory-whatis.md) **앱 등록** 을 설정 하 여 수행 합니다.

이 앱 등록에서는 [Azure Digital Twins api](how-to-use-apis-sdks.md)에 대 한 액세스 권한을 구성 합니다. 나중에 클라이언트 앱이 앱 등록에 대해 인증 되 고, 그 결과 Api에 대해 구성 된 액세스 권한이 부여 됩니다.

>[!TIP]
> 구독 소유자는 모든 새 Azure Digital Twins 인스턴스에 대해 새 앱 등록을 설정 *하거나* , 한 번만 수행 하 고 구독의 모든 Azure Digital twins 인스턴스 간에 공유 되는 단일 앱 등록을 설정할 수 있습니다. 이것은 Microsoft의 고유한 테 넌 트 내에서 수행 되는 방법입니다.

### <a name="create-the-registration"></a>등록 만들기

앱 등록을 만들려면 Azure Digital Twins Api에 대 한 리소스 Id와 API에 대 한 기준 권한을 제공 해야 합니다. 작업 디렉터리에서 새 파일을 열고 다음 JSON 코드 조각을 입력 하 여 이러한 세부 정보를 구성 합니다. 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

이 파일을 *manifest.js*로 저장 합니다.

> [!NOTE] 
> "친숙 한" 사람이 인식할 수 있는 문자열을 `https://digitaltwins.azure.net` GUID 대신 Azure 디지털 Twins 리소스 앱 ID에 사용할 수 있는 몇 가지 위치가 있습니다 `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . 예를 들어이 설명서의 많은 예제에서는 MSAL 라이브러리를 사용 하 여 인증을 사용 하 고이에 대 한 문자열을 사용할 수 있습니다. 그러나 앱 등록을 만드는이 단계에서 ID의 GUID 형식은 위에 표시 된 것 처럼 필요 합니다. 

Cloud Shell 창에서 "파일 업로드/다운로드" 아이콘을 클릭 하 고 "업로드"를 선택 합니다.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="업로드 옵션 선택 항목을 보여 주는 Cloud Shell 창":::
방금 만든 *manifest.js* 로 이동 하 고 "열기"를 누릅니다.

다음 명령을 실행 하 여 앱 등록을 만듭니다 (필요에 따라 자리 표시자 바꾸기).

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

다음은 사용자가 만든 등록에 대 한 정보를 보여 주는이 명령의 출력에 대 한 발췌입니다.

:::image type="content" source="media/how-to-set-up-instance/new-app-registration.png" alt-text="새 Azure AD 앱 등록의 Cloud Shell 출력":::

### <a name="verify-success"></a>성공 확인

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

먼저, 업로드 된 *manifest.js* 의 설정이 등록에 올바르게 설정 되었는지 확인 합니다. 이렇게 하려면 메뉴 모음에서 *매니페스트* 를 선택 하 여 앱 등록의 매니페스트 코드를 확인 합니다. 코드 창의 아래쪽으로 스크롤하고에서 *manifest.js* 의 필드를 찾습니다 `requiredResourceAccess` .

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>중요 한 값 수집

그런 다음 메뉴 모음에서 *개요* 를 선택 하 여 앱 등록에 대 한 세부 정보를 확인 합니다.

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="앱 등록에 대 한 중요 한 값의 포털 보기":::

**페이지에** 표시 된 *응용 프로그램 (클라이언트) id* 및 *디렉터리 (테 넌 트) id* 를 기록해 둡니다. 이러한 값은 나중에 [Azure Digital Twins api에 대해 클라이언트 앱을 인증](how-to-authenticate-client.md)하는 데 필요 합니다. 이러한 응용 프로그램에 대 한 코드를 작성 하는 사람이 아닌 경우이 값을 사용할 사용자와 공유 해야 합니다.

### <a name="other-possible-steps-for-your-organization"></a>조직의 다른 가능한 단계

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>다음 단계

클라이언트 앱의 인증 코드를 작성 하 여 클라이언트 응용 프로그램을 인스턴스에 연결 하는 방법을 참조 하세요.
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)
