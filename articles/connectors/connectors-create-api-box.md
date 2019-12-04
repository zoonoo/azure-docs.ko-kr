---
title: Box에 연결
description: Box REST API 및 Azure Logic Apps를 사용하여 파일 만들기 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 38e061e918d445de07961af1789891f44c59090a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789873"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Box에서 파일 만들기 및 관리

이 문서에서는 Box 커넥터가 있는 논리 앱 내의 Box에서 파일을 만들고 관리하는 방법을 보여 줍니다. 이러한 방식으로 파일 및 기타 작업을 관리하는 작업(task) 및 워크플로를 자동화하는 논리 앱을 만들 수 있습니다.

* Box에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다.

* 파일을 만들거나 업데이트할 때 자동화된 작업 및 워크플로를 트리거합니다.

* 파일을 복사 하거나 파일을 삭제 하는 작업을 실행 합니다.

  이러한 작업으로 응답을 가져올 때 출력을 다른 작업에 사용할 수 있도록 설정합니다. 
  예를 들어, Box에서 파일이 변경된 경우 Office 365를 사용하여 해당 파일을 이메일로 보낼 수 있습니다.

## <a name="prerequisites"></a>전제 조건

* [Box 계정](https://www.box.com/home)

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Box 계정에 액세스하려는 논리 앱입니다. Box 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식
논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토하세요.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI (이전의 Swagger) 파일에 설명 된 대로 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/box/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.