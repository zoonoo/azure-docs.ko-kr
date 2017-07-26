---

title: "Azure AD Reporting API 시작 | Microsoft Docs"
description: "Azure Active Directory Reporting API를 시작하는 방법"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/18/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: fd750c1bf960678e0fecef61a67b73781d9777ef
ms.contentlocale: ko-kr
ms.lasthandoff: 06/20/2017


---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory Reporting API 시작

Azure Active Directory는 다양한 보고서를 제공합니다. 이러한 보고서의 데이터는 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 응용 프로그램에 매우 유용할 수 있습니다. Azure AD Reporting API는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다.

이 문서에서는 Azure AD Reporting API를 시작하는 데 필요한 정보를 제공합니다.
다음 섹션에는 감사 및 로그인 API를 사용하는 방법에 대한 자세한 정보가 있습니다. 다른 모든 API는 [Azure AD 보고서 및 이벤트](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview) 문서를 참조하세요.

질문과 대답(FAQ)는 [FAQ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq)를 참고하세요. 문제는 [지원 티켓을 파일로 저장하세요](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="learning-map"></a>학습 맵
1. **준비** - API 샘플을 테스트하기 전에 [Azure AD Reporting API에 액세스하기 위한 필수 구성 요소](active-directory-reporting-api-prerequisites-azure-portal.md)를 완료해야 합니다.
2. **탐색** - Reporting API의 첫 인상을 살펴봅니다.
   
   * [감사 API에 대한 샘플 사용](active-directory-reporting-api-audit-samples.md) 
   * [로그인 활동 보고서 API에 대한 샘플 사용](active-directory-reporting-api-sign-in-activity-samples.md)
3. **사용자 지정** - 고유한 솔루션을 만듭니다. 
   
   * [감사 API 참조 사용](active-directory-reporting-api-audit-reference.md) 
   * [로그인 활동 보고서 API 참조 사용](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>다음 단계
궁금하시다면 [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta)로 이동하여 모든 사용 가능한 Azure AD Graph API 끝점을 볼 수 있습니다.


