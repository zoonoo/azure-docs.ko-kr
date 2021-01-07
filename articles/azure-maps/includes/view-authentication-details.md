---
title: Azure Maps의 인증 세부 정보 보기
description: Azure Maps의 인증 세부 정보를 보려면 Azure Portal를 사용 합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87126461"
---
Azure Portal에서 Azure Maps 계정 인증 세부 정보를 볼 수 있습니다. 계정에서 **설정** 메뉴의 **인증**을 선택 합니다.

![인증 세부 정보](../media/how-to-manage-authentication/how-to-view-auth.png)

Azure Maps 계정이 만들어지면 Azure Maps `x-ms-client-id` 값이 Azure Portal 인증 정보 페이지에 표시 됩니다. 이 값은 REST API 요청에 사용 되는 계정을 나타냅니다. Azure Maps에서 Azure AD 인증을 사용 하는 경우 http 요청을 수행 하기 전에이 값을 응용 프로그램 구성에 저장 하 고 검색 해야 합니다.
