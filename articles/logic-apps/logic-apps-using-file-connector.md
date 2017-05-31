---
title: "Azure Logic Apps에서 온-프레미스 파일 시스템에 연결 | Microsoft Docs"
description: "온-프레미스 데이터 게이트웨이 및 파일 시스템 커넥터를 통해 논리 앱 워크플로에서 온-프레미스 파일 시스템에 연결"
keywords: "파일 시스템"
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: LADocs; deli
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3faa238b882c88f1a0eb59a4e7a2f8bb6d6e6b9b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>파일 시스템 커넥터를 사용하여 Logic Apps에서 온-프레미스 파일 시스템에 연결

하이브리드 클라우드 연결은 Logic Apps의 핵심이므로, 온-프레미스 리소스에서 데이터를 관리하고 안전하게 액세스하기 위해 Logic Apps에서는 온-프레미스 데이터 게이트웨이를 사용할 수 있습니다. 이 문서에서는 기본적인 시나리오 “Dropbox에 업로드된 파일을 파일 공유에 복사한 다음 전자 메일 전송”를 사용하여 온-프레미스 파일 시스템에 연결하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- 최신 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127)를 설치하고 구성합니다.
- 최신 온-프레미스 데이터 게이트웨이 버전 1.15.6150.1 이상을 설치합니다. [온-프레미스 데이터 게이트웨이에 연결](http://aka.ms/logicapps-gateway)에서 단계를 나열하고 있습니다. 나머지 단계를 계속하기 전에 게이트웨이를 온-프레미스 컴퓨터에 설치해야 합니다.

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>파일 시스템에 연결하기 위한 트리거 및 작업 추가

1. 논리 앱을 만들고 Dropbox 트리거: **파일이 만들어진 경우**를 추가합니다. 
2. 트리거에서 **다음 단계** > **작업 추가**를 선택합니다. 
3. 파일 시스템 커넥터에 대해 지원되는 모든 작업을 볼 수 있도록 검색 상자에서 `file system`을 입력합니다.

   ![파일 커넥터 검색](media/logic-apps-using-file-connector/search-file-connector.png)

2. **파일 만들기** 작업을 선택하고 파일 시스템에 대한 연결을 만듭니다.

   기존 연결이 없으면 새로 만들라는 메시지가 표시됩니다.

   1. **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택합니다. 더 많은 속성이 표시됩니다.
   2. 파일 시스템에 대한 루트 폴더를 선택합니다.
      
       > [!NOTE]
       > 루트 폴더는 모든 파일 관련 작업의 상대 경로에 사용되는 기본 상위 폴더입니다. 온-프레미스 데이터 게이트웨이가 설치된 컴퓨터의 로컬 폴더 또는 컴퓨터에서 액세스할 수 있는 네트워크 공유 폴더를 지정할 수 있습니다.

   3. 게이트웨이에 대한 사용자 이름과 암호를 입력합니다.
   4. 이전에 설치한 게이트웨이를 선택합니다.

       ![연결 구성](media/logic-apps-using-file-connector/create-file.png)

3. 모든 세부 정보를 제공한 후 **만들기**를 선택합니다. 

   Logic Apps는 연결을 구성하고 테스트하여 제대로 작동되는지 확인합니다. 
   연결이 제대로 설치되면 이전에 선택한 작업에 대한 옵션이 표시됩니다. 
   이제 파일 시스템 커넥터를 사용할 준비가 되었습니다.

4. 온-프레미스 파일 공유를 위해 Dropbox의 파일을 루트 폴더로 복사할 것임을 지정합니다.

   ![파일 작업 만들기](media/logic-apps-using-file-connector/create-file-filled.png)

5. 논리 앱에서 파일을 복사한 후에는 관련 사용자만 새 파일에 대해 알 수 있도록 전자 메일을 보내는 Outlook 작업을 추가합니다. 전자 메일의 받는 사람, 제목 및 본문을 입력합니다. 

   동적 콘텐츠 선택기에는 전자 메일에 더 많은 세부 정보를 추가할 수 있도록 파일 커넥터의 데이터 출력을 선택할 수 있습니다.

   ![전자 메일 보내기 작업](media/logic-apps-using-file-connector/send-email.png)

6. 논리 앱을 저장합니다. Dropbox에 파일을 업로드하여 앱을 테스트합니다. 파일은 온-프레미스 파일 공유로 복사되며 해당 작업에 대한 전자 메일 알림이 수신됩니다.

   > [!TIP] 
   > [Logic Apps 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md) 방법을 확인합니다.

축하합니다. 이제 온-프레미스 파일 시스템에 연결할 수 있는 작업 논리 앱이 생성되었습니다. 다음과 같이 커넥터가 제공하는 다른 기능도 함께 탐색해 보세요.

- 파일 만들기
- 폴더의 파일 나열
- 파일 첨부
- 파일 삭제
- 파일 콘텐츠 가져오기
- 경로를 사용하여 파일 콘텐츠 가져오기
- 파일 메타데이터 가져오기
- 경로를 사용하여 파일 메타데이터 가져오기
- 루트 폴더의 파일 나열
- 파일 업데이트

## <a name="view-the-swagger"></a>swagger 보기
[swagger 정보](/connectors/fileconnector/)를 참조하세요. 

## <a name="get-help"></a>도움말 보기

질문하고, 질문에 답변하고, 다른 Azure Logic Apps 사용자가 어떤 일을 하는지 알아보려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

Azure Logic Apps 및 커넥터 개선에 도움을 주려면 [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요.

## <a name="next-steps"></a>다음 단계

- Logic Apps에서 [온-프레미스 데이터에 연결](../logic-apps/logic-apps-gateway-connection.md)
- [엔터프라이즈 통합](../logic-apps/logic-apps-enterprise-integration-overview.md)에 대해 알아봅니다.

