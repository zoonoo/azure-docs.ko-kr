---
title: 자습서 - Azure Storage 계정에 Azure Active Directory 로그 보관(미리 보기) | Microsoft Docs
description: 저장소 계정으로 Azure Active Directory 로그를 푸시하도록 Azure 진단을 설정하는 방법 알아보기(미리 보기)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240246"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>자습서: Azure Storage 계정에 Azure Active Directory 로그 보관(미리 보기)

이 자습서에서는 Azure Storage 계정으로 Azure Active Directory 로그를 라우팅하도록 Azure Monitor 진단 설정을 지정하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건 

다음 작업을 수행해야 합니다.

* Azure 구독과 Azure Storage 계정. Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure Active Directory 테넌트.
* 테넌트의 전역 관리자 또는 보안 관리자인 사용자.

## <a name="archive-logs-to-an-azure-storage-account"></a>Azure Storage 계정에 로그 보관

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory** -> **활동** -> **감사 로그**를 클릭합니다. 
3. **설정 내보내기**를 클릭하여 진단 설정 블레이드를 엽니다. 기존 설정을 변경하려는 경우 **설정 편집**을 클릭하고, 새로 추가하려면 **진단 설정 추가**를 클릭합니다. 최대 세 개의 설정을 지정할 수 있습니다. 
    ![설정 내보내기](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "설정 내보내기")

4. 설정에 용도를 쉽게 알 수 있는 이름을 추가합니다. 예를 들어, "Azure Storage 계정에 보내기"가 있습니다. 
5. **저장소 계정에 보관** 확인란을 선택하고 **저장소 계정**을 클릭하여 Azure Storage 계정을 선택합니다. 
6. 로그를 라우팅하려는 Azure 구독 및 저장소 계정을 선택하고 **확인**을 클릭하여 구성을 닫습니다.
7. **AuditLogs** 확인란을 선택하여 저장소 계정에 감사 로그를 보냅니다. 
8. **SignInLogs** 확인란을 선택하여 저장소 계정에 로그인 로그를 보냅니다.
9. 슬라이더를 사용하여 로그 데이터의 보존 기간을 설정합니다. 기본적으로 이 값은 "0"이고 로그는 저장소 계정에 무기한 보존됩니다. 아니면 값을 설정할 수 있으며, 선택된 일수보다 오래된 이벤트는 자동으로 정리됩니다.
10. **저장**을 클릭하여 설정을 저장합니다.
    ![진단 설정](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "진단 설정")

11. 약 15분 후 저장소 계정으로 로그가 푸시되었는지 확인합니다. Azure Portal로 이동하고, **저장소 계정**을 클릭하고, 이전에 사용한 저장소 계정을 선택하고, **BLOB**을 클릭합니다. 
12. **감사 로그**에 **insights-log-audit**를 클릭합니다. **로그인 로그**에 **insights-log-signin**을 클릭합니다.
    ![저장소 계정](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "저장소 계정")

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Azure Monitor에서 로그인 로그 스키마 해석](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [질문과 대답 및 알려진 문제](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)