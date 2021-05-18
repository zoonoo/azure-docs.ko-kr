---
title: Azure IoT Hub의 미리 보기 모드 켜기
description: IoT Hub에 대한 미리 보기 모드를 켜는 방법, 원하는 이유 및 몇 가지 경고에 대해 알아보기
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621708"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>IoT Hub의 미리 보기 모드를 켜서 새 선택 기능 살펴보기

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

다음과 같은 새 기능이 IoT Hub의 공개 미리 보기로 제공됩니다.

- MQTT 5
- ECC 서버 인증서
- TLS 조각 길이 협상
- HTTPS/WebSocket의 X509 CA 체인 지원

이러한 기능은 IoT Hub 프로토콜 및 인증 계층의 개선 사항이므로 지금은 **새로운** IoT Hub에서만 사용할 수 있으며 기존 IoT Hub에서는 아직 사용할 수 *없습니다*. 이러한 기능을 미리 보려면 미리 보기 모드가 켜진 상태에서 IoT Hub를 만들어야 합니다.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Azure IoT Hub에 대한 미리 보기 모드 켜기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 홈페이지에서 **+리소스 만들기** 를 클릭한 다음, **Marketplace 검색** 필드에 *IoT Hub* 를 입력합니다.

1. 검색 결과에서 **IoT Hub**, **만들기** 를 차례로 선택합니다.

1. **기본** 탭에서 **지역** 을 제외하고 [평소처럼](iot-hub-create-through-portal.md) 필드를 완료합니다. 다음 지역 중 하나를 선택합니다.
    
    - 미국 중부
    - 서유럽
    - 동남아시아

1. **관리** 탭을 선택한 다음 **고급 설정** 섹션을 확장합니다.

1. **미리 보기 모드** 옆에 있는 **켜기** 를 선택합니다. 경고문을 신중히 검토합니다.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="새 IoT Hub를 만들 때 미리 보기 모드 옵션을 선택하는 위치를 보여 주는 이미지":::

1. **다음: 검토 + 만들기** 를 선택하고 **만들기** 를 선택합니다.

한번 만들어지면 미리 보기 모드의 IoT Hub는 항상이 배너를 표시하여 이 IoT Hub는 미리 보기용으로만 사용하도록 알립니다. 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="미리 보기 모드 IoT Hub의 배너를 보여 주는 이미지":::

## <a name="using-an-iot-hub-in-preview-mode"></a>미리 보기 모드에서 IoT Hub 사용

미리 보기 모드의 IoT Hub를 프로덕션에 사용하지 *않아야 합니다*. 미리 보기 모드는 이 페이지의 맨 위에 나열된 선택 기능을 *미리 보기만* 하는 용도입니다. IoT Hub 미리 보기 모드에는 다음과 같은 몇 가지 제한 사항이 있습니다.

- IP 필터, 프라이빗 링크, 관리 ID, 디바이스 스트림 및 장애 조치(failover)와 같은 기존 IoT Hub 기능 중 일부가 예기치 않게 작동하거나 전혀 작동하지 않을 수 있습니다.
- 미리 보기 모드의 IoT Hub는 변경하거나 일반적인 IoT Hub로 업그레이드할 수 없습니다.
- 정상적인 [IoT Hub SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/)를 보장할 수 없습니다. 프로덕션에 사용하지 마세요.

> [!TIP]
> [디바이스 스트림](iot-hub-device-streams-overview.md) 및 [분산 추적](iot-hub-distributed-tracing.md)에는 미리 보기 모드가 필요하지 않습니다. 이와 같은 이전 미리 보기 기능을 사용하려면 일반적인 방식으로 해당 설명서를 따릅니다. 

## <a name="next-steps"></a>다음 단계

- MQTT 5 지원을 미리 보려면 [IoT Hub MQTT 5 지원 개요(미리 보기)](iot-hub-mqtt-5.md)를 참조하세요.
- ECC 서버 인증서를 미리 보려면 [ECC(Elliptic Curve Cryptography) 서버 TLS 인증서(미리 보기)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)를 참조하세요.
- TLS 조각 크기 협상을 미리 보려면 [TLS 최대 조각 길이 협상(미리 보기)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)을 참조하세요.