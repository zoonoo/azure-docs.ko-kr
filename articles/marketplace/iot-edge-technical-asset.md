---
title: Azure Marketplace에서 IoT Edge 모듈 기술 자산 준비
description: Azure Marketplace에서 IoT Edge 모듈 기술 자산을 준비합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 8ab847eee55eb9f2fc4d0d4ee7de1fa9eef3bcf0
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294304"
---
# <a name="prepare-iot-edge-module-technical-assets"></a>IoT Edge 모듈 기술 자산 준비

이 문서에서는 IoT(사물 인터넷) Edge 모듈 기술 자산이 Azure Marketplace에 게시되기 전에 충족해야 하는 요구 사항을 설명합니다.

## <a name="get-started"></a>시작하기

IoT Edge 모듈은 IoT Edge 디바이스에서 실행하는 Docker 호환 컨테이너입니다.

- IoT Edge 모듈에 대한 자세한 내용은 [Azure IoT Edge 모듈 이해](../iot-edge/iot-edge-modules.md)를 참조하세요.
- IoT Edge 모듈 개발을 시작하려면 [자체 IoT Edge 모듈 개발](../iot-edge/module-development.md)을 참조하세요.

## <a name="technical-requirements"></a>기술 요구 사항

IoT Edge 모듈은 Azure Marketplace에 인증하고 게시하려면 다음 기술 요구 사항을 충족해야 합니다.

### <a name="platform-support"></a>플랫폼 지원

IoT Edge 모듈은 다음 플랫폼 옵션 중 하나를 지원해야 합니다.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge에서 지원되는 계층 1 플랫폼

모듈은 IoT Edge에서 지원하는 모든 계층 1 플랫폼을 지원해야 합니다([Azure IoT Edge 지원](../iot-edge/support.md)에 기록됨). 더 나은 고객 경험을 제공하기 때문에 이 옵션을 권장합니다. 이 기준을 충족하는 모듈이 소개됩니다. 이 플랫폼 옵션을 사용하는 모듈은 다음을 수행해야 합니다.

