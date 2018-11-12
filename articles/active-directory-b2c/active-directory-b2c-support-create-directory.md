---
title: Azure Active Directory B2C에서 테넌트 생성 문제 해결 | Microsoft Docs
description: Azure Active Directory 또는 Azure Active Directory B2C 테넌트 생성과 관련된 문제 및 해결 방법입니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d510f51ed28c28c698437f905c4911a8c32e5ce
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234989"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Azure Active Directory 또는 Azure Active Directory B2C 테넌트 생성 문제 해결 

## <a name="create-an-azure-ad-tenant"></a>Azure AD 테넌트 만들기
첫 번째 시도에서 Azure AD(Azure Active Directory) 테넌트를 만들 수 없는 경우 다시 시도하세요. 문제가 지속되면 Azure 지원에 문의하세요.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기
[Azure AD B2C(Azure Active Directory B2C) 테넌트를 만드는](active-directory-b2c-get-started.md) 동안 문제가 발생할 경우 다음 옵션을 시도해보세요.

* Azure AD B2C 테넌트가 테넌트 목록에 표시되지 않는 경우 다시 테넌트를 만들어 보세요.
* Azure AD B2C 테넌트가 테넌트 목록에 표시되지만 다음과 같은 오류 메시지가 나타나는 경우 테넌트를 삭제하고 다시 만드세요.

    "B2C 테넌트 'contosob2c' 생성을 완료할 수 없습니다. 자세한 지침을 보려면 이 [링크](https://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)를 방문하세요."
* 기존 Azure AD B2C 테넌트를 삭제하고 동일한 도메인 이름으로 다시 만들 때 발생하는 알려진 문제가 있습니다. 새 Azure AD B2C 테넌트를 만들 때는 다른 도메인 이름을 사용해야 합니다.
* 이러한 해결 방법이 효과가 없으면 Azure 지원에 문의하세요. 자세한 내용은 [Azure AD B2C에 대한 파일 지원 요청](active-directory-b2c-support.md)을 참조하세요.

