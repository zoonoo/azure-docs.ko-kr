---
title: IoT 플러그 앤 플레이 미리 보기 디바이스 인증 | Microsoft Docs
description: 이 자습서에서는 IoT용 Azure Certified 디바이스 카탈로그에 제품 정보를 추가하고, 디바이스를 Azure IoT 인증 서비스에 연결한 다음, IoT 플러그 앤 플레이 인증 테스트를 실행하는 방법을 설명합니다.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550165"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>자습서: IoT 플러그 앤 플레이 미리 보기 디바이스 인증

[IoT용 Azure Certified 디바이스 카탈로그](https://aka.ms/iotdevcat)에서 IoT 플러그 앤 플레이 미리 보기 디바이스를 게시하려면 인증 테스트 세트를 통과해야 합니다. [IoT용 Azure Certified](https://aka.ms/ACFI) 포털을 사용하여 인증할 디바이스를 제출합니다. [Azure IoT 인증 서비스](https://aka.ms/azure-iot-aics)는 인증 테스트를 실행합니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * IoT 플러그 앤 플레이 인증 요구 사항은 무엇인가요?
> * 포털에 제품 이름 및 정보를 추가하는 방법
> * IoT 플러그 앤 플레이 인터페이스를 연결하고 검색하는 방법
> * IoT 플러그 앤 플레이 인터페이스를 검토하고 인증 테스트를 실행하는 방법
> * 인증된 IoT 플러그 앤 플레이 디바이스를 카탈로그에 게시하는 방법

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* Microsoft 파트너 센터 계정
* Microsoft 파트너 네트워크 ID

자세한 내용은 [IoT용 Azure Certified 포털에 온보딩하는 방법](howto-onboard-portal.md)을 참조하세요.

## <a name="certification-requirements"></a>인증 요구 사항

IoT 플러그 앤 플레이 디바이스를 인증하려면 디바이스가 다음 요구 사항을 충족해야 합니다.

* IoT 플러그 앤 플레이 디바이스 코드가 디바이스에 설치되어 있습니다.
* IoT 플러그 앤 플레이 디바이스 코드가 Azure IoT SDK를 사용하여 빌드되었습니다.
* 디바이스 코드가 [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md)를 지원합니다.
* 디바이스 코드가 [디바이스 정보 인터페이스](concepts-common-interfaces.md)를 구현합니다.
* 기능 모델 및 디바이스 코드가 IoT Central과 호환됩니다.

현재 카탈로그에 있는 모든 디바이스는 사전 인증된 IoT 플러그 앤 플레이 디바이스로 간주되며, IoT 플러그 앤 플레이 소프트웨어 구성 요소(예: SDK 및 디지털 쌍 정의 언어)의 최종 제품 품질 및 규정 준수를 보장하지 않습니다.

사전 인증된 모든 IoT 플러그 앤 플레이 디바이스는 Microsoft에서 제공하는 인증 요구 사항 및 소프트웨어 구성 요소의 최종 버전에 따라 IoT 플러그 앤 플레이의 일반 공급 시에 다시 인증을 받아야 합니다.

## <a name="add-product-name"></a>제품 이름 추가

**제품**은 고객이 구매할 수 있는 제품 모델의 식별 이름입니다. 제품을 추가하는 방법은 다음과 같습니다.

1. [IoT용 Azure Certified](https://aka.ms/ACFI) 포털로 이동합니다.
1. 포털의 왼쪽 메뉴에서 **제품** 페이지로 이동하여 **+ 새로 만들기**를 선택합니다.
1. 제품 식별 이름을 입력하고 **만들기**를 선택합니다. 여기에 입력한 이름은 디바이스 카탈로그에 표시되는 이름과 다릅니다.

## <a name="add-product-information"></a>제품 정보 추가

포털에서 제품을 성공적으로 만들었으면 제품이 **제품** 페이지에 표시됩니다. 제품 정보를 추가하는 방법은 다음과 같습니다.

1. 제품 열에서 **제품** 페이지를 보면 앞에서 만든 제품 링크가 있습니다. 이 링크를 선택합니다. 상태가 초안 상태일 것입니다.
1. **제품 정보** 제목 옆에 있는 **편집** 링크를 선택합니다. 제품 정보 페이지에서 제품에 대한 정보를 입력하고 모든 필수 필드를 완료합니다.
1. **저장**을 선택합니다. **저장** 단추는 모든 필수 필드를 완료해야만 표시됩니다. 완료되지 않은 필드가 있으면 단추가 **나중에 저장하고 마침**이라고 표시됩니다.
1. 입력한 내용을 검토합니다. 디바이스를 디바이스 카탈로그에 게시하는 데 필요한 모든 필드를 완료합니다. 언제든지 **제품 정보** 제목 옆에 있는 **편집** 링크를 클릭하여 제품 정보 페이지에서 제품 정보를 편집할 수 있습니다.

## <a name="connect-and-discover-interfaces"></a>인터페이스 연결 및 검색

인증 테스트를 실행하려면 포털에서 사용할 수 있는 [AICS(Azure IoT 인증 서비스)](https://aka.ms/azure-iot-aics)에 디바이스를 연결합니다.

이러한 단계는 인증 테스트를 실행하기 위해 한 번만 수행하는 일회성 단계이며, 제품 디바이스 코드를 변경하지 않아도 됩니다. 시작하려면 다음 단계에 따라 AICS에 연결합니다.

1. 파트너 센터 계정을 사용하여 포털에 로그인합니다.
1. **연결 + 테스트**를 클릭하여 인증 흐름을 시작합니다.
1. [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md)를 사용하여 디바이스를 AICS에 프로비저닝할 [인증 방법](../iot-dps/concepts-security.md#attestation-mechanism)을 선택합니다.
   * [X.509 인증서](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)를 사용하는 경우 생성된 X.509 인증서를 업로드합니다. X.509 인증서 사용 방법을 보여주는 [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md) 샘플 코드를 검토하는 것이 좋습니다.
   * [대칭 키](../iot-dps/concepts-symmetric-key-attestation.md)를 사용하는 경우 대칭 키를 복사하여 디바이스 코드에 붙여넣습니다.
   * 현재는 TPM 인증 방법이 지원되지 않습니다.
1. 생성된 다음 ID를 복사하여 디바이스 코드에 붙여넣습니다.
   * [등록 ID](../iot-dps/use-hsm-with-sdk.md)
   * [DPS ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS 엔드포인트](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. 디바이스 코드가 빌드되고 디바이스에 배포되면 **연결**을 선택하여 IoT 플러그 앤 플레이 인터페이스를 검색합니다.
1. AICS에 성공적으로 연결되면 **다음**을 선택하여 검색된 IoT 플러그 앤 플레이 인터페이스를 검토합니다.

## <a name="run-tests-and-publish-the-device"></a>테스트 실행 및 디바이스 게시

검토 페이지에서 검색된 IoT 플러그 앤 플레이 인터페이스를 검토할 수 있습니다. 이 페이지를 사용하여 인터페이스에 구현된 기본 형식이 올바르게 표시되는지 확인합니다. 인터페이스의 위치도 확인할 수 있습니다.

1. 필수 필드의 페이로드 입력을 입력했는지 확인합니다. 이러한 필드에는 지정된 인터페이스의 명령 기본 형식에 대한 페이로드 정보가 포함됩니다.
1. 필수 정보를 모두 입력한 후 **다음**을 선택합니다.
1. 구현된 IoT 플러그 앤 플레이 인터페이스를 테스트하려면 **테스트 실행**을 선택합니다.
1. 모든 테스트는 자동으로 실행됩니다. 테스트가 실패할 경우 **로그 보기**를 선택하여 AICS의 오류 메시지와 Azure IoT Hub에 전송된 원시 원격 분석 데이터를 확인합니다.
1. 인증 테스트를 완료하려면 **마침**을 선택합니다.
1. 인증된 IoT 플러그 앤 플레이 디바이스를 카탈로그에 게시합니다. 인증된 디바이스를 카탈로그에 추가하려면 도구 모음에서 **카탈로그에 추가**를 선택합니다. **카탈로그에 추가**가 회색으로 표시되면 제품 정보가 불완전하거나 테스트가 실패한 것입니다. 
1. "CERTIFIED AND IN THE CATALOG(인증된 카탈로그의 디바이스)" 링크를 선택하고 디바이스 카탈로그에 게시된 디바이스를 살펴봅니다.

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이 디바이스 인증에 대해 알아보았으므로, 다음 단계로 기능 모델 관리에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [모델 관리](./howto-manage-models.md)
