---
title: Azure 포털 탐색을 위한 참조
description: 앱 서비스 웹에 대한 사용자 환경과 관련해서 관리 포털과 Azure 포털 간의 차이점을 알아봅니다.
services: app-service
documentationcenter: ''
author: jaime-espinosa
manager: wpickett
editor: jimbe

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: jaime-espinosa

---
# Azure 포털 탐색을 위한 참조
Azure 웹 사이트를 이제 [앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)이라고 합니다. 이 이름 변경을 반영하고 Azure 포털에 대한 지침을 제공하기 위해 모든 설명서를 업데이트하고 있습니다. 해당 프로세스가 완료될 때까지 이 문서를 Azure 포털의 웹앱 작업을 위한 가이드로 사용할 수 있습니다.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Azure 클래식 포털의 미래
Azure 클래식 포털의 브랜딩 변경과 더불어 해당 포털도 Azure 포털로 대체되는 중입니다. 클래식 포털이 단계적으로 중단됨에 따라 새 개발의 포커스도 Azure 포털로 전환되고 있습니다. 웹앱에 대해 제공될 예정인 모든 새 기능은 Azure 포털에서 소개됩니다. 웹앱이 제공하는 가장 강력한 최신 기능을 이용하려면 Azure 포털을 사용하세요.

## Azure 클래식 포털과 Azure 포털 간의 레이아웃 차이점
클래식 포털에서는 모든 Azure 서비스가 왼쪽에 나열됩니다. 클래식 포털의 탐색은 트리 구조를 따르므로 서비스부터 시작하여 각 요소로 이동합니다. 이 구조는 독립 구성 요소를 관리할 때 효과적입니다. 그러나 Azure에서 빌드된 응용 프로그램은 상호 연결된 서비스 컬렉션이며 이 트리 구조는 서비스 컬렉션 작업에 적합하지 않습니다.

Azure 포털에서는 여러 서비스의 구성 요소로 종단 간 응용 프로그램을 쉽게 빌드할 수 있습니다. 포털은 *추이*로 구성됩니다. *추이*는 다양한 구성 요소의 컨테이너인 일련의 *블레이드*입니다. 예를 들어 웹앱에 대해 자동 크기 조정을 설정하는 작업은 다음 예제와 같이 **웹 사이트** 블레이드(해당 블레이드 제목은 아직 새 용어를 사용하도록 업데이트되지 않음), **설정** 블레이드, **규모 확장** 블레이드 등의 여러 블레이드를 안내하는 *추이*입니다. 예제에서 자동 크기 조정은 CPU 사용에 종속되도록 설정되므로 **CPU 백분율** 블레이드도 있습니다. *블레이드* 내의 구성 요소를 *파트*라고 하며 타일과 유사합니다.

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## 탐색 예제: 웹앱 만들기
새 웹앱을 만드는 작업은 여전히 1-2-3만큼 쉽습니다. 다음 이미지는 클래식 포털과 포털을 나란히 표시하여 웹앱을 작동하고 실행하는 데 필요한 단계 수가 많이 변경되지 않았음을 보여 줍니다.

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

포털에서는 WordPress와 같은 인기 있는 갤러리 응용 프로그램을 포함하여 가장 일반적인 유형의 웹앱에서 선택할 수 있습니다. 사용 가능한 응용 프로그램의 전체 목록은 [Azure 마켓플레이스]를 참조하세요.

웹앱을 만드는 경우 클래식 포털과 마찬가지로 포털에서 URL, 앱 서비스 계획 및 위치를 지정합니다.

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

또한 포털에서는 다른 일반적인 설정을 정의할 수 있습니다. 예를 들어 [리소스 그룹](../resource-group-overview.md)을 사용하면 관련된 Azure 리소스를 간단하게 확인하고 관리할 수 있습니다.

## 탐색 예제: 설정 및 기능
이제 모든 설정과 기능이 논리적으로 단일 블레이드에 그룹화되며, 이 블레이드에서 탐색할 수 있습니다.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

예를 들어 **설정** 블레이드에서 **사용자 지정 도메인 및 SSL**을 클릭하여 사용자 지정 도메인을 만들 수 있습니다.

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

모니터링 경고를 설정하려면 **요청 및 오류**를 클릭한 다음 **경고 추가**를 클릭합니다.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

진단을 사용하도록 설정하려면 **설정** 블레이드에서 **진단 로그**를 클릭합니다.

![](./media/app-service-web-app-azure-portal/Diagnostics.png)

응용 프로그램 설정을 구성하려면 **설정** 블레이드에서 **응용 프로그램 설정**을 클릭합니다.

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

브랜드 이름 외에 포털의 몇 가지 항목이 찾기 쉽도록 이름이 바뀌거나 다르게 그룹화되었습니다. 예를 들어 다음은 클래식 포털의 앱 설정(**구성**)에 해당하는 페이지의 스크린샷입니다.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## 추가 리소스
[Azure Portal]: https://portal.azure.com
[Azure 마켓플레이스]: /marketplace/

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!---HONumber=AcomDC_0302_2016-->