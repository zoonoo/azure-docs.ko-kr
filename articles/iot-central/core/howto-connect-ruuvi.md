---
title: Azure IoT Central에서 RuuviTag 연결 | Microsoft Docs
description: IoT Central 응용 프로그램에 RuuviTag 환경 센서를 연결 하는 방법에 대해 알아봅니다.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 93e4d3d0bed9090573d2b6ee87a29b86ccd72e42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758946"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에 RuuviTag 센서 연결

*이 문서는 솔루션 빌더 및 디바이스 개발자에게 적용됩니다.*

이 문서에서는 RuuviTag 센서를 Microsoft Azure IoT Central 응용 프로그램에 연결할 수 있는 방법에 대해 설명 합니다.

무엇 인가요?

RuuviTag는 비즈니스 고객, 개발자, 결정자, 학생 및 취미 사용자의 요구를 충족 하도록 설계 된 고급 오픈 소스 센서 신호 플랫폼입니다. 장치는 해당 장치를 사용 하는 즉시 작동 하도록 설정 되며, 필요에 따라 배포할 준비가 됩니다. 환경 센서 및가 속도계 기본 제공 되는 Bluetooth LE 신호입니다.

RuuviTag는 매우 심한 (Bluetooth 저 에너지)를 통해 통신 하며, Azure IoT Central와 통신 하기 위해 게이트웨이 장치가 필요 합니다. Rigado Cascade 500와 같은 게이트웨이 장치가 있는지 확인 하 고 RuuviTag가 IoT Central에 연결할 수 있도록 설정 합니다.

Rigado Cascade 500 게이트웨이 장치를 설정 하려면 [여기의 지침](./howto-connect-rigado-cascade-500.md) 을 따르세요.

## <a name="prerequisites"></a>사전 요구 사항

RuuviTag 센서를 연결 하려면 다음 리소스가 필요 합니다.

* RuuviTag 센서입니다. 자세한 내용은 [RuuviTag](https://ruuvi.com/)를 참조 하세요.
* Rigado Cascade 500 장치 또는 다른의 다른 게이트웨이. 자세한 내용은 [Rigado](https://www.rigado.com/)를 참조 하세요.
* Azure IoT Central 애플리케이션. 자세한 내용은 [새 응용 프로그램 만들기](./quick-deploy-iot-central.md)를 참조 하세요.

## <a name="add-a-ruuvitag-device-template"></a>RuuviTag 장치 템플릿 추가

RuuviTag 센서를 Azure IoT Central 응용 프로그램 인스턴스에 등록 하려면 응용 프로그램 내에서 해당 하는 장치 템플릿을 구성 해야 합니다.

RuuviTag 장치 템플릿을 추가 하려면:

1. 왼쪽 창에서 ***장치 템플릿*** 탭으로 이동 하 고 **+ 새로**만들기를 선택 ![ 합니다. 새 장치 템플릿 만들기 ](./media/howto-connect-ruuvi/devicetemplate-new.png) 페이지에서 ***사용자 지정 템플릿을 만들거나*** ***미리 구성 된 장치 템플릿을 사용할*** 수 있는 옵션을 제공 합니다.
1. 아래와 같이 미리 구성 된 장치 템플릿 목록에서 RuuviTag 장치 템플릿을 선택 합니다. ![ RuuviTag 장치 템플릿 선택](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. 다음 ***: 사용자 지정*** 을 선택 하 여 다음 단계를 계속 합니다.
1. 다음 화면에서 ***만들기*** 를 선택 하 여 IoT Central 응용 프로그램에 C500 장치 템플릿을 등록 합니다.

## <a name="connect-a-ruuvitag-sensor"></a>RuuviTag 센서 연결

앞서 언급 했 듯이 RuuviTag을 IoT Central 응용 프로그램과 연결 하려면 게이트웨이 장치를 설정 해야 합니다. 아래 단계에서는 Rigado Cascade 500 게이트웨이 장치를 설정 했다고 가정 합니다.  

1. Rigado Cascade 500 장치를 켜고 이더넷 또는 무선을 통해 네트워크 연결에 연결 합니다.
1. RuuviTag 커버를 팝 하 고 플라스틱 탭을 당겨 배터리와의 연결을 보호 합니다.
1. IoT Central 응용 프로그램에 이미 구성 되어 있는 Rigado Cascade 500 게이트웨이에 가까운 RuuviTag를 추가 합니다.
1. 몇 초만에 IoT Central 내의 장치 목록에 RuuviTag이 표시 됩니다.  
    ![RuuviTag 장치 목록](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

이제 IoT Central 응용 프로그램 내에서이 RuuviTag를 사용할 수 있습니다.  

## <a name="create-a-simulated-ruuvitag"></a>시뮬레이션 된 RuuviTag 만들기

물리적 RuuviTag 장치가 없는 경우 Azure IoT Central 응용 프로그램 내에서 테스트에 사용할 시뮬레이트된 RuuviTag 센서를 만들 수 있습니다.

시뮬레이션 된 RuuviTag을 만들려면 다음을 수행 합니다.

1. **장치 > RuuviTag**를 선택 합니다.
1. **+새로 만들기**를 선택합니다.
1. 고유한 **장치 ID** 및 친숙 한 **장치 이름을**지정 합니다.  
1. **시뮬레이션된** 설정을 사용하도록 설정합니다.
1. **만들기**를 선택합니다.  

## <a name="next-steps"></a>다음 단계

디바이스 개발자라면 다음과 같은 몇 가지 단계를 살펴보세요.

- [Azure IoT Central에서 디바이스 연결](./concepts-get-connected.md)에 대해 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.
