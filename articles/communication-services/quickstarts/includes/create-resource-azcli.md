---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820009"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Azure Communication 리소스 만들기

Azure Communication Services 리소스를 만들려면 [Azure CLI에 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)한 후, 다음 명령을 실행합니다.

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

다음 옵션을 사용하여 Communication Services 리소스를 구성할 수 있습니다.

* 리소스 그룹
* Communication Services 리소스의 이름
* 리소스가 연결되는 지리적 위치

다음 단계에서는 리소스에 태그를 할당할 수 있습니다. 태그를 사용하여 Azure 리소스를 구성할 수 있습니다. 태그에 대한 자세한 내용은 [리소스 태그 지정 설명서](../../../azure-resource-manager/management/tag-resources.md)를 참조하세요.

## <a name="manage-your-communication-services-resource"></a>Communication Services 리소스 관리

Communication Services 리소스에 태그를 추가하려면 다음 명령을 실행합니다.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

추가 명령에 대한 자세한 내용은 [az communication](https://docs.microsoft.com/cli/azure/ext/communication/communication)을 참조하세요.