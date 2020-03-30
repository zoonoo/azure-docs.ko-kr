---
title: 권한 있는 ID 관리 - Azure Active Directory문제 해결 | 마이크로 소프트 문서
description: Azure AD 권한 ID 관리(PIM)의 역할로 시스템 오류를 해결하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299689"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>권한 있는 ID 관리문제 해결

Azure Active Directory(Azure AD)에서 PIM(권한 있는 ID 관리)에 문제가 있습니까? 다음에 나오는 정보는 작업을 다시 수행하는 데 도움이 될 수 있습니다.

## <a name="access-to-azure-resources-denied"></a>Azure 리소스에 대한 액세스가 거부됨

### <a name="problem"></a>문제

Azure 리소스에 대한 활성 소유자 또는 사용자 액세스 관리자로서 권한 있는 ID 관리 내에서 리소스를 볼 수 있지만 적격 할당을 수행하거나 리소스에서 역할 할당 목록을 보는 등의 작업을 수행할 수 없습니다. 개요 페이지. 이러한 작업으로 인해 권한 부여 오류가 발생합니다.

### <a name="cause"></a>원인

이 문제는 PIM 서비스 주체에 대한 사용자 액세스 관리자 역할이 구독에서 실수로 제거된 경우에 발생할 수 있습니다. 권한 있는 ID 관리 서비스가 Azure 리소스에 액세스할 수 있도록 하려면 MS-PIM 서비스 주체는 항상 Azure 구독에 대한 [사용자 액세스 관리자 역할을](../../role-based-access-control/built-in-roles.md#user-access-administrator) 할당받아야 합니다.

### <a name="resolution"></a>해결 방법

구독 수준에서 사용자 액세스 관리자 역할을 권한 있는 ID 관리 서비스 주체 이름(MS-PIM)에 할당합니다. 이 할당을 통해 권한 있는 ID 관리 서비스가 Azure 리소스에 액세스할 수 있습니다. 역할은 요구 사항에 따라 관리 그룹 수준 또는 구독 수준에서 할당할 수 있습니다. 자세한 내용은 서비스 주체를 역할에 [응용 프로그램 할당을](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)참조하세요.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management를 사용하기 위한 라이선스 요구 사항](subscription-requirements.md)
- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management 배포](pim-deployment-plan.md)
