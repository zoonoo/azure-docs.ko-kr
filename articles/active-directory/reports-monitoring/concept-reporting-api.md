---
title: Azure AD reporting API 시작 | Microsoft Docs
description: Azure Active Directory Reporting API를 시작하는 방법
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7de7c87e5cf1747f4899f33d9e6b9cbf0bb430e1
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42141392"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory reporting API 시작하기

Azure Active Directory는 다양한 [보고서](overview-reports.md)를 제공합니다. 이러한 보고서의 데이터는 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 응용 프로그램에 매우 유용할 수 있습니다. 

Azure AD reporting API를 사용하면 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스할 수 있습니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다.

이 문서에서는 관련 API를 사용하여 보고 데이터에 액세스할 수 있는 로드맵을 제공합니다.

문제가 발생하면 [Azure Active Directory에 대한 지원을 받는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

reporting API에 액세스하려면, 스크립트를 사용하여 API에 액세스하려는 경우에도, 다음과 같은 작업이 필요합니다.

1. 역할(보안 읽기 권한자, 보안 관리자, 전역 관리자) 할당
2. 응용 프로그램 등록
3. 권한 부여
4. 구성 설정 수집

자세한 지침은 [Azure Active Directory reporting API에 액세스하기 위한 전제 조건](howto-configure-prerequisites-for-reporting-api.md)을 참조하세요.

## <a name="apis-with-graph-explorer"></a>Graph 탐색기가 있는 API

[MSGraph 탐색기](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 로그인 및 감사 API 데이터를 확인할 수 있습니다. Graph 탐색기 UI에서 두 로그인 단추를 사용하여 계정에 로그인하고 표시된 것처럼 테넌트에 대해 **AuditLog.Read.All** 및 **Directory.Read.All** 권한을 설정합니다.   

![Graph 탐색기](./media/concept-reporting-api/graph-explorer.png)

![권한 수정 UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>인증서를 사용하여 Azure AD Reporting API에 액세스 

사용자 개입 없이 보고 데이터를 검색하려는 경우 인증서와 함께 Azure AD Reporting API를 사용합니다.

자세한 지침은 [인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

 * [감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [로그인 활동 보고서 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Azure AD Reporting API의 오류 문제 해결](troubleshoot-graph-api.md)


