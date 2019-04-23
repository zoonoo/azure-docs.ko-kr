---
title: Azure IoT Central에서 연산자의 뷰 사용자 지정 | Microsoft Docs
description: 작성기로서 Azure IoT Central 애플리케이션에서 연산자의 뷰를 사용자 지정합니다.
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 7fc42b24090b5fc68176fea2c7b993643ea24200
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678227"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>자습서: Azure IoT Central 연산자의 보기 사용자 지정

이 자습서에서는 작성기로서 애플리케이션의 연산자의 뷰를 사용자 지정하는 방법을 설명합니다. 작성기로서 애플리케이션을 변경하는 경우 Microsoft Azure IoT Central 애플리케이션에서 연산자의 뷰를 미리 볼 수 있습니다.

이 자습서에서는 연결된 공조 장치에 대한 관련 정보를 연산자에 표시하려면 애플리케이션을 사용자 지정합니다. 사용자 지정을 사용하면 연산자가 애플리케이션에 연결된 공조 장치를 관리하게 할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 디바이스 대시보드 구성
> * 디바이스 설정 레이아웃 구성
> * 디바이스 속성 레이아웃 구성
> * 연산자로서 디바이스 미리 보기
> * 기본 애플리케이션 대시보드 구성
> * 운영자 권한으로 기본 애플리케이션 대시보드 미리 보기

## <a name="prerequisites"></a>필수 조건

시작하기 전에 이전의 두 자습서를 완료해야 합니다.

