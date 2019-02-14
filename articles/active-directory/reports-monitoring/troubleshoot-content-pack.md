---
title: Azure Active Directory 활동 로그 콘텐츠 팩 오류 문제 해결 | Microsoft Docs
description: Azure Active Directory 활동 콘텐츠 팩의 오류 메시지 목록 및 수정 단계를 제공합니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5220c5f28505bd83110705e08a6b1c7fb12529
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210699"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Azure Active Directory 활동 로그 콘텐츠 팩 오류 문제 해결 

|  |
|--|
|현재, Azure AD Power BI 콘텐츠 팩은 Azure AD Graph API를 사용하여 Azure AD 테넌트에 데이터를 검색합니다. 결과적으로, 콘텐츠 팩에서 사용할 수 있는 데이터와 [보고용 Microsoft Graph API](concept-reporting-api.md)를 사용하여 검색한 데이터 간에 몇 가지 불일치를 확인할 수 있습니다. |
|  |

Azure Active Directory(Azure AD)용 Power BI 콘텐츠 팩으로 작업할 때 다음과 같은 오류가 발생할 수 있습니다. 

- [새로 고침 실패](troubleshoot-content-pack.md#refresh-failed) 
- [데이터 원본 자격 증명을 업데이트하지 못했습니다.](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [데이터를 가져오는 데 너무 오래 걸립니다.](#data-import-is-too-slow) 

이 문서에서는 가능한 원인 및 이러한 오류를 해결하는 방법에 대한 정보를 제공합니다.
 
## <a name="refresh-failed"></a>새로 고침 실패 
 
**이 오류가 표시되는 방법**: Power BI 또는 새로 고침 기록 실패 상태로 인해 이메일을 받습니다. 


| 원인 | 해결 방법 |
| ---   | ---        |
| 콘텐츠 팩에 연결되는 사용자의 자격 증명이 다시 설정되었지만 콘텐츠 팩의 연결 설정에서 업데이트되지 않은 경우 새로 고침 실패 오류가 발생할 수 있습니다. | Power BI에서 Azure AD 활동 로그 대시보드(**Azure Active Directory 활동 로그**)에 해당하는 데이터 세트를 찾아서 새로 고침 예약을 선택하고 Azure AD 자격 증명을 입력합니다. |
| 기본 콘텐츠 팩에서 발생한 데이터 문제로 인해 새로 고침에 실패할 수 있습니다. | [지원 티켓을 제출합니다](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>데이터 원본 자격 증명을 업데이트하지 못했습니다. 
 
**이 오류가 표시되는 방법**: Power BI에서 Azure AD 활동 로그 콘텐츠 팩에 연결하면 표시됩니다. 

| 원인 | 해결 방법 |
| ---   | ---        |
| 연결된 사용자가 전역 관리자 또는 보안 읽기 권한자 또는 보안 관리자가 아닙니다. | 전역 관리자 또는 보안 읽기 권한자 또는 보안 관리자인 계정을 사용하여 콘텐츠 팩에 액세스합니다. |
| 사용자의 테넌트가 프리미엄 테넌트가 아니거나 적어도 프리미엄 라이선스 파일이 있는 사용자가 없습니다. | [지원 티켓을 제출합니다](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>데이터 가져오기가 너무 느립니다. 
 
**이 오류가 표시되는 방법**: Power BI에서 콘텐츠 팩을 연결하면 데이터 가져오기 프로세스가 Azure AD 활동 로그를 위해 대시보드를 준비하기 시작합니다. 메시지가 표시됩니다. 더 이상 진행되지 않고 **데이터를 가져오는 중...** 이라는 메시지가 표시됩니다.  

| 원인 | 해결 방법 |
| ---   | ---        |
| 이 단계는 테넌트의 크기에 따라 몇 분에서 30분까지 걸릴 수 있습니다. | 한 시간 내에 메시지가 대시보드 표시로 변경되지 않으면 [지원 티켓을 제출합니다](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>다음 단계

* [Azure AD 보고서용 Power BI 콘텐츠 팩 설치](quickstart-install-power-bi-content-pack.md)
* [Azure AD 보고서용 Power BI 콘텐츠 팩을 사용하여 데이터 시각화](howto-power-bi-content-pack.md)
* [Azure Active Directory를 지원하는 방법](../fundamentals/active-directory-troubleshooting-support-howto.md)
