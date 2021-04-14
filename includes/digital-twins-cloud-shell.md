---
author: baanders
description: Azure Digital Twins용 파일 포함 - Cloud Shell 및 IoT 확장 설정
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: d4d9efd99a60c93dbfef2d6f45971781d71e83fb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105065"
---
열려 있는 [Azure Cloud Shell](https://shell.azure.com) 창에서 Azure Digital Twins 작업을 시작하려면 먼저 로그인하여 셸 컨텍스트를 이 세션에 대한 구독으로 설정합니다. Cloud Shell에서 다음 명령을 실행합니다.

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> 위의 명령에서 ID 대신 제품 이름을 사용할 수도 있습니다. 

Azure Digital Twins에서 이 구독을 처음 사용하는 경우 이 명령을 실행하여 Azure Digital Twins 네임스페이스에 등록합니다. (확실하지 않으면 이전에 수행한 적이 있더라도 다시 실행해도 됩니다.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

다음으로, [**Azure CLI용 Microsoft Azure IoT 확장**](/cli/azure/service-page/azure%20iot)을 Cloud Shell에 추가하여 Azure Digital Twins 및 기타 IoT 서비스와 상호 작용하는 명령을 사용하도록 설정합니다. 다음 명령을 실행하여 최신 버전의 확장이 있는지 확인합니다.

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

이제 Cloud Shell에서 Azure Digital Twins를 사용할 준비가 되었습니다.

사용 가능한 최상위 Azure Digital Twins 명령 목록을 보려면 언제든지 `az dt -h`를 실행하여 이를 확인할 수 있습니다.