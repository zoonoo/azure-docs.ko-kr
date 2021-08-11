---
title: IoT Central 애플리케이션 만들기 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central 사이트, Azure Portal 및 명령줄 환경 등에서 IoT Central 애플리케이션을 만드는 옵션을 설명합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 10791fac984a5df4a7fba95217f00ef6a2114b86
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109689337"
---
# <a name="create-an-iot-central-application"></a>IoT Central 애플리케이션 만들기

IoT Central 애플리케이션을 만드는 방법은 여러 가지입니다. 수동 방식을 선호하는 경우에는 GUI 기반 메서드 중 하나를 사용하고, 프로세스를 자동화하려는 경우에는 CLI 또는 프로그래밍 방식 중 하나를 사용할 수 있습니다.

어떤 방식을 선택하든 구성 옵션은 동일하며 프로세스를 완료하는 데 일반적으로 1분 미만이 걸립니다.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="options"></a>옵션

이 섹션에서는 IoT Central 애플리케이션을 만들 때 사용할 수 있는 옵션에 대해 설명합니다. 선택한 방법에 따라 양식에 옵션을 제공하거나 명령줄 매개변수로 제공해야 할 수도 있습니다.

### <a name="pricing-plans"></a>가격 책정 계획

체험 플랜을 사용하면 IoT Central 애플리케이션을 만들어 7일 동안 사용할 수 있습니다. 체험 플랜은:

- Azure 구독이 필요하지 않습니다.
- [Azure IoT Central](https://aka.ms/iotcentral) 사이트에서만 만들고 관리할 수 있습니다.
- 최대 5개의 디바이스를 연결할 수 있습니다.
- 애플리케이션을 유지하려는 경우 표준 플랜으로 업그레이드할 수 있습니다.

*표준* 플랜은:

- Azure 구독이 필요합니다. Azure 구독에서 **기여자** 이상의 액세스 권한이 있어야 합니다. 구독을 직접 만든 경우 자동으로 충분한 액세스 권한이 있는 관리자가 됩니다. 자세한 내용은 [Azure 역할 기반 액세스 제어란?](../../role-based-access-control/overview.md)을 참조하세요.
- 사용 가능한 모든 방법으로 IoT Central 애플리케이션을 만들고 관리할 수 있습니다.
- 디바이스를 필요한 수 만큼 연결할 수 있습니다. 디바이스별로 요금이 청구됩니다. 자세히 알아보려면 [Azure IoT Central 가격 책정](/pricing/details/iot-central/)을 참조하세요.
- 체험 플랜으로 다운그레이드할 수는 없지만 다른 표준 플랜으로 업그레이드하거나 다운그레이드할 수 있습니다.

다음 표에는 세 가지 표준 플랜 간의 차이점이 요약되어 있습니다.

| 플랜 이름 | 체험 디바이스 | 메시지/월 | 사용 사례 |
| --------- | ------------ | -------------- | -------- |
| S0        | 2            | 400            | 하루에 몇 개의 메시지 |
| S1        | 2            | 5,000          | 시간당 몇 개의 메시지 |
| S2        | 2            | 30,000         | 몇 분 마다 여러 개의 메시지 |

자세히 알아보려면 [IoT Central 애플리케이션에서 청구 관리](howto-view-bill.md)를 참조하세요.

### <a name="application-name"></a>애플리케이션 이름

선택한 애플리케이션 이름은 IoT Central 애플리케이션의 모든 페이지에 있는 제목 표시줄에 표시됩니다. 또한 [Azure IoT Central](https://aka.ms/iotcentral) 사이트의 **내 앱** 페이지에 있는 애플리케이션 타일에도 나타납니다.

선택한 하위 도메인은 애플리케이션을 고유하게 식별합니다. 하위 도메인은 애플리케이션에 액세스하는 데 사용하는 URL의 일부입니다. IoT Central 애플리케이션의 URL은 `https://yoursubdomain.azureiotcentral.com`과 같은 모양입니다.

### <a name="application-template-id"></a>애플리케이션 템플릿 ID

선택한 애플리케이션 템플릿에 따라 대시보드 및 디바이스 템플릿과 같은 애플리케이션의 초기 콘텐츠가 결정됩니다. 사용자 지정 애플리케이션의 템플릿 ID는 `iotc-pnp-preview`를 템플릿 ID로 사용합니다.

사용자 지정 및 업계 중심의 애플리케이션 템플릿에 대한 자세한 내용은 [애플리케이션 템플릿이란?](concepts-app-templates.md)을 참조하세요.

### <a name="billing-information"></a>대금 청구 정보

표준 플랜 중 하나를 선택하면 다음과 같은 청구 정보를 제공해야 합니다.

- 사용 중인 Azure 구독
- 사용 중인 구독이 포함된 디렉터리
- 애플리케이션을 호스하는 위치. IoT Central은 Azure 지역을 미국, 유럽, 아시아 태평양, 오스트레일리아, 영국 또는 일본 위치로 사용합니다.

## <a name="azure-iot-central-site"></a>Azure IoT Central 사이트

IoT Central 애플리케이션을 만들기 가장 쉬운 방법은 [Azure IoT Central](https://aka.ms/iotcentral) 사이트에 있습니다.

[빌드](https://apps.azureiotcentral.com/build)에서 사용할 애플리케이션 템플릿을 선택할 수 있습니다.

:::image type="content" source="media/howto-create-iot-central-application/choose-template.png" alt-text="애플리케이션 템플릿을 선택할 수 있는 빌드 페이지 스크린샷":::

**앱 만들기** 를 선택하면 템플릿에서 애플리케이션을 만드는 데 필요한 정보를 제공할 수 있습니다.

:::image type="content" source="media/howto-create-iot-central-application/create-application.png" alt-text="IoT Central의 애플리케이션 만들기 페이지를 보여주는 스크린샷":::

**내 앱** 페이지에는 액세스 권한이 있는 모든 IoT Central 애플리케이션이 나열됩니다. 이 목록에는 직접 만든 애플리케이션과 액세스 권한이 부여된 애플리케이션이 포함됩니다.

> [!TIP]
> Azure IoT Central 사이트에서 표준 가격 책정 플랜을 사용하여 만드는 모든 애플리케이션은 구독에서 **IOTC** 리소스 그룹을 사용합니다. 다음 섹션에 설명된 방식을 통해 사용할 리소스 그룹을 선택할 수 있습니다.

## <a name="other-approaches"></a>다른 방법

다음 방식을 사용하여 IoT Central 애플리케이션을 만들 수도 있습니다.

- [Azure Portal에서 IoT Central 애플리케이션 만들기](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [Azure CLI를 사용하여 IoT Central 애플리케이션 만들기](howto-manage-iot-central-from-cli.md#create-an-application)
- [PowerShell을 사용하여 IoT Central 애플리케이션 만들기](howto-manage-iot-central-from-powershell.md#create-an-application)
- [프로그래밍 방식으로 IoT Central 애플리케이션 만들기](howto-manage-iot-central-programmatically.md)

## <a name="next-steps"></a>다음 단계

Azure CLI에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으면 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
