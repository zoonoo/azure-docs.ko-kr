---
title: 사용 및 인사이트 보고서 | 마이크로 소프트 문서
description: Azure Active Directory 포털의 사용 및 인사이트 보고서 소개
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008257"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 사용 및 인사이트 보고서

사용 및 인사이트 보고서를 통해 로그인 데이터에 대한 응용 프로그램 중심 보기를 얻을 수 있습니다. 다음 질문에 대한 답을 찾을 수 있습니다.

*   조직에서 가장 일반적으로 사용되는 응용 프로그램은 무엇입니까?
*   가장 실패한 응용 프로그램은 무엇입니까? 
*   각 응용 프로그램의 최상위 로그인 오류는 무엇입니까?

## <a name="prerequisites"></a>사전 요구 사항 

사용 및 인사이트 보고서에서 데이터에 액세스하려면 다음이 필요합니다.

* Azure AD 테넌트
* 로그인 데이터를 볼 수 있는 Azure AD 프리미엄(P1/P2) 라이선스
* 글로벌 관리자, 보안 관리자, 보안 판독기 또는 보고서 판독기 역할의 사용자입니다. 또한 모든 사용자(관리자가 아닌 사용자)는 자신의 로그인에 액세스할 수 있습니다. 

## <a name="access-the-usage-and-insights-report"></a>사용 및 인사이트 보고서에 액세스

1. [Azure 포털로](https://portal.azure.com)이동합니다.
2. 올바른 디렉터리를 선택한 다음 **Azure Active Directory를** 선택하고 **엔터프라이즈 응용 프로그램을 선택합니다.**
3. **활동** 섹션에서 사용 **& 인사이트를** 선택하여 보고서를 엽니다. 

![사용량 및 인사이트 보고서](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>보고서 사용

사용 및 인사이트 보고서는 하나 이상의 로그인 시도가 있는 응용 프로그램 목록을 보여 주며 성공적인 로그인 수, 실패한 로그인 및 성공률을 따라 정렬할 수 있습니다.

목록 맨 아래에 로드를 더 클릭하면 페이지에서 추가 응용 프로그램을 볼 수 있습니다. 날짜 범위를 선택하여 범위 내에서 사용된 모든 응용 프로그램을 볼 수 있습니다.

특정 응용 프로그램에 포커스를 설정할 수도 있습니다. **로그인 활동 보기를** 선택하여 응용 프로그램에 대한 시간 지남에 따라 로그인 활동과 상위 오류를 확인합니다.  

응용 프로그램 사용 그래프에서 날짜를 선택하면 응용 프로그램의 로그인 활동에 대한 자세한 목록이 표시됩니다.  

![사용량 및 인사이트 보고서](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>다음 단계

* [로그인 보고서](concept-sign-ins.md)