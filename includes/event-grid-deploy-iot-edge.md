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
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844594"
---
## <a name="deploy-event-grid-iot-edge-module"></a>이벤트 그리드 IoT 에지 모듈 배포

IoT Edge 장치에 모듈을 배포하는 방법에는 여러 가지가 있으며 모두 IoT Edge의 Azure 이벤트 그리드에서 작동합니다. 이 문서에서는 Azure 포털에서 IoT 가장자리에 이벤트 그리드를 배포하는 단계를 설명합니다.

>[!NOTE]
> 이 자습서에서는 지속성 없이 이벤트 그리드 모듈을 배포합니다. 즉, 모듈을 다시 배포할 때 이 자습서에서 만든 모든 토픽 및 구독이 삭제됩니다. 지속성 설정 방법에 대한 자세한 내용은 다음 문서를 참조하십시오: [Linux에서 상태를 유지하거나 Windows에서 상태를](../articles/event-grid/edge/persist-state-linux.md) [유지합니다.](../articles/event-grid/edge/persist-state-windows.md) 프로덕션 워크로드의 경우 지속성이 있는 Event Grid 모듈을 설치하는 것이 좋습니다.

>[!IMPORTANT]
> 이 자습서에서는 이벤트 그리드 모듈이 클라이언트 인증을 해제하여 배포되고 HTTP 구독자를 허용합니다. 프로덕션 워크로드의 경우 클라이언트 인증을 사용하도록 설정한 HTTPS 요청 및 구독자만 사용하도록 설정하는 것이 좋습니다. 이벤트 그리드 모듈을 안전하게 구성하는 방법에 대한 자세한 내용은 [보안 및 인증을](../articles/event-grid/edge/security-authentication.md)참조하십시오.
 
### <a name="select-your-iot-edge-device"></a>IoT 에지 장치 선택

1. [Azure 포털에](https://portal.azure.com) 로그인
1. IoT Hub로 이동합니다.
1. **자동 장치 관리** 섹션의 메뉴에서 **IoT 가장자리를** 선택합니다. 
1. 장치 목록에서 대상 장치의 ID를 클릭합니다.
1. **모듈 설정**선택 . 페이지를 열어 두십시오. 다음 섹션의 단계를 계속합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure 포털에는 JSON 문서를 수동으로 빌드하는 대신 배포 매니페스트를 만드는 마법사가 있습니다.  **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. 배포 **모듈** 섹션에서 **추가**
1. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈을** 선택합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공합니다.

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **이름**: 이벤트 그리드 모듈
   * **이미지 URI**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
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

 1. **저장**을 클릭합니다.
 1. **다음을** 클릭하여 경로 섹션을 계속합니다.

    > [!NOTE]
    > Azure VM을 에지 장치로 사용하는 경우 인바운드 포트 규칙을 추가하여 포트 4438에서 인바운드 트래픽을 허용합니다. 규칙 추가에 대한 지침은 [VM에 대한 포트를 여는 방법을](../articles/virtual-machines/windows/nsg-quickstart-portal.md)참조하십시오.


### <a name="setup-routes"></a>설정 경로

 기본 경로를 유지하고 **다음을** 선택하여 검토 섹션으로 계속

### <a name="review-deployment"></a>배포 검토

1. 검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. $edgeAgent **및** **$edgeHub**두 모듈이 목록에 있는지 확인합니다. 이 두 개의 모듈은 IoT Edge 런타임을 구성하며 모든 배포에서 필수 기본값입니다.
1. 배포 정보를 검토한 다음 **제출**을 선택합니다.

### <a name="verify-your-deployment"></a>배포 확인

1. 배포를 제출한 후 IoT Hub의 IoT Edge 페이지로 돌아갑니다.
1. 배포를 대상으로 한 **IoT Edge 장치를** 선택하여 세부 정보를 엽니다.
1. 장치 세부 정보에서 이벤트 그리드 모듈이 **배포에 지정되고** **장치에 의해 보고됨으로**나열되어 있는지 확인합니다.

모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.