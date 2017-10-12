---
title: "Azure API Management를 사용하여 API 버전 게시 | Microsoft Docs"
description: "이 자습서의 단계에 따라 API Management에서 여러 버전을 게시하는 방법에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>예측 가능한 방식으로 API의 여러 버전 게시
이 자습서에는 API의 버전을 설정하고 API 개발자가 API를 호출하는 방식을 선택하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 API Management 서비스를 만들어야 하며, Conference API 대신 변경 가능한 기존 API가 있어야 합니다.

## <a name="about-versions"></a>버전 정보
모든 API 호출자가 정확히 같은 버전을 사용하기가 어려운 경우가 있습니다. 그리고 특정 사용자를 대상으로 새로운 API 기능이나 다른 API 기능을 게시하려고 하는데, 현재 잘 사용하고 있는 API를 그대로 사용하려는 사용자도 있을 수 있습니다. 최신 버전으로 업그레이드하려는 호출자는 이해하기 쉬운 방식을 사용하여 업그레이드를 수행하기를 원할 것입니다.  Azure API Management에서 **버전**을 사용하면 이러한 작업을 수행할 수 있습니다.

## <a name="walkthrough"></a>연습
이 연습에서는 버전 관리 체계와 버전 식별자를 선택하여 기존 API에 새 버전을 추가합니다.

## <a name="add-a-new-version"></a>새 버전 추가
![API 상황에 맞는 메뉴 - 버전 추가](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Azure Portal의 API Management 서비스 내에서 **API** 페이지로 이동합니다.
2. API 목록에서 **Conference API**를 선택한 다음 옆에 있는 상황에 맞는 메뉴(**...**)를 선택합니다.
3. **+ 버전 추가**를 선택합니다.

    > [!TIP]
    > 새 API를 처음 만들 때 버전을 사용하도록 설정할 수도 있습니다. 이렇게 하려면 **API 추가** 화면에서 **이 API를 버전 관리하시겠습니까?**를 선택합니다.

## <a name="choose-a-versioning-scheme"></a>버전 관리 체계 선택
Azure API Management에서는 호출자가 원하는 API 버전을 지정하도록 허용할 방식을 선택할 수 있습니다. 이렇게 하려면 **버전 관리 체계**를 선택합니다. 이 체계는 **경로, 헤더 또는 쿼리 문자열**일 수 있습니다. 이 예제에서는 경로를 사용합니다.
![버전 추가 화면](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. **버전 관리 체계**에서 선택된 **경로**를 그대로 유지합니다.
2. **버전 식별자**로 **v1**을 추가합니다.

    > [!TIP]
    > 버전 관리 체계로 **헤더** 또는 **쿼리 문자열**을 선택하는 경우에는 추가 값(헤더의 이름 또는 쿼리 문자열 매개 변수)을 제공해야 합니다.

3. 원하는 경우 설명을 입력합니다.
4. **만들기**를 선택하여 새 버전을 설정합니다.
5. 이제 API 목록의 **Big Conference API** 아래에 고유 API 두 개(**원본** 및 **v1**)가 표시됩니다.
![Azure Portal에서 API 아래에 나열된 버전](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > 버전이 없는 API에 버전을 추가하는 경우에는 항상 기본 URL에서 응답하는 **원본** 버전이 작성됩니다. 따라서 버전 추가 프로세스로 인해 기존 호출자의 호출이 중단되지 않습니다. 시작 시 버전을 사용하도록 설정하여 새 API를 만들면 원본이 작성되지 않습니다.

6. 이제 **원본**과는 완전히 별개의 API로 **v1**을 편집하고 구성할 수 있습니다. 즉, 버전 하나를 변경해도 다른 버전에는 영향을 주지 않습니다.

## <a name="add-the-version-to-a-product"></a>제품에 버전 추가
호출자에게 새 버전을 표시하려면 **제품**에 해당 버전을 추가해야 합니다. 제품은 부모 버전에서 상속되지 않습니다.

1. 서비스 관리 페이지에서 **제품**을 선택합니다.
2. **무제한**을 선택합니다.
3. **API**를 선택합니다.
4. **추가**를 선택합니다.
5. **Conference API, 버전 v1**을 선택합니다.
6. 서비스 관리 페이지로 돌아와서 **API**를 선택합니다.

## <a name="browse-the-developer-portal-to-see-the-version"></a>개발자 포털을 검색하여 버전 확인
1. 상단 메뉴에서 **개발자 포털**을 선택합니다.
2. **API**를 선택합니다. **Conference API**에 **원본** 및 **v1** 버전이 표시됩니다.
3. **v1**을 선택합니다.
4. 목록에서 첫 번째 작업의 **요청 URL**을 확인합니다. API URL 경로에 **v1**이 포함되어 있습니다.
![개발자 포털에 표시되는 버전](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)
