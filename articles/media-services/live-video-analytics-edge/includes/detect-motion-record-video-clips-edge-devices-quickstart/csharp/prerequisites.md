---
ms.openlocfilehash: d3a65122772a7cb89de6d3f7f27c2f49d3f74c98
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633383"
---
* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  > [!NOTE]
  > 서비스 주체를 만들 수 있는 권한이 있는 Azure 구독이 필요합니다(**소유자 역할** 이 이를 제공함). 적절한 권한이 없는 경우 계정 관리자에게 문의하여 적절한 권한을 부여하세요. 
* 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
        > [!TIP]
        > Azure IoT Tools를 설치할 때 Docker를 설치하라는 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 됩니다.
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [동작 감지 및 이벤트 내보내기](../../../detect-motion-emit-events-quickstart.md) 빠른 시작을 아직 완료하지 않았으면 다음 단계를 수행합니다.
     1. [Azure 리소스 설정](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [개발 환경 설정](../../../detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [IoT Edge 배포 매니페스트 생성 및 배포](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [이벤트 모니터링 준비](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> 만든 Azure 리소스와 관련된 문제가 발생하는 경우 **[문제 해결 가이드](../../../troubleshoot-how-to.md#common-error-resolutions)** 를 참조하여 일반적으로 발생하는 문제를 해결하세요.