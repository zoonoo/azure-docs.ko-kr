---
title: Dropbox-Azure Logic Apps에 연결
description: Dropbox REST API 및 Azure Logic Apps를 사용하여 파일 업로드 및 관리
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312561"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>업로드 하 고 Azure Logic Apps를 사용 하 여 Dropbox의 파일 관리

Dropbox 커넥터 및 Azure Logic Apps를 사용 하 여 업로드 하 고 Dropbox 계정의 파일을 관리 하는 자동화 된 워크플로 만들 수 있습니다. 

이 문서에서는 논리 앱에서 Dropbox에 연결 하 여 다음 Dropbox를 추가 하는 방법을 보여 줍니다 **파일을 만들면** 트리거와 Dropbox **경로 사용 하 여 파일 콘텐츠 가져오기** 작업 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* A [Dropbox 계정](https://www.dropbox.com/)는 무료로 등록할 수 있습니다. 계정 자격 증명은 논리 앱과 Dropbox 계정 간에 연결을 만드는 데 필요한 합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 이 예에서는 빈 논리 앱이 필요합니다.

## <a name="add-trigger"></a>트리거 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 검색 상자에서 **모두**를 선택합니다. 검색 상자에 필터로 "dropbox"를 입력합니다.
트리거 목록에서 다음 트리거를 선택합니다. **파일을 만들 때**

   ![Dropbox 트리거 선택](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Dropbox 계정 자격 증명을 사용하여 로그인하고, Dropbox 데이터에 대한 액세스 권한을 Azure Logic Apps에 부여합니다.

1. 트리거에 필요한 정보를 입력합니다. 

   이 예제에서는 파일 생성을 추적 하려는 폴더를 선택 합니다. 폴더를 이동할 폴더 아이콘을 옆에 선택 합니다 **폴더** 상자입니다.

## <a name="add-action"></a>작업 추가

이제 모든 새 파일에서 콘텐츠를 가져오는 작업을 추가 합니다.

1. 트리거 아래에서 **다음 단계**를 선택합니다. 

1. 검색 상자에서 **모두**를 선택합니다. 검색 상자에 필터로 "dropbox"를 입력합니다.
작업 목록에서 다음 작업을 선택합니다. **경로를 사용하여 파일 콘텐츠 가져오기**

1. Azure 논리 앱이 Dropbox 액세스 권한이 이미 하지 않은 경우 이제 액세스 권한을 부여 합니다.

1. 옆에 사용 하려면 원하는 파일 경로 이동 하는 **파일 경로** 상자에서 줄임표 ( **...** ) 단추입니다. 

   내에서 클릭할 수도 있습니다는 **파일 경로** 상자의 및 동적 콘텐츠 목록에서 선택 **파일 경로**, 이전 섹션에서 추가한 트리거에서 출력 값은 사용할 수 있습니다.

1. 완료되면 논리 앱을 저장합니다.

1. 논리 앱을 트리거하기 위해 Dropbox에 새 파일을 만듭니다.

## <a name="connector-reference"></a>커넥터 참조

트리거, 작업 및 커넥터의 OpenAPI에 설명 된 대로 제한 등의 기술 세부 정보에 대 한 (이전의 Swagger) 파일, 참조를 [커넥터의 참조 페이지](/connectors/dropbox/)합니다.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
