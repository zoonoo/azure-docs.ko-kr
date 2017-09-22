---
title: "Azure API Management에서 수정 버전을 사용하여 API 관리 | Microsoft Docs"
description: "이 자습서의 단계에 따라 API Management에서 수정 버전을 사용하여 작업을 중단하지 않는 변경을 수행하는 방법에 대해 알아봅니다."
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
ms.translationtype: HT
ms.sourcegitcommit: 212b163c49fdd133b0ed2d99b33035fcc391aec6
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.contentlocale: ko-kr
ms.lasthandoff: 09/21/2017

---

# <a name="make-non-breaking-changes-safely-using-revisions"></a>수정 버전을 사용하여 작업을 중단하지 않는 변경을 안전하게 수행
이 자습서에는 API를 안전하게 변경하고 개발자에게 변경 내용을 알리는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 API Management 서비스를 만들어야 하며, Conference API 대신 변경 가능한 기존 API가 있어야 합니다.

## <a name="about-revisions"></a>수정 버전 정보
API가 준비되어 개발자들이 사용하기 시작하면 대개 API 호출자의 작업을 중단하지 않는 방식으로 해당 API의 변경을 수행해야 합니다. 이렇게 하면 개발자에게 변경 내용을 알릴 때도 유용합니다. Azure API Management에서 **수정 버전**을 사용하면 이러한 작업을 수행할 수 있습니다.

## <a name="walkthrough"></a>연습
이 연습에서는 새 수정 버전을 추가하고, 수정 버전에 대한 작업을 추가한 다음 해당 수정 버전을 현재 항목으로 설정하며, 작업 시 변경 로그 항목을 작성합니다.

## <a name="add-a-new-revision"></a>새 수정 버전 추가
1. Azure Portal의 API Management 서비스 내에서 **API** 페이지로 이동합니다.
2. API 목록에서 **Conference API**를 선택한 다음 페이지 상단 근처의 메뉴에서 **수정 버전** 탭을 선택합니다.
3. **+ 수정 버전 추가**를 선택합니다.

    > [!TIP]
    > API의 상황에 맞는 메뉴(**...**)에서 **수정 버전 추가**를 선택할 수도 있습니다.
![화면 상단 근처의 수정 버전 메뉴](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. 새 수정 버전의 용도를 쉽게 기억할 수 있도록 해당 버전의 설명을 입력합니다.
5. **만들기**
6. 이제 새 수정 버전이 작성되었습니다.

    > [!NOTE]
    > 원본 API는 **수정 버전 1**로 유지됩니다. 다른 수정 버전을 현재 항목으로 설정할 때까지는 사용자가 이 수정 버전을 계속 호출합니다.

## <a name="make-non-breaking-changes-to-your-revision"></a>작업을 중단하지 않는 방식으로 수정 버전 변경
1. 화면 상단 근처에서 **디자인** 탭을 선택합니다.
2. 디자인 탭 바로 위의 **수정 버전 선택기**에 현재 수정 버전이 **수정 버전 2**로 표시됩니다.

    > [!TIP]
    > 수정 버전 선택기를 사용하여 작업하려는 수정 버전 간을 전환합니다.

3. **+ 작업 추가**를 선택합니다.
4. 새 작업을 **POST**로 설정하고 작업의 이름 및 표시 이름을 **피드백**으로 설정합니다.
5. 새 작업을 **저장**합니다.
6. 이제 **수정 버전 2**가 변경되었습니다. 페이지 상단 근처의 **수정 버전 선택기**를 사용하여 **수정 버전 1**로 다시 전환합니다.
7. **수정 버전 1**에는 새 작업이 표시되지 않습니다. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>수정 버전을 현재 항목으로 설정하고 변경 로그 항목 추가
1. 페이지 상단 근처의 메뉴에서 **수정 버전** 탭을 선택합니다.
![수정 버전 화면의 수정 버전 메뉴](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. **수정 버전 2**의 상황에 맞는 메뉴(**...**)를 엽니다.
3. **현재로 설정**을 선택합니다.
![수정 버전을 현재 항목으로 설정하고 변경 로그에 게시](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. **이 API에 대한 공용 변경 로그에 게시**를 선택합니다.
5. **"새 피드백 작업을 추가했습니다."**와 같이 개발자에게 표시할 변경의 설명을 입력합니다.
6. 이제 **수정 버전 2**가 현재 항목으로 설정되었습니다.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>개발자 포털로 이동하여 변경 내용과 변경 로그 확인
1. 상단 메뉴에서 **개발자 포털**을 선택합니다.
2. **API**, **Conference API**를 차례로 선택합니다.
3. 이제 새 **피드백** 작업을 사용할 수 있습니다.
4. API 이름 아래에서 **API 변경 기록**을 선택합니다.
5. 이 목록에 변경 로그 항목이 표시됩니다.
![개발자 포털에서 로그온 변경](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>다음 단계
[Azure API Management를 사용하여 API 버전 게시](#api-management-getstarted-publish-versions.md)
