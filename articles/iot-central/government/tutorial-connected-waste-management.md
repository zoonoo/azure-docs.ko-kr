---
title: '자습서: Azure IoT Central에서 연결된 폐기물 관리 앱 만들기'
description: '자습서: Azure IoT Central 애플리케이션 템플릿을 사용하여 연결된 폐기물 관리 애플리케이션을 빌드하는 방법 알아보기'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 65245b3c4b7e18670682f7e9e890453e32337644
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99822091"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>자습서: 연결된 폐기물 관리 앱 만들기

이 자습서에서는 Azure IoT Central을 사용하여 연결된 폐기물 관리 애플리케이션을 만드는 방법을 보여줍니다. 

특히 다음 방법을 알아봅니다. 

> [!div class="checklist"]
> Azure IoT Central *연결된 폐기물 관리* 템플릿을 사용하여 앱을 만듭니다.
> 운영자 대시보드를 살펴보기 및 사용자 지정합니다. 연결된 폐기물 수집통 디바이스 템플릿을 살펴봅니다.
> 시뮬레이션된 디바이스를 살펴봅니다.
> 규칙을 살펴보고 구성합니다.
> 작업을 구성합니다.
> 애플리케이션 브랜딩을 사용자 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 권장됩니다. 대신 무료 7일 평가판을 사용할 수 있습니다. Azure 구독이 아직 없는 경우 [Azure 가입 페이지](https://aka.ms/createazuresubscription)에서 만들 수 있습니다.

## <a name="create-your-app-in-azure-iot-central"></a>Azure IoT Central에서 앱 만들기

이 섹션에서는 연결된 폐기물 관리 템플릿을 사용하여 Azure IoT Central에서 앱을 만듭니다. 방법은 다음과 같습니다.

1. [Azure IoT Central](https://aka.ms/iotcentral)로 이동합니다.

    Azure 구독이 있으면 액세스하는 데 사용하는 자격 증명을 사용하여 로그인합니다. 그렇지 않으면 Microsoft 계정을 사용하여 로그인합니다.

    ![Microsoft 로그인의 스크린샷.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. 왼쪽 창에서 **빌드** 를 선택합니다. 그런 다음, **정부** 탭을 선택합니다. 정부 페이지에는 몇 가지 정부 애플리케이션 템플릿이 표시됩니다.

    ![Azure IoT Central 빌드 페이지의 스크린샷.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. **연결된 폐기물 관리** 애플리케이션 템플릿을 선택합니다. 이 템플릿에는 연결된 폐기물 수집통 디바이스 템플릿 샘플, 시뮬레이션된 디바이스, 운영자 대시보드 및 미리 구성된 모니터링 규칙이 포함되어 있습니다.    

1. **앱 만들기** 를 선택하면 **새 애플리케이션** 대화 상자가 열립니다. 다음 필드에 대한 정보를 입력합니다.
    * **애플리케이션 이름**. 기본적으로 이 애플리케이션에는 Azure IoT Central이 생성하는 고유한 ID 문자열 다음에 오는 **연결된 폐기물 관리** 가 사용됩니다. 필요에 따라 친숙한 애플리케이션 이름을 선택할 수 있습니다. 애플리케이션 이름은 나중에 변경할 수도 있습니다.
    * **URL**. 선택적으로 원하는 URL을 선택할 수 있습니다. 나중에 URL을 변경할 수 있습니다. 
    * **요금제**. Azure 구독이 있는 경우 **청구 정보** 대화 상자의 해당 필드에 디렉터리, Azure 구독 및 지역을 입력합니다. 구독이 없는 경우 **무료** 를 선택하여 7일 평가판 구독을 활성화하고 필요한 연락처 정보를 작성합니다.  

    디렉터리 및 구독에 대한 자세한 내용은 [빠른 시작 - Azure IoT Central 애플리케이션 만들기](../core/quick-deploy-iot-central.md)를 참조합니다.

1. 페이지의 맨 아래에서 **만들기** 를 선택합니다. 

    ![Azure IoT Central 새 애플리케이션 대화 상자 만들기의 스크린샷.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central 청구 정보 대화 상자의 스크린샷.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
새로 만든 애플리케이션은 다음 항목이 미리 구성된 상태로 제공됩니다.
* 작업자 대시보드 샘플.
* 미리 정의된 연결된 폐기물 수집통 디바이스 샘플 템플릿.
* 시뮬레이션된 연결된 폐기물 수집통 디바이스.
* 규칙 및 작업.
* 샘플 브랜딩. 

빌드 중인 애플리케이션이므로 언제든지 수정할 수 있습니다. 이제 애플리케이션을 살펴보고 일부 사용자 지정을 수행합니다.  

## <a name="explore-and-customize-the-operator-dashboard"></a>운영자 대시보드 살펴보기 및 사용자 지정 

앱을 만든 후 표시되는 **Wide World Waste 관리 대시보드** 를 살펴봅니다.

   ![Wide World Waste 관리 대시보드의 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

개발자는 운영자를 위한 대시보드 보기를 만들고 사용자 지정할 수 있습니다. 먼저 대시보드를 살펴보겠습니다. 

>[!NOTE]
>대시보드에 표시되는 모든 데이터는 시뮬레이션된 디바이스 데이터를 기반으로 하며, 다음 섹션에서 자세히 살펴보겠습니다. 

대시보드는 다음과 같은 여러 종류의 타일로 구성됩니다.

* **Wide World Waste 공공 서비스 이미지 타일**: 대시보드의 첫 번째 타일은 가상의 폐기물 공공 서비스인 "Wide World Waste"에 대한 이미지 타일입니다. 이 타일을 사용자 지정하고 고유 이미지를 추가하거나 제거할 수 있습니다. 

* **폐기물 수집통 이미지 타일**: 이미지 및 콘텐츠 타일을 사용하여 설명과 함께 모니터링되는 디바이스의 시각적 표현을 만들 수 있습니다. 

* **수집량 수준 KPI 타일**: 이 타일은 폐기물 수집통의 *수집량 수준* 센서로 보고되는 값을 표시합니다. 수집량 수준과 폐기물 수집통의 *냄새 측정기* 또는 *중량* 과 같은 기타 센서도 원격으로 모니터링할 수 있습니다. 운영자는 폐기물 수거 트럭 파견과 같은 작업을 수행할 수 있습니다. 

* **폐기물 모니터링 영역 지도**: 이 타일은 Azure IoT Central에서 직접 구성할 수 있는 Azure Maps를 사용합니다. 지도 타일에는 디바이스 위치가 표시됩니다. 지도 위로 커서를 이동하고 확대, 축소 또는 확장 등을 조작해보세요.

     ![연결된 폐기물 관리 템플릿 대시보드 지도의 스크린샷](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **수집량, 냄새, 중량 가로 막대형 차트**: 하나 이상의 디바이스 원격 분석 데이터를 하나의 막대형 차트로 시각화할 수 있습니다. 막대형 차트를 확장할 수도 있습니다.  

  ![연결된 폐기물 관리 템플릿 대시보드 막대형 차트의 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **필드 서비스**: 이 대시보드에는 Azure IoT Central 애플리케이션의 Dynamics 365 필드 서비스와의 통합 방법 링크가 포함되어 있습니다. 예를 들어, 필드 서비스를 사용해서 폐기물 수집 서비스를 파견하기 위한 티켓을 만들 수 있습니다. 


### <a name="customize-the-dashboard"></a>대시보드 사용자 지정 

**편집** 메뉴를 선택하여 대시보드를 사용자 지정할 수 있습니다. 그런 다음, 새 타일을 추가하거나 기존 타일을 구성할 수 있습니다. 편집 모드에서 대시보드는 다음과 같습니다. 

![연결된 폐기물 관리 템플릿 편집 모드 대시보드의 스크린샷.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

**+ 새로 만들기** 를 선택하여 새 대시보드를 만들고 처음부터 구성할 수도 있습니다. 대시보드를 여러 개 넣을 수 있으며, 대시보드 메뉴에서 대시보드 사이를 전환할 수 있습니다. 

## <a name="explore-the-device-template"></a>디바이스 템플릿 살펴보기

Azure IoT Central의 디바이스 템플릿은 디바이스 기능을 정의하며, 여기에는 원격 분석, 속성 또는 명령을 포함할 수 있습니다. 개발자는 연결된 디바이스의 기능을 나타내는 디바이스 템플릿을 정의할 수 있습니다. 

연결된 폐기물 관리 애플리케이션에는 연결된 폐기물 수집통 디바이스의 샘플 템플릿이 함께 제공됩니다.

디바이스 템플릿을 보려면 다음을 수행합니다.

1. Azure IoT Central에서 앱의 왼쪽 창에 있는 **디바이스 템플릿** 을 선택합니다. 

    ![애플리케이션의 디바이스 템플릿 목록을 보여주는 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. **디바이스 템플릿** 목록에 **연결된 폐기물 수집통** 을 선택합니다.

1. 디바이스 템플릿 기능을 검사합니다. **수집량 수준**, **냄새 측정기**, **중량**, **위치** 등의 센서가 정의된 것을 알 수 있습니다.

   ![연결된 휴지통 디바이스 템플릿의 세부 정보를 보여주는 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>디바이스 템플릿 사용자 지정

다음을 수행하여 사용자 지정 작업을 시도합니다.
1. 디바이스 템플릿 메뉴에서 **사용자 지정** 을 선택합니다.
1. **냄새 측정기** 원격 분석 유형을 찾습니다.
1. **냄새 측정기** 의 **표시 이름** 을 **냄새 수준** 으로 업데이트합니다.
1. 측정 단위를 업데이트하거나 **최솟값** 및 **최댓값** 을 설정해봅니다.
1. **저장** 을 선택합니다. 

### <a name="add-a-cloud-property"></a>클라우드 속성을 추가합니다. 

방법은 다음과 같습니다.
1. 디바이스 템플릿 메뉴에서 **클라우드 속성** 을 선택합니다.
1. **+ 클라우드 속성 추가** 를 선택합니다. Azure IoT Central에서는 디바이스와 관련이 있지만 디바이스에서 보낼 필요가 없는 속성을 추가할 수 있습니다. 예를 들어, 설치 영역, 자산 정보 또는 유지 관리 정보와 관련된 경고 임계값 등은 클라우드 속성이 될 수 있습니다. 
1. **저장** 을 선택합니다. 
 
### <a name="views"></a>보기 
연결된 폐기물 수집통 디바이스 템플릿에는 미리 정의된 보기가 포함되어 있습니다. 보기를 살펴보고 원하는 경우 업데이트합니다. 보기는 운영자가 디바이스 데이터를 보고 클라우드 속성을 입력하는 방법을 정의합니다. 

  ![연결된 폐기물 관리 템플릿 디바이스 템플릿 보기의 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>게시 

변경 작업을 수행한 경우 디바이스 템플릿을 게시해야 합니다. 

### <a name="create-a-new-device-template"></a>새 디바이스 템플릿 만들기 

새 디바이스 템플릿을 만들려면 **+ 새로 만들기** 를 선택하고 단계를 따릅니다. 사용자 지정 디바이스 템플릿을 처음부터 만들거나 Azure 디바이스 카탈로그에서 디바이스 템플릿을 선택할 수 있습니다. 

## <a name="explore-simulated-devices"></a>시뮬레이션된 디바이스 살펴보기

Azure IoT Central에서는 시뮬레이션된 디바이스를 만들어 디바이스 템플릿과 애플리케이션을 테스트할 수 있습니다. 

연결된 폐기물 관리 애플리케이션에는 연결된 폐기물 수집통 디바이스 템플릿과 연관된 두 가지 시뮬레이션된 디바이스가 있습니다. 

### <a name="view-the-devices"></a>디바이스 보기

1. Azure IoT Central의 왼쪽 창에서 **디바이스** 를 선택합니다. 

   ![연결된 폐기물 관리 템플릿 디바이스의 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. **연결된 폐기물 수집통** 디바이스를 선택합니다.  

     ![연결된 폐기물 관리 템플릿 디바이스 속성의 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. **클라우드 속성** 탭으로 이동합니다. **수집통 가득 참 경고 임계값** 의 값을 **95** 에서 **100** 으로 업데이트합니다. 

**디바이스 속성** 및 **디바이스 대시보드** 탭을 살펴봅니다. 

> [!NOTE]
> 모든 탭은 디바이스 템플릿 보기에서 구성되었습니다.

### <a name="add-new-devices"></a>새 디바이스 추가

**디바이스** 탭에서 **+ 새로 만들기** 를 선택하여 새 디바이스를 추가할 수 있습니다. 

## <a name="explore-and-configure-rules"></a>규칙 살펴보기 및 구성

Azure IoT Central에서는 디바이스 원격 분석을 자동으로 모니터링하고 하나 이상의 조건이 충족되면 작업을 트리거하는 규칙을 만들 수 있습니다. 이메일 알림을 보내거나 Power Automate에서 작업을 트리거링하거나 웹후크 작업을 시작하여 데이터를 다른 서비스에 보내는 작업이 포함될 수 있습니다.

연결된 폐기물 관리 애플리케이션에는 4개의 샘플 규칙이 있습니다.

### <a name="view-rules"></a>규칙 보기
1. Azure IoT Central의 왼쪽 창에서 **규칙** 을 선택합니다.

   ![연결된 폐기물 관리 템플릿 규칙의 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. **수집통 가득 참 경고** 를 선택합니다.

     ![수집통 가득 참 경고의 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. **수집통 가득 참 경고** 는 다음 조건을 확인합니다. **수집량 수준이 수집통 가득 참 경고보다 크거나 같습니다**.

    **수집통 가득 참 경고 임계값** 은 연결된 폐기물 수집통 디바이스 템플릿에서 정의된 클라우드 속성입니다. 

이제 이메일 작업을 만들겠습니다.

### <a name="create-an-email-action"></a>이메일 작업 만들기

규칙의 **작업** 목록에서 이메일 작업을 구성할 수 있습니다.
1. **+메일** 을 선택합니다. 
1. **표시 이름** 상자에서 **높은 pH 경고** 를 입력합니다.
1. **받는 사람** 에 Azure IoT Central 계정과 연결된 이메일 주소를 입력합니다. 
1. 필요에 따라 이메일 텍스트에 포함할 메모를 입력합니다.
1. **완료** > **저장** 을 선택합니다. 

구성된 조건이 충족되면 이제 이메일을 받게 됩니다.

>[!NOTE]
>조건이 충족될 때마다 애플리케이션에서 이메일을 보냅니다. 자동화된 규칙으로부터 이메일 수신을 중지하려면 규칙을 사용 안 함으로 설정합니다. 
  
새 규칙을 만들려면 **규칙** 의 왼쪽 창에서 **+ 새로 만들기** 를 선택합니다.

## <a name="configure-jobs"></a>작업 구성

Azure IoT Central 작업을 사용하면 여러 디바이스에서 디바이스 또는 클라우드 속성 업데이트를 트리거할 수 있습니다. 또한 작업을 사용하여 여러 디바이스에서 디바이스 명령을 트리거할 수도 있습니다. Azure IoT Central에서는 워크플로를 자동화합니다. 

1. Azure IoT Central의 왼쪽 창에서 **작업** 을 선택합니다. 
1. **+ 새로 만들기** 를 선택하고 하나 이상의 작업을 구성합니다. 

## <a name="customize-your-application"></a>애플리케이션 사용자 지정 

빌더는 여러 설정을 변경하여 애플리케이션에서 사용자 환경을 사용자 지정할 수 있습니다.

### <a name="change-the-application-theme"></a>애플리케이션 테마 변경

방법은 다음과 같습니다.
1. **관리** > **애플리케이션 사용자 지정** 으로 이동합니다.
1. **변경** 을 선택하여 **애플리케이션 로고** 로 업로드할 이미지를 선택합니다.
1. **변경** 을 선택하여 **브라우저 아이콘**(브라우저 탭에 나타나는 이미지)에 업로드할 이미지를 선택합니다.
1. HTML 16진수 색 코드를 추가하여 기본 브라우저 색을 바꿀 수도 있습니다. 이를 위해 **헤더** 및 **액센트** 필드를 사용합니다.

   ![연결된 폐기물 관리 템플릿 애플리케이션 사용자 지정의 스크린샷.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. 애플리케이션 이미지도 변경할 수 있습니다. **관리** > **애플리케이션 설정** > **이미지 선택** 을 선택하여 애플리케이션 이미지로 업로드할 이미지를 선택합니다.
1. 마지막으로 애플리케이션의 발행인란에서 **설정** 을 클릭하여 테마를 변경할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 더 이상 사용하지 않으려면 다음 단계를 수행하여 애플리케이션을 삭제합니다.

1. Azure IoT Central 앱의 왼쪽 창에서 **관리** 를 선택합니다.
1. **애플리케이션 설정** > **설정** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [연결된 폐기물 관리 개념](./concepts-connectedwastemanagement-architecture.md)
