---
title: Microsoft 365 Government-Azure Active Directory에서에서 Azure Active Directory의 새로운 기능 | Microsoft Docs
description: Azure Active Directory (Azure AD)에 있습니다 영향을 줄 수 있는 Microsoft 365 Government 클라우드 인스턴스를 일부 변경에 알아봅니다.
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
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258896"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Microsoft 365 Government의 Azure Active Directory의 새로운 기능

다음 서비스를 사용 하 여 고객에 게 적용할 수 있는 Microsoft 365 Government 클라우드 인스턴스에 Azure Active Directory (Azure AD)에 일부 변경 내용이 만들었고:

- Microsoft Azure Government

- Microsoft 365 Government – GCC High

- Microsoft 365 Government DoD

이 문서에서는 Microsoft 365 Government – GCC 고객에 게 적용 되지 않습니다.

## <a name="changes-to-the-initial-domain-name"></a>초기 도메인 이름으로 변경

조직의 도메인 이름을 선택 하는 요청 받았습니다 조직의 초기 Microsoft 365 Government online service 등록, 하는 동안 `<your-domain-name>.onmicrosoft.com`합니다. .Com 접미사를 사용 하 여 도메인 이름을 이미 있는 경우 아무 것도 변경 됩니다.

그러나 새 Microsoft 365 Government 서비스에 등록 하는 경우 메시지가 표시 됩니다 사용 하 여 도메인 이름을 선택 하 여 `.us` 접미사. 따라서 것 `<your-domain-name>.onmicrosoft.us`입니다.

>[!Note]
>클라우드 서비스 공급자 (Csp)에서 관리 되는 모든 고객에 게이 변경 적용 되지 않습니다.

## <a name="changes-to-portal-access"></a>포털 액세스 변경

에 표시 된 대로 Microsoft Azure Government, Microsoft 365 Government – GCC High 및 국방부, Microsoft 365 Government에 대 한 포털 끝점을 업데이트 했습니다 합니다 [끝점 매핑 테이블](#endpoint-mapping)합니다.

이전에 고객은 전 세계 Azure (portal.azure.com) 및 Office 365 (portal.office.com) 포털을 사용 하 여 로그인 할 수 있습니다. 이 업데이트를 사용 하 여 고객 이제 특정 Microsoft Azure Government, Microsoft 365 Government-GCC High 및 Microsoft 365 Government DoD 포털을 사용 하 여에 로그인 해야 합니다.

## <a name="endpoint-mapping"></a>엔드포인트 매핑

다음 표에서 모든 고객에 대 한 끝점을 보여줍니다.

| 이름 | 끝점 세부 정보 |
|------|------------------|
| 포털 |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 Government-GCC 높음: https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Azure Active Directory 기관 끝점 | https://login.microsoftonline.us |
| Azure Active Directory Graph API | https://graph.windows.net |
| Microsoft Graph API Microsoft 365 government-GCC 높음 | https://graph.microsoft.us |
| Microsoft 365 government-DoD Microsoft Graph API | https://dod-graph.microsoft.us |
| Azure Government 서비스 끝점 | 세부 정보를 참조 하세요. [Azure Government 개발자 가이드](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government-GCC High 끝점 | 세부 정보를 참조 하세요. [Office 365 미국 Government GCC High 끝점](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | 세부 정보를 참조 하세요. [Office 365 미국 Government DoD 끝점](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure Government란?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure Government AAD 기관 끝점 업데이트](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [미국 정부 클라우드에서 Microsoft Graph 끝점](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 미국 정부 GCC High 및 국방부](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)