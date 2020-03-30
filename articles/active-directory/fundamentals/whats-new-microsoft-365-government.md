---
title: 마이크로소프트 365 정부에서 Azure AD에 대 한 새로운 무엇입니까? | Microsoft Docs
description: 영향을 줄 수 있는 Microsoft 365 정부 클라우드 인스턴스에서 Azure Active Directory(Azure AD)에 대한 몇 가지 변경 사항에 대해 알아봅니다.
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
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425547"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>마이크로소프트 365 정부에서 Azure 활성 디렉토리에 대 한 새로운 것

Microsoft 365 정부 클라우드 인스턴스에서 Azure Active Directory(Azure AD)를 일부 변경했으며, 이 서비스는 다음 서비스를 사용하는 고객에게 적용됩니다.

- Microsoft Azure Government

- 마이크로소프트 365 정부 – GCC 높은

- 마이크로 소프트 365 정부 – DoD

이 문서는 Microsoft 365 정부 – GCC 고객에는 적용되지 않습니다.

## <a name="changes-to-the-initial-domain-name"></a>초기 도메인 이름 변경

Microsoft 365 정부 온라인 서비스에 대한 조직의 초기 등록 중에 조직의 도메인 이름을 선택하라는 `<your-domain-name>.onmicrosoft.com`메시지가 표시됩니다. .com 접미사가 있는 도메인 이름이 이미 있는 경우 아무 것도 변경되지 않습니다.

그러나 새 Microsoft 365 정부 서비스에 등록하는 경우 접미사를 사용하여 도메인 이름을 선택하라는 `.us` 메시지가 표시됩니다. 그래서, 그것은 `<your-domain-name>.onmicrosoft.us`될 것입니다 .

>[!Note]
>이 변경 사항은 CSP(클라우드 서비스 공급자)가 관리하는 고객에게는 적용되지 않습니다.

## <a name="changes-to-portal-access"></a>포털 액세스 변경 사항

우리는 마이크로 소프트 Azure 정부에 대한 포털 끝점을 업데이트했습니다, 마이크로 소프트 365 정부 – GCC 높은, 마이크로 소프트 365 정부 – DoD, [엔드 포인트 매핑 테이블에](#endpoint-mapping)표시된 대로 .

이전에는 고객이 전 세계 Azure(portal.azure.com) 및 Office 365(portal.office.com) 포털을 사용하여 로그인할 수 있었습니다. 이 업데이트를 통해 고객은 이제 특정 Microsoft Azure 정부, Microsoft 365 정부 - GCC High 및 Microsoft 365 정부 - DoD 포털을 사용하여 로그인해야 합니다.

## <a name="endpoint-mapping"></a>엔드포인트 매핑

다음 표에서는 모든 고객의 끝점을 보여 주며, 다음 표는 다음과 같은 것입니다.

| 이름 | 끝점 세부 정보 |
|------|------------------|
| 포털 |마이크로 소프트 Azure 정부:https://portal.azure.us<p>마이크로소프트 365 정부 – GCC 높은:https://portal.office365.us<p>마이크로 소프트 365 정부 – DoD:https://portal.apps.mil |
| Azure 활성 디렉터리 기관 끝점 | https://login.microsoftonline.us |
| 마이크로 소프트 에 대한 마이크로 소프트 그래프 API 365 정부 - GCC 높은 | https://graph.microsoft.us |
| 마이크로 소프트 365 정부에 대한 마이크로 소프트 그래프 API - DoD | https://dod-graph.microsoft.us |
| Azure 정부 서비스 끝점 | 자세한 내용은 [Azure 정부 개발자 가이드를](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) 참조하십시오. |
| 마이크로소프트 365 정부 - GCC 하이 엔드 포인트 | 자세한 내용은 [Office 365 미국 정부 GCC 하이 엔드포인트를 참조하십시오.](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| 마이크로 소프트 365 정부 - DoD | 자세한 내용은 [Office 365 미국 정부 DoD 엔드포인트를 참조하십시오.](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure Government란?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure 정부 AAD 기관 엔드포인트 업데이트](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [미국 정부 클라우드의 마이크로소프트 그래프 엔드포인트](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [사무실 365 미국 정부 GCC 높은 및 DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
