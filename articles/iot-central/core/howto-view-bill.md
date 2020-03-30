---
title: Azure IoT Central 애플리케이션에서 청구서를 관리하고 무료 요금제에서 변환 | 마이크로 소프트 문서
description: 관리자는 청구서를 관리하고 무료 가격 책정 계획에서 Azure IoT Central 응용 프로그램의 표준 가격 책정 계획으로 이동하는 방법을 알아봅니다.
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157519"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>IoT 중앙 애플리케이션에서 청구서 관리

이 문서에서는 관리 섹션에서 Azure IoT Central 응용 프로그램에서 청구서를 관리하는 방법에 대해 설명합니다. 응용 프로그램을 무료 가격 책정 계획에서 표준 가격 책정 계획으로 이동하는 방법과 가격 책정 계획을 업그레이드하거나 다운그레이드하는 방법에 대해 알아봅니다.

**관리** 섹션에 액세스하고 사용하려면 *관리자* 역할에 있거나 Azure IoT Central 응용 프로그램에 대한 청구를 볼 수 있는 사용자 지정 *사용자 역할이* 있어야 합니다. Azure IoT Central 애플리케이션을 만드는 사용자에게는 자동으로 해당 애플리케이션의 **관리자** 역할이 할당됩니다.

## <a name="move-from-free-to-standard-pricing-plan"></a>무료 요금제에서 표준 요금제로 전환

- 무료 요금제 는 만료되기 7일 전에 무료입니다. 데이터 손실을 방지하기 위해 데이터가 만료되기 전에 언제든지 표준 요금제로 이동할 수 있습니다.
- 표준 가격 책정 계획을 사용하는 응용 프로그램은 장치당 요금이 부과되며 처음 두 장치는 응용 프로그램당 무료입니다.

[Azure IoT Central 가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-central/)에서 가격 책정에 대해 자세히 알아보세요.

가격 책정 섹션에서 응용 프로그램을 무료 요금제에서 표준 가격 책정 요금제로 이동할 수 있습니다.

이 셀프 서비스 프로세스를 완료하려면 다음 단계를 수행합니다.

1. **관리** 섹션의 **가격 책정** 페이지로 이동합니다.

    ![평가판 상태](media/howto-view-bill/freetrialbilling.png)

1. **유료 요금제로 변환을**선택합니다.

    ![평가판 변환](media/howto-view-bill/convert.png)

1. 적절한 Azure Active Directory를 선택한 다음 유료 계획을 사용하는 응용 프로그램에 사용할 Azure 구독을 선택합니다.

1. **Convert를**선택하면 응용 프로그램이 유료 요금제(유료 요금제)를 사용하고 요금이 청구됩니다.

> [!Note]
> 기본적으로 *표준 2* 요금제로 변환됩니다.

## <a name="how-to-change-your-application-pricing-plan"></a>응용 프로그램 가격 책정 계획을 변경하는 방법

표준 가격 책정 계획을 사용하는 응용 프로그램은 장치당 요금이 부과되며 처음 두 장치는 응용 프로그램당 무료입니다.

가격 책정 섹션에서 언제든지 Azure IoT 가격 책정 계획을 업그레이드하거나 다운그레이드할 수 있습니다.

1. **관리** 섹션의 **가격 책정** 페이지로 이동합니다.

    ![평가판 상태](media/howto-view-bill/pricing.png)

1. **계획을** 선택하고 **저장을** 클릭하여 업그레이드하거나 다운그레이드합니다.

## <a name="view-your-bill"></a>청구서 보기

1. 적절한 Azure Active Directory를 선택한 다음 유료 계획을 사용하는 응용 프로그램에 사용할 Azure 구독을 선택합니다.

1. **Convert를**선택하면 응용 프로그램이 유료 요금제(유료 요금제)를 사용하고 요금이 청구됩니다.

> [!Note]
> 기본적으로 *표준 2* 요금제로 변환됩니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 청구서를 관리하는 방법에 대해 배웠으니 다음 단계는 Azure IoT Central에서 [응용 프로그램 UI 사용자 지정에](howto-customize-ui.md) 대해 알아보는 것입니다.