---
title: Azure IoT Central 솔루션에서 위치 데이터 사용
description: IoT Central 응용 프로그램에 연결 된 장치에서 전송 되는 위치 데이터를 사용 하는 방법을 알아봅니다. 지도에 위치 데이터를 그리거나 지 오 펜싱 규칙을 만듭니다.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128023"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Azure IoT Central 솔루션에서 위치 데이터 사용

이 문서에서는 IoT Central 응용 프로그램에서 위치 데이터를 사용 하는 방법을 보여 줍니다. IoT Central에 연결 된 장치는 원격 분석 스트림으로 위치 데이터를 전송 하거나 장치 속성을 사용 하 여 위치 데이터를 보고할 수 있습니다.

솔루션 빌더는 위치 데이터를 사용 하 여 다음을 수행할 수 있습니다.

* 지도에 보고 된 위치를 플롯 합니다.
* 원격 분석 위치 기록 om 지도를 플롯 합니다.
* 장치가 특정 영역에 들어가거나 나갈 때 운영자에 게 알리려면 지 오 펜싱 규칙을 만듭니다.

## <a name="add-location-capabilities-to-a-device-template"></a>장치 템플릿에 위치 기능 추가

다음 스크린샷은 위치 데이터를 사용 하는 원격 분석 유형 및 장치 속성 예제가 포함 된 장치 템플릿을 보여 줍니다. 정의는 위치 의미 체계 형식 및 **지리적** **위치** 스키마 유형을 사용 합니다.

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="장치 템플릿의 위치 속성 정의를 보여 주는 스크린샷":::

참조용으로 이러한 기능에 대 한 [DTDL (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 정의는 다음 코드 조각과 같습니다.

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> **Geopoint** 스키마 유형은 [dtdl 사양의](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)일부가 아닙니다. 현재 IoT Central은 이전 버전과의 호환성을 위해 **geopoint** 스키마 형식 및 **위치** 의미 체계 형식을 지원 합니다.

## <a name="send-location-data-from-a-device"></a>장치에서 위치 데이터 보내기

장치가 이전 섹션에 표시 된 **Devicelocation** 속성에 대 한 데이터를 보내면 페이로드는 다음 JSON 코드 조각과 같습니다.

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

장치가 이전 섹션에 표시 된 **추적** 원격 분석에 대 한 데이터를 보내면 페이로드는 다음 JSON 코드 조각과 같습니다.

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>장치 위치 표시

IoT Central 응용 프로그램의 여러 위치에 위치 데이터를 표시할 수 있습니다. 예를 들어 개별 장치나 대시보드에 연결 된 보기의 경우입니다.

장치에 대 한 보기를 만들 때 지도에 위치를 플롯 하거나 개별 값을 표시 하도록 선택할 수 있습니다.

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="위치 데이터를 포함 하는 예제 뷰를 보여 주는 스크린샷":::

대시보드에 지도 타일을 추가 하 여 하나 이상의 장치 위치를 그릴 수 있습니다. 위치 원격 분석을 표시 하는 지도 타일을 추가 하는 경우 일정 기간 동안 위치를 그릴 수 있습니다. 다음 스크린샷은 지난 30 분 동안 시뮬레이션 된 장치에서 보고 한 위치를 보여 줍니다.

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="위치 데이터를 포함 하는 예제 대시보드를 보여 주는 스크린샷":::

## <a name="create-a-geofencing-rule"></a>지 오 펜싱 규칙 만들기

위치 원격 분석을 사용 하 여 장치가 사각형 영역으로 이동 하거나 외부로 이동 하는 경우 경고를 생성 하는 지 오 펜싱 규칙을 만들 수 있습니다. 다음 스크린샷에서는 네 가지 조건을 사용 하 여 위도 및 경도 값을 사용 하는 사각형 영역을 정의 하는 규칙을 보여 줍니다. 이 규칙은 장치가 사각형 영역으로 이동 하면 전자 메일을 생성 합니다.

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="지 오 펜싱 규칙 정의를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에서 속성을 사용 하는 방법을 알아보았습니다. 다음을 참조 하세요.

* [Payloads](concepts-telemetry-properties-commands.md)
* [클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)