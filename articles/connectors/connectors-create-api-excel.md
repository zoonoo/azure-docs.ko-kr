---
title: Online-Azure Logic Apps에 Excel 연결
description: Excel Online REST API 및 Azure Logic Apps를 사용하여 데이터 관리
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 28739ad65462acc9f2d2ed7db1e9ed14d19f032c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311967"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Excel Online 데이터 관리

Azure Logic Apps 및 Excel Online 커넥터를 사용하여 비즈니스 또는 OneDrive용 Excel Online에서 데이터 기반 자동화된 작업 및 워크플로를 만들 수 있습니다. 이 커넥터는 데이터를 사용하고 스프레드시트를 관리하는 데 도움이 되는 작업을 제공합니다. 예를 들어 다음과 같습니다.

* 새 워크시트와 테이블을 만듭니다.
* 워크시트, 테이블 및 행을 가져오고 관리합니다.
* 단일 행 및 키 열을 추가합니다.

그런 다음, 다른 서비스에 대한 작업을 사용하여 이러한 작업의 출력을 사용할 수 있습니다. 예를 들어 매주 워크시트를 만드는 작업을 사용하는 경우 Office 365 Outlook 커넥터를 사용하여 확인 이메일을 보내는 다른 작업을 사용할 수 있습니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

> [!NOTE]
> [비즈니스용 Excel Online](/connectors/excelonlinebusiness/) 및 [OneDrive용 Excel Online](/connectors/excelonline/) 커넥터는 Azure Logic Apps와 작동하며 [PowerApps용 Excel 커넥터](/connectors/excel/)와 다릅니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* 작업 계정 또는 개인 Microsoft 계정에 대한 [Office 365 계정](https://www.office.com/)

  Excel 데이터는 저장소 폴더(예: OneDrive)에 쉼표로 구분된 값(CSV)으로 존재할 수 있습니다. 
  또한 동일한 CSV 파일을 [플랫 파일 커넥터](../logic-apps/logic-apps-enterprise-integration-flatfile.md)와 함께 사용할 수도 있습니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Excel Online 데이터에 액세스하려는 논리 앱입니다. 이 커넥터에서는 논리 앱을 시작하고 **되풀이** 트리거 같은 별도 트리거를 선택하도록 작업만 제공합니다.

## <a name="add-excel-action"></a>Excel 작업 추가

1. [Azure Portal](https://portal.azure.com)의 Logic App 디자이너에서 논리 앱을 엽니다(아직 열려 있지 않은 경우).

1. 트리거 아래에서 **새 단계**를 선택합니다.

1. 검색 상자에서 필터로 “excel”을 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

1. Office 365 계정에 로그인하라는 메시지가 표시되면 **로그인**을 선택합니다.

   자격 증명을 통해 Excel Online에 대한 연결을 만들고 데이터에 액세스하는 권한이 논리 앱에 부여됩니다.

1. 계속해서 선택한 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 빌드합니다.

## <a name="connector-reference"></a>커넥터 참조

트리거, 작업 및 커넥터의 OpenAPI에 설명 된 대로 제한 등의 기술 세부 정보에 대 한 (이전의 Swagger) 파일을 이러한 커넥터 참조 페이지를 참조 하세요.

* [비즈니스용 Excel Online](/connectors/excelonlinebusiness/)
* [OneDrive용 Excel Online](/connectors/excelonline/)

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
