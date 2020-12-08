---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017519"
---
Azure IoT Central 애플리케이션의 운영자는 다음을 수행할 수 있습니다.

* **개요** 페이지에서 디바이스에서 보낸 원격 분석을 봅니다.

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="디바이스 원격 분석 보기":::

* **정보** 페이지에서 디바이스 속성을 봅니다.

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="디바이스 속성 보기":::

## <a name="customize-the-device-template"></a>디바이스 템플릿 사용자 지정

솔루션 개발자는 자동 온도 조절 디바이스가 연결되면 IoT Central이 자동으로 만든 디바이스 템플릿을 사용자 지정할 수 있습니다.

디바이스와 연결된 고객 이름을 저장하는 클라우드 속성을 추가하려면 다음을 수행합니다.

1. IoT Central 애플리케이션에서 **디바이스 템플릿** 페이지의 **자동 온도 조절** 디바이스 템플릿으로 이동합니다.

1. **자동 온도 조절** 디바이스 템플릿에서 **클라우드 속성** 을 선택합니다.

1. **클라우드 속성 추가** 를 선택합니다. *고객 이름* 을 **표시 이름** 으로 입력하고 **스키마** 로 **문자열** 을 선택합니다. 그런 다음 **저장** 을 선택합니다.

IoT Central 애플리케이션에서 **Get Max-Min report** 명령이 표시되는 방식을 사용자 지정하려면 디바이스 템플릿에서 **사용자 지정** 을 선택합니다. **최대-최소 보고서 가져오기** 를 대체합니다. *상태 보고서 가져오기*. 그런 다음 **저장** 을 선택합니다.

**자동 온도 조절** 모델에는 **대상 온도** 쓰기 가능 속성이 포함되고, 디바이스 템플릿에는 **고객 이름** 클라우드 속성이 포함됩니다. 운영자가 다음 속성을 편집하는 데 사용할 수 있는 보기를 만듭니다.

1. **보기**, **디바이스 및 클라우드 데이터를 편집하는 중** 타일을 차례로 선택합니다.

1. 양식 이름으로 _속성_ 을 입력합니다.

1. **대상 온도** 및 **고객 이름** 속성을 선택합니다. 그런 다음, **섹션 추가** 를 선택합니다.

1. 변경 내용을 저장합니다.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="속성 값 업데이트 보기":::

## <a name="publish-the-device-template"></a>디바이스 템플릿 게시

운영자가 만든 사용자 지정을 보고 사용하려면 먼저 디바이스 템플릿을 게시해야 합니다.

**자동 온도 조절** 디바이스 템플릿에서 **게시** 를 선택합니다. **이 디바이스 템플릿을 애플리케이션에 게시** 패널에서 **게시** 를 선택합니다.

이제 운영자는 **속성** 보기를 사용하여 속성 값을 업데이트하고 디바이스 명령 페이지에서 **상태 보고서 가져오기** 라는 명령을 호출할 수 있습니다.

* **속성** 페이지에서 쓰기 가능한 속성 값을 업데이트합니다.

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="디바이스 속성 업데이트":::

* **명령** 페이지에서 명령을 호출합니다.

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="명령 호출":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="명령 응답 보기":::
