---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 08f23f483e8dc42a697a7a9196d35503b52f2af3
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383882"
---
이 자습서의 필수 구성 요소는 다음과 같습니다.

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

    > [!NOTE]    
    > [기여자](../../../../../role-based-access-control/built-in-roles.md#contributor) 역할 및 [사용자 액세스 관리자](../../../../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할에 모두 액세스할 수 있는 Azure 구독이 필요합니다. 이러한 권한이 없는 경우 계정 관리자에게 적절한 권한을 부여해 달라고 요청합니다.
- 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  
   > [!TIP]
   > Azure IoT Tools를 설치할 때 Docker를 설치하라는 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 됩니다.
   * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
* [Python 3](https://www.python.org/downloads/)(3.6.9 이상), [Pip 3](https://pip.pypa.io/en/stable/installing/) 및 필요에 따라 [venv](https://docs.python.org/3/library/venv.html). 

> [!Important]
> 이 Custom Vision 모듈은 **Intel x86 및 amd64** 아키텍처만 지원합니다. 계속하기 전에 에지 디바이스의 아키텍처를 확인하세요.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]


