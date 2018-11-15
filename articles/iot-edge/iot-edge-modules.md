---
title: Azure IoT Edge 모듈 이해 | Microsoft Docs
description: Azure IoT Edge 모듈 및 구성 방법에 대해 알아보기
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3d76f5931e3636f19c2030c4090116a0791db819
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567335"
---
# <a name="understand-azure-iot-edge-modules"></a>Azure IoT Edge 모듈 이해

Azure IoT Edge를 사용하면 에지에서 비즈니스 논리를 *모듈* 형태로 배포하고 관리할 수 있습니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 계산의 최소 단위이며 Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션별 코드를 포함할 수 있습니다. 모듈을 개발, 배포 및 유지 관리하는 방법을 이해하려면 모듈을 구성하는 4가지 개념적 요소를 고려하는 것이 좋습니다.

* **모듈 이미지**는 모듈을 정의하는 소프트웨어가 포함된 패키지입니다.
* **모듈 인스턴스**는 IoT Edge 장치에서 모듈 이미지를 실행하는 특정 계산 단위입니다. 모듈 인스턴스는 IoT Edge 런타임에 의해 시작됩니다.
* **모듈 ID**는 각 모듈 인스턴스에 연결된 IoT Hub에 저장된 정보(보안 자격 증명 포함)입니다.
* **모듈 쌍**은 IoT Hub에 저장된 JSON 문서로, 메타데이터, 구성 및 조건 등 모듈 인스턴스의 상태 정보를 포함합니다. 

## <a name="module-images-and-instances"></a>모듈 이미지 및 인스턴스

IoT Edge 모듈 이미지에는 IoT Edge 런타임의 관리, 보안 및 통신 기능을 활용하는 응용 프로그램이 포함되어 있습니다. 자신만의 모듈 이미지를 개발하거나 Azure Stream Analytics와 같이 지원되는 Azure 서비스에서 모듈 이미지를 내보낼 수 있습니다.
이미지는 클라우드에 존재하며 다른 솔루션으로 업데이트, 변경 및 배포할 수 있습니다. 예를 들어 기계 학습을 사용하여 생산 라인의 결과를 예측하는 모듈은 컴퓨터 시각을 사용하여 드론을 제어하는 모듈과는 별도의 이미지로 존재합니다. 

모듈 이미지가 장치에 배포되고 IoT Edge 런타임에서 시작될 때마다 해당 모듈의 새 인스턴스가 만들어집니다. 전 세계의 다른 지역에 있는 두 장치가 동일한 모듈 이미지를 사용할 수 있습니다. 그러나 각 모듈이 장치에서 시작될 때 고유한 모듈 인스턴스를 사용하게 됩니다. 

![클라우드의 모듈 이미지 - 장치의 모듈 인스턴스](./media/iot-edge-modules/image_instance.png)

구현 시 모듈 이미지는 컨테이너 이미지로 리포지토리에 존재하며 모듈 인스턴스는 장치의 컨테이너입니다. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>모듈 ID

새로운 모듈 인스턴스가 IoT Edge 런타임에 의해 생성되면 인스턴스가 해당 모듈 ID와 연관됩니다. 모듈 ID는 IoT Hub에 저장되며 특정 모듈 인스턴스의 모든 로컬 및 클라우드 통신에 대한 주소 지정 및 보안 범위로 사용됩니다.
모듈 인스턴스와 연관된 ID는 인스턴스가 실행 중인 장치의 ID와 솔루션의 해당 모듈에 제공한 이름에 따라 다릅니다. 예를 들어 Azure Stream Analytics를 사용하는 `insight` 모듈을 호출하여 `Hannover01`이라는 장치에 배포하면 IoT Edge 런타임에서 `/devices/Hannover01/modules/insight`이라는 해당 모듈 ID를 만듭니다.

동일한 장치에 하나의 모듈 이미지를 여러 번 배포해야 하는 경우 동일한 이미지를 다른 이름으로 여러 번 배포할 수 있습니다.

![모듈 ID가 고유한 경우](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>모듈 쌍

각 모듈 인스턴스에는 모듈 인스턴스를 구성하는 데 사용할 수 있는 해당 모듈 쌍이 있습니다. 인스턴스와 쌍은 모듈 ID를 통해 서로 연결됩니다. 

모듈 쌍은 모듈 정보와 구성 속성을 저장하는 JSON 문서입니다. 이 개념은 IoT Hub의 [장치 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 개념과 유사합니다. 모듈 쌍의 구조는 장치 쌍과 정확하게 같습니다. 두 유형의 쌍과 상호 작용하는 데 사용되는 API도 같습니다. 이 둘의 유일한 차이점은 클라이언트 SDK를 인스턴스화하는 데 사용되는 ID입니다. 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>오프라인 기능

Azure IoT Edge는 IoT Edge 장치에서 오프라인 작업을 지원합니다. 현재는 이러한 기능이 제한됩니다. 

IoT Edge 모듈은 다음 요구 사항이 충족되는 한 확장된 기간 동안 오프라인일 수 있습니다. 

* **메시지 TTL(Time to Live)이 만료되지 않았습니다**. 메시지 TTL에 대한 기본값은 2시간이지만 IoT Edge 허브 설정의 저장소 및 착신 구성에서 높거나 낮게 변경될 수 있습니다. 
* **모듈은 오프라인일 때 IoT Edge 허브로 재인증할 필요가 없습니다**. 모듈은 IoT 허브와 활성 연결이 있는 Edge 허브로만 인증될 수 있습니다. 어떠한 이유로 다시 시작된 경우 모듈은 다시 인증되어야 합니다. 모듈은 해당 SAS 토큰이 만료된 후에도 Edge 허브에 메시지를 여전히 보낼 수 있습니다. 연결이 다시 시작되면 Edge 허브는 모듈에서 새 토큰을 요청하고 IoT 허브로 유효성을 검사합니다. 성공한 경우 Edge 허브는 모듈의 토큰이 만료된 동안 전송된 메시지이더라도 저장한 모듈 메시지를 전달합니다. 
* **오프라인 동안 메시지를 전송한 모듈은 연결이 다시 시작될 때 여전히 작동 중입니다**. IoT Hub에 재연결 시 Edge 허브는 모듈 메시지를 전달하기 전에 새 모듈 토큰의 유효성을 검사해야 합니다(이전 토큰이 만료된 경우). 모듈에서 새 토큰을 제공할 수 없는 경우 Edge 허브는 모듈의 저장된 메시지에서 역할을 수행할 수 없습니다. 
* **Edge 허브에는 메시지를 저장할 디스크 공간이 있습니다**. 기본적으로 메시지는 Edge 허브 컨테이너의 파일 시스템에 저장됩니다. 메시지를 대신 저장할 탑재된 볼륨을 지정하는 구성 옵션이 있습니다. 두 경우에서 IoT Hub에 지연된 전송에 대한 메시지를 저장할 수 있는 공간이 있어야 합니다.  

추가 오프라인 기능은 공개 미리 보기로 제공됩니다. 자세한 내용은 [IoT Edge 장치, 모듈 및 하위 장치용 확장 오프라인 기능 이해](offline-capabilities.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
 - [IoT Edge 모듈을 개발하기 위한 요구 사항 및 도구 이해](module-development.md)
 - [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)

<!-- Images -->
[2]: ./media/iot-edge-modules/identity.png
