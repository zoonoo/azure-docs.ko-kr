---
title: Azure Maps의 인증 세부 정보 보기
description: Azure Portal을 사용하여 Azure Maps의 인증 세부 정보를 봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87126461"
---
Azure Portal에서 Azure Maps 계정 인증 세부 정보를 볼 수 있습니다. 계정의 **설정** 메뉴에서 **인증** 을 선택합니다.

![인증 세부 정보](../media/how-to-manage-authentication/how-to-view-auth.png)

Azure Maps 계정이 만들어지면 Azure Maps `x-ms-client-id` 값이 Azure Portal 인증 정보 페이지에 표시됩니다. 이 값은 REST API 요청에 사용되는 계정을 나타냅니다. Azure Maps에서 Azure AD 인증을 사용하는 경우 http 요청을 수행하기 전에 이 값을 애플리케이션 구성에 저장하고 검색해야 합니다.
