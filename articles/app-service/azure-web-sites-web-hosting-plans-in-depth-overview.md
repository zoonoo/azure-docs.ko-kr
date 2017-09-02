---
title: "Azure App Service 계획의 포괄 개요 | Microsoft Docs"
description: "Azure App Service에 대한 App Service 계획의 작동 방식 및 이러한 계획을 통해 관리 환경을 향상시킬 수 있는 방법을 알아봅니다."
keywords: "앱 서비스, Azure 앱 서비스, 규모, 확장성, 앱 서비스 계획, 앱 서비스 비용"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: f97be571d104e3cc1c6ee732886fa7133ba0dc83
ms.contentlocale: ko-kr
ms.lasthandoff: 08/19/2017

---
# <a name="azure-app-service-plans-in-depth-overview"></a>Azure App Service 계획의 포괄 개요

App Service 계획은 앱을 호스트하는 데 사용되는 실제 리소스의 컬렉션을 나타냅니다.

App Service 계획은 다음을 정의합니다.

- 지역(미국 서부, 미국 동부 등)
- 확장 개수(1, 2, 3개 인스턴스 등)
- 인스턴스 크기(소, 중, 대)
- SKU(무료, 공유, 기본, 표준, 프리미엄)

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)의 Web Apps, Mobile Apps, API Apps, 함수 앱(또는 함수)은 모두 App Service 계획에서 실행됩니다.  동일한 구독 및 하위 지역의 앱은 App Service 계획을 공유할 수 있습니다. 

**App Service 계획**에 할당된 모든 응용 프로그램은 정의된 리소스를 공유합니다. 따라서 단일 App Service 계획에서 여러 앱을 호스팅할 때 비용을 절감할 수 있습니다.

**App Service 계획**은 **무료** 및 **공유** SKU에서 **기본**, **표준**, **프리미엄** SKU로 확장하여 이에 따라 더 많은 리소스 및 기능에 액세스할 수 있도록 합니다.

App Service 계획이 **기본** SKU 이상으로 설정되면 VM **크기** 및 확장 개수를 제어할 수 있습니다.

예를 들어 계획이 표준 서비스 계층의 "작은" 인스턴스 2개를 사용하도록 구성되어 있으면 해당 계획과 연결된 모든 앱이 두 인스턴스에서 실행됩니다. 앱은 표준 서비스 계층 기능에 액세스할 수도 있습니다. 앱을 실행 중인 계획 인스턴스는 완전히 관리되며 가용성이 높습니다.

> [!IMPORTANT]
> App Service 계획의 **SKU** 및 **규모**에 따라 호스트되는 앱 수가 아닌 비용이 결정됩니다.

이 문서에서는 App Service 계획의 계층 및 크기와 앱을 관리하는 동안 계획이 진행되는 방식과 같은 주요 특징을 살펴보겠습니다.

## <a name="apps-and-app-service-plans"></a>앱 및 App Service 계획

App Service의 앱은 주어진 시간에 항상 하나의 앱 서비스 계획에만 연결될 수 있습니다.

앱과 계획은 둘 다 **리소스 그룹**에 포함됩니다. 리소스 그룹은 그룹 내에 포함된 모든 리소스에 대한 수명 주기 경계 역할을 합니다. 리소스 그룹을 사용하여 응용 프로그램의 모든 부분을 함께 관리할 수 있습니다.

단일 리소스 그룹은 여러 App Service 계획을 가질 수 있으므로 각 물리적 리소스에 다른 앱을 할당할 수 있습니다.

예를 들어 개발, 테스트 및 프로덕션 환경 간에 리소스를 구분할 수 있습니다. 프로덕션 및 개발/테스트에 대해 별도의 환경을 두면 리소스를 격리할 수 있습니다. 이렇게 하면 새 버전의 앱에 대한 부하 테스트가 실제 고객에게 서비스를 제공하는 프로덕션 앱과 동일한 리소스를 경쟁하지 않습니다.

단일 리소스 그룹에 여러 계획이 있는 경우 지역에 걸쳐 있는 응용 프로그램을 정의할 수도 있습니다.

