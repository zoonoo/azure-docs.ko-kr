---
title: "Visual Studio WebApi 프로젝트에서 Azure AD 시작 | Microsoft Docs"
description: "Visual Studio 연결 서비스를 사용하여 Azure AD를 만들거나 연결한 후에 WebApi 프로젝트에 Azure Active Directory를 사용하여 시작하는 방법입니다."
services: active-directory
documentationcenter: 
author: kraigb
manager: mtillman
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 3d510c193ab8c7e65340275017cb2dcd4c76def0
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Azure Active Directory 및 Visual Studio 연결 서비스 시작(WebApi 프로젝트)
> [!div class="op_single_selector"]
> * [시작](vs-active-directory-webapi-getting-started.md)
> * [변경된 내용](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>컨트롤러에 액세스하려면 인증 필요
프로젝트의 모든 컨트롤러는 **Authorize** 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에서 정의한 API에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요. 더 세부적인 수준에서 권한을 설정하려면 특성을 컨트롤러 클래스에 적용하는 대신 인증이 필요한 각 메서드에 적용하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory에 대한 자세한 정보](https://azure.microsoft.com/services/active-directory/)

