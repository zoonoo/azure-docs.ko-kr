---
title: Office 365 비디오에 연결
description: Office 365 비디오 REST API 및 Azure Logic Apps로 비디오 관리
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: bf3c7c4faac3fcd4dd0c3f368fbe18a224ed591b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789490"
---
# <a name="get-started-with-the-office365-video-connector"></a>Office 365 비디오 커넥터 시작

Office 365 비디오에 연결하여 Office 365 비디오에 대한 정보 가져오기, 비디오 목록 가져오기 등을 수행합니다. Office 365 비디오로 다음을 수행할 수 있습니다.

* Office 365 비디오에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 

* 비디오 포털 상태 확인, 채널의 모든 비디오 목록 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 

예를 들어 Bing 검색 커넥터를 사용하여 Office 365 비디오를 검색한 다음, Office 365 비디오 커넥터를 사용하여 해당 비디오에 대한 정보를 가져올 수 있습니다. 비디오가 요구 사항을 충족하면 Facebook에 이 비디오를 게시할 수 있습니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-office365-video-connector"></a>Office 365 비디오 커넥터에 대한 연결 만들기

논리 앱에 이 커넥터를 추가할 때 Office 365 비디오 계정에 로그인하고 논리 앱을 계정에 연결해야 합니다.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

연결을 만든 후 테넌트 이름 또는 채널 ID 등의 Office 365 비디오 속성을 입력합니다. 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/office365videoconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.