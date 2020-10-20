---
title: 인스턴스 및 인증 설정(CLI)
titleSuffix: Azure Digital Twins
description: CLI를 사용 하 여 Azure Digital Twins 서비스 인스턴스를 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 081eb10166ff681990af15110829030176efa3fa
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207787"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Azure Digital Twins 인스턴스 및 인증 (CLI) 설정

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정 하는 등 **새 Azure Digital Twins 인스턴스를 설정**하는 단계에 대해 설명 합니다. 이 문서를 완료 한 후에는에 대 한 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스를 사용할 수 있습니다.

이 문서의 버전에서는 CLI를 사용 하 여 이러한 단계를 하나씩 수동으로 진행 합니다.
* Azure Portal를 사용 하 여 이러한 단계를 수동으로 진행 하려면이 문서의 포털 버전 [*(방법: 인스턴스 및 인증 설정 (포털))*](how-to-set-up-instance-portal.md)을 참조 하세요.
* 배포 스크립트 샘플을 사용 하 여 자동화 된 설치를 실행 하려면이 문서의 스크립팅된 버전 [*(방법: 인스턴스 및 인증 설정 (스크립팅된))*](how-to-set-up-instance-scripted.md)을 참조 하세요.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Cloud Shell 세션 설정
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

이 섹션에서는 Cloud Shell 명령을 사용 하 여 **Azure Digital Twins의 새 인스턴스를 만듭니다** . 다음을 제공 해야 합니다.
* 배포할 리소스 그룹입니다. 기존 리소스 그룹이 아직 없는 경우 다음 명령을 사용 하 여 지금 만들 수 있습니다.
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 배포에 대 한 지역입니다. Azure Digital Twins를 지 원하는 지역을 확인 하려면 [*지역별 제공 되는 azure 제품*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 방문 하세요.
* 인스턴스의 이름입니다. 새 인스턴스의 이름은 구독에 대 한 지역 내에서 고유 해야 합니다. 즉, 사용자가 선택한 이름을 이미 사용 하 고 있는 지역에 다른 Azure 디지털 Twins 인스턴스가 있는 경우 다른 이름을 선택 하 라는 메시지가 표시 됩니다.

다음 명령에서 이러한 값을 사용 하 여 인스턴스를 만듭니다.

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요 한 값 수집

인스턴스가 성공적으로 만들어지면 Cloud Shell 결과가 다음과 같이 표시 되어 사용자가 만든 리소스에 대 한 정보를 출력 합니다.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="리소스 그룹 및 Azure Digital Twins 인스턴스를 성공적으로 만든 명령 창":::

출력에서 Azure Digital Twins 인스턴스의 *호스트*이름, *이름*및 *resourceGroup* 을 확인 합니다. 이러한 모든 중요 한 값은 Azure Digital Twins 인스턴스 작업을 계속 하 여 인증 및 관련 Azure 리소스를 설정 하는 데 필요할 수 있습니다. 다른 사용자가 인스턴스에 대 한 프로그래밍을 수행 하는 경우 이러한 값을 공유 해야 합니다.

> [!TIP]
> 을 실행 하 여 언제 든 지 인스턴스의 모든 속성과 함께 이러한 속성을 볼 수 있습니다 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 그런 다음 적절 한 Azure 사용자에 게 관리 권한을 부여 합니다.

## <a name="set-up-user-access-permissions"></a>사용자 액세스 권한 설정

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

다음 명령을 사용 하 여 역할을 할당 합니다 (Azure 구독에 [충분 한 권한이](#prerequisites-permission-requirements) 있는 사용자가 실행 해야 함). 명령을 사용 하려면 역할을 할당 해야 하는 사용자에 대 한 Azure AD 계정의 *사용자 계정 이름을* 전달 해야 합니다. 대부분의 경우이는 Azure AD 계정의 사용자 전자 메일과 일치 합니다.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

이 명령의 결과는 생성 된 역할 할당에 대 한 출력 된 정보입니다.

> [!NOTE]
> 이 명령을 실행할 때 CLI **가 graph 데이터베이스에서 사용자 또는 서비스 주체를 찾을 수**없다는 오류를 반환 하는 경우:
>
> 대신 사용자의 *개체 ID* 를 사용 하 여 역할을 할당 합니다. 이는 [MSAs (개인 Microsoft 계정)](https://account.microsoft.com/account)의 사용자에 게 발생할 수 있습니다. 
>
> 사용자 [Azure Active Directory의 Azure Portal 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 를 사용 하 여 사용자 계정을 선택 하 고 세부 정보를 엽니다. 사용자의 *ObjectID*를 복사 합니다.
>
> :::image type="content" source="media/includes/user-id.png" alt-text="리소스 그룹 및 Azure Digital Twins 인스턴스를 성공적으로 만든 명령 창" lightbox="media/includes/user-id.png":::
>
> 그런 다음 위의 매개 변수에 대 한 사용자의 *개체 ID* 를 사용 하 여 역할 할당 목록 명령을 반복 합니다 `assignee` .

### <a name="verify-success"></a>성공 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되 고 관리 권한이 할당 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins CLI 명령을 사용 하 여 인스턴스에서 개별 REST API 호출을 테스트 합니다. 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)

또는 인증 코드를 사용 하 여 인스턴스에 클라이언트 응용 프로그램을 연결 하는 방법을 참조 하세요.
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)