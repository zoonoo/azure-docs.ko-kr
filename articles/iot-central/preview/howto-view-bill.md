---
title: 청구서를 관리 하 고 Azure IoT Central 응용 프로그램에서 종 량 제로 전환 합니다. Microsoft Docs
description: 관리자는 Azure IoT Central 응용 프로그램에서 청구를 관리 하 고 평가판에서 지불로 전환 하는 방법을 알아봅니다.
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 11bcdaab34cf4d82b688a578ab2d9ee686ffe07e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894813"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>IoT Central 응용 프로그램에서 청구 관리

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

이 문서에서는 관리자가 관리 섹션에서 Azure IoT Central 응용 프로그램의 청구서를 관리할 수 있는 방법을 설명 하 고 평가판을 종 량 제로 변환 하는 방법을 설명 합니다.

**관리** 섹션에 액세스하여 사용하려면 Azure IoT Central 애플리케이션에서 **관리자** 역할이어야 합니다. Azure IoT Central 애플리케이션을 만드는 사용자에게는 자동으로 해당 애플리케이션의 **관리자** 역할이 할당됩니다.

## <a name="view-your-bill"></a>청구서 보기

청구서를 보려면 **관리** 섹션의 **청구** 페이지로 이동합니다. 새 탭에서 Azure 청구 페이지가 열리고, 여기서 각 Azure IoT Central 애플리케이션에 대한 청구서를 볼 수 있습니다.

## <a name="convert-your-trial-to-pay-as-you-go"></a>평가판을 종 량 제로 변환

- **평가판** 애플리케이션은 7일 동안 무료이며 이후에는 만료됩니다. 만료되기 전에 언제든지 종량제로 변환할 수 있습니다.
- **종 량** 제 응용 프로그램은 장치당 처음 5 개의 장치를 무료로 사용 하 여 장치당 청구 됩니다.

[Azure IoT Central 가격 책정 페이지](https://azure.microsoft.com/pricing/details/iot-central/)에서 가격 책정에 대해 자세히 알아보세요.

청구 섹션에서 평가판 응용 프로그램을 종 량 제로로 전환할 수 있습니다.

이 셀프 서비스 프로세스를 완료하려면 다음 단계를 수행합니다.

1. **관리** 섹션의 **청구** 페이지로 이동합니다.

    ![평가판 상태](media/howto-view-bill/freetrialbilling.png)

1. **종 량 제로 변환을**선택 합니다.

    ![평가판 변환](media/howto-view-bill/convert.png)

1. 적절한 Azure Active Directory를 선택한 다음, 종량제 애플리케이션에 사용할 Azure 구독을 선택합니다.

1. **변환**을 선택 하면 응용 프로그램이 이제 종 량 제 응용 프로그램으로 청구 되기 시작 합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 청구서를 관리 하는 방법에 대해 알아보았습니다. 다음 단계에서는 Azure IoT Central에서 [응용 프로그램 UI 사용자 지정](howto-customize-ui.md) 에 대해 알아봅니다.