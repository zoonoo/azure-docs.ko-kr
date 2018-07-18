---
title: '문제 해결: 다운로드한 Azure Active Directory 활동 로그의 누락된 데이터 | Microsoft Docs'
description: 다운로드한 Azure Active Directory 활동 로그에서 누락된 데이터에 대한 해결 방법을 제공합니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d0638404ec6f5b6d13aa207ef54913c1bd3ecc1a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232230"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>다운로드한 Azure Active Directory 활동 로그에서 데이터를 찾을 수 없습니다.


## <a name="symptoms"></a>증상

활동 로그(감사 또는 로그인)를 다운로드했고 선택한 시간에 대한 모든 레코드가 표시되지 않습니다. 그 이유는 

 ![보고](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>원인

Azure Portal에서 활동 로그를 다운로드할 때 크기를 가장 최근에 먼저 정렬된 5000개의 레코드로 제한합니다. 

## <a name="resolution"></a>해결 방법

[Azure AD Reporting API](active-directory-reporting-api-getting-started.md)를 활용하여 특정 시점에서 최대 백만 개의 레코드를 가져올 수 있습니다. 기간에 따라(예: 매일 또는 매주) 증분 방식으로 레코드를 가져오도록 Reporting API를 호출하는 스크립트를 일정에 따라 실행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 보고 FAQ](active-directory-reporting-faq.md)를 참조하세요.