예를 들어 두 개의 지역에서 실행되는 고가용성 앱에는 각 지역별로 하나씩 두 개 이상의 계획이 포함되고 각 계획과 연결된 앱이 하나씩 포함됩니다. 이러한 경우 앱의 모든 복사본이 단일 리소스 그룹에 포함됩니다. 여러 계획과 여러 앱을 포함하는 하나의 리소스 그룹이 있으면 응용 프로그램의 상태를 쉽게 관리, 제어 및 확인할 수 있습니다.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>App Service 계획 만들기 또는 기존 계획 사용

앱을 만들 때 리소스 그룹을 만드는 것을 고려해야 합니다. 반면, 앱이 대규모 응용 프로그램의 구성 요소인 경우 대규모 응용 프로그램에 대해 할당된 리소스 그룹 내에 만들어야 합니다.

앱이 완전히 새로운 응용 프로그램이든, 대규모 응용 프로그램의 일부이든 관계없이 기존 계획을 사용하여 앱을 호스트하거나 새 계획을 만들 수 있습니다. 이 선택은 용량과 예상 부하에 따라 결정됩니다.

다음의 경우 새 App Service 계획으로 앱을 격리하는 것이 좋습니다.

- 앱이 리소스를 많이 사용합니다.
- 앱의 크기 조정 인수가 기존 계획에서 호스트되는 다른 앱과 다릅니다.
- 앱에 서로 다른 지역의 리소스가 필요합니다.

이 방식을 사용하면 앱에 새 리소스 집합을 할당하고 앱을 더 잘 제어할 수 있습니다.

## <a name="create-an-app-service-plan"></a>App Service 계획 만들기

> [!TIP]
> App Service Environment가 있는 경우 [App Service Environment에서 App Service 계획 만들기](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)에서 App Service Environment와 관련된 설명서를 검토할 수 있습니다.

App Service 계획 찾아보기 환경에서 또는 앱 만들기 과정의 일부로 빈 App Service 계획을 만들 수 있습니다.

