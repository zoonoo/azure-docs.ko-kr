---
title: Azure App Configuration REST API - 권한 부여
description: Azure App Configuration REST API를 사용하는 권한 부여에 대한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932645"
---
# <a name="authorization"></a>권한 부여

권한 부여는 요청을 생성할 때 호출자에게 부여되는 권한을 결정하는 데 사용되는 프로시저를 나타냅니다. 여러 권한 부여 모델이 있습니다. 요청에 사용되는 권한 부여 모델은 사용되는 [인증](./rest-api-authentication-index.md) 방법에 따라 달라집니다. 권한 부여 모델이 아래에 나열되어 있습니다.

## <a name="hmac"></a>HMAC

HMAC 인증과 연결된 [권한 부여 모델](./rest-api-authorization-hmac.md) 모델은 사용 권한을 읽기 전용 또는 읽기-쓰기용으로 나눕니다. 자세한 내용은 [HMAC 권한 부여](./rest-api-authorization-hmac.md) 페이지를 참조하세요.

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory(Azure AD) 인증과 연결된 [권한 부여 모델](./rest-api-authorization-azure-ad.md)은 Azure RBAC를 사용하여 권한을 제어합니다. 자세한 내용은 [Azure AD 권한 부여](./rest-api-authorization-azure-ad.md) 페이지를 참조하세요.
