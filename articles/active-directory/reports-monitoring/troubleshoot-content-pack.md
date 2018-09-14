---
title: Azure Active Directory 활동 로그 콘텐츠 팩 오류 문제 해결 | Microsoft Docs
description: Azure Active Directory 활동 콘텐츠 팩의 오류 메시지 목록 및 수정 단계를 제공합니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bf50dbf942dc7a82afbb60455be45b6c4b287ccd
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782178"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Azure Active Directory 활동 로그 콘텐츠 팩 오류 문제 해결 

|  |
|--|
|현재, Azure AD Power BI 콘텐츠 팩은 Azure AD Graph API를 사용하여 Azure AD 테넌트에 데이터를 검색합니다. 결과적으로, 콘텐츠 팩에서 사용할 수 있는 데이터와 [보고용 Microsoft Graph API](concept-reporting-api.md)를 사용하여 검색한 데이터 간에 몇 가지 불일치를 확인할 수 있습니다. |
|  |

Azure Active Directory 미리 보기용 Power BI 콘텐츠 팩에서 작업할 경우 다음과 같은 오류가 발생할 수 있습니다. 

- [새로 고침 실패](troubleshoot-content-pack.md#refresh-failed) 
- [데이터 원본 자격 증명을 업데이트하지 못했습니다.](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [데이터를 가져오는 데 너무 오래 걸립니다.](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
이 문서에서는 가능한 원인 및 이러한 오류를 해결하는 방법에 대한 정보를 제공합니다.
 
## <a name="refresh-failed"></a>새로 고침 실패 
 
**이 오류가 표시되는 방법**: Power BI 또는 새로 고침 기록 실패 상태로 인해 전자 메일을 받습니다. 


| 원인 | 해결 방법 |
| ---   | ---        |
| 콘텐츠 팩에 연결되는 사용자의 자격 증명이 다시 설정되었지만 콘텐츠 팩의 연결 설정에서 업데이트되지 않은 경우 새로 고침 실패 오류가 발생할 수 있습니다. | Power BI에서 Azure Active Directory 활동 로그 대시보드(Azure Active Directory 활동 로그)에 해당하는 데이터 집합을 찾고 일정 새로 고침을 선택하고 Azure AD 자격 증명을 입력합니다. |
| 기본 콘텐츠 팩에서 발생한 데이터 문제로 인해 새로 고침에 실패할 수 있습니다. | 지원 티켓을 파일로 저장합니다. 자세한 내용은 [Azure Active Directory에 대한 지원을 받는 방법](../fundamentals/active-directory-troubleshooting-support-howto.md)을 참조하세요.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>데이터 원본 자격 증명을 업데이트하지 못했습니다. 
 
**이 오류가 표시되는 경우**: Power BI에서 Azure Active Directory 활동 로그(미리 보기) 콘텐츠 팩에 연결하는 경우입니다. 

| 원인 | 해결 방법 |
| ---   | ---        |
| 연결된 사용자는 글로벌 관리자 또는 보안 판독기나 보안 관리자가 아닙니다. | 전역 관리자 또는 보안 판독기 또는 보안 관리자인 계정을 사용하여 콘텐츠 팩에 액세스합니다. |
| 사용자의 테넌트가 프리미엄 테넌트가 아니거나 적어도 프리미엄 라이선스 파일이 있는 사용자가 없습니다. | 지원 티켓을 파일로 저장합니다. 자세한 내용은 [Azure Active Directory에 대한 지원을 받는 방법](../fundamentals/active-directory-troubleshooting-support-howto.md)을 참조하세요.|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>데이터를 가져오는 데 너무 오래 걸립니다. 
 
**이 오류가 표시되는 경우**: Power BI에서 콘텐츠 팩을 연결하면 데이터 가져오기 프로세스가 Azure Active Directory 활동 로그의 대시보드를 준비하기 시작합니다. "*데이터 가져오기...*"라는 메시지가 표시됩니다.  

| 원인 | 해결 방법 |
| ---   | ---        |
| 테넌트의 크기에 따라 이 단계는 몇 분에서 최대 30분까지 걸릴 수 있습니다. | 기다려 주세요. 한 시간 내에 대시보드를 표시하도록 메시지가 변경되지 않는 경우 지원 티켓을 파일로 저장합니다. 자세한 내용은 [Azure Active Directory에 대한 지원을 받는 방법](../fundamentals/active-directory-troubleshooting-support-howto.md)을 참조하세요.|

## <a name="next-steps"></a>다음 단계

Azure Active Directory 미리 보기용 Power BI 콘텐츠 팩을 설치하려면 [여기](https://powerbi.microsoft.com/blog/azure-active-directory-meets-power-bi/)를 클릭합니다.


