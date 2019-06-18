---
title: Azure Active Directory 포털의 사용량 및 insights 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 사용량 및 insights 보고서 소개
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806358"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 사용량 및 insights 보고서

사용량 및 insights 보고서를 사용 하 여 로그인 데이터의 응용 프로그램 중심 보기를 가져올 수 있습니다. 다음 질문에 대 한 답변을 찾을 수 있습니다.

*   응용 프로그램 내 조직에서 사용 되는 맨 이란?
*   응용 프로그램에 가장 많이 실패 한 로그인 있나요? 
*   각 응용 프로그램에 대 한 상위 로그인 한 오류는 무엇 인가요?

## <a name="prerequisites"></a>필수 조건 

사용량 및 insights 보고서에서 데이터에 액세스 하려면 다음을 수행 해야 합니다.

* Azure AD 테넌트
* 로그인 데이터를 보려면 Azure AD premium (P1/P2) 라이선스
* 전역 관리자, 보안 관리자, 보안 읽기 권한자 또는 보고서 판독기 역할의 사용자입니다. 또한 모든 사용자 (비관리자)는 자신의 로그인 액세스할 수 있습니다. 

## <a name="access-the-usage-and-insights-report"></a>사용량 및 insights 보고서에 액세스

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 올바른 디렉터리를 선택한 다음 선택 **Azure Active Directory** 선택한 **Enterprise 응용 프로그램**합니다.
3. **활동** 섹션에서 **사용량 및 insights** 보고서를 엽니다. 

![사용량 및 인사이트 보고서](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>보고서 사용

사용량과 insights 보고서 시도에서 하나 이상의 로그인을 사용 하 여 응용 프로그램의 목록을 표시 하 고 성공적인 로그인, 실패 한 로그인 및 성공률이 수가 정렬할 수 있습니다.

목록의 맨 아래에서 더 클릭 하면 부하를 사용 하면 페이지에서 추가 응용 프로그램을 볼 수 있습니다. 범위 내에서 사용 된 모든 응용 프로그램을 보려는 날짜 범위를 선택할 수 있습니다.

또한 특정 응용 프로그램에서 포커스를 설정할 수 있습니다. 선택 **로그인 활동 보기** 상위 오류 뿐만 아니라 응용 프로그램에 대 한 기간별 활동의 부호를 표시 합니다.  

응용 프로그램 사용 현황 그래프에서 날짜를 선택 하면 응용 프로그램에 대 한 로그인 활동의 자세한 목록을 가져옵니다.  

![사용량 및 인사이트 보고서](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>다음 단계

* [로그인 보고서](concept-sign-ins.md)