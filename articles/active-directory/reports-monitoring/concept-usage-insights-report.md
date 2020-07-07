---
title: 사용량 및 통찰력 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 사용 현황 및 정보 보고서 소개
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
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74008257"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 사용 현황 및 정보 보고서

사용량 및 통찰력 보고서를 사용 하 여 로그인 데이터의 응용 프로그램 중심 보기를 가져올 수 있습니다. 다음 질문에 대 한 답을 찾을 수 있습니다.

*   조직에서 가장 많이 사용 되는 응용 프로그램은 무엇 인가요?
*   실패 한 로그인이 가장 많은 응용 프로그램은 무엇 인가요? 
*   각 응용 프로그램에 대 한 상위 로그인 오류는 무엇 인가요?

## <a name="prerequisites"></a>사전 요구 사항 

사용량 및 통찰력 보고서의 데이터에 액세스 하려면 다음이 필요 합니다.

* Azure AD 테넌트
* 로그인 데이터를 볼 수 있는 Azure AD premium (P1/P2) 라이선스
* 전역 관리자, 보안 관리자, 보안 읽기 권한자 또는 보고서 구독자 역할의 사용자입니다. 또한 관리자가 아닌 사용자는 자신의 로그인에 액세스할 수 있습니다. 

## <a name="access-the-usage-and-insights-report"></a>사용량 및 통찰력 보고서 액세스

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 올바른 디렉터리를 선택한 다음 **Azure Active Directory** 를 선택 하 고 **엔터프라이즈 응용 프로그램**을 선택 합니다.
3. **활동** 섹션에서 **사용 현황 & 정보** 를 선택 하 여 보고서를 엽니다. 

![사용량 및 인사이트 보고서](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>보고서 사용

사용 및 정보 보고서에는 하나 이상의 로그인을 사용 하는 응용 프로그램 목록이 표시 되며 성공한 로그인 횟수, 실패 한 로그인 횟수 및 성공률을 기준으로 정렬할 수 있습니다.

목록의 맨 아래에서 더 로드를 클릭 하면 페이지에서 추가 응용 프로그램을 볼 수 있습니다. 날짜 범위를 선택 하 여 범위 내에 사용 된 모든 응용 프로그램을 볼 수 있습니다.

특정 응용 프로그램에 포커스를 설정할 수도 있습니다. **로그인 활동 보기** 를 선택 하 여 응용 프로그램 및 상위 오류에 대 한 시간에 따른 로그인 활동을 확인 합니다.  

응용 프로그램 사용 그래프에서 날짜를 선택 하면 응용 프로그램에 대 한 로그인 활동의 자세한 목록이 표시 됩니다.  

![사용량 및 인사이트 보고서](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>다음 단계

* [로그인 보고서](concept-sign-ins.md)