---
title: 포함 파일
description: 포함 파일
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 07/02/2019
ms.openlocfilehash: a463e3cf475909c34054717460dc10dbba4ad8f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543894"
---
> [!IMPORTANT]
> * 2019년 5월에 새 **Azure Active Directory** > **앱 등록** 블레이드가 레거시 **Azure Active Directory** > **앱 등록(레거시)** 블레이드를 대체합니다.
> * 레거시 블레이드에 생성되거나 표시된 앱 등록은 자동으로 새 블레이드에 나타납니다.
> * 새 Azure 앱 등록 환경으로 마이그레이션하는 방법에 대한 전체 정보는 [Azure 앱 등록 학습 가이드](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) 및 [Azure Active Directory 빠른 시작](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)을 참조하세요.

1. [Azure portal](https://ms.portal.azure.com/)에서 **Azure Active Directory** > **앱 등록** > **새 등록**을 선택합니다.

   [![Azure Active Directory에서 새 애플리케이션 등록](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > 새 Azure Active Directory 앱 등록 패널을 사용하면 **소유한 애플리케이션**을 선택하여 표시된 앱을 필터링할 수 있습니다.

    등록한 후에 앱이 여기 나열됩니다.

1. 애플리케이션 이름을 지정하고 **이 조직 디렉터리의 계정만**을 선택하여 API에 액세스할 수 있는 **지원되는 계정 유형**을 지정합니다. 인증된 후 사용자를 리디렉션할 유효한 URI를 선택한 다음, **등록**을 선택합니다.

   [![Azure Active Directory에서 애플리케이션 만들기](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. 중요 Azure Active Directory 앱 정보가 나열된 앱의 **개요** 블레이드에 표시됩니다. **소유한 애플리케이션**에서 앱을 선택한 다음, **개요**를 선택합니다.

   [![애플리케이션 ID 복사](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   클라이언트 애플리케이션에서 사용하도록 **애플리케이션(클라이언트) ID**를 복사합니다.

1. **인증** 블레이드에서는 중요한 인증 구성 설정을 지정합니다. 

    1. **리디렉션 URI**는 인증 요청에 의해 제공된 주소와 일치해야 합니다.

        * 로컬 개발 환경에서 호스팅되는 앱의 경우 **공용 클라이언트(모바일 및 데스크톱)** 를 선택합니다. **기본 클라이언트 형식**을 예로 설정해야 합니다.
        * Azure App Service에서 호스팅되는 단일 페이지 앱의 경우 **웹**을 선택합니다.

    1. **ID 토큰**을 확인하여 암시적 허용 흐름을 사용하도록 설정합니다.

   [![새 클라이언트 비밀 만들기](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   **저장**을 클릭합니다.

1. Azure Active Directory 앱 Azure Time Series Insights에 연결합니다. **API 사용 권한** > **사용 권한 추가** > **내 조직에서 사용하는 API**를 선택합니다. 

    [![Azure Active Directory 앱을 사용하여 API에 연결](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   `Azure Time Series Insights`를 검색 창에 입력한 다음, `Azure Time Series Insights`를 선택합니다.

1. 다음으로 앱에 필요한 API 사용 권한 종류를 지정합니다. 기본적으로 **위임된 사용 권한**이 강조 표시됩니다. 사용 권한 유형을 선택한 다음, **사용 권한 추가**를 선택합니다.

    [![앱에 필요한 API 사용 권한 종류 지정](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)