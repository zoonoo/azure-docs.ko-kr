---
title: '문제 해결: 다운로드한 활동 로그의 누락된 데이터 | Microsoft Docs'
description: 다운로드한 Azure Active Directory 활동 로그에서 누락된 데이터에 대한 해결 방법을 제공합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5003d7b644a0c75401a17ed6a37f31acd8180aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85608078"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>다운로드한 Azure Active Directory 활동 로그에서 데이터를 찾을 수 없습니다.

## <a name="symptoms"></a>증상

활동 로그(감사 또는 로그인)를 다운로드했고 선택한 시간에 대한 모든 레코드가 표시되지 않습니다. 그 이유는 

 ![보고](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>원인

Azure Portal에서 활동 로그를 다운로드할 때 규모를 가장 최근 처음 기준 250,000개의 레코드로 제한합니다. 

## <a name="resolution"></a>해결 방법

[Azure AD Reporting API](concept-reporting-api.md)를 활용하여 특정 시점에서 최대 백만 개의 레코드를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 보고서 FAQ](reports-faq.md)

