---
title: Azure IoT Central에서 연산자의 뷰 사용자 지정 | Microsoft Docs
description: 작성기로서 Azure IoT Central 응용 프로그램에서 연산자의 뷰를 사용자 지정합니다.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 070a6ee4bc712b2dbec598cdb9be5c324895c033
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154984"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>자습서: Azure IoT Central 연산자의 뷰 사용자 지정

이 자습서에서는 작성기로서 응용 프로그램의 연산자의 뷰를 사용자 지정하는 방법을 설명합니다. 작성기로서 응용 프로그램을 변경하는 경우 Microsoft Azure IoT Central 응용 프로그램에서 연산자의 뷰를 미리 볼 수 있습니다.

이 자습서에서는 연결된 공조 디바이스에 대한 관련 정보를 연산자에 표시하려면 응용 프로그램을 사용자 지정합니다. 사용자 지정을 사용하면 연산자가 응용 프로그램에 연결된 공조 디바이스를 관리하게 할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 디바이스 대시보드 구성
> * 디바이스 설정 레이아웃 구성
> * 디바이스 속성 레이아웃 구성
> * 연산자로서 디바이스 미리 보기
> * 기본 홈 페이지 구성
> * 연산자로서 기본 홈 페이지 미리 보기

## <a name="prerequisites"></a>필수 조건

시작하기 전에 이전의 두 자습서를 완료해야 합니다.

