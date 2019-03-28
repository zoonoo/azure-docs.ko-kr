---
title: Azure AD Power BI 콘텐츠 팩 설치 | Microsoft Docs
description: Azure AD Power BI 콘텐츠 팩을 설치하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69a69732d8cb42c248fa954ef9047e5876f40837
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437427"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>빠른 시작: Azure Active Directory Power BI 콘텐츠 팩 설치

|  |
|--|
|현재, Azure AD Power BI 콘텐츠 팩은 Azure AD Graph API를 사용하여 Azure AD 테넌트에 데이터를 검색합니다. 결과적으로, 콘텐츠 팩에서 사용할 수 있는 데이터와 [보고용 Microsoft Graph API](concept-reporting-api.md)를 사용하여 검색한 데이터 간에 몇 가지 불일치를 확인할 수 있습니다. |
|  |

Azure AD(Azure Active Directory)에 대한 Power BI 콘텐츠 팩을 통해 환경에서 보고 데이터를 시각화하는 기능을 제공합니다. 미리 빌드된 콘텐츠 팩을 다운로드하고, 이를 사용하여 Power BI에서 제공하는 풍부한 시각화 환경을 통해 디렉터리 내의 모든 활동을 보고할 수 있습니다. 또한 사용자 고유의 대시보드를 만들어 조직 내의 모든 사용자와 쉽게 공유할 수도 있습니다. 

이 빠른 시작에서는 콘텐츠 팩을 설치하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

* Power BI 계정 O365 또는 Azure AD 계정과 동일한 계정입니다. 
* Azure AD 테넌트 ID. Azure Portal의 [속성 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)에 있는 디렉터리의 **디렉터리 ID**입니다.
* Azure AD Premium(P1/P2) 라이선스 [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요.

## <a name="install-azure-ad-power-bi-content-pack"></a>Azure AD Power BI 콘텐츠 팩 설치 

1. Power BI 계정으로 [Power BI](https://app.powerbi.com/groups/me/getdata/services)에 로그인합니다. O365 또는 Azure AD 계정과 동일한 계정입니다.

2. **앱** 페이지에서 **Azure Active Directory 활동 로그**를 검색하고, **지금 설치하기**를 선택합니다. 

   ![Azure Active Directory Power BI 콘텐츠 팩](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. 팝업 창에서 Azure AD 테넌트 ID를 입력하고, 쿼리 기간(일 단위)에 대해 **7**을 입력하고, **다음**을 선택합니다.
    
   ![Azure Active Directory Power BI 콘텐츠 팩](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Azure Active Directory 작업 로그 대시보드가 만들어지면 이를 선택합니다.

   ![Azure Active Directory Power BI 콘텐츠 팩](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>다음 단계

* [Power BI 콘텐츠 팩을 사용합니다](howto-power-bi-content-pack.md).
* [콘텐츠 팩 오류 문제를 해결합니다](troubleshoot-content-pack.md).
