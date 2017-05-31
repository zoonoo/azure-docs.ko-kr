---
title: "Azure Logic Apps의 GitHub 커넥터 | Microsoft Docs"
description: "Azure 앱 서비스로 논리 앱을 만듭니다. GitHub은 서비스를 호스팅하는 웹 기반 Git 리포지토리입니다. 고유한 기능을 추가할 뿐 아니라 Git의 분산된 수정 버전 제어 및 SCM(소스 코드 관리) 기능을 모두 제공합니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 2bb378984cdeb7fc5b6fddd933d1019b3bfd1232
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017


---
# <a name="get-started-with-the-github-connector"></a>GitHub 커넥터 시작
GitHub은 서비스를 호스팅하는 웹 기반 Git 리포지토리입니다. 고유한 기능을 추가할 뿐 아니라 Git의 분산된 수정 버전 제어 및 SCM(소스 코드 관리) 기능을 모두 제공합니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)를 참조하세요.

## <a name="create-a-connection-to-github"></a>GitHub에 대한 연결 만들기
GitHub로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다. 

| 속성 | 필수 | 설명 |
| --- | --- | --- |
| 신뢰 |예 |GitHub 자격 증명 제공 |

연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="view-the-swagger"></a>swagger 보기

[swagger 정보](/connectors/github/)를 참조하세요.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.
