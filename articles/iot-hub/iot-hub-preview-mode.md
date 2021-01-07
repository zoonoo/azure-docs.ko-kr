---
title: Azure IoT Hub에 대 한 미리 보기 모드 설정
description: IoT Hub에 대 한 미리 보기 모드를 설정 하는 방법, 원하는 이유 및 일부 경고에 대해 알아봅니다.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621708"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>IoT Hub의 미리 보기 모드를 사용 하 여 새 기능 선택 시도

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

새 기능은 다음을 포함 하 여 IoT Hub에 대 한 공개 미리 보기로 제공 됩니다.

- MQTT 5
- ECC 서버 인증서
- TLS 조각 길이 협상
- HTTPS/WebSocket에 대 한 X509 CA 체인 지원

이러한 기능은 IoT Hub 프로토콜 및 인증 계층에서 개선 되었으므로 지금은 **새** IoT Hub에만 사용할 수 있습니다. 기존 IoT hub에서 아직 사용할 수 *없습니다* . 이러한 기능을 미리 보려면 미리 보기 모드가 설정 된 상태에서 IoT hub를 만들어야 합니다.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>새 IoT hub에 대 한 미리 보기 모드 설정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 홈페이지에서 **+리소스 만들기** 를 클릭한 다음, **Marketplace 검색** 필드에 *IoT Hub* 를 입력합니다.

1. 검색 결과에서 **IoT Hub**, **만들기** 를 차례로 선택합니다.

1. **기본** 탭에서 **영역** 을 제외 하 고 필드를 [정상적](iot-hub-create-through-portal.md) 으로 완료 합니다. 다음 지역 중 하나를 선택 합니다.
    
    - 미국 중부
    - 서유럽
    - 동남아시아

1. **관리** 탭을 선택한 다음 **고급 설정** 섹션을 확장 합니다.

1. **미리 보기 모드** 옆에서 **켜기** 를 선택 합니다. 경고 텍스트를 신중 하 게 검토 합니다.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="새 IoT hub를 만들 때 미리 보기 모드 옵션을 선택할 수 있는 위치를 보여 주는 이미지":::

1. **다음: 검토 + 만들기**, **만들기** 를 차례로 선택 합니다.

일단 만들어지면 미리 보기 모드의 IoT Hub는 항상이 배너를 표시 하 여 미리 보기 목적 으로만이 IoT Hub를 사용 하는 것을 알려줍니다. 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="미리 보기 모드 IoT hub에 대 한 배너를 표시 하는 이미지":::

## <a name="using-an-iot-hub-in-preview-mode"></a>미리 보기 모드에서 IoT hub 사용

프로덕션 환경에는 미리 보기 모드에서 IoT hub를 사용 *하지* 마세요. 미리 보기 모드는이 페이지의 맨 위에 나열 된 선택 *기능을 미리 보기만 하기 위한 것* 입니다. IoT Hub 미리 보기 모드에는 다음과 같은 몇 가지 제한 사항이 있습니다.

- IP 필터, 개인 링크, 관리 되는 id, 장치 스트림 및 장애 조치 (failover)와 같은 기존 IoT Hub 기능 중 일부는 예기치 않게 작동 하거나 전혀 작동 하지 않을 수 있습니다.
- 미리 보기 모드의 IoT hub는 변경 하거나 일반적인 IoT hub로 업그레이드할 수 없습니다.
- 정상적인 [IOT HUB SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) 를 보장할 수 없습니다. 프로덕션에 사용 하지 마세요.

> [!TIP]
> [장치 스트림과](iot-hub-device-streams-overview.md) [분산 추적](iot-hub-distributed-tracing.md)에는 미리 보기 모드가 필요 하지 않습니다. 이러한 이전 미리 보기 기능을 사용 하려면 일반적인 문서를 따르세요. 

## <a name="next-steps"></a>다음 단계

- MQTT 5 지원을 미리 보려면 [IoT Hub MQTT 5 지원 개요 (미리 보기)](iot-hub-mqtt-5.md) 를 참조 하세요.
- ECC 서버 인증서를 미리 보려면 ECC ( [타원 Curve Cryptography) 서버 TLS 인증서 (미리 보기)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview) 를 참조 하세요.
- TLS 조각 크기 협상을 미리 보려면 [tls 최대 조각 길이 협상 (미리 보기)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview) 을 참조 하세요.