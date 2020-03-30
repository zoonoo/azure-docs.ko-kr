---
title: 권한 있는 ID 관리(PIM)에서 Azure AD 역할 관리 | 마이크로 소프트 문서
description: 할당에 대 한 Azure AD 역할을 관리 하는 방법 권한 있는 ID 관리 (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245981"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure AD 역할에 대한 관리 기능

권한 있는 Id 관리에서 Azure AD 역할에 대한 관리 환경이 업데이트되어 Azure AD 역할 및 Azure 리소스 역할이 관리되는 방법을 통합합니다. 이전에는 Azure 리소스 역할에 대한 권한 있는 ID 관리에 Azure AD 역할에 사용할 수 없었던 몇 가지 주요 기능이 있었습니다.

현재 업데이트가 롤아웃되고 있으므로 두 가지를 단일 관리 환경으로 병합하고 Azure 리소스 역할과 동일한 Azure AD 역할에 대한 기능을 얻습니다. 이 문서에서는 업데이트된 기능 및 요구 사항을 알려줍니다.


## <a name="time-bound-assignments"></a>시간 바인딩된 할당

이전에 Azure AD 역할에 대한 권한 있는 ID 관리에서 *자격* 및 *영구*상태인 두 가지 가능한 상태인 역할 할당에 익숙했습니다. 이제 각 과제 유형에 대해 시작 및 종료 시간을 설정할 수 있습니다. 이 추가는 할당을 배치할 수 있는 네 가지 가능한 상태를 제공합니다.

- 영구적으로 적격
- 영구적으로 활성 상태
- 지정된 시작/종료 날짜가 지정된 적격
- 할당에 대해 지정된 시작/종료 날짜가 있는 활성

대부분의 경우 사용자가 매번 적격 할당을 수행하고 역할을 활성화하지 않도록 하지 않더라도 할당 만료 시간을 설정하여 Azure AD 조직을 보호할 수 있습니다. 예를 들어 자격이 있는 임시 사용자가 있는 경우 작업이 완료되면 역할 할당에서 자동으로 제거하도록 만료를 설정하는 것이 좋습니다.

## <a name="new-role-settings"></a>새 역할 설정

Azure AD 역할에 대한 새 설정도 추가됩니다. 이전에는 역할별로만 활성화 설정을 구성할 수 있었습니다. 즉, 다단계 인증 요구 사항 및 인시던트/요청 티켓 요구 사항과 같은 활성화 설정이 지정된 역할을 받을 수 있는 모든 사용자에게 적용되었습니다. 이제 개별 사용자가 역할을 활성화하기 전에 다단계 인증을 수행해야 하는지 여부를 구성할 수 있습니다. 또한 특정 역할과 관련된 권한 있는 ID 관리 전자 메일을 고급으로 제어할 수 있습니다.

## <a name="extend-and-renew-assignments"></a>과제 확장 및 갱신

시간 바인딩된 할당을 파악하는 즉시 첫 번째 질문은 역할이 만료되면 어떻게 됩니까? 이 새 버전에서는 이 시나리오에 대한 두 가지 옵션을 제공합니다.

- 확장 – 역할 할당이 만료에 가까워지면 사용자는 권한 있는 ID 관리를 사용하여 해당 역할 할당에 대한 확장을 요청할 수 있습니다.
- 갱신 - 역할 할당이 만료되면 사용자는 권한 있는 ID 관리를 사용하여 해당 역할 할당에 대한 갱신을 요청할 수 있습니다.

사용자가 시작한 두 작업 모두 글로벌 관리자 또는 권한 있는 역할 관리자의 승인이 필요합니다. 관리자는 더 이상 이러한 만료를 관리하는 업무에 있을 필요가 없습니다. 확장 또는 갱신 요청을 기다렸다가 요청이 유효한지 승인하기만 하면 됩니다.

## <a name="api-changes"></a>API 변경

고객이 Azure AD 조직에 업데이트된 버전을 롤아웃하면 기존 그래프 API가 작동하지 않습니다. Azure 리소스 역할에 [대한 그래프 API를](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)사용하려면 전환해야 합니다. 해당 API를 사용하여 Azure AD `/azureResources` `/aadroles` 역할을 관리하려면 서명에서 교환하고 `resourceId`에 대한 디렉터리 ID를 사용합니다.

이전 API를 사용하는 모든 고객에게 이러한 변경 에 대해 미리 알리기 위해 최선을 다했습니다. Azure AD 조직이 새 버전으로 이동되었지만 이전 API에 계속 의존하는 경우 에서 pim_preview@microsoft.com팀에 문의하십시오.

## <a name="powershell-change"></a>파워쉘 변경

Azure AD 역할에 대해 권한 있는 ID 관리 PowerShell 모듈을 사용하는 고객의 경우 PowerShell은 업데이트 작업을 중지합니다. 이전 cmdlet 대신 Azure AD 미리 보기 PowerShell 모듈 내에서 권한 있는 ID 관리 cmdlet을 사용해야 합니다. [PowerShell 갤러리에서](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)Azure AD PowerShell 모듈을 설치합니다. 이제 [이 PowerShell 모듈에서 PIM 작업에 대한 설명서 및 샘플을 읽을](powershell-for-azure-ad-roles.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 사용자 지정 역할 할당](azure-ad-custom-roles-assign.md)
- [Azure AD 사용자 지정 역할 할당 제거 또는 업데이트](azure-ad-custom-roles-update-remove.md)
- [Azure AD 사용자 지정 역할 할당 구성](azure-ad-custom-roles-configure.md)
- [Azure AD의 역할 정의](../users-groups-roles/directory-assign-admin-roles.md)
