---
title: Azure IoT Central 응용 프로그램의 상태 모니터링 | Microsoft Docs
description: 운영자 또는 관리자는 IoT Central 응용 프로그램에 연결 된 장치의 전반적인 상태를 모니터링 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d246adbf8e87d5503bc19bcd497dd00370c0a867
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001920"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>IoT Central 응용 프로그램에 연결 된 장치의 전반적인 상태를 모니터링 합니다.

> [!NOTE]
> 메트릭은 버전 3 IoT Central 응용 프로그램에만 사용할 수 있습니다. 응용 프로그램 버전을 확인 하는 방법에 [대 한 자세한 내용은 응용 프로그램 정보](./howto-get-app-info.md)를 참조 하세요.

*이 문서는 운영자 및 관리자에 게 적용 됩니다.*

이 문서에서는 IoT Central에서 제공 하는 메트릭 집합을 사용 하 여 IoT Central 응용 프로그램에 연결 된 장치의 전반적인 상태를 평가 하는 방법에 대해 알아봅니다.

메트릭은 IoT Central 응용 프로그램에 대해 기본적으로 사용 하도록 설정 되며 [Azure Portal](https://portal.azure.com/)에서 액세스할 수 있습니다. [Azure Monitor 데이터 플랫폼은 이러한 메트릭을 노출](../../azure-monitor/platform/data-platform-metrics.md) 하 고 여러 가지 방법을 제공 하 여 상호 작용할 수 있습니다. 예를 들어 Azure Portal, REST API 또는 PowerShell 또는 Azure CLI의 쿼리에서 차트를 사용할 수 있습니다.

### <a name="trial-applications"></a>평가판 응용 프로그램

무료 평가판 요금제를 사용 하는 응용 프로그램에는 연결 된 Azure 구독이 없으므로 Azure Monitor 메트릭을 지원 하지 않습니다. [응용 프로그램을 표준 요금제로 변환](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) 하 고 이러한 메트릭에 대 한 액세스 권한을 얻을 수 있습니다.

## <a name="view-metrics-in-the-azure-portal"></a>Azure Portal에서 메트릭 보기

다음 단계에서는 일부 [연결 된 장치](./tutorial-connect-device-nodejs.md)를 포함 하는 [IoT Central 응용 프로그램이](./quick-deploy-iot-central.md) 있다고 가정 합니다.

포털에서 IoT Central 메트릭을 보려면 다음을 수행 합니다.

1. 포털에서 IoT Central 응용 프로그램 리소스로 이동 합니다. 기본적으로 IoT Central 리소스는 **Iotc**라는 리소스 그룹에 있습니다.
1. 응용 프로그램의 메트릭에 따라 차트를 만들려면 **모니터링** 섹션에서 **메트릭** 을 선택 합니다.

![Azure Metrics](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal 권한

Azure Portal의 메트릭에 대 한 액세스는 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 통해 관리 됩니다. Azure Portal를 사용 하 여 IoT Central 응용 프로그램/리소스 그룹/구독에 사용자를 추가 하 여 액세스 권한을 부여 합니다. 사용자가 이미 IoT Central 응용 프로그램에 추가 된 경우에도 포털에서 사용자를 추가 해야 합니다. 보다 세분화 된 액세스 제어를 위해 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 을 사용 합니다.

## <a name="iot-central-metrics"></a>IoT Central 메트릭

현재 IoT Central 사용할 수 있는 메트릭 목록은 [Azure Monitor에서 지원 되는 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftiotcentraliotapps)을 참조 하세요.

### <a name="metrics-and-invoices"></a>메트릭 및 송장

메트릭은 Azure IoT Central 청구서에 표시 된 숫자와 다를 수 있습니다. 이러한 상황은 다음과 같은 여러 가지 이유로 발생 합니다.

- IoT Central [표준 가격 책정 요금제](https://azure.microsoft.com/pricing/details/iot-central/) 에는 두 개의 장치와 다양 한 메시지 할당량이 무료로 포함 됩니다. 청구에서 제외 된 항목은 계속 해 서 메트릭에 계산 됩니다.

- 응용 프로그램의 각 장치 템플릿에 대해 하나의 테스트 장치 ID를 경로도 IoT Central 합니다. 이 장치 ID는 장치 템플릿에 대 한 **테스트 장치 관리** 페이지에 표시 됩니다. 솔루션 빌더는 이러한 테스트 장치 Id를 사용 하는 코드를 생성 하 여 해당 장치 템플릿을 게시 하기 전에 [유효성을 검사](./overview-iot-central.md#create-device-templates) 하도록 선택할 수 있습니다. 이러한 장치는 청구에서 제외 되지만 여전히 메트릭에 계산 됩니다.

- 메트릭은 장치-클라우드 통신의 하위 집합을 표시할 수 있지만 장치와 클라우드 간의 모든 통신은 [요금 청구를 위해 메시지로 계산](https://azure.microsoft.com/pricing/details/iot-central/)됩니다.

## <a name="next-steps"></a>다음 단계

이제 응용 프로그램 템플릿을 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 [Azure Portal에서 IoT Central를 관리](howto-manage-iot-central-from-portal.md) 하는 방법을 배우는 것입니다.
