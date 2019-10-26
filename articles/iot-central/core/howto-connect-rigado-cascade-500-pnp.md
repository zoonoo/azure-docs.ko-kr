---
title: Azure IoT Central에서 Rigado Cascade 500 연결 | Microsoft Docs
description: IoT Central 응용 프로그램에 Rigado Cascade 500 게이트웨이 장치를 연결 하는 방법에 대해 알아봅니다.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: 8727de7b13b6ac036e714ac5aca7ae017200097c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951225"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Rigado Cascade 500 게이트웨이 장치를 Azure IoT Central 응용 프로그램에 연결


이 문서에서는 솔루션 빌더를 통해 Microsoft Azure IoT Central 응용 프로그램에 Rigado Cascade 500 게이트웨이 장치를 연결할 수 있는 방법을 설명 합니다. 

## <a name="what-is-cascade-500"></a>Cascade 500 이란?

Cascade 500 IoT gateway는 Rigado에서 제공 하는 하드웨어 제품으로, Cascade 최첨단 서비스 솔루션의 일부로 포함 되어 있습니다. 유연한 edge 컴퓨팅 기능, 강력한 컨테이너 화 된 응용 프로그램 환경 및 Bluetooth 5, LTE & Wi-fi를 비롯 한 다양 한 무선 장치 연결 옵션을 제공 하는 상용 IoT 프로젝트 및 제품 팀을 제공 합니다.

Cascade 500은 솔루션 빌더가 종단 간 솔루션에 장치를 쉽게 등록할 수 있도록 Azure IoT 플러그 앤 플레이 (PnP)를 미리 인증 합니다. Cascade gateway를 사용 하면 게이트웨이 장치와 근접 한 다양 한 조건 모니터링 센서에 무선으로 연결할 수 있습니다. 이러한 센서는 게이트웨이 장치를 통해 IoT Central에 등록 수 있습니다.

## <a name="prerequisites"></a>전제 조건
이 방법 가이드를 단계별로 실행 하려면 다음 리소스가 필요 합니다.

* Rigado Cascade 500 장치입니다. 자세한 내용은 [Rigado](https://www.rigado.com/)를 참조 하세요.
* Preview 응용 프로그램 템플릿 중 하나에서 만든 Azure IoT Central 응용 프로그램입니다. 자세한 내용은 [새 응용 프로그램 만들기](https://docs.microsoft.com/azure/iot-central/quick-deploy-iot-central-pnp?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)를 참조 하세요.

## <a name="add-a-device-template"></a>디바이스 템플릿 추가

캐스케이드 500 게이트웨이 장치를 Azure IoT Central 응용 프로그램 인스턴스에 등록 하려면 응용 프로그램 내에서 해당 하는 장치 템플릿을 구성 해야 합니다.

캐스케이드 500 장치 템플릿을 추가 하려면: 

1. 왼쪽 창에서 ***장치 템플릿*** 탭으로 이동 하 고 **+ 새로**만들기: ![새 장치 템플릿 만들기를 선택](./media/howto-connect-rigado-cascade500-pnp/device-template-new.png)
1. 이 페이지에서는 ***사용자 지정 템플릿을 만들거나*** ***미리 구성 된 장치 템플릿을 사용할*** 수 있는 옵션을 제공 합니다.
1. 아래와 같이 미리 구성 된 장치 템플릿 목록에서 C500 장치 템플릿을 선택 합니다. ![C500 장치 템플릿을 선택](./media/howto-connect-rigado-cascade500-pnp/device-template-preconfigured.png)
1. 다음 ***: 사용자 지정*** 을 선택 하 여 다음 단계를 계속 합니다. 
1. 다음 화면에서 ***만들기*** 를 선택 하 여 IoT Central 응용 프로그램에 C500 장치 템플릿을 등록 합니다.

## <a name="retrieve-application-connection-details"></a>응용 프로그램 연결 정보 검색

이제 캐스케이드 500 장치를 연결 하기 위해 Azure IoT Central 응용 프로그램에 대 한 **범위 ID** 및 **기본 키** 를 검색 해야 합니다. 

1. 왼쪽 창에서 **관리** 로 이동 하 여 **장치 연결**을 클릭 합니다. 
2. IoT Central 응용 프로그램의 **범위 ID** 를 적어 둡니다.
![앱 범위 ID](./media/howto-connect-rigado-cascade500-pnp/app-scope-id.png)
3. 이제 **키 보기** 를 클릭 하 **고 기본 키
![** 기본 키를 적어 둡니다](./media/howto-connect-rigado-cascade500-pnp/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>게이트웨이를 연결 하려면 Rigado에 문의 하세요. 

Cascade 500 장치를 IoT Central 응용 프로그램에 연결 하려면 Rigado에 문의 하 고 위의 단계에서 제공 하는 응용 프로그램 연결 세부 정보를 제공 해야 합니다. 

장치가 인터넷에 연결 되 면 Rigado은 보안 채널을 통해 케스케이드 500 게이트웨이 장치에 구성 업데이트를 푸시할 수 있습니다. 

이 업데이트는 캐스케이드 500 장치에 IoT Central 연결 정보를 적용 하 고 장치 목록에 표시 됩니다. 

![기본 키](./media/howto-connect-rigado-cascade500-pnp/devices-list-c500.png)  

이제 IoT Central 응용 프로그램에서 C500 장치를 사용할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

Rigado Cascade 500을 Azure IoT Central 응용 프로그램에 연결 하는 방법을 배웠으므로 이제 제안 된 다음 단계는 종단 간 솔루션을 빌드하는 [매장 내 분석 응용 프로그램을 만드는](../retail/tutorial-in-store-analytics-create-app-pnp.md) 방법을 알아보는 것입니다. 