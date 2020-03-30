---
title: Azure IoT 센트럴에서 루비태그 연결 | 마이크로 소프트 문서
description: RuuviTag 환경 센서를 IoT 중앙 애플리케이션에 연결하는 방법을 알아보십시오.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: e8d1c4a605e8db2e9753bb80c9712dd6c2be7b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158250"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>루비태그 센서를 Azure IoT 중앙 애플리케이션에 연결

이 문서에서는 솔루션 빌더로서 RuuviTag 센서를 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법에 대해 설명합니다.

루비 태그란?

RuuviTag는 비즈니스 고객, 개발자, 제작자, 학생 및 취미의 요구를 충족하도록 설계된 고급 오픈 소스 센서 비콘 플랫폼입니다. 이 장치는 즉시 즉시 작동하도록 설정되어 있으며 필요한 곳에 배포할 준비가 되었습니다. 환경 센서와 가속도계가 내장된 블루투스 LE 비콘입니다.

RuuviTag는 BLE(Bluetooth 저에너지)을 통해 통신하며 Azure IoT Central과 통신하려면 게이트웨이 장치가 필요합니다. RuuviTag가 IoT Central에 연결할 수 있도록 하는 Rigado Cascade 500과 같은 게이트웨이 장치가 설정되어 있는지 확인합니다.

리가도 캐스케이드 500 게이트웨이 장치를 설정하려면 [여기의 지침을](./howto-connect-rigado-cascade-500.md) 따르십시오.

## <a name="prerequisites"></a>사전 요구 사항

RuuviTag 센서를 연결하려면 다음 리소스가 필요합니다.

* 루비태그 센서. 자세한 내용은 [RuuviTag](https://ruuvi.com/)를 방문하십시오.
* 리가도 캐스케이드 500 장치 또는 다른 BLE 게이트웨이. 자세한 내용은 [리가도를](https://www.rigado.com/)방문하십시오.
* Azure IoT Central 애플리케이션. 자세한 내용은 새 [응용 프로그램 만들기](./quick-deploy-iot-central.md)를 참조하십시오.

## <a name="add-a-ruuvitag-device-template"></a>RuuviTag 장치 템플릿 추가

RuuviTag 센서를 Azure IoT Central 응용 프로그램 인스턴스에 온보싱하려면 응용 프로그램 내에서 해당 장치 템플릿을 구성해야 합니다.

RuuviTag 장치 템플릿을 추가하려면 다음을 수행하십시오.

1. 왼쪽 창에서 ***장치 템플릿*** 탭으로 이동, 선택 + **새**](./media/howto-connect-ruuvi/devicetemplate-new.png) : 새 장치 템플릿 만들기 페이지에서 ![ ***사용자 지정 템플릿 만들기*** 또는 미리 구성된 장치 템플릿 ***사용*** 옵션을 제공합니다.
1. 아래와 같이 미리 구성된 장치 템플릿 목록에서 RuuviTag 장치 ![템플릿 선택: RuuviTag 장치 템플릿 선택](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. 다음: 다음 단계로 계속 하려면 ***사용자 지정을*** 선택 합니다.
1. 다음 화면에서 C500 장치 템플릿을 IoT Central 응용 프로그램에 온보로 등록하려면 ***만들기를*** 선택합니다.

## <a name="connect-a-ruuvitag-sensor"></a>루비태그 센서 연결

앞에서 설명한 것처럼 RuuviTag를 IoT Central 응용 프로그램과 연결하려면 게이트웨이 장치를 설정해야 합니다. 아래 단계는 리가도 캐스케이드 500 게이트웨이 장치를 설정했다고 가정합니다.  

1. 리가도 캐스케이드 500 장치의 전원을 켜고 이더넷 또는 무선을 통해 네트워크 연결에 연결합니다.
1. RuuviTag의 덮개를 팝하고 플라스틱 탭을 당겨 배터리와의 연결을 고정합니다.
1. IoT 중앙 응용 프로그램에서 이미 구성된 리가도 캐스케이드 500 게이트웨이에 RuuviTag를 가까이 배치합니다.
1. 단 몇 초 만에 RuuviTag가 IoT Central 내의 장치 목록에 나타납니다.  
    ![루비태그 장치 목록](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

이제 IoT 중앙 응용 프로그램에서 이 RuuviTag를 사용할 수 있습니다.  

## <a name="create-a-simulated-ruuvitag"></a>시뮬레이션된 RuuviTag 만들기

실제 RuuviTag 장치가 없는 경우 Azure IoT Central 응용 프로그램 내에서 테스트에 사용할 시뮬레이션된 RuuviTag 센서를 만들 수 있습니다.

시뮬레이션된 RuuviTag를 만들려면 다음을 수행합니다.

1. **RuuviTag에 > 장치를**선택합니다.
1. **+ 새**.
1. 고유한 **장치 ID와** 친숙한 **장치 이름을**지정합니다.  
1. 시뮬레이션 **된** 설정을 사용 합니다.
1. **만들기**를 선택합니다.  

## <a name="next-steps"></a>다음 단계

RuuviTag를 Azure IoT Central 응용 프로그램에 연결하는 방법을 배웠으니 다음 단계는 [IoT Central 응용 프로그램을 사용자 지정하여](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) 종단 간 솔루션을 빌드하는 방법을 알아보는 것입니다.
