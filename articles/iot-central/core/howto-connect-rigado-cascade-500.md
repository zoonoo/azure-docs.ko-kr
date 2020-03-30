---
title: Azure IoT 센트럴에서 리가도 캐스케이드 500 연결 | 마이크로 소프트 문서
description: 리가도 캐스케이드 500 게이트웨이 장치를 IoT Central 애플리케이션에 연결하는 방법을 알아보십시오.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: d52366684d772f91b53a1ab385b51ae4f11f0a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158374"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>리가도 캐스케이드 500 게이트웨이 장치를 Azure IoT Central 애플리케이션에 연결


이 문서에서는 솔루션 빌더로서 Rigado Cascade 500 게이트웨이 장치를 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법에 대해 설명합니다. 

## <a name="what-is-cascade-500"></a>캐스케이드 500이란?

Cascade 500 IoT 게이트웨이는 리가도의 하드웨어 제품으로, 캐스케이드 에지-어-서비스 솔루션의 일부로 포함되어 있습니다. 상용 IoT 프로젝트 및 제품 팀에게 유연한 에지 컴퓨팅 파워, 견고한 컨테이너형 애플리케이션 환경, Bluetooth 5, LTE, & Wi-Fi 를 비롯한 다양한 무선 장치 연결 옵션을 제공합니다.

Cascade 500은 Azure IoT 플러그 앤 플레이(미리 보기)에 대해 사전 인증을 받았으며, 솔루션 빌더가 장치를 엔드 투 엔드 솔루션에 쉽게 온보온할 수 있도록 합니다. Cascade 게이트웨이를 사용하면 게이트웨이 장치에 근접한 다양한 상태 모니터링 센서에 무선으로 연결할 수 있습니다. 이러한 센서는 게이트웨이 장치를 통해 IoT Central에 온보온할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 단계별로 진행하려면 다음 리소스가 필요합니다.

* 리가도 캐스케이드 500 장치. 자세한 내용은 [리가도를](https://www.rigado.com/)방문하십시오.
* Azure IoT Central 애플리케이션. 자세한 내용은 새 [응용 프로그램 만들기](./quick-deploy-iot-central.md)를 참조하십시오.

## <a name="add-a-device-template"></a>디바이스 템플릿 추가

Cascade 500 게이트웨이 장치를 Azure IoT Central 응용 프로그램 인스턴스에 온보싱하려면 응용 프로그램 내에서 해당 장치 템플릿을 구성해야 합니다.

캐스케이드 500 장치 템플릿을 추가하려면 다음을 수행하십시오. 

1. 왼쪽 창의 ***장치 템플릿*** 탭으로 이동, **+New**: ![새 장치 템플릿 만들기](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. 이 ***페이지에서는 사용자 지정 템플릿 을 만들거나*** ***미리 구성된 장치 템플릿을 사용할*** 수 있는 옵션이 있습니다.
1. 아래와 같이 미리 구성된 장치 템플릿 목록에서 C500 장치 ![템플릿 선택: C500 장치 템플릿 선택](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. 다음: 다음 단계로 계속 하려면 ***사용자 지정을*** 선택 합니다. 
1. 다음 화면에서 C500 장치 템플릿을 IoT Central 응용 프로그램에 온보로 등록하려면 ***만들기를*** 선택합니다.

## <a name="retrieve-application-connection-details"></a>응용 프로그램 연결 세부 정보 검색

이제 Cascade 500 장치를 연결하려면 Azure IoT Central 응용 프로그램에 대한 **범위 ID** 및 **기본 키를** 검색해야 합니다. 

1. 왼쪽 창에서 **관리로** 이동하여 **장치 연결을**클릭합니다. 
2. IoT 중앙 응용 프로그램에 대한 **Scope ID를** 기록합니다.
![앱 범위 ID](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. 이제 **보기 키를** 클릭하고 **기본 키**
![기본 키를 기록합니다.](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>게이트웨이를 연결하려면 리가도에 문의하십시오. 

Cascade 500 장치를 IoT Central 애플리케이션에 연결하려면 Rigado에 연락하여 위의 단계의 응용 프로그램 연결 세부 정보를 제공해야 합니다. 

장치가 인터넷에 연결되면 Rigado는 보안 채널을 통해 Cascade 500 게이트웨이 장치로 구성 업데이트를 푸시다운할 수 있습니다. 

이 업데이트는 Cascade 500 장치에 IoT 중앙 연결 세부 정보를 적용하고 장치 목록에 나타납니다. 

![기본 키](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

이제 IoT Central 애플리케이션에서 C500 장치를 사용할 준비가 되었습니다!

## <a name="next-steps"></a>다음 단계

이제 Rigado Cascade 500을 Azure IoT Central 응용 프로그램에 연결하는 방법을 배웠으니 다음 단계는 엔드 투 엔드 솔루션을 빌드하기 위해 [매장 내 분석 응용 프로그램을 만드는](../retail/tutorial-in-store-analytics-create-app-pnp.md) 방법을 알아보는 것입니다. 