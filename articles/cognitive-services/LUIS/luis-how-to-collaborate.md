---
title: Azure의 LUIS 앱에서 다른 참가자와 공동 작업 | Microsoft Docs
description: LUIS(Language Understanding) 응용 프로그램에서 다른 참가자와 공동으로 작업하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: diberry
ms.openlocfilehash: 9ea0269439b3d00bf36186cf2fd5c73311526bec
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225604"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>LUIS(Language Understanding) 앱에서 다른 사용자와 공동 작업  

LUIS 앱에서 다른 사용자와 공동으로 작업할 수 있습니다. 

## <a name="owner-and-collaborators"></a>소유자 및 협력자
앱의 소유자는 한 명이지만 협력자는 많을 수 있습니다. 

## <a name="add-collaborator"></a>협력자 추가

협력자가 LUIS 앱을 편집할 수 있도록 허용하려면 LUIS 앱의 **설정** 페이지에서 협력자의 메일을 입력하고 **협력자 추가**를 클릭합니다.

![협력자 추가](./media/luis-how-to-collaborate/add-collaborator.png)

* 협력자는 사용자가 앱에서 작업하는 동안 로그인하여 LUIS 앱을 편집할 수 있습니다. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* 협력자는 다른 협력자를 추가할 수 없습니다.

Active Directory 사용자 계정에 자세히 알아보려면 [Azure Active Directory 테넌트 사용자](luis-how-to-account-settings.md#azure-active-directory-tenant-user)를 참조하세요. 

## <a name="set-application-as-public"></a>응용 프로그램을 공용으로 설정
자세한 내용은 [공용 앱 끝점 액세스](luis-concept-security.md#public-app-endpoint-access)를 참조하세요.

### <a name="transfer-of-ownership"></a>소유권 이전
LUIS는 현재 소유권 이전을 지원하지 않지만, 앱을 내보내고 다른 LUIS 사용자가 앱을 가져올 수 있습니다. 두 응용 프로그램의 LUIS 점수가 약간 다를 수도 있습니다. 
