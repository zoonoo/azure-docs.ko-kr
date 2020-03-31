---
title: Dropbox에 연결
description: Azure Logic Apps를 사용하여 Dropbox에서 파일을 업로드하고 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665754"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Azure 논리 앱을 사용하여 Dropbox에서 파일 업로드 및 관리

Dropbox 커넥터와 Azure Logic Apps를 사용하면 Dropbox 계정에서 파일을 업로드하고 관리하는 자동화된 워크플로를 만들 수 있습니다. 

이 도움말에서는 로직 앱에서 Dropbox에 연결한 다음 **파일이 생성되면** Dropbox 트리거를 추가하고 **Dropbox Get 파일 콘텐츠는 경로** 작업을 사용하여 추가하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 무료로 가입할 수 있는 [Dropbox 계정.](https://www.dropbox.com/) 로직 앱과 Dropbox 계정 간의 연결을 만드는 데 계정 자격 증명이 필요합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 이 예에서는 빈 논리 앱이 필요합니다.

## <a name="add-trigger"></a>트리거 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 검색 상자에서 **모두**를 선택합니다. 검색 상자에 필터로 "dropbox"를 입력합니다.
트리거 목록에서 **파일을 만들 때** 트리거를 선택합니다.

   ![Dropbox 트리거 선택](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Dropbox 계정 자격 증명을 사용하여 로그인하고, Dropbox 데이터에 대한 액세스 권한을 Azure Logic Apps에 부여합니다.

1. 트리거에 필요한 정보를 입력합니다. 

   이 예제에서는 파일 생성을 추적할 폴더를 선택합니다. 폴더를 찾아보시려면 **폴더** 상자 옆에 있는 폴더 아이콘을 선택합니다.

## <a name="add-action"></a>작업 추가

이제 새 파일에서 콘텐츠를 얻는 작업을 추가합니다.

1. 트리거 아래에서 **다음 단계**를 선택합니다. 

1. 검색 상자에서 **모두**를 선택합니다. 검색 상자에 필터로 "dropbox"를 입력합니다.
작업 목록에서 이 작업을 선택합니다: **경로를 사용하여 파일 콘텐츠 가져옵니다.**

1. Dropbox에 액세스할 수 있는 Azure Logic 앱이 아직 승인되지 않은 경우 지금 액세스 권한을 부여합니다.

1. 사용하려는 파일 경로를 찾아보려는 **파일 경로** 상자 옆에 있는 타원(... ) 버튼을 선택합니다.**...** 

   **파일 경로** 상자 내부를 클릭하고 동적 콘텐츠 목록에서 이전 섹션에서 추가한 트리거의 출력으로 값을 사용할 수 있는 **파일 경로를**선택할 수도 있습니다.

1. 완료되면 논리 앱을 저장합니다.

1. 논리 앱을 트리거하려면 Dropbox에 새 파일을 만듭니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에서 설명한 것처럼 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/dropbox/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
