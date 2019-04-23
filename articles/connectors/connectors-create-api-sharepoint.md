---
title: Azure Logic Apps에서 SharePoint에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 온-프레미스에서 SharePoint Online 또는 SharePoint Server의 리소스를 모니터링 및 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e636b2bb08477e6c56c6ae41f08983fc5bfa2a9b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798658"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 SharePoint 리소스 모니터링 및 관리

Azure Logic Apps 및 SharePoint 커넥터를 사용하여 온-프레미스의 SharePoint Online 또는 SharePoint Server에서 파일, 폴더, 목록, 항목, 사람 등의 리소스를 모니터링 및 관리하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 다음과 같습니다.

* 파일 또는 항목이 생성, 변경 또는 삭제되는 경우를 모니터링합니다.
* 항목을 만들고, 가져오고 업데이트하거나 삭제합니다.
* 첨부 파일을 추가하고, 가져오거나 삭제합니다. 첨부 파일에서 콘텐츠를 가져옵니다.
* 파일을 만들고, 복사하고, 업데이트하거나 삭제합니다. 
* 파일 속성을 업데이트합니다. 파일에 대한 콘텐츠, 메타데이터 또는 속성을 가져옵니다.
* 폴더를 나열하거나 추출합니다.
* 목록을 가져오거나 보기를 나열합니다.
* 콘텐츠 승인 상태를 설정합니다.
* 사람을 확인합니다.
* SharePoint에 HTTP 요청을 보냅니다.
* 엔터티 값을 가져옵니다.

트리거를 사용하여 SharePoint에서 응답을 가져오고 다른 작업에서 출력을 사용하도록 할 수 있습니다. 논리 앱에서 작업을 사용하여 SharePoint에서 다양한 작업을 수행할 수 있습니다. 또한 다른 작업에서 SharePoint 작업의 출력을 사용하도록 할 수 있습니다. 예를 들어 SharePoint에서 정기적으로 파일을 인출하는 경우 Slack 커넥터를 사용하여 팀에 메시지를 보낼 수 있습니다.
논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* SharePoint 사이트 주소 및 사용자 자격 증명

  자격 증명을 통해 SharePoint 계정에 대한 연결을 만들고 액세스하는 권한이 논리 앱에 부여됩니다. 

* 논리 앱을 SharePoint Server와 같은 온-프레미스 시스템에 연결하려면 먼저 [온-프레미스 데이터 게이트웨이를 설치 및 설정](../logic-apps/logic-apps-gateway-install.md)해야 합니다. 이런 방식으로 논리 앱에 대한 SharePoint Server 연결을 만들 때 게이트웨이 설치를 사용하도록 지정할 수 있습니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* SharePoint 계정에 액세스하려는 논리 앱입니다. SharePoint 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). SharePoint 작업을 사용하려면 Salesforce 계정이 있는 경우 Salesforce 트리거와 같은 트리거를 사용하여 논리 앱을 시작합니다.

  예를 들어 **레코드가 만들어지는 경우** Salesforce 트리거를 사용하여 논리 앱을 시작할 수 있습니다. 
  이 트리거는 잠재 고객과 같은 새 레코드가 Salesforce에서 만들어질 때마다 발생합니다. 
  그런 다음, SharePoint **파일 만들기** 작업을 사용하여 이 트리거를 따를 수 있습니다. 이런 방식으로 새 레코드가 만들어질 때 논리 앱에서 해당 새 레코드에 대한 정보로 SharePoint에 파일을 만듭니다.

## <a name="connect-to-sharepoint"></a>SharePoint에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 빈 논리 앱의 경우 검색 상자에서 필터로 "sharepoint"를 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다. 

   또는

   기존 논리 앱의 경우 SharePoint 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 
   검색 상자에서 필터로 "sharepoint"를 입력합니다. 
   작업 목록에서 원하는 작업을 선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

1. 로그인하라는 메시지가 표시되면 필요한 연결 정보를 입력합니다. SharePoint Server를 사용 하는 경우 선택 해야 **온-프레미스 데이터 게이트웨이 통해 연결**합니다. 작업을 완료하면 **만들기**를 선택합니다.

1. 선택한 트리거 또는 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/sharepoint/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
