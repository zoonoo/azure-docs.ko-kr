---
title: Microsoft 365 Government에서 제공되는 Azure AD의 새로운 기능 | Microsoft 문서
description: 사용자에게 영향을 줄 수 있는 Microsoft 365 Government 클라우드 인스턴스의 Azure AD(Azure Active Directory)에 대한 몇 가지 변경 사항에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89318933"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 Government에서 제공되는 Azure Active Directory의 새로운 기능

다음 서비스를 사용하는 고객에게 적용되는 Microsoft 365 Government 클라우드 인스턴스에서 Azure AD(Azure Active Directory)를 일부 변경했습니다:

- Microsoft Azure Government

- Microsoft 365 Government – GCC High

- Microsoft 365 Government – DoD

이 문서는 Microsoft 365 Government – GCC 고객에게는 적용되지 않습니다.

## <a name="changes-to-the-initial-domain-name"></a>초기 도메인 이름에 대한 변경 내용

조직에서 Microsoft 365 Government 온라인 서비스 초기 등록 중에 조직의 도메인 이름 `<your-domain-name>.onmicrosoft.com`을 선택하라는 메시지가 표시되었습니다. .com 접미사의 도메인 이름이 이미 있는 경우 아무것도 변경되지 않습니다.

그러나 새 Microsoft 365 Government 서비스에 등록하는 경우 `.us` 접미사를 사용하여 도메인 이름을 선택하라는 메시지가 표시됩니다. 이것은 `<your-domain-name>.onmicrosoft.us` 입니다.

>[!Note]
>이 변경 사항은 CSP(클라우드 서비스 공급자)가 관리하는 고객에게는 적용되지 않습니다.

## <a name="changes-to-portal-access"></a>포털 액세스에 대한 변경

[엔드포인트 매핑 표](#endpoint-mapping)에 표시된 것처럼 Microsoft Azure Government, Microsoft 365 Government – GCC High 및 Microsoft 365 Government – DoD에 대한 포털 엔드포인트를 업데이트했습니다.

이전에는 고객이 전 세계 Azure(portal.azure.com) 및 Office 365(portal.office.com) 포털을 사용하여 로그인 할 수 있었습니다. 이 업데이트를 통해 고객은 이제 특정 Microsoft Azure Government, Microsoft 365 Government-GCC High 및 Microsoft 365 Government-DoD 포털을 사용하여 로그인해야 합니다.

## <a name="endpoint-mapping"></a>엔드포인트 매핑

다음 표에서는 모든 고객의 엔드포인트를 보여줍니다:

| 이름 | 엔드포인트 세부 정보 |
|------|------------------|
| 포털 |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 Government – GCC High: https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Azure Active Directory 기관 엔드포인트 | https://login.microsoftonline.us |
| Microsoft 365 Government용 Microsoft Graph API - GCC High | https://graph.microsoft.us |
| Microsoft 365 Government용 Microsoft Graph API - DoD | https://dod-graph.microsoft.us |
| Azure Government 서비스 엔드포인트 | 자세한 내용은 [Azure Government 개발자 가이드](../../azure-government/documentation-government-developer-guide.md) 참조 |
| Microsoft 365 Government - GCC High 엔드포인트 | 자세한 내용은 [Office 365 U.S. Government GCC High 엔드포인트](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) 참조 |
| Microsoft 365 Government - DoD | 자세한 내용은 [Office 365 U.S. Government DoD 엔드포인트](/office365/enterprise/office-365-u-s-government-dod-endpoints) 참조 |

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure Government란?](../../azure-government/documentation-government-welcome.md)

- [Azure Government AAD 기관 엔드포인트 업데이트](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [US Government 클라우드의 Microsoft Graph 엔드포인트](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC High 및 DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)