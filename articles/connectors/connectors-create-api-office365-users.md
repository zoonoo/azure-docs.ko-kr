---
title: "논리 앱에 Office 365 사용자 커넥터 추가 | Microsoft Docs"
description: "REST API 매개 변수를 사용하는 Office 365 사용자 커넥터 개요"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 8f544b781bbf11214183bc0f46215bb9e16c9f55
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017


---
# <a name="get-started-with-the-office-365-users-connector"></a>Office 365 사용자 커넥터 시작
Office 365 사용자에 연결하여 프로필 가져오기, 사용자 검색 등을 수행합니다. Office 365 사용자를 사용하여 다음을 수행할 수 있습니다.

* Office 365 사용자에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 부하 직원 가져오기, 관리자의 사용자 프로필 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 사용자의 부하 직원을 가져온 다음 이 정보를 사용하여 SQL Azure 데이터베이스를 업데이트합니다. 

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)를 참조하세요.

## <a name="create-a-connection-to-office-365-users"></a>Office 365 사용자에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 Office 365 사용자 계정에 로그인하고 논리 앱을 계정에 연결해야 합니다.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

연결을 만든 후 사용자 ID 등의 Office 365 사용자 속성을 입력합니다. 이 항목의 **REST API 참조** 에서는 이러한 속성에 대해 설명합니다.

## <a name="view-the-swagger"></a>swagger 보기

[swagger 정보](/connectors/officeusers/)를 참조하세요.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.
