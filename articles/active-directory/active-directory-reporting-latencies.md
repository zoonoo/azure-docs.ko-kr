---
title: "Azure Active Directory Reporting 대기 시간 | Microsoft Docs"
description: "Azure Active Directory에 보고 이벤트를 표시하는 데 걸리는 시간"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 346b14f8-d16d-4b07-8211-e6c5eec07062
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e15c82330ac27f58f3faec3a224123cb45c9b28b


---
# <a name="azure-active-directory-report-latencies"></a>Azure Active Directory 보고서 대기 시간
*이 설명서는 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)의 일부입니다.*

| 보고서 | 최소 | 평균 | 최대 |
| --- | --- | --- | --- |
| **보안 보고서** | | | |
| 비정상적인 로그인 작업 |2시간 |4시간 |8시간 |
| 알 수 없는 원본에서 로그인 |2시간 |4시간 |8시간 |
| 여러 번의 실패 후 로그인 |2시간 |4시간 |8시간 |
| 여러 지역에서의 로그인 |2시간 |4시간 |8시간 |
| 의심스러운 작업이 있는 IP 주소에서 로그인 |2시간 |4시간 |8시간 |
| 감염 가능성이 있는 장치에서 로그인 |2시간 |4시간 |8시간 |
| 비정상적인 로그인 활동을 포함하는 사용자 |2시간 |4시간 |8시간 |
| 자격 증명이 손실된 사용자 |2시간 |4시간 |8시간 |
| **응용 프로그램 보고서** | | | |
| 계정 프로비전 활동 |2시간 |4시간 |8시간 |
| 계정 프로비전 오류 |2시간 |4시간 |8시간 |
| 응용 프로그램 사용 현황 |2시간 |4시간 |8시간 |
| 암호 롤오버 상태 |2시간 |4시간 |8시간 |
| **감사 및 작업 보고서** | | | |
| 감사 보고서 |1분 |15분 |30분 |
| 암호 재설정 활동(Azure AD) |2시간 |4시간 |8시간 |
| 암호 재설정 활동(ID 관리자) |2시간 |4시간 |8시간 |
| 암호 재설정 등록 활동(Azure AD) |2시간 |4시간 |8시간 |
| 암호 재설정 등록 활동(ID 관리자) |2시간 |4시간 |8시간 |
| 셀프 서비스 그룹 활동(Azure AD) |2시간 |4시간 |8시간 |
| 셀프 서비스 그룹 활동(ID 관리자) |2시간 |4시간 |8시간 |
| **RMS 보고서** | | | |
| 가장 활동적인 RMS 사용자 |2시간 |4시간 |8시간 |
| RMS 사용 현황 |2시간 |4시간 |8시간 |
| RMS 장치 사용 |2시간 |4시간 |8시간 |
| RMS 사용 응용 프로그램 사용 현황 |2시간 |4시간 |8시간 |
| **비공개 미리 보기 보고서** | | | |
| 모든 사용자 로그인 활동 |2시간 |4시간 |8시간 |




<!--HONumber=Nov16_HO3-->


