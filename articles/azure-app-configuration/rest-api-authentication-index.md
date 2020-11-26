---
title: Azure 앱 구성 REST API-인증
description: Azure 앱 구성을 사용 하는 인증에 대 한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183467"
---
# <a name="authentication"></a>인증

모든 HTTP 요청을 인증 해야 합니다. 지원 되는 인증 스키마는 다음과 같습니다.

## <a name="hmac"></a>HMAC

[HMAC 인증은](./rest-api-authentication-hmac.md) 임의로 생성 된 암호를 사용 하 여 요청 페이로드에 서명 합니다. 이 인증 방법을 사용 하는 요청에 대 한 권한을 부여 하는 방법에 대 한 자세한 내용은 [HMAC 권한 부여](./rest-api-authorization-hmac.md) 섹션에서 찾을 수 있습니다.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (AZURE AD) 인증은](../active-directory/authentication/overview-authentication.md) Azure Active Directory에서 가져온 전달자 토큰을 활용 하 여 요청을 인증 합니다. 이 인증 방법을 사용 하는 요청을 인증 하는 방법에 대 한 자세한 내용은 [AZURE AD 권한 부여](./rest-api-authorization-azure-ad.md) 섹션에서 확인할 수 있습니다.