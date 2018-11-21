---
title: Azure Active Directory 보고 대기 시간 | Microsoft Docs
description: Azure Portal에 보고 이벤트를 표시하는 데 걸리는 시간에 대해 알아보기
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e5ceae2959f79c677f5b89c0c3f0a487f92ad1c6
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623182"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory 보고 대기 시간

대기 시간은 Azure Active Directory(Azure AD) 보고 데이터가 [Azure Portal](https://portal.azure.com)에 표시되는 데 걸리는 시간입니다. 이 문서에서는 여러 보고서 유형의 예상 대기 시간을 안내합니다. 

## <a name="activity-reports"></a>작업 보고서

활동 보고서에는 다음 두 가지 유형이 있습니다.

- [로그인](concept-sign-ins.md) – 관리되는 애플리케이션 및 사용자 로그인 활동의 사용 정보를 제공합니다.
- [감사 로그](concept-audit-logs.md) - 사용자 및 그룹 관리, 관리되는 애플리케이션 및 디렉터리 활동에 대한 시스템 활동 정보를 제공합니다.

다음 표에는 활동 보고서에 대한 대기 시간 정보가 나와 있습니다. 

> [!NOTE]
> **대기 시간(95번째 백분위수)** 은 로그의 95%가 보고되는 시간을 가리키고, **대기 시간(99번째 백분위수)** 은 로그의 99%각 보고되는 시간을 가리킵니다. 
>

| 보고서 | 대기 시간(95번째 백분위수) |대기 시간(99번째 백분위수)|
| :-- | --- | --- | 
| 감사 로그 | 2분  | 5분  |
| 로그인 | 2분  | 5분 |

## <a name="security-reports"></a>보안 보고서

두 가지 형식의 보안 보고서가 있습니다.

- [위험한 로그인](concept-risky-sign-ins.md) - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다. 
- [위험 플래그가 지정된 사용자](concept-user-at-risk.md) - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 

다음 표에는 보안 보고서에 대한 대기 시간 정보가 나와 있습니다.

| 보고서 | 최소 | 평균 | 최대 |
| :-- | --- | --- | --- |
| 위험에 노출된 사용자          | 5분   | 15분  | 2시간  |
| 위험한 로그인         | 5분   | 15분  | 2시간  |

## <a name="risk-events"></a>위험 이벤트

Azure AD는 적응형 기계 학습 알고리즘 및 추론을 사용하여 사용자 계정과 관련된 의심스러운 동작을 감지합니다. 감지된 각 의심스러운 동작은 **위험 이벤트**라는 레코드에 저장됩니다.

다음 표에는 위험 이벤트에 대한 대기 시간 정보가 나와 있습니다.

| 보고서 | 최소 | 평균 | 최대 |
| :-- | --- | --- | --- |
| 익명 IP 주소에서 로그인 |5분 |15분 |2시간 |
| 알 수 없는 위치에서 로그인 |5분 |15분 |2시간 |
| 자격 증명이 손실된 사용자 |2시간 |4시간 |8시간 |
| 비정상적 위치로 불가능한 이동 |5분 |1시간 |8시간  |
| 감염된 장치에서 로그인 |2시간 |4시간 |8시간  |
| 의심스러운 작업이 있는 IP 주소에서 로그인 |2시간 |4시간 |8시간  |


## <a name="next-steps"></a>다음 단계

* [Azure AD 보고서 개요](overview-reports.md)
* [Azure AD 보고서에 대한 프로그래밍 방식 액세스](concept-reporting-api.md)
* [Azure Active Directory 위험 이벤트](concept-risk-events.md)
