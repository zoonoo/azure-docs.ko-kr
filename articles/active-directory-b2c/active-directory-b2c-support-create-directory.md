---
title: "Azure Active Directory B2C: 테넌트 생성 문제 해결 | Microsoft Docs"
description: "Azure Active Directory 또는 Azure Active Directory B2C 테넌트 생성과 관련된 문제 및 해결 방법입니다."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 81af4536fc223319369aff262d42149cfbf1a1e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Azure Active Directory 또는 Azure Active Directory B2C 테넌트 생성 문제 해결 

## <a name="create-an-azure-ad-tenant"></a>Azure AD 테넌트 만들기
첫 번째 시도에서 Azure AD(Azure Active Directory) 테넌트를 만들 수 없는 경우 다시 시도하세요. 문제가 지속되면 Azure 지원에 문의하세요.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기
[Azure AD B2C(Azure Active Directory B2C) 테넌트를 만드는](active-directory-b2c-get-started.md) 동안 문제가 발생할 경우 다음 옵션을 시도해보세요.

* Azure AD B2C 테넌트가 테넌트 목록에 표시되지 않는 경우 다시 테넌트를 만들어 보세요.
* Azure AD B2C 테넌트가 테넌트 목록에 표시되지만 다음과 같은 오류 메시지가 나타나는 경우 테넌트를 삭제하고 다시 만드세요.

    "B2C 테넌트 'contosob2c' 생성을 완료할 수 없습니다. 자세한 지침을 보려면 이 [링크](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)를 방문하세요."
* 기존 Azure AD B2C 테넌트를 삭제하고 동일한 도메인 이름으로 다시 만들 때 발생하는 알려진 문제가 있습니다. 새 Azure AD B2C 테넌트를 만들 때는 다른 도메인 이름을 사용해야 합니다.
* 이러한 해결 방법이 효과가 없으면 Azure 지원에 문의하세요. 자세한 내용은 [Azure AD B2C에 대한 파일 지원 요청](active-directory-b2c-support.md)을 참조하세요.

