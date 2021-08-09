---
title: Azure App Configuration REST API - 인증
description: Azure App Configuration REST API를 사용하는 인증에 대한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932696"
---
# <a name="authentication"></a>인증

모든 HTTP 요청은 인증되어야 합니다. 지원되는 인증 스키마는 다음과 같습니다.

## <a name="hmac"></a>HMAC

[HMAC 인증](./rest-api-authentication-hmac.md)은 임의로 생성된 비밀을 사용하여 요청 페이로드에 서명합니다. 이 인증 방법을 사용하는 요청에 권한이 부여되는 방법에 대한 자세한 내용은 [HMAC 권한 부여](./rest-api-authorization-hmac.md) 섹션에서 확인할 수 있습니다.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure AD(Azure Active Directory) 인증은](../active-directory/authentication/overview-authentication.md) Azure Active Directory에서 가져온 전달자 토큰을 활용하여 요청을 인증합니다. 이 인증 방법을 사용하는 요청에 권한이 부여되는 방법에 대한 자세한 내용은 [Azure AD 권한 부여](./rest-api-authorization-azure-ad.md) 섹션에서 확인할 수 있습니다.