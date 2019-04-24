---
title: Azure Active Directory Reporting API에서 오류 해결 | Microsoft Docs
description: Azure Active Directory Reporting API를 호출하는 동안 오류에 대한 해결 방법을 제공합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b517204fb650020bdebf8172186f30fff58f722
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60284991"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Azure Active Directory Reporting API에서 오류 해결

이 문서에는 MS Graph API 및 해결 단계를 사용하여 작업 보고서에 액세스하는 동안 발생할 수 있는 일반적인 오류 메시지가 나와 있습니다.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Microsoft Graph V2 엔드포인트에 액세스하는 동안 500 HTTP 내부 서버 오류 발생

현재 Microsoft Graph v2 엔드포인트가 지원되지 않습니다. Microsoft Graph v1 엔드포인트를 사용하여 활동 로그에 액세스해야 합니다.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>오류: AD Graph에서 사용자 역할을 가져오지 못함

Graph 탐색기를 사용하여 로그인에 액세스할 때 이 오류 메시지가 표시될 수 있습니다. 다음 이미지와 같이 Graph 탐색기 UI에서 두 로그인 단추를 모두 사용하여 계정에 로그인했는지 확인합니다. 

![Graph 탐색기](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>오류: AD Graph에서 Premium 라이선스를 확인하지 못함 

Graph 탐색기를 사용하여 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 왼쪽 탐색에 있는 계정 아래에서 **권한 수정**을 선택하고 **Tasks.ReadWrite** 및 **Directory.Read.All**을 선택합니다. 

![권한 수정 UI](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>오류: 테넌트가 B2C가 아니거나 테넌트에 Premium 라이선스가 없음

로그인 보고서에 액세스하려면 Azure Active Directory P1(Premium 1) 라이선스가 필요합니다. 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 Azure AD P1 라이선스를 사용하여 테넌트에게 사용이 허가되었는지 확인합니다.

### <a name="error-user-is-not-in-the-allowed-roles"></a>오류: 사용자가 허용된 역할이 아님 

API를 사용하여 감사 로그 또는 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 Azure Active Directory 테넌트의 **보안 읽기 권한자** 또는 **보고서 구독자** 역할이 계정에 포함되어 있는지 확인합니다. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>오류: 애플리케이션에 AAD '디렉터리 데이터 읽기' 권한이 없음 

[Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)의 단계에 따라 애플리케이션이 적합한 권한 집합으로 실행되고 있는지 확인합니다. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>오류: 애플리케이션에 MSGraph API '모든 감사 로그 데이터 읽기' 권한이 없음

[Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)의 단계에 따라 애플리케이션이 적합한 권한 집합으로 실행되고 있는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

[감사 API 참조 사용](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[로그인 작업 보고서 API 참조 사용](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
