---
title: Azure Digital Twins Explorer 인증 오류
description: "'인증 실패'의 원인 및 해결 Azure Digital Twins Explorer입니다."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107491337"
---
# <a name="authentication-failed"></a>인증 실패

이 문서에서는 로컬 컴퓨터에서 [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 샘플을 실행하는 동안 '인증 실패' 오류를 수신하는 원인 및 해결 단계를 설명합니다. 

## <a name="symptoms"></a>증상

Azure Digital Twins Explorer 애플리케이션을 설정하고 실행할 때 앱에서 인증을 시도하면 다음과 같은 오류 메시지가 표시됩니다.

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="‘인증 실패’라는 텍스트가 나타나는 Azure Digital Twins explorer의 오류 메시지 스크린샷. 앱을 로컬로 실행하는 경우 호스트 컴퓨터에서 Azure에 로그인했는지, 또는 명령 프롬프트에서 'az login'을 실행하거나 Visual Studio 또는 VS Code에 로그인하거나 환경 변수를 설정하여 로그인했는지 확인하세요. 자세한 내용은 추가 정보를 참조하거나 Azure.Identity 설명서에서 DefaultAzureCredential을 검색하세요. 클라우드에서 호스트되는 adt-explorer를 실행하는 경우 호스팅 Azure Function에 시스템 할당 관리 ID가 설정되어 있는지 확인하세요. 자세한 내용은 추가 정보를 참조하세요.":::

## <a name="causes"></a>원인

### <a name="cause-1"></a>원인 1

Azure Digital Twins Explorer 애플리케이션은 로컬 환경 내에서 자격 증명을 검색하는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)(`Azure.Identity` 라이브러리의 일부)을 사용합니다.

오류 텍스트에 표시된 대로 이 오류는 `DefaultAzureCredential`가 선택할 로컬 자격 증명을 제공하지 않은 경우 발생할 수 있습니다.

Azure Digital Twins Explorer에서 로컬 자격 증명을 사용하는 방법에 대한 자세한 내용은 Azure Digital Twins *빠른 시작: 샘플 시나리오 탐색* 의 [*로컬 Azure 자격 증명 설정*](quickstart-adt-explorer.md#set-up-local-azure-credentials) 섹션을 참조하세요.

### <a name="cause-2"></a>원인 2

Azure 계정에 Azure Digital Twins 인스턴스에 필요한 Azure RBAC(역할 기반 액세스 제어) 권한이 설정되어 있지 않은 경우에도 이 오류가 발생할 수 있습니다. 인스턴스의 데이터에 액세스하려면 각각 읽거나 관리하려는 인스턴스에 대한 **Azure Digital Twins 데이터 판독기** 또는 **Azure Digital Twins 데이터 소유자** 역할이 있어야 합니다. 

Azure Digital Twins의 보안 및 역할에 대한 자세한 내용은 [*개념: Azure Digital twins 솔루션의 보안*](concepts-security.md)을 참조하세요.

## <a name="solutions"></a>솔루션

### <a name="solution-1"></a>솔루션 1

먼저 애플리케이션에 필요한 자격 증명을 제공했는지 확인합니다.

#### <a name="provide-local-credentials"></a>로컬 자격 증명 제공

`DefaultAzureCredential`은 로컬 Azure 로그인의 정보를 사용하여 서비스에 인증합니다. 로컬 [Azure CLI](/cli/azure/install-azure-cli) 창이나 Visual Studio 또는 Visual Studio Code에서 Azure 계정에 로그인하여 Azure 자격 증명을 제공할 수 있습니다.

[DefaultAzureCredential에 대한 Azure ID 설명서](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)에서 `DefaultAzureCredential`이 승인하는 자격 증명 형식 및 해당 형식이 시도되는 순서를 볼 수 있습니다.

올바른 Azure 계정에 로컬로 로그인하고도 문제가 해결되지 않은 경우 다음 솔루션으로 계속 진행합니다.

### <a name="solution-2"></a>솔루션 2

데이터를 읽으려고 하는 경우 Azure 사용자가 Azure Digital twins 인스턴스에서 **Azure Digital Twins 데이터 판독기** 역할을 가지고 있는지 확인하고, 데이터를 관리하려는 경우 인스턴스에서 **Azure Digital Twins 데이터 소유자** 역할이 맞는지 확인합니다.

이 역할은...
* 미리 보기 중에 이 역할의 이전 이름인 *Azure Digital Twins 소유자(미리 보기)* 와는 다릅니다(역할은 동일하지만 이름이 변경됨).
* 전체 Azure 구독에서의 *소유자* 역할과는 다릅니다. *Azure Digital Twins 데이터 소유자* 는 Azure Digital Twins 내에서의 역할이며, 이 개별 Azure Digital Twins 인스턴스로 범위가 지정됩니다.
* Azure Digital Twins의 *소유자* 역할과는 다릅니다. 이것들은 서로 다른 두 가지 Azure Digital Twins 관리 역할이며, *Azure Digital Twins 데이터 소유자* 는 관리에 사용해야 하는 역할입니다.

 이 역할이 없으면 이를 설정하여 문제를 해결합니다.

#### <a name="check-current-setup"></a>현재 설정 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>문제 해결 

이 역할 할당이 없는 경우 **Azure 구독** 에 소유자 역할이 있는 사용자는 다음 명령을 실행하여 Azure 사용자에게 **Azure Digital Twins 인스턴스** 에서 적절한 역할을 제공해야 합니다. 

구독에 대한 소유자인 경우 이 명령을 직접 실행할 수 있습니다. 그렇지 않은 경우 소유자에게 문의하여 대신 이 명령을 실행하도록 합니다. 역할 이름은 편집 액세스를 위한 **Azure Digital Twins 데이터 소유자** 이거나 읽기 액세스를 위한 **Azure Digital Twins 데이터 판독기** 입니다.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

이 역할 요구 사항과 할당 프로세스에 대한 자세한 내용은 *방법: 인스턴스 및 인증 설정(CLI 또는 포털)* 의 [*사용자 액세스 권한 설정* 섹션](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)을 참조하세요.

## <a name="next-steps"></a>다음 단계

새 Azure Digital Twins 인스턴스를 만들고 인증하는 방법에 대한 설치 단계를 읽어보세요.
* [*방법: 인스턴스 및 인증 설정(CLI)* ](how-to-set-up-instance-cli.md)

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 솔루션 보안*](concepts-security.md)
