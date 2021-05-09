---
ms.openlocfilehash: 340635f273c20c9d8b42fde0c07a7aa97d9f348a
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293262"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet/).
- [Azure Az PowerShell 모듈](/powershell/azure/) 설치

## <a name="create-azure-communication-resource"></a>Azure Communication 리소스 만들기

Azure Communication Services 리소스를 만들려면 [Azure CLI에 로그인합니다](/cli/azure/authenticate-azure-cli). ```Connect-AzAccount``` 명령을 사용하고 자격 증명을 제공하여 터미널을 통해 이 작업을 수행할 수 있습니다. 다음 명령을 실행하여 리소스를 만듭니다.

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

특정 구독을 선택하려는 경우 ```--subscription``` 플래그를 지정하고 구독 ID를 제공할 수도 있습니다.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

다음 옵션을 사용하여 Communication Services 리소스를 구성할 수 있습니다.

* 리소스 그룹
* Communication Services 리소스의 이름
* 리소스가 연결되는 지리적 위치

다음 단계에서는 리소스에 태그를 할당할 수 있습니다. 태그를 사용하여 Azure 리소스를 구성할 수 있습니다. 태그에 대한 자세한 내용은 [리소스 태그 지정 설명서](../../../azure-resource-manager/management/tag-resources.md)를 참조하세요.

## <a name="manage-your-communication-services-resource"></a>Communication Services 리소스 관리

Communication Services 리소스에 태그를 추가하려면 다음 명령을 실행합니다. 특정 구독을 대상으로 지정할 수도 있습니다.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

지정된 구독의 모든 Azure Communication Services 리소스를 나열하려면 다음 명령을 사용합니다.

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

지정된 리소스에 대한 모든 정보를 나열하려면 다음 명령을 사용합니다.

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```