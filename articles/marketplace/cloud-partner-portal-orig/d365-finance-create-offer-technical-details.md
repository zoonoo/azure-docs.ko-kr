---
title: 기술 정보 양식 작성 방법 | Microsoft Docs
description: 새 Dynamics 365 Business Central 앱에 대한 기술 정보 양식의 값을 입력하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cb8e5074d0cd99f5c7cb2130e6f9181eb5382015
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807570"
---
<a name="how-to-fill-out-the-technical-info-form"></a>기술 정보 양식 작성 방법
===========================================

1.  **응용 프로그램 종류 선택** 섹션에서 확장 패키지 파일(.app) 및 확장에 종속성이 있는 모든 확장 패키지 파일을 업로드합니다.

    ![애플리케이션 패키지 정보](./media/d365-financials/image015.png)

-   **확장 패키지 파일** - 필수 - 확장 패키지 파일(.app)입니다.

-   **종속성 패키지 파일** - 앱이 AppSource에 게시된 다른 앱에 대한 종속성을 가진 경우 필수입니다. 현재 앱이 종속된, AppSource에 이미 게시된 확장의 이 .app 파일입니다. 

-   **라이브러리 패키지 파일** - 앱이 AppSource에 게시되지 *않은* 다른 앱에 대한 종속성을 가진 경우 필수입니다. AppSource에 게시되지 않았으며 게시되지 않을 기존 앱의 이 .app 파일입니다.

-   **앱 테스트 자동화** - 필수 - 확장의 자동화된 테스트를 위해 만들어야 하는 VS 코딩된 테스트 패키지입니다.

2.  **확장에 대한 추가 정보** 섹션에서 확장에 대한 추가 정보를 업로드합니다. 이 정보는 유효성 검사 중에 사용됩니다.

    ![앱 확장 양식에 대한 추가 정보](./media/d365-financials/image016.png)


-   **제품 설명서 URL** - 필수 - 확장 설명서의 URL입니다.

-   **키 사용 시나리오** - 필수 - 확장에 대한 단계별 설정 및 사용법 세부 정보를 나열하는 문서입니다. 예제는 [사용자 시나리오 문서](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/)에서 확인할 수 있습니다.

-   **대상 릴리스** - 필수 - 앱을 배포할 릴리스를 선택합니다. 현재 출시된 버전에 배포하려면 **현재**를 선택합니다. 릴리스할 다음 부 버전에 배포하려면 **다음 부 버전**을 선택합니다. 릴리스할 다음 주 버전에 배포하려면 **다음 주 버전**을 선택합니다.

-   **프리미엄 SKU 필요** - 선택 사항 - 앱에 프리미엄 SKU가 필요한 경우 프리미엄 단추를 선택합니다. 서비스 관리 및 제조는 프리미엄에서만 사용할 수 있습니다. 필수 및 프리미엄에 대한 자세한 내용은 [표시되는 기능 변경](https://docs.microsoft.com/dynamics365/financials/ui-experiences) 문서에서 확인할 수 있습니다.

-   **코드 분석 오류에 대한 설명** - 선택 사항 - 요구 사항을 충족하지 않는 코드를 나열하고 근거를 제공하는 문서입니다.

-   **영향을 받는 핵심 기능에 대한 설명** - 선택 사항 - 확장에 의해 제한되는 핵심 기능을 나열하고 설명하는 문서입니다.

-   **테스트 계정** - 선택 사항 - 종료 간 사용 테스트를 완료하는 데 필요한 원격 서비스, 웹 사이트 등에 대한 사용자 계정입니다.

-   **UX 요구 사항 예외** - 선택 사항 - 확장에 의해 충족되지 않은 사용자 환경 요구 사항을 나열하고 근거를 제공하는 문서입니다.

다음 단계는 제품에 대한 상점 세부 정보를 추가하는 것입니다.
