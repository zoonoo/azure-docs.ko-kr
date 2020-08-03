---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 07/07/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b32465091f82fec0aeae288ee9bfd5540bfe8b9d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002049"
---
## <a name="create-a-device-template"></a>디바이스 템플릿 만들기

`environmental-sensor`라는 폴더를 로컬 머신에 만듭니다.

[환경 센서 기능 모델](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON 파일을 다운로드하여 `environmental-sensor` 폴더에 저장합니다.

텍스트 편집기를 사용하여 다운로드한 `EnvironmentalSensorInline.capabilitymodel.json` 파일에서 `{YOUR_COMPANY_NAME_HERE}`의 두 인스턴스를 회사 이름으로 바꿉니다. a-z, A-Z, 0-9 및 밑줄 문자만 사용합니다.

Azure IoT Central 애플리케이션에서 `EnvironmentalSensorInline.capabilitymodel.json` 디바이스 기능 모델 파일을 가져와서 *환경 센서*라는 디바이스 템플릿을 만듭니다.

![가져온 디바이스 기능 모델을 사용하는 디바이스 템플릿](./media/iot-central-add-environmental-sensor/device-template.png)

디바이스 기능 모델에는 표준 **디바이스 정보** 인터페이스와 사용자 지정 **환경 센서** 인터페이스라는 두 개의 인터페이스가 포함되어 있습니다. **환경 센서** 인터페이스에서 정의하는 기능은 다음과 같습니다.

| Type | 표시 이름 | Description |
| ---- | ------------ | ----------- |
| 속성 | 디바이스 상태     | 디바이스 상태입니다. 온라인/오프라인이라는 두 가지 상태를 사용할 수 있습니다. |
| 속성(쓰기 가능) | 고객 이름    | 현재 디바이스를 작동하는 고객의 이름입니다. |
| 속성(쓰기 가능) | 밝기 수준 | 디바이스의 표시등에 대한 밝기 수준입니다. 1(높음), 2(중간), 3(낮음)으로 지정할 수 있습니다. |
| 원격 분석 | 온도 | 디바이스에서 감지한 현재 온도입니다. |
| 원격 분석 | 습도    | 디바이스에서 감지한 현재 습도입니다. |
| 명령 | blink          | 지정된 시간 간격 동안 디바이스의 LED가 깜박이기 시작합니다. |
| 명령 | turnon         | 디바이스의 LED를 켭니다. |
| 명령 | turnoff        | 디바이스의 LED를 끕니다. |
| 명령 | rundiagnostics | 이 비동기 명령은 디바이스에서 진단 실행을 시작합니다. |

IoT Central 애플리케이션에서 **디바이스 상태** 속성이 표시되는 방식을 사용자 지정하려면 디바이스 템플릿에서 **사용자 지정**을 선택합니다. **디바이스 상태** 항목을 펼치고, **True 이름**으로 _온라인_을 입력하고, **False 이름**으로 _오프라인_을 입력합니다. 그런 다음, 변경 내용을 저장합니다.

![디바이스 템플릿 사용자 지정](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>뷰 만들기

보기를 통해 IoT Central 애플리케이션에 연결된 디바이스와 상호 작용할 수 있습니다. 예를 들어 원격 분석을 표시하는 보기, 속성을 표시하는 보기, 쓰기 가능 및 클라우드 속성을 편집할 수 있는 보기를 사용할 수 있습니다. 보기는 디바이스 템플릿의 일부입니다.

일부 기본 보기를 **환경 센서** 디바이스 템플릿에 추가하려면 디바이스 템플릿으로 이동하여 **보기**, **기본 보기 생성** 타일을 차례로 선택합니다. **개요** 및 **정보**가 **켜기**인지 확인한 다음, **기본 대시보드 보기 생성**을 선택합니다. 이제 두 개의 기본 보기가 템플릿에 정의됩니다.

**환경 센서** 인터페이스에는 **고객 이름** 및 **밝기 수준**의 쓰기 가능한 속성이 포함되어 있습니다. 보기를 만들려면 다음 속성을 사용하여 편집할 수 있습니다.

1. **보기**, **디바이스 및 클라우드 데이터를 편집하는 중** 타일을 차례로 선택합니다.

1. 양식 이름으로 _속성_을 입력합니다.

1. **밝기 수준** 및 **고객 이름** 속성을 선택합니다. 그런 다음, **섹션 추가**를 선택합니다.

1. 변경 내용을 저장합니다.

![속성을 편집하는 데 사용할 수 있는 보기 추가](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>템플릿 게시

**환경 센서** 디바이스 템플릿을 사용하는 디바이스를 추가하려면 먼저 이를 게시해야 합니다.

디바이스 템플릿에서 **게시**를 선택합니다. **이 디바이스 템플릿을 애플리케이션에 게시** 패널에서 **게시**를 선택합니다.

템플릿을 사용할 준비가 되었는지 확인하려면 IoT Central 애플리케이션에서 **디바이스** 페이지로 이동합니다. **디바이스** 섹션에는 애플리케이션에 게시된 디바이스의 목록이 표시됩니다.

![디바이스 페이지에 게시된 템플릿](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 애플리케이션에서 실제 디바이스를 이전 섹션에서 만든 디바이스 템플릿에 추가합니다.

1. **디바이스** 페이지에서 **환경 센서** 디바이스 템플릿을 선택합니다.

1. **+새로 만들기**를 선택합니다.

1. **새 디바이스 만들기** 대화 상자에서 **환경 센서**가 템플릿 유형이고, **이 디바이스를 시뮬레이션하시겠습니까?** 가 **아니요**로 설정되어 있는지 확인합니다.

1. 그런 다음 **만들기**를 선택합니다.

디바이스 이름을 클릭한 다음, **연결**을 선택합니다. **디바이스 연결** 페이지에서 디바이스 연결 정보(**ID 범위**, **디바이스 ID** 및 **기본 키** )를 적어 둡니다. 이러한 값은 디바이스 코드를 만들 때 필요합니다.

![디바이스 연결 정보](./media/iot-central-add-environmental-sensor/device-connection.png)
