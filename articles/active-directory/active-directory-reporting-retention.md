---
title: "Azure Active Directory 보고서 보존 정책 | Microsoft Docs"
description: "Azure Active Directory에서 보고서 데이터 보존 정책"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 47594d1d435ee92d57150a01a99765bab0e87846
ms.openlocfilehash: 2ac62c2c04975167b243b49bf2c536900e77df73


---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory 보고서 보존 정책
*이 설명서는 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)의 일부입니다.*


이 문서에서는 Azure Active Directory의 여러 작업 보고서에 대한 데이터 보존과 함께 가장 일반적인 질문에 대한 대답을 제공합니다. 

작업 데이터 수집을 시작하려면 어떻게 해야 합니까?

| Azure AD 버전 | 수집 시작 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 구독 등록 시 |
| Azure AD Free | 처음으로 [Azure Active Directory 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)를 열거나 [보고 API](https://aka.ms/aadreports)를 사용할 때  |


Azure Portal에서 작업 데이터를 사용할 수 있는 시기는 언제입니까?

- **즉시** - 이미 Azure 클래식 포털에서 보고서로 작업한 적이 있는 경우
- **2시간 이내** - Azure 클래식 포털에서 보고 기능을 설정하지 않은 경우

보안 신호 수집을 시작하려면 어떻게 해야 합니까?  
보안 신호의 경우 ID 보호 센터를 사용하도록 옵트인할 때 수집 프로세스가 시작됩니다. 

수집된 데이터의 저장 기간은 얼마입니까?

**작업 보고서**    

| 보고서 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| 디렉터리 감사 | 7 일 | 30일 | 30일 |
| 로그인 작업 |    7 일 | 30일 | 30일 |

**보안 신호**

| 보고서 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| 위험한 로그인 | 7 일 | 30일 | 90일 |





<!--HONumber=Feb17_HO1-->