* [Azure IoT Central 애플리케이션에서 새 장치 유형을 정의합니다](tutorial-define-device-type.md).
* [디바이스에 대한 규칙 및 작업을 구성합니다](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>디바이스 대시보드 구성

작성기로서 디바이스 대시보드에 표시되는 정보를 정의할 수 있습니다. [애플리케이션에서 새 디바이스 유형 정의](tutorial-define-device-type.md) 자습서에서 꺾은선형 차트 및 기타 정보를 **연결된 공조 장치** 대시보드에 추가했습니다.

1. **연결된 공조 장치** 디바이스 템플릿을 편집하려면 왼쪽 탐색 메뉴에서 **디바이스 템플릿**을 선택합니다.

    ![디바이스 템플릿 페이지](media/tutorial-customize-operator/devicetemplates.png)

2. 디바이스 대시보드를 사용자 지정하려면 [애플리케이션에서 새 디바이스 유형 정의](tutorial-define-device-type.md) 자습서에서 만든 **연결된 공조 장치(1.0.0)** 를 선택합니다.

3. 대시보드를 편집하려면 **대시보드** 탭을 선택합니다.

4. 대시보드에 KPI(핵심 성과 지표) 타일을 추가하려면 **KPI**를 선택합니다.

    KPI를 정의하려면 다음 표의 정보를 사용합니다.

    | 설정     | 값 |
    | ----------- | ----- |
    | Name        | 최대 온도 |
    | 시간 범위  | 지난 1주 |
    | 측정값 형식 | 원격 분석 |
    | 측정 | 온도 |
    | 집계 | 최대 |
    | 표시 유형  | 사용 |

    ![KPI 추가](media/tutorial-customize-operator/addkpi.png)

5. **저장**을 선택합니다. 이제 대시보드에서 KPI 타일을 확인할 수 있습니다.

    ![KPI 타일](media/tutorial-customize-operator/temperaturekpi.png)

6. 대시보드에서 타일을 이동하거나 크기를 조정하려면 타일 위로 마우스 포인터를 이동합니다. 타일을 새 위치로 끌거나 크기를 조정할 수 있습니다.

## <a name="configure-your-settings-layout"></a>설정 레이아웃 구성

작성기로서 디바이스 설정의 연산자 보기를 구성할 수 있습니다. 운영자는 디바이스 설정 탭을 사용하여 디바이스를 구성합니다. 예를 들어 운영자는 설정 탭을 사용하여 연결된 공조 장치의 목표 온도를 설정합니다.

1. 연결된 공조 장치의 설정 레이아웃을 편집하려면 **설정** 탭을 선택합니다.

2. 설정 타일을 이동하고 크기를 조정할 수 있습니다.

    ![설정 레이아웃 편집](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>속성 레이아웃 구성

대시보드 및 설정 외에도 디바이스 속성의 연산자 보기를 구성할 수 있습니다. 운영자는 디바이스 속성 탭을 사용하여 디바이스 메타데이터를 관리합니다. 예를 들어 운영자는 속성 탭을 사용하여 디바이스 일련 번호를 확인하거나 제조업체의 연락처 세부 정보를 업데이트합니다.

1. 연결된 공조 장치의 속성 레이아웃을 편집하려면 **속성** 탭을 선택합니다.

2. 속성 필드를 이동하고 크기를 조정할 수 있습니다.

    ![속성 레이아웃 편집](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>디바이스 미리 보기

**디바이스 템플릿** 페이지를 사용하여 운영자용 대시보드, 설정 및 속성 탭을 사용자 지정합니다. **Device Explorer** 페이지를 사용하여 디바이스 템플릿을 살펴보고 사용합니다.

1. 운영자로서 연결된 공조 템플릿을 사용하려면 **Device Explorer** 페이지로 이동한 후 IoT Central이 템플릿에서 생성한 시뮬레이션된 디바이스를 선택합니다.

    ![디바이스 템플릿 보기 및 사용](media/tutorial-customize-operator/usetemplate.png)

2. 이 디바이스의 위치를 업데이트하려면 [위치] 타일에서 **속성**을 선택하고 값을 편집합니다. 그런 다음, **저장**을 선택합니다.

    ![속성 값 편집](media/tutorial-customize-operator/editproperty.png)

3. 연결된 공조 장치에 설정을 보내려면 **설정**을 선택하고 타일에서 설정 값을 변경하고 **업데이트**를 선택합니다.

    ![디바이스에 설정 보내기](media/tutorial-customize-operator/sendsetting.png)

    디바이스가 새 설정 값을 인식하는 경우 설정은 타일에 **동기화됨**으로 표시됩니다.

4. 연산자로서 작성기가 구성한 대로 디바이스 대시보드를 볼 수 있습니다.

    ![디바이스 대시보드의 연산자의 보기](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>기본 대시보드 구성

개발자 또는 운영자가 Azure IoT Central 애플리케이션에 로그인하면 애플리케이션 대시보드가 표시됩니다. 개발자는 운영자에게 가장 유용하고 적절한 콘텐츠를 포함하도록 기본 대시보드의 콘텐츠를 구성할 수 있습니다.

> [!NOTE]
> 또한 사용자가 자신의 개인 대시보드를 만들고 그중 하나를 기본 대시보드로 선택할 수도 있습니다.

1. 기본 애플리케이션 대시보드를 사용자 지정하려면 **대시보드** 페이지로 이동한 후 페이지의 오른쪽 상단에서 **편집**을 선택합니다. 대시보드에 추가할 수 있는 개체 라이브러리를 포함하는 패널이 표시됩니다.

    ![대시보드 페이지](media/tutorial-customize-operator/builderhome.png)

2. 대시보드를 사용자 지정하려면 **라이브러리**에서 타일을 추가합니다. **링크**를 선택하고 조직의 웹 사이트의 세부 정보를 추가합니다. 그런 다음, **저장**을 선택합니다.

    ![대시보드에 링크 추가](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Azure IoT Central 애플리케이션 내에서 페이지에 링크를 추가할 수 있습니다. 예를 들어 디바이스 대시보드 또는 설정 페이지에 링크를 추가할 수 있습니다.

3. 필요에 따라 **이미지**를 선택하고 대시보드에 표시할 이미지를 업로드합니다. 이미지에는 선택하면 이동되는 URL이 포함될 수 있습니다.

    ![대시보드에 이미지 추가](media/tutorial-customize-operator/addimage.png)

    자세한 내용은 [Azure IoT Central 애플리케이션에 이미지를 업로드하고 준비하는 방법](howto-prepare-images.md)을 참조합니다.

## <a name="preview-the-dashboard"></a>대시보드 미리 보기

운영자로서 애플리케이션 대시보드를 미리 보려면 페이지의 오른쪽 상단에서 **완료**를 선택합니다.

![디자인 모드 설정/해제](media/tutorial-customize-operator/operatorviewhome.png)

링크 및 이미지 타일을 선택하여 개발자로서 설정한 URL로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 애플리케이션의 연산자의 뷰를 사용자 지정하는 방법을 알아보았습니다.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 디바이스 대시보드 구성
> * 디바이스 설정 레이아웃 구성
> * 디바이스 속성 레이아웃 구성
> * 연산자로서 디바이스 미리 보기
> * 기본 홈 페이지 구성
> * 연산자로서 기본 홈 페이지 미리 보기

애플리케이션의 연산자의 뷰를 사용자 지정하는 방법을 알아보았으므로 제안된 다음 단계는 다음과 같습니다.

* [디바이스 모니터링(연산자로서)](tutorial-monitor-devices.md)
* [애플리케이션에 새 장치 추가(연산자 및 장치 개발자로서)](tutorial-add-device.md)