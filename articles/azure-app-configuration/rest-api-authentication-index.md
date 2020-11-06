---
title: Azure 앱 구성 REST API-인증
description: Azure 앱 구성을 사용 하는 인증에 대 한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424330"
---
# <a name="authentication"></a>인증

모든 HTTP 요청을 인증 해야 합니다. 지원 되는 인증 스키마는 다음과 같습니다.

## <a name="hmac"></a>HMAC

[HMAC 인증은](./rest-api-authentication-hmac.md) 임의로 생성 된 암호를 사용 하 여 요청 페이로드에 서명 합니다. 이 인증 방법을 사용 하는 요청에 대 한 권한을 부여 하는 방법에 대 한 자세한 내용은 [HMAC 권한 부여](./rest-api-authorization-hmac.md) 섹션에서 찾을 수 있습니다.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (AZURE AD) 인증은](/azure/active-directory/authentication/overview-authentication) Azure Active Directory에서 가져온 전달자 토큰을 활용 하 여 요청을 인증 합니다. 이 인증 방법을 사용 하는 요청을 인증 하는 방법에 대 한 자세한 내용은 [AZURE AD 권한 부여](./rest-api-authorization-azure-ad.md) 섹션에서 확인할 수 있습니다.
