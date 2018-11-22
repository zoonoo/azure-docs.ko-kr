---
title: Azure Active Directory 보고서 보존 정책 | Microsoft Docs
description: Azure Active Directory에서 보고서 데이터 보존 정책
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 601169cc62a99438f661adc06ab166b545606edb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624593"
---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory 보고서 보존 정책

이 문서에서는 Azure Active Directory의 다른 작업 보고서에 대한 데이터 보존 정책에 대해 알아봅니다. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Azure AD는 언제 데이터 수집을 시작하나요?

| Azure AD 버전 | 수집 시작 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 구독을 위한 가입 시기 |
| Azure AD Free | 처음으로 [Azure Active Directory 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)를 열거나 [보고 API](https://aka.ms/aadreports)를 사용할 때  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Azure Portal에서 활동 데이터를 사용할 수 있는 시기는 언제입니까?

- **즉시** - 이미 Azure Portal에서 보고서로 작업한 적이 있는 경우
- **2시간 이내** - Azure Portal에서 보고 기능을 설정하지 않은 경우

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Azure AD는 언제 보안 신호 데이터 수집을 시작하나요?  

보안 신호의 경우 **ID 보호 센터**를 사용하도록 옵트인할 때 수집 프로세스가 시작됩니다. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Azure AD는 데이터를 얼마나 오래 저장하나요?

**작업 보고서**    

| 보고서                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 디렉터리 감사        | 7 일        | 30일             | 30일             |
| 로그인 작업       | 해당 없음           | 30일             | 30일             |
| Azure MFA 사용        | 30일       | 30일             | 30일             |

**보안 신호**

| 보고서         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| 위험에 노출된 사용자  | 7 일        | 30일             | 90일             |
| 위험한 로그인 | 7 일        | 30일             | 90일             |

---
