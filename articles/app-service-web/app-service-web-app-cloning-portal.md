---
title: "Azure 포털을 사용하여 웹앱 복제"
description: "Azure 포털을 사용하여 웹앱을 새 웹앱에 복제하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 20b0ae4e-67e8-4bae-9d74-8a24dc445cce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2016
ms.author: aelnably
ms.translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 9ebfa91c7972ab3c264032ead8376c23c1197a4b
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017


---
# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Azure 포털을 사용하여 Azure 앱 서비스 앱 복제
[Azure 앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714) 에서 복제 기능을 사용하면 다른 지역 또는 같은 지역에 새로 만든 앱으로 기존 웹앱을 쉽게 복제할 수 있습니다. 이를 통해 고객은 수많은 앱을 다른 지역에 배포할 수 있습니다.

앱 복제는 현재 프리미엄 계층의 앱 서비스 계획에만 지원됩니다. 새로운 기능은 웹앱 백업 기능과 동일한 제한 사항을 사용합니다. [Azure App Service에서 웹앱 백업](web-sites-backup.md)을 참조하세요.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>기존 앱 복제
**프리미엄** 모드에서 웹앱을 실행해야 웹앱에 대한 복제본을 만들 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에서 웹앱의 블레이드를 엽니다.
2. **도구**를 클릭합니다. 그런 다음 **도구** 블레이드에서 **앱 복제**를 클릭합니다.
   
    ![][1]
   
   > [!NOTE]
   > 웹앱이 **프리미엄** 모드가 아닌 경우 앱 복제에 대한 지원 모델을 나타내는 메시지를 받게 됩니다. 이때 **업그레이드**옵션을 선택할 수 있습니다.
   > 
   > 
3. **앱 복제** 블레이드에서 새 웹앱, 리소스 그룹 및 앱 서비스 계획의 이름을 제공합니다. 또한 많은 원본 웹앱 설정을 복제할 것인지 여부를 선택할 수 있습니다.
   
    ![][2]
4. **만들기** 를 클릭하면 플랫폼은 원본 웹앱의 복제본을 만드는 작업을 시작합니다.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>기존 앱을 앱 서비스 환경으로 복제
**앱 복제** 블레이드에서 고객이 기존 앱 서비스 환경의 앱 풀을 옵션으로 선택할 수 있습니다.

## <a name="current-restrictions"></a>현재 제한 사항
이 기능은 현재 미리 보기 상태이며 점차 새 기능을 추가하기 위해 작업 중입니다. 다음 목록은 Azure 포털에서 앱 복제의 현재 지원에 대해 알려진 제한 사항입니다.

* Azure 트래픽 관리자 설정은 복제되지 않습니다.
* 자동 크기 설정은 복제되지 않습니다.
* 백업 일정 설정은 복제되지 않습니다.
* VNET 설정은 복제되지 않습니다.
* App Insights는 대상 웹앱에서 자동으로 설치되지 않습니다.
* 간편한 인증 설정은 복제되지 않습니다.
* Kudu 확장은 복제되지 않습니다.
* TiP 규칙은 복제되지 않습니다.
* 데이터베이스 내용이 복제되지 않습니다.

### <a name="references"></a>참조
* [PowerShell을 사용하여 웹앱 복제](app-service-web-app-cloning.md)
* [Azure 앱 서비스에서 웹앱 백업](web-sites-backup.md)
* [앱 서비스 환경을 만드는 방법](app-service-web-how-to-create-an-app-service-environment.md)
* [앱 서비스 환경에서 웹앱 만들기](app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [앱 서비스 환경 소개](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png