- [GitHub 매니페스트 도구](https://github.com/estesp/manifest-tool)로 빌드된 매니페스트 태그인 최신 태그와 버전 태그(예: 1.0.1)를 제공합니다.

- [파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace)의 제품 목록 탭을 사용하여 **유용한 링크** 섹션 아래에 있는 링크를 [Azure IoT Edge 인증 디바이스 카탈로그](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible)에 추가합니다.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge에서 지원되는 계층 1 플랫폼의 하위 집합

모듈은 [Azure IoT Edge 지원](../iot-edge/support.md)에 기록된 대로 IoT Edge에서 지원하는 계층 1 플랫폼의 하위 집합(최소 하나)을 지원해야 합니다. 이 플랫폼 옵션을 사용하는 모듈은 다음을 수행해야 합니다.

- 둘 이상의 플랫폼이 지원되는 경우 GitHub [manifest-tool](https://github.com/estesp/manifest-tool)로 빌드된 매니페스트 태그인 최신 태그 및 버전 태그(예: 1.0.1)를 제공합니다. 하나의 플랫폼이 지원되는 경우에만 매니페스트 태그는 선택 사항입니다.
- [파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace)의 제품 목록 탭을 사용하여 **유용한 링크** 섹션 아래에 있는 링크를 [Azure IoT Edge 인증 디바이스 카탈로그](https://devicecatalog.azure.com/)에 있는 하나 이상의 IoT Edge 디바이스에 추가합니다.

:::image type="content" source="media/iot-edge/iot-edge-module-technical-assets-offer-listing.png" alt-text="파트너 센터 내의 제품 목록 섹션 이미지입니다.":::

### <a name="device-dimensions"></a>디바이스 차원

대상 IoT Edge 디바이스의 IoT Edge 모듈 차원(예: CPU, RAM, 스토리지 및 GPU)은 다음 요구 사항을 충족해야 합니다.

- 모듈은 [Azure IoT Edge 인증 디바이스 카탈로그](https://devicecatalog.azure.com/)에 있는 하나 이상의 IoT Edge 디바이스와 작동해야 합니다.

- 최소 하드웨어 요구 사항은 제품 설명의 마지막 단락([파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace)의 제품 목록 탭 아래)에 문서화되어야 합니다. 필요에 따라 크게 다를 경우 권장되는 하드웨어 요구 사항을 나열할 수도 있습니다. 예를 들어 제품 설명의 끝에 다음 섹션을 추가합니다.

이 HTML 텍스트를 복사하거나 편집 창에서 해당하는 서식 있는 텍스트 함수를 사용합니다.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>구성

모듈에는 IoT Edge 디바이스에 최대한 간단하게 배포하는 기본 구성 설정이 포함되어야 합니다. 이 정보는 [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165290)의 계획에 대한 **기술 구성** 페이지에서 제공할 수 있습니다. 컨테이너에는 Edge 허브 및 IoT Hub와 통신할 수 있도록 IoT Edge Module SDK가 포함될 수도 있습니다.

#### <a name="default-configuration"></a>기본 구성

IoT Edge 모듈은 [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165290)의 계획에 대한 **기술 구성** 페이지에 제공된 기본 설정으로 시작할 수 있어야 합니다. 다음 기본 설정을 사용할 수 있습니다.

- 기본 **경로**
- 기본 **모듈 쌍 desired 속성**
- 기본 **환경 변수**
- 기본 **컨테이너 만들기 옵션**.

기본값에 필요한 매개 변수가 적합하지 않은 시나리오에서(예: 고객 서버의 IP 주소) 매개 변수를 기본값으로 추가합니다. 이 값은 대문자이며 대괄호로 묶여 있습니다. 이 예제의 경우 다음 기본 환경 변수를 설정할 수 있습니다.

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>구성 문서

IoT Edge 모듈의 모든 구성 설정은 명확하게 문서화되어야 합니다. 예를 들어 경로, 원하는 쌍 속성, 환경 변수, createOptions 등을 사용하는 방법을 문서화해야 합니다. 설명서에 대한 링크를 제공하거나 제품 또는 계획 설명의 일부로 만들어야 합니다. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165290)의 **제품 목록** 및 **계획 목록** 페이지에 이 정보를 제공할 수 있습니다.

#### <a name="tags-and-versioning"></a>태그 및 버전 관리

고객은 모듈을 쉽게 배포하고 마켓플레이스에서 업데이트를 자동으로 가져올 수 있어야 합니다(개발자 시나리오에서). 테스트한 정확한 버전을 사용 및 고정할 수도 있어야 합니다(프로덕션 시나리오에서).

이러한 고객의 기대를 충족하고 Marketplace에 게시하려면 IoT Edge 모듈이 다음 요구 사항을 충족해야 합니다.

- 지원되는 모든 플랫폼에서 최신 버전을 가리키는 매니페스트 최신 태그를 포함합니다.
- 버전 태그를 X. Y. Z 형식으로 만듭니다. 여기서 X, Y 및 Z는 정수입니다.
- 지원되는 모든 플랫폼의 특정 버전을 가리키는 1.0.1과 같은 "버전" 태그를 포함합니다.
- 1\.0.1과 같은 "버전" 태그는 변경해서는 안 되므로 업데이트하지 마세요.

> [!NOTE]
> 선택적으로 버전 관리에는 2.0 및 1.0과 같은 "롤링 버전" 태그가 포함될 수 있습니다. 이는 동시에 여러 주 버전 유지 관리를 지원합니다.

### <a name="telemetry"></a>원격 분석

IoT 모듈 SDK를 사용하는 모듈은 원격 분석을 위해 PublisherId.OfferId.SkuId에 고유 모듈 식별자를 설정해야 합니다. 고유 식별자를 통해 Azure Marketplace에서 실행되는 모듈 인스턴스의 번호를 식별할 수 있습니다.

IoT 모듈 SDK에서 다음 방법 중 하나를 사용하여 ProductInfo를 다음 식별자로 설정합니다.

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

IoT 모듈 SDK를 사용하지 않는 모듈의 경우 파트너 센터를 통해 다운로드 횟수와 같은 덜 정확한 인사이트를 얻을 수 있습니다.

### <a name="security"></a>보안

IoT Edge 모듈은 [권한이 있는 모듈](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)을 피해야 합니다. 대신 가능한 한 호스트에 대한 최소 권한 있는 액세스를 요청합니다.

### <a name="module-iot-sdk"></a>모듈 IoT SDK

IoT 모듈 SDK를 포함하는 것은 인증을 위한 필수 조건이 아닙니다. 그러나 IoT 모듈 SDK가 포함되면 더 나은 사용자 환경을 제공할 수 있습니다. 예를 들어 라우팅 또는 클라우드에 메시지를 보낼 수 있습니다.

IoT 모듈 SDK는 실행되는 모듈 인스턴스의 번호에 대한 원격 분석 데이터를 가져와야 합니다.

## <a name="recertification-process"></a>재인증 프로세스

파트너에게는 다음과 같은 모듈에 영향을 주는 호환성이 손상되는 변경이 있을 때마다 알림이 표시됩니다.

- IoT Edge에서 지원되는 계층 1 OS/아키텍처 지원 매트릭스
- IoT 모듈 SDK
- IoT Edge 런타임
- IoT Edge 모듈 인증 지침

파트너는 제품을 [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165290)에 다시 게시하여 업데이트하고 재인증해야 합니다.

새 이미지 태그 추가와 같이 업데이트하는 경우에도 제품이 재인증됩니다.

## <a name="host-module-in-azure-container-registry"></a>Azure Container Registry의 호스트 모듈

IoT Edge 모듈을 Azure Marketplace에 업로드하려면 먼저 ACR([Azure Container Registry](https://azure.microsoft.com/services/container-registry/))에서 호스팅해야 합니다. 모듈은 매니페스트 태그에서 참조하는 이미지 태그를 포함하여 게시하려는 모든 태그를 포함해야 합니다. 자세한 내용은 [Azure Container Registry 만들기 및 컨테이너 이미지 푸시](../container-instances/container-instances-tutorial-prepare-acr.md) 자습서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [IoT Edge 모듈 제품 만들기](iot-edge-offer-setup.md)