[Azure Portal](https://portal.azure.com)에서 **새로 만들기** > **웹+모바일**을 클릭하고 **웹앱** 또는 다른 App Service 앱 종류를 선택합니다.

![Azure Portal에서 앱을 만듭니다.][createWebApp]

그런 다음 새 앱에 대한 App Service 계획을 선택하거나 만들 수 있습니다.

 ![App Service 계획을 만듭니다.][createASP]

새 App Service 계획을 만들려면 **[+] 새로 만들기**를 클릭하고 **App Service 계획** 이름을 입력한 다음 적절한 **위치**를 선택합니다. **가격 책정 계층**을 클릭한 다음 서비스에 대한 적절한 가격 책정 계층을 선택합니다. **무료** 및 **공유** 등의 더 많은 가격 책정 옵션을 보려면 **모두 보기**를 선택합니다. 가격 책정 계층을 선택한 다음 **선택** 단추를 클릭합니다.

## <a name="move-an-app-to-a-different-app-service-plan"></a>다른 App Service 계획으로 앱 이동

[Azure Portal](https://portal.azure.com)에서 앱을 다른 App Service 계획으로 이동할 수 있습니다. 계획이 동일한 리소스 그룹 및 지리적 지역에 있는 한 계획 간에 앱을 이동할 수 있습니다.

앱을 다른 계획으로 이동하려면:

- 이동하려는 앱으로 이동합니다.
- **메뉴**에서 **App Service 계획** 섹션을 찾아봅니다.
- **App Service 계획 변경**을 선택하여 프로세스를 시작합니다.

**App Service 계획 변경**이 열리면서 **App Service 계획** 선택 기능이 열립니다. 이때 이 앱을 이동할 기존 계획을 선택할 수 있습니다.

> [!IMPORTANT]
> 선택한 App Service 계획 UI는 다음 기준으로 필터링됩니다.
> - 동일한 리소스 그룹 내에 존재
> - 동일한 지리적 지역에 존재
> - 동일한 웹 공간 내에 존재
>
> 웹 공간은 서버 리소스의 그룹화를 정의하는 App Service 내의 논리적 구문입니다. 지리적 지역(예: 미국 서부)에는 App Service를 사용하여 고객을 할당하기 위한 여러 웹 공간이 포함되어 있습니다. 현재 App Service 리소스는 웹 공간 간에 이동될 수 없습니다.
>

![App Service 계획 선택기입니다.][change]

각 계획에는 고유한 가격 책정 계층이 있습니다. 예를 들어 무료 계층에서 표준 계층으로 사이트를 이동하면 여기에 할당된 모든 앱이 표준 계층의 기능과 리소스를 사용할 수 있습니다.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>앱을 다른 App Service 계획으로 변경

앱을 다른 지역으로 이동하는 한 가지 대안은 앱 복제입니다. 복제를 수행하면 모든 하위 지역의 새로운 또는 기존 App Service 계획으로 앱이 복사됩니다.

메뉴의 **개발 도구** 섹션에서 **앱 복제**를 찾을 수 있습니다.

> [!IMPORTANT]
> 복제에는 몇 가지 제한이 있으며 [Azure Portal을 사용하여 Azure App Service 앱 복제](../app-service-web/app-service-web-app-cloning-portal.md)에서 이에 대해 읽을 수 있습니다.

## <a name="scale-an-app-service-plan"></a>App Service 계획 크기 조정

계획의 크기를 조정하는 세 가지 방법이 있습니다.

- **계획의 가격 책정 계층을 변경합니다**. 기본 계층의 계획을 표준 계층으로 변환할 수 있고 여기에 할당된 모든 앱이 표준 계층의 기능을 사용할 수 있습니다.
- **계획의 인스턴스 크기를 변경합니다**. 예를 들어 작은 인스턴스를 사용하는 기본 계층의 계획을 큰 인스턴스를 사용하도록 변경할 수 있습니다. 이제 해당 계획과 연결된 모든 앱이 큰 인스턴스 크기에서 제공하는 추가 메모리 및 CPU 리소스를 사용할 수 있습니다.
- **계획의 인스턴스 수를 변경합니다**. 예를 들어 3개의 인스턴스로 확장되는 표준 계획을 10개의 인스턴스로 확장할 수 있습니다. 프리미엄 계획은 20개의 인스턴스로 확장될 수 있습니다(가용성에 따라). 이제 해당 계획과 연결된 모든 앱이 더 많은 인스턴스 수가 제공하는 추가 메모리 및 CPU 리소스를 사용할 수 있습니다.

앱 또는 App Service 계획에 대한 설정에서 **강화** 항목을 클릭하여 가격 책정 계층과 인스턴스 크기를 변경할 수 있습니다. 변경 내용이 App Service 계획에 적용되고 호스팅하는 모든 앱에 영향을 미칩니다.

 ![앱을 강화하도록 값을 설정합니다.][pricingtier]

## <a name="app-service-plan-cleanup"></a>App Service 계획 정리

> [!IMPORTANT]
> 연결된 앱이 없는 **App Service 계획**이더라도 계산 용량을 계속 예약하고 있으므로 여전히 요금이 부과됩니다.

예기치 않은 요금이 부과되지 않게 하기 위해 App Service 계획에 마지막으로 호스트된 앱이 삭제될 때 빈 상태가 된 App Service 계획도 삭제됩니다.

## <a name="summary"></a>요약

App Service 계획은 앱 간에 공유할 수 있는 기능 및 용량 집합을 나타냅니다. App Service 계획은 리소스 집합에 특정 앱을 할당하고 Azure 리소스 사용률을 더욱 최적화하는 유연성을 제공합니다. 따라서 테스트 환경에서 비용을 절약하려는 경우 여러 앱에서 계획을 공유할 수 있습니다. 여러 지역과 계획에 걸쳐 크기를 조정하여 프로덕션 환경의 생산량을 최대화할 수도 있습니다.

## <a name="whats-changed"></a>변경된 내용

- Websites에서 App Service로의 변경에 대한 지침은 [Azure App Service와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png

