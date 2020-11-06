---
title: Azure 앱 구성 REST API-권한 부여
description: Azure 앱 구성을 사용 하 여 권한 부여에 대 한 참조 페이지 REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424179"
---
# <a name="authorization"></a>권한 부여

권한 부여는 요청을 만들 때 호출자에 게 부여 된 권한을 확인 하는 데 사용 되는 프로시저를 나타냅니다. 여러 권한 부여 모델이 있습니다. 요청에 사용 되는 권한 부여 모델은 사용 되는 [인증](./rest-api-authentication-index.md) 방법에 따라 달라 집니다. 권한 부여 모델은 아래에 나열 되어 있습니다.

## <a name="hmac"></a>HMAC

HMAC 인증과 연결 된 [권한 부여 모델](./rest-api-authorization-hmac.md) 모델은 사용 권한을 읽기 전용 또는 읽기/쓰기로 분할 합니다. 자세한 내용은 [HMAC 권한 부여](./rest-api-authorization-hmac.md) 페이지를 참조 하세요.

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 인증에 연결 된 [권한 부여 모델](./rest-api-authorization-azure-ad.md) 은 azure RBAC를 사용 하 여 권한을 제어 합니다. 자세한 내용은 [AZURE AD 권한 부여](./rest-api-authorization-azure-ad.md) 페이지를 참조 하세요.
