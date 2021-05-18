---
title: Azure IoT Central 애플리케이션에서 청구서를 관리하고 무료 요금제에서 전환 | Microsoft Docs
description: 관리자로서 청구서를 관리하고 Azure IoT Central 애플리케이션에서 무료 요금제를 표준 요금제로 전환하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 50d0119b08d2c76a5f6111e485408ebcdace83c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549024"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>IoT Central 애플리케이션에서 청구 관리

이 문서에서는 관리자로서 Azure IoT Central 청구를 관리할 수 있는 방법을 설명합니다. 애플리케이션을 무료 요금제에서 표준 요금제로 전환하고, 현재 사용 중인 요금제를 업그레이드하거나 다운그레이드할 수도 있습니다.

**관리** 섹션에 액세스하려면 *관리자* 역할에 있거나 청구를 볼 수 있는 *사용자 지정 사용자 역할이* 있어야 합니다. Azure IoT Central 애플리케이션을 만드는 사용자에게는 자동으로 **관리자** 역할이 할당됩니다.

## <a name="move-from-free-to-standard-pricing-plan"></a>무료에서 표준 요금제로 전환

- 무료 요금제를 사용하는 애플리케이션은 만료되기 전까지 7일간 무료입니다. 데이터가 손실되지 않도록 하려면 만료되기 전에 언제든지 표준 요금제로 전환할 수 있습니다.
- 표준 요금제를 사용하는 애플리케이션은 디바이스 별로 청구되며, 처음 두 디바이스는 애플리케이션 당 무료로 제공됩니다.

[Azure IoT Central 가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-central/)에서 가격 책정에 대해 자세히 알아보세요.

요금제 섹션에서는 애플리케이션을 무료에서 표준 요금제로 전환할 수 있습니다.

이 셀프 서비스 프로세스를 완료하려면 다음 단계를 수행합니다.

1. **관리** 섹션의 **가격 책정** 페이지로 이동합니다.

    :::image type="content" source="media/howto-view-bill/freetrialbilling.png" alt-text="평가판 상태":::

1. **유료 요금제로 전환** 을 선택합니다.

    :::image type="content" source="media/howto-view-bill/convert.png" alt-text="평가판 변환":::

1. 해당 Azure Active Directory를 선택한 다음, 유료 요금제를 사용하는 애플리케이션에 사용할 Azure 구독을 선택합니다.

1. **전환** 을 선택하면 평가판이 유료 애플리케이션으로 전환되고 대금 청구가 시작됩니다.

> [!Note]
> 기본적으로 *표준 2* 요금제로 전환됩니다.

## <a name="how-to-change-your-application-pricing-plan"></a>애플리케이션 요금제를 변경하는 방법

표준 요금제를 사용하는 애플리케이션은 디바이스 별로 청구되며, 처음 두 디바이스는 애플리케이션 당 무료로 제공됩니다.

가격 책정 섹션에서는 언제든지 Azure IoT 요금제를 업그레이드하거나 다운그레이드할 수 있습니다.

1. **관리** 섹션의 **가격 책정** 페이지로 이동합니다.

    :::image type="content" source="media/howto-view-bill/pricing.png" alt-text="요금제 업그레이드":::

1. **요금제** 를 선택한 다음 **저장** 을 선택하여 업그레이드 또는 다운그레이드를 선택합니다.

## <a name="view-your-bill"></a>청구서 보기

1. 해당 Azure Active Directory를 선택한 다음, 유료 요금제를 사용하는 애플리케이션에 사용할 Azure 구독을 선택합니다.

1. **전환** 을 선택하면 평가판이 유료 애플리케이션으로 전환되고 대금 청구가 시작됩니다.

> [!Note]
> 기본적으로 *표준 2* 요금제로 전환됩니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 애플리케이션에서 청구를 관리하는 방법을 배웠으므로 다음 단계로는 Azure IoT Central에서 [애플리케이션 UI 사용자 지정](howto-customize-ui.md)에 대해 알아보는 것을 제안합니다.