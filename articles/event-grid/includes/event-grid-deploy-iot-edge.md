---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 05d436ae7ae9e92d0bbdce4d540b2d7bda34c2dd
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412724"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge 모듈 배포

IoT Edge 디바이스에 모듈을 배포하는 방법은 여러 가지이며 해당 방법은 모두 IoT Edge의 Azure Event Grid에서 작동합니다. 이 문서에서는 Azure Portal에서 IoT Edge의 Event Grid를 배포하는 단계를 설명합니다.

>[!NOTE]
> 이 자습서에서는 지속성을 사용하지 않는 Event Grid 모듈을 배포합니다. 즉, 모듈을 다시 배포하는 경우 이 자습서에서 만든 모든 토픽 및 구독은 삭제됩니다. 지속성을 설정하는 방법에 대한 자세한 내용은 [Linux에서 상태 유지](../edge/persist-state-linux.md) 또는 [Windows에서 상태 유지](../edge/persist-state-windows.md) 문서를 참조하세요. 프로덕션 워크로드의 경우 지속성을 사용하는 Event Grid 모듈을 설치하는 것이 좋습니다.

>[!IMPORTANT]
> 이 자습서에서는 클라이언트 인증이 해제된 상태에서 Event Grid 모듈이 배포되며 HTTP 구독자가 허용됩니다. 프로덕션 워크로드의 경우 클라이언트 인증을 사용하는 HTTPS 요청 및 구독자만 허용하는 것이 좋습니다. Event Grid 모듈을 안전하게 구성하는 방법에 대한 자세한 내용은 [보안 및 인증](../edge/security-authentication.md)을 참조하세요.
 
### <a name="select-your-iot-edge-device"></a>IoT Edge 디바이스 선택

1. [Azure 포털](https://portal.azure.com)
1. IoT Hub로 이동합니다.
1. **자동 디바이스 관리** 섹션의 메뉴에서 **IoT Edge** 를 선택합니다. 
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. **모듈 설정** 을 선택합니다. 이 페이지를 열린 상태로 유지합니다. 다음 섹션에서 단계를 계속 진행합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에서는 JSON 문서를 수동으로 빌드하지 않고 배포 매니페스트를 만드는 방법을 설명하는 마법사를 제공합니다.  **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 선택합니다.
1. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈** 을 선택합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공합니다.

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **이름**: eventgridmodule
   * **이미지 URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **컨테이너 만들기 옵션**:

```json
    {
      "Env": [
        "inbound__clientAuth:clientCert__enabled=false",
        "outbound__webhook__httpsOnly=false"
      ],
      "HostConfig": {
        "PortBindings": {
          "4438/tcp": [
            {
              "HostPort": "4438"
            }
          ]
        }
      }
    }
```

 1. 페이지 맨 아래에 있는 **저장**
 1. **다음** 을 클릭하여 경로 섹션에서 이어서 진행합니다.

    > [!NOTE]
    > Azure VM을 에지 디바이스로 사용하는 경우 포트 4438에서 인바운드 트래픽을 허용하는 인바운드 포트 규칙을 추가합니다. 규칙을 추가하는 방법에 대한 지침은 [VM에 포트를 여는 방법](../../virtual-machines/windows/nsg-quickstart-portal.md)을 참조하세요.


### <a name="setup-routes"></a>경로 설정

 기본 경로를 유지하고 **다음** 을 선택하여 검토 섹션에서 이어서 진행합니다.

### <a name="review-deployment"></a>배포 검토

1. 검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. 목록에 두 개의 모듈 **$edgeAgent** 및 **$edgeHub** 가 있는지 확인합니다. 이 두 개의 모듈은 IoT Edge 런타임을 구성하며 모든 배포에서 필수 기본값입니다.
1. 배포 정보를 검토한 다음 **제출** 을 선택합니다.

### <a name="verify-your-deployment"></a>배포 확인

1. 배포를 제출한 후 IoT Hub의 IoT Edge 페이지로 돌아갑니다.
1. 해당 세부 정보를 열려면 배포할 대상으로 지정한 **IoT Edge 디바이스** 를 선택합니다.
1. 디바이스 세부 정보에서 Event Grid 모듈이 **배포에 지정됨** 및 **디바이스에서 보고됨** 모두로 나열되어 있는지를 확인합니다.

모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.