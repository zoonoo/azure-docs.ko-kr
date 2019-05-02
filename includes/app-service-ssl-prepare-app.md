---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ecdd419331c88e712644851f9213861f882cf0f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832875"
---
## <a name="prepare-your-web-app"></a>웹앱 준비

사용자 지정 SSL 인증서(타사 인증서 또는 App Service 인증서)를 웹앱에 바인딩하려면 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/)이 **기본**, **표준**, **프리미엄** 또는 **격리**계층에 있어야 합니다. 이 단계에서는 웹앱이 지원되는 가격 책정 계층에 있음을 확인합니다.

### <a name="log-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)을 엽니다.

### <a name="navigate-to-your-web-app"></a>웹앱으로 이동

왼쪽 메뉴에서 **App Services**를 클릭한 다음 웹앱의 이름을 클릭합니다.

![웹앱 선택](./media/app-service-ssl-prepare-app/select-app.png)

웹앱의 관리 페이지에 연결되었습니다.  

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

웹앱 페이지의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하고 **강화(App Service 계획)** 를 선택합니다.

![강화 메뉴](./media/app-service-ssl-prepare-app/scale-up-menu.png)

웹앱이 **F1** 또는 **D1** 계층이 아닌지 확인합니다. 웹앱의 현재 계층이 진한 파란색 상자로 강조 표시됩니다.

![가격 책정 계층 확인](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

사용자 지정 SSL은 **F1** 또는 **D1** 계층에서 지원되지 않습니다. 강화해야 하는 경우 다음 섹션의 단계를 수행합니다. 그렇지 않으면 **강화** 페이지를 닫고, [App Service 계획 강화](#scale-up-your-app-service-plan) 섹션을 건너뜁니다.

### <a name="scale-up-your-app-service-plan"></a>App Service 계획 강화

유료 계층(**B1**, **B2**, **B3**, 또는 **프로덕션** 범주의 모든 계층) 중 하나를 선택합니다. 추가 옵션을 보려면 **추가 옵션 보기**를 클릭합니다.

**적용**을 클릭합니다.

![가격 책정 계층 선택](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

다음 알림이 표시되면 강화 작업이 완료됩니다.

![강화 알림](./media/app-service-ssl-prepare-app/scale-notification.png)

