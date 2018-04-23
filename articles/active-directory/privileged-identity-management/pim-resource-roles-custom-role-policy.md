---
title: Azure 리소스에 대한 Privileged Identity Management - 대상 PIM 설정에 사용자 지정 역할 사용 | Microsoft Docs
description: Azure 리소스에 대한 PIM의 사용자 지정 역할을 사용하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>대상 PIM 설정에 사용자 지정 역할 사용

다른 사용자에게 더 많은 자율성을 제공하면서 엄격한 PIM 설정을 역할의 일부 멤버에게 적용해야 할 수도 있습니다. Azure 구독에서 실행될 응용 프로그램 개발을 지원하기 위해 조직이 일부 계약 직원을 채용하는 시나리오를 가정합니다. 

사용자가 리소스 관리자인 경우 승인할 필요 없이 적격 액세스 권한이 있는 조직의 직원을 선호하지만 모든 계약 직원은 활성화를 요청할 때 승인을 요구해야 합니다. 아래 단계를 수행하여 Azure 리소스 역할에 대해 대상 PIM 설정을 사용하는 프로세스를 간략히 설명합니다.

## <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

[이 가이드를 사용하여 리소스에 대한 사용자 지정 역할을 만듭니다](../../role-based-access-control/custom-roles.md).

복제하려는 기본 제공 역할을 쉽게 기억할 수 있도록 설명이 포함된 이름을 사용합니다.

>[!NOTE]
>사용자 정의 역할이 의도한 역할의 복제본이 되고, 해당 범위는 기본 제공 역할과 일치하도록 합니다.

## <a name="apply-pim-settings"></a>PIM 설정 적용

테넌트에서 역할을 만들면 PIM로 이동하고 역할의 범위를 정한 리소스를 선택합니다.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

이러한 멤버에 적용해야 하는 [PIM 역할 설정을 구성](pim-resource-roles-configure-role-settings.md)합니다.

마지막으로 이러한 설정으로 대상으로 할 멤버의 고유 그룹에 [역할을 할당](pim-resource-roles-assign-roles.md)합니다.

## <a name="next-steps"></a>다음 단계

[구독 소유자 검토](pim-resource-roles-perform-access-review.md)