* [Azure IoT Central 응용 프로그램에서 새 장치 유형을 정의합니다](tutorial-define-device-type.md).
* [장치에 대한 규칙 및 작업을 구성합니다](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>디바이스 대시보드 구성

작성기로서 디바이스 대시보드에 표시되는 정보를 정의할 수 있습니다. [응용 프로그램에서 새 장치 유형 정의](tutorial-define-device-type.md) 자습서에서 꺾은선형 차트 및 기타 정보를 **연결된 공조 장치-1** 대시보드에 추가했습니다.

1. **연결된 공조 장치** 템플릿을 편집하려면 왼쪽 탐색 메뉴에서 **탐색기**를 선택합니다.

    ![탐색기 페이지](media/tutorial-customize-operator/explorer.png)

2. 연결된 공조 디바이스 대시보드의 사용자 지정을 시작하려면 **연결된 공조 디바이스(1.0.0)** 템플릿을 선택합니다. [응용 프로그램에서 새 장치 유형 정의](tutorial-define-device-type.md) 자습서에서 만든 **연결된 공조 장치-1**을 선택합니다.

    ![연결된 공조 디바이스 선택](media/tutorial-customize-operator/selectdevice.png)

    **연결된 공조 장치-1**과 같은 장치 내에서 **템플릿 편집**을 선택하여 기본 템플릿을 변경할 수 있습니다. 자세한 내용은 [새 디바이스 템플릿 버전 만들기](howto-version-devicetemplate.md)를 참조하세요.

3. 대시보드를 편집하려면 **대시보드**를 선택하고, **템플릿 편집**을 선택합니다.

    ![디바이스 템플릿 대시보드 페이지](media/tutorial-customize-operator/dashboard.png)

4. 대시보드에 KPI(핵심 성과 지표) 타일을 추가하려면 **KPI**를 선택합니다.

    ![KPI 추가](media/tutorial-customize-operator/addkpi.png)

    KPI를 정의하려면 다음 표의 정보를 사용합니다.

    | 설정     | 값 |
    | ----------- | ----- |
    | 이름        | 최대 온도 |
    | 측정 | 온도 |
    | 집계 | 최대 |
    | 시간 범위  | 지난 1주 |

5. **저장**을 선택합니다. 이제 대시보드에서 KPI 타일을 확인할 수 있습니다.

    ![KPI 타일](media/tutorial-customize-operator/temperaturekpi.png)

6. 대시보드에서 타일을 이동하거나 크기를 조정하려면 타일 위로 마우스 포인터를 이동합니다. 타일을 새 위치로 끌거나 크기를 조정할 수 있습니다.

    ![대시보드 레이아웃 편집](media/tutorial-customize-operator/dashboardlayout.png)

7. 변경 작업을 마쳤으면 **완료**를 클릭합니다.

## <a name="configure-your-settings-layout"></a>설정 레이아웃 구성

작성기로서 디바이스 설정의 연산자 보기를 구성할 수 있습니다. 연산자는 디바이스 설정 페이지를 사용하여 디바이스를 구성합니다. 예를 들어 연산자는 연결된 공조 장치에 대한 대상 온도를 설정하기 위한 설정 페이지를 사용합니다.

1. 연결된 공조 장치에 대한 설정 레이아웃을 편집하려면 **설정**을 선택하고, **템플릿 편집**을 선택합니다.

    ![설정 페이지](media/tutorial-customize-operator/settings.png)

2. 설정 타일을 이동하고 크기를 조정할 수 있습니다.

    ![설정 레이아웃 편집](media/tutorial-customize-operator/settingslayout.png)

3. 변경 작업을 마쳤으면 **완료**를 클릭합니다.

> [!NOTE]
> **템플릿 편집** 모드에서는 설정 값을 편집할 수 없습니다.

## <a name="configure-your-properties-layout"></a>속성 레이아웃 구성

대시보드 및 설정 외에도 디바이스 속성의 연산자 보기를 구성할 수 있습니다. 연산자는 디바이스 속성 페이지를 사용하여 디바이스 메타데이터를 관리합니다. 예를 들어 연산자는 속성 페이지를 사용하여 디바이스 일련 번호를 확인하거나 제조업체의 연락처 세부 정보를 업데이트합니다.

1. 연결된 공조 장치에 대한 속성 레이아웃을 편집하려면 **속성**을 선택하고, **템플릿 편집**을 선택합니다.

    ![속성 페이지](media/tutorial-customize-operator/properties.png)

2. 속성 필드를 이동하고 크기를 조정할 수 있습니다.

    ![속성 레이아웃 편집](media/tutorial-customize-operator/propertieslayout.png)

3. 변경 작업을 마쳤으면 **완료**를 클릭합니다.

> [!NOTE]
> **템플릿 편집** 모드에서는 속성 값을 편집할 수 없습니다.

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>연산자로서 연결된 공조 디바이스 미리 보기

**템플릿 편집** 모드에서 연산자에 대한 대시보드, 설정 및 속성 페이지를 사용자 지정할 수 있습니다. **템플릿 편집** 모드에 있지 않은 경우 운영자 권한으로 응용 프로그램을 볼 수 있습니다.

1. 운영자로서 연결된 공조 디바이스를 보려면 **완료**를 클릭하여 템플릿 편집을 중지하고 운영자의 디바이스 보기로 돌아갑니다.

2. 이 디바이스의 위치를 업데이트하려면 위치 타일에서 값을 편집하고 **저장**을 선택합니다.

    ![속성 값 편집](media/tutorial-customize-operator/editproperty.png)

3. 연결된 공조 장치에 설정을 보내려면 **설정**을 선택하고 타일에서 설정 값을 변경하고 **업데이트**를 선택합니다.

    ![디바이스에 설정 보내기](media/tutorial-customize-operator/sendsetting.png)

    디바이스가 새 설정 값을 인식하는 경우 설정은 타일에 **동기화됨**으로 표시됩니다.

4. 연산자로서 작성기가 구성한 대로 디바이스 대시보드를 볼 수 있습니다.

    ![디바이스 대시보드의 연산자의 보기](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>기본 홈 페이지 구성

작성기 또는 연산자가 Azure IoT Central 응용 프로그램에 로그인하면 홈 페이지가 표시됩니다. 작성기로서 연산자에 대한 가장 유용하고 관련이 큰 콘텐츠를 포함하려면 이 홈 페이지의 콘텐츠를 구성할 수 있습니다.

1. 기본 홈 페이지를 사용자 지정하려면 **홈** 페이지로 이동하여 페이지의 오른쪽 상단에서 **편집**을 선택합니다. **편집**을 선택하면 패널은 홈 페이지에 추가할 수 있는 개체의 목록을 사용하여 오른쪽에서 슬라이드 아웃됩니다.

    ![응용 프로그램 개발자 페이지](media/tutorial-customize-operator/builderhome.png)

2. 홈 페이지를 사용자 지정하려면 **라이브러리**에서 타일을 추가합니다. **링크**를 선택하고 조직의 웹 사이트의 세부 정보를 추가합니다. 그런 다음, **저장**을 선택합니다.

    ![홈페이지에 링크 추가](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Azure IoT Central 응용 프로그램 내에서 페이지에 링크를 추가할 수 있습니다. 예를 들어 디바이스 대시보드 또는 설정 페이지에 링크를 추가할 수 있습니다.

3. 필요에 따라 **이미지**를 선택하고 홈 페이지에 표시할 이미지를 업로드합니다. 이미지에는 클릭하면 이동할 URL이 포함될 수 있습니다.

    ![홈 페이지에 이미지 추가](media/tutorial-customize-operator/addimage.png)

    자세한 내용은 [Azure IoT Central 응용 프로그램에 이미지를 업로드하고 준비하는 방법](howto-prepare-images.md)을 참조합니다.

## <a name="preview-the-default-home-page-as-an-operator"></a>연산자로서 기본 홈 페이지 미리 보기

운영자로서 홈 페이지를 미리 보기하고 더 이상 편집을 하지 않으려면 페이지의 오른쪽 상단에서 **완료**를 선택합니다.

![디자인 모드 설정/해제](media/tutorial-customize-operator/operatorviewhome.png)

링크 및 이미지 타일을 클릭하여 작성기로서 설정한 URL로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 응용 프로그램의 연산자의 뷰를 사용자 지정하는 방법을 알아보았습니다.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 디바이스 대시보드 구성
> * 디바이스 설정 레이아웃 구성
> * 디바이스 속성 레이아웃 구성
> * 연산자로서 디바이스 미리 보기
> * 기본 홈 페이지 구성
> * 연산자로서 기본 홈 페이지 미리 보기

응용 프로그램의 연산자의 뷰를 사용자 지정하는 방법을 알아보았으므로 제안된 다음 단계는 다음과 같습니다.

* [장치 모니터링(연산자로서)](tutorial-monitor-devices.md)
* [응용 프로그램에 새 장치 추가(연산자 및 장치 개발자로서)](tutorial-add-device.md)
