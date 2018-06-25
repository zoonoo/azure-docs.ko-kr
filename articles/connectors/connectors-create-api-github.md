---
title: GitHub에 연결 - Azure Logic Apps | Microsoft Docs
description: GitHub REST API 및 Azure Logic Apps를 사용하여 GitHub 이벤트 모니터링
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ce567dc631c3a147b795eb2355a4961faa8881d6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295815"
---
# <a name="connect-to-github"></a>GitHub에 연결

GitHub는 Git의 배포된 버전 관리와 SCM(소스 코드 관리) 기능 및 다른 기능을 제공하는 웹 기반 리포지토리 호스팅 서비스입니다.

GitHub 커넥터를 시작하려면 [먼저 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>GitHub에 대한 연결 만들기

논리 앱에서 GitHub 커넥터를 사용하려면 먼저 *연결*을 만들고, 다음 속성에 대한 세부 정보를 제공해야 합니다. 

| 자산 | 필수 | 설명 | 
| -------- | -------- | ----------- | 
| 신뢰 | 예 | GitHub 자격 증명을 제공합니다. |

연결을 만든 후에 이 작업을 실행하고 이 문서에서 설명하는 트리거를 수신 대기할 수 있습니다.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

Swagger 및 제한에 정의된 트리거 및 작업은 [커넥터 정보](/connectors/github/)를 검토하세요.

## <a name="find-more-connectors"></a>더 많은 커넥터 찾기

* [커넥터 목록](apis-list.md)을 검토하세요.