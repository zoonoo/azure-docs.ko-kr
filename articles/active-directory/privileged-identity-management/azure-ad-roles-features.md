---
title: Privileged Identity Management의 Azure AD 역할 기능 | Microsoft Docs
description: 할당 PIM(Privileged Identity Management)을 위해 Azure AD 역할을 관리하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4113c3cf8fd5942a2fb8f644c67b15066d85b41
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109517859"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할을 위한 관리 기능

Privileged Identity Management에서 Azure AD 역할을 위한 관리 환경은 Azure AD 역할과 Azure 리소스 역할을 관리하는 방법을 통합하도록 업데이트되었습니다. 이전에 Azure 리소스 역할을 위한 Privileged Identity Management에는 Azure AD 역할에 사용할 수 없었던 몇 가지 주요 기능이 있었습니다.

현재 출시된 업데이트를 적용하면 두 가지를 단일 관리 환경으로 병합하고 해당 환경에서 Azure 리소스 역할과 마찬가지로 Azure AD 역할에 대해 동일한 기능을 제공합니다. 이 문서에서는 업데이트된 기능 및 모든 요구 사항을 알려 드립니다.

## <a name="time-bound-assignments"></a>시간 제한 할당

이전에는 역할 할당에 두 가지 가능한 상태가 있었습니다( *적격* 및 *영구*). 이제 각 할당 형식에 대해 시작과 종료 시간을 설정할 수도 있습니다. 이와 같이 추가하면 할당할 수 있는 네 가지 상태가 제공됩니다.

- 영구적으로 적격
- 영구적으로 활성
- 적격, 할당 시작 날짜와 종료 날짜가 지정되어 있음
- 활성, 할당 시작 날짜와 종료 날짜가 지정되어 있음

대부분의 경우에는 사용자가 적격한 할당을 가지고 매번 역할을 활성화할 필요가 없더라도 할당에 대한 만료 시간을 설정하여 Azure AD 조직을 보호할 수 있습니다. 예를 들어 적격한 임시 사용자가 있는 경우 작업이 완료될 때 역할 할당에서 해당 사용자가 자동으로 제거되도록 만료를 설정하는 것이 좋습니다.

## <a name="new-role-settings"></a>새 역할 설정

또한 Azure AD 역할에 대한 새로운 설정을 추가합니다.

- **이전** 에는 역할별로 활성화 설정만 구성할 수 있었습니다. 즉, 다단계 인증 요구 사항과 인시던트/요청 티켓 요구 사항과 같은 활성화 설정이 지정된 역할에 적합한 모든 사용자에게 적용되었습니다.
- **이제** 개별 사용자가 다단계 인증을 수행해야 역할을 활성화할 수 있는지 여부를 구성할 수 있습니다. 또한 특정 역할과 관련된 Privileged Identity Management 메일을 더욱 효율적으로 제어할 수 있습니다.

## <a name="extend-and-renew-assignments"></a>할당 확장 또는 갱신

시간 제한 할당을 확인하면 가장 먼저 역할이 만료되면 어떻게 될지 궁금할 것입니다. 이 새 버전에서는 이 시나리오에 대한 다음 두 가지 옵션이 제공됩니다.

- **연장**: 역할 할당이 만료될 시기가 다가오면 사용자는 Privileged Identity Management를 사용하여 역할 할당 연장을 요청할 수 있습니다.
- **갱신**: 역할 할당이 이미 만료된 경우 사용자는 Privileged Identity Management를 사용하여 역할 할당 갱신을 요청할 수 있습니다.

사용자가 시작하는 두 작업은 글로벌 관리자 또는 권한 있는 역할 관리자의 승인이 필요합니다. 관리자는 더 이상 만료를 관리하는 업무를 수행할 필요가 없습니다. 연장 또는 갱신 요청을 기다리다가 요청이 유효하면 요청을 승인해야 합니다.

## <a name="api-changes"></a>API 변경 내용

고객이 Azure AD 조직에 업데이트된 버전을 출시할 때 기존 Graph API의 작동이 중지됩니다. [Azure 리소스 역할을 위한 Graph API](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta&preserve-view=true)를 사용하도록 전환해야 합니다. 해당 API를 사용하여 Azure AD 역할을 관리하려면 서명에서 `/azureResources`를 `/aadroles`로 바꾸고 `resourceId`에 디렉터리 ID를 사용합니다.

이전 API를 사용하는 모든 고객에게 연락하여 이러한 변경 사항을 미리 알리기 위해 최선을 다했습니다. Azure AD 조직이 새 버전으로 이동했는데 여전히 이전 API를 사용하는 경우의 팀(pim_preview@microsoft.com)에 문의하세요.

## <a name="powershell-change"></a>PowerShell 변경

Azure AD 역할에 Privileged Identity Management PowerShell 모듈을 사용하는 고객의 경우 PowerShell에서 업데이트 작업을 중지합니다. 이전 cmdlet 대신 Azure AD Preview PowerShell 모듈 내에서 Privileged Identity Management cmdlet을 사용해야 합니다. [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)에서 Azure AD PowerShell 모듈을 설치합니다. 이제 [이 PowerShell 모듈의 PIM 작업에 대한 설명서와 샘플을 읽을](powershell-for-azure-ad-roles.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 사용자 지정 역할 할당](azure-ad-custom-roles-assign.md)
- [Azure AD 사용자 지정 역할 할당 제거 또는 업데이트](azure-ad-custom-roles-update-remove.md)
- [Azure AD 사용자 지정 역할 할당 구성](azure-ad-custom-roles-configure.md)
- [Azure AD의 역할 정의](../roles/permissions-reference.md)
