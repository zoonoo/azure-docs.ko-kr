---
title: 파일 포함
description: 파일 포함
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 0ce9575f078058c821ffffe1b9fe45eed5a4ad94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724179"
---
* [플랫폼 설정 구성](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#configure-platform-settings) 의 4 단계에서 적절 한 플랫폼을 선택한 후 사용자 인터페이스 오른쪽의 측면 패널에서 **리디렉션 Uri** 및 **액세스 토큰** 을 구성 합니다.

    * **리디렉션 URI** 는 인증 요청에 의해 제공된 주소와 일치해야 합니다.

        * 로컬 개발 환경에서 호스팅되는 앱의 경우 **공용 클라이언트(모바일 및 데스크톱)** 를 선택합니다. **퍼블릭 클라이언트** 를 **예** 로 설정해야 합니다.
        * Azure App Service에서 호스팅되는 단일 페이지 앱의 경우 **웹** 을 선택합니다.

    * **로그아웃 URL** 이 적절한지 확인합니다.

    * **액세스 토큰** 또는 **ID 토큰** 을 확인하여 암시적 허용 흐름을 사용하도록 설정합니다.

    [![리디렉션 URI 만들기](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    **구성** 을 클릭한 다음, **저장** 을 클릭합니다.

* Azure Active Directory 앱 Azure Time Series Insights 연결 합니다. **API 사용 권한** > **사용 권한 추가** > **내 조직에서 사용하는 API** 를 선택합니다.

    [![Azure Active Directory 앱을 사용하여 API에 연결](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   `Azure Time Series Insights`를 검색 창에 입력한 다음, `Azure Time Series Insights`를 선택합니다.

* 다음으로 앱에 필요한 API 사용 권한 종류를 지정합니다. 기본적으로 **위임된 사용 권한** 이 강조 표시됩니다. 사용 권한 유형을 선택한 다음, **사용 권한 추가** 를 선택합니다.

    [![앱에 필요한 API 사용 권한 종류 지정](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* 응용 프로그램에서 환경의 Api를 자체로 호출 하는 경우 [자격 증명을 추가](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#add-credentials) 합니다. 자격 증명을 사용하면 애플리케이션에서 자체적으로 인증할 수 있으므로 런타임에 사용자의 상호 작용이 필요하지 않습니다.