---
title: IoT 에지 모듈 기술 자산 준비 - Azure 마켓플레이스
description: 사물 인터넷(IoT) Edge 모듈 기술 자산이 충족해야 Azure Marketplace에 게시하기 전에 기술 및 구성 요구 사항에 대해 알아봅니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: e83c70424c131e5324192b07e65321d63bf06e2e
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674216"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>IoT Edge 모듈 기술 자산 준비

> [!IMPORTANT]
> 클라우드 파트너 포털에서 파트너 센터로 IoT Edge 모듈 의 관리를 이전하고 있습니다. 오퍼가 마이그레이션될 때까지 클라우드 파트너 포털의 [IoT Edge 모듈 기술 자산 준비의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) 지침을 따라 오퍼를 관리하십시오.

이 문서에서는 Azure Marketplace에 게시하기 전에 IoT(사물 인터넷) Edge 모듈 기술 자산이 충족해야 하는 요구 사항을 설명합니다.

## <a name="get-started"></a>시작하기

IoT Edge 모듈은 IoT Edge 장치에서 실행되는 Docker 호환 컨테이너입니다.

- IoT Edge 모듈에 대한 자세한 내용은 [Azure IoT Edge 모듈 이해](https://aka.ms/UnderstandAzureIoTEdgemodules)를 참조하세요.
- IoT Edge 모듈 개발을 시작하려면 [고유한 IoT Edge 모듈 개발을](https://aka.ms/DevelopyourownIoTEdgemodules)참조하십시오.

## <a name="technical-requirements"></a>기술적인 요구 사항

IoT Edge 모듈은 Azure Marketplace에서 인증 및 게시하려면 다음과 같은 기술 요구 사항을 충족해야 합니다.

### <a name="platform-support"></a>플랫폼 지원

IoT Edge 모듈은 다음 플랫폼 옵션 중 하나를 지원해야 합니다.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge에서 지원되는 계층 1 플랫폼

모듈은 IoT Edge에서 지원하는 모든 계층 1 플랫폼을 지원해야 [합니다(Azure IoT Edge 지원에](https://aka.ms/AzureIoTEdgesupport)기록됨). 더 나은 고객 경험을 제공하기 때문에 이 옵션을 권장합니다. 이 기준을 충족하는 모듈이 전시됩니다. 이 플랫폼 옵션을 사용하는 모듈은 다음을 수행해야 합니다.

- [GitHub 매니페스트 도구로](https://aka.ms/GitHubmanifest-tool)빌드된 매니페스트 태그인 최신 태그와 버전 태그(예: 1.0.1)를 제공합니다.

- [파트너 센터의](https://partner.microsoft.com/dashboard/commercial-marketplace) 오퍼 목록 탭을 사용하여 [Azure IoT Edge 인증 장치 카탈로그에](https://catalog.azureiotsolutions.com/)대한 **유용한 링크** 섹션 아래에 있는 링크를 추가합니다. Azure https://aka.ms/iot-edge-certified [IoT Edge 장치 카탈로그로](https://catalog.azureiotsolutions.com/)연결되는 리디렉션 링크를 사용할 수 있습니다.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge에서 지원되는 계층 1 플랫폼의 하위 집합

모듈은 IoT Edge에서 지원하는 계층 1 플랫폼의 하위 집합(하나 이상)을 지원해야 [합니다(Azure IoT Edge 지원에](https://aka.ms/AzureIoTEdgesupport)기록됨). 이 플랫폼 옵션을 사용하는 모듈은 다음을 수행해야 합니다.

- 둘 이상의 플랫폼이 지원되는 경우 GitHub [매니페스트 도구로](https://github.com/estesp/manifest-tool) 빌드된 매니페스트 태그인 최신 태그와 버전 태그(예: 1.0.1)를 제공합니다. 하나의 플랫폼이 지원되는 경우에만 매니페스트 태그는 선택 사항입니다.
- [파트너 센터의](https://partner.microsoft.com/dashboard/commercial-marketplace) 오퍼 목록 탭을 사용하여 [Azure IoT Edge 인증 장치 카탈로그에서](https://catalog.azureiotsolutions.com/)하나 이상의 IoT Edge 장치에 **유용한 링크** 섹션 아래에 있는 링크를 추가합니다.

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="파트너 센터 내의 오퍼 리스팅 섹션의 이미지입니다.":::

### <a name="device-dimensions"></a>디바이스 차원

대상 IoT Edge 장치의 IoT Edge 모듈 크기(예: CPU, RAM, 스토리지 및 GPU)는 다음 요구 사항을 충족해야 합니다.

- 모듈은 Azure IoT Edge 인증 장치 카탈로그에서 하나 이상의 [IoT Edge 장치와](https://catalog.azureiotsolutions.com/)함께 작동해야 합니다.

- 최소 하드웨어 요구 사항은 오퍼 설명의 마지막 단락으로 문서화되어야 [합니다(파트너 센터의](https://partner.microsoft.com/dashboard/commercial-marketplace)오퍼 목록 탭 아래). 필요에 따라 크게 다를 경우 권장되는 하드웨어 요구 사항을 나열할 수도 있습니다. 예를 들어 제품 설명의 끝에 다음 섹션을 추가합니다.

이 HTML 텍스트를 복사하거나 편집 창에서 해당 리치 텍스트 함수를 사용합니다.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>구성

IoT Edge 장치에 배포를 가능한 한 간단하게 만들려면 모듈에 기본 구성 설정이 포함되어야 합니다. 이 정보는 [파트너 센터의](https://partner.microsoft.com/dashboard/commercial-marketplace)계획에 대한 **기술 구성** 페이지에서 제공할 수 있습니다. 컨테이너에는 에지 허브 및 IoT Hub와의 통신을 가능하게 하는 IoT 에지 모듈 SDK도 포함될 수 있다.

#### <a name="default-configuration"></a>기본 구성

IoT Edge 모듈은 [파트너 센터의](https://partner.microsoft.com/dashboard/commercial-marketplace)계획에 대한 **기술 구성** 페이지에 제공된 기본 설정으로 시작할 수 있어야 합니다. 다음 기본 설정을 사용할 수 있습니다.

- 기본 **경로**
- 기본 **모듈 트윈 원하는 속성**
- 기본 **환경 변수**
- 기본 **컨테이너 만들기 옵션**

기본값에 필요한 매개 변수가 의미가 없는 시나리오(예: 고객 서버의 IP 주소)에서는 매개 변수를 기본값으로 추가합니다. 이 값은 대문자이며 괄호로 둘러싸여 있습니다. 이 예제의 경우 다음 기본 환경 변수를 설정할 수 있습니다.

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>구성 문서

IoT Edge 모듈의 모든 구성 설정을 명확하게 문서화해야 합니다. 예를 들어 해당 배관, 원하는 속성 쌍, 환경 변수, createOptions 등을 사용하는 방법을 문서화해야 합니다. 문서에 대한 링크를 제공하거나 제안 또는 계획 설명의 일부로 만들어야 합니다. 파트너 센터의 제안 **목록** 및 **계획 목록** 페이지에서 이 정보를 제공할 수 [있습니다.](https://partner.microsoft.com/dashboard/commercial-marketplace)

#### <a name="tags-and-versioning"></a>태그 및 버전 관리

고객은 모듈을 쉽게 배포하고 개발자 시나리오에서 마켓플레이스에서 자동으로 업데이트를 받을 수 있어야 합니다. 또한 프로덕션 시나리오에서 테스트한 정확한 버전을 사용하고 동결할 수 있어야 합니다.

이러한 고객의 기대를 충족하고 시장에 게시하려면 IoT Edge 모듈이 다음 요구 사항을 충족해야 합니다.

- 지원되는 모든 플랫폼에서 최신 버전을 가리키는 매니페스트 최신 태그를 포함합니다.
- X, Y 및 Z가 정수인 X.Y.Z 형식으로 버전 태그를 만듭니다.
- 지원되는 모든 플랫폼에서 특정 버전을 가리키는 1.0.1과 같은 "버전" 태그를 포함합니다.
- 1.0.1과 같은 "버전" 태그는 변경하지 않아야 하므로 업데이트하지 마십시오.

> [!NOTE]
> 선택적으로 버전 전환에는 2.0 및 1.0과 같은 "롤링 버전" 태그가 포함될 수 있습니다. 이는 동시에 여러 주 버전 유지 관리를 지원합니다.

### <a name="telemetry"></a>원격 분석

IoT 모듈 SDK를 사용하는 모듈은 원격 분석을 위해 고유 모듈 식별자를 PublisherId.OfferId.SkuId로 설정해야 합니다. 고유 식별자는 Azure Marketplace에서 실행 중인 모듈 인스턴스 수를 식별하는 데 도움이 됩니다.

IoT 모듈 SDK의 다음 방법 중 하나를 사용하여 ProductInfo를 이 식별자로 설정합니다.

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

IoT 모듈 SDK를 사용하지 않는 모듈의 경우 다운로드 수와 같은 파트너 센터를 통해 보다 정밀한 통찰력을 얻을 수 있습니다.

### <a name="security"></a>보안

IoT Edge 모듈은 [권한 있는 모듈을](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)피해야 합니다. 대신 가능한 한 호스트에 대한 최소 권한 액세스를 요청합니다.

### <a name="module-iot-sdk"></a>모듈 IoT SDK

IoT 모듈 SDK를 포함하는 것은 인증을 위한 필수 조건이 아닙니다. 그러나 IoT 모듈 SDK가 포함되면 더 나은 사용자 환경을 제공할 수 있습니다. 예를 들어 라우팅 또는 클라우드에 메시지를 보낼 수 있습니다.

IoT 모듈 SDK는 실행 중인 모듈 인스턴스 수에 대한 원격 분석 데이터를 가져오려면 필요합니다.

## <a name="recertification-process"></a>재인증 프로세스

파트너는 다음과 같이 모듈에 영향을 주는 주요 변경 이 있을 때마다 알림을 받습니다.

- IoT Edge에서 지원하는 계층 1 OS/아치 지원 매트릭스
- IoT 모듈 SDK
- IoT Edge 런타임
- IoT 에지 모듈 인증 가이드라인

파트너는 [파트너 센터에](https://partner.microsoft.com/dashboard/commercial-marketplace)다시 게시하여 오퍼를 업데이트하고 다시 인증해야 합니다.

새 이미지 태그 추가와 같이 업데이트하는 경우에도 오퍼가 다시 인증됩니다.

## <a name="host-module-in-azure-container-registry"></a>Azure 컨테이너 레지스트리의 호스트 모듈

IoT Edge 모듈을 Azure 마켓플레이스에 업로드하려면 먼저 [ACR(Azure 컨테이너 레지스트리)에서](https://azure.microsoft.com/services/container-registry/) 호스팅해야 합니다. 모듈에는 매니페스트 태그에서 참조하는 이미지 태그를 포함하여 게시하려는 모든 태그가 포함되어야 합니다. 자세한 내용은 자습서를 참조하여 [Azure 컨테이너 레지스트리 만들기 및 컨테이너 이미지 푸시를](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)참조하세요.

## <a name="next-steps"></a>다음 단계

- [IoT Edge 모듈 제품 만들기](https://aka.ms/AzureCreateIoT)