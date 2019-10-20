---
title: Privileged Identity Management Azure Active Directory 문제 해결 | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 역할을 사용 하 여 시스템 오류 문제를 해결 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: f54382d652c3fc59b8ff462d41ec8c0fbdbe8498
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596702"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Privileged Identity Management 문제 해결

Azure Active Directory (Azure AD)의 Privileged Identity Management (PIM)에 문제가 있나요? 다음에 나오는 정보는 작업을 다시 수행하는 데 도움이 될 수 있습니다.

## <a name="access-to-azure-resources-denied"></a>Azure 리소스에 대 한 액세스가 거부 되었습니다.

### <a name="problem"></a>문제

Azure 리소스에 대 한 활성 소유자 또는 사용자 액세스 관리자는 Privileged Identity Management 내에서 리소스를 볼 수 있지만 적격 할당 만들기 또는 리소스에서 역할 할당 목록 보기와 같은 작업을 수행할 수 없습니다. 개요 페이지. 이러한 작업으로 인해 권한 부여 오류가 발생 합니다.

### <a name="cause"></a>원인

이 문제는 PIM 서비스 사용자에 대 한 사용자 액세스 관리자 역할이 실수로 구독에서 제거 된 경우에 발생할 수 있습니다. Privileged Identity Management 서비스에서 Azure 리소스에 액세스할 수 있게 하려면 Azure 구독에 대해 항상 [사용자 액세스 관리자 역할](../../role-based-access-control/built-in-roles.md#user-access-administrator) 을 할당 받아야 합니다.

### <a name="resolution"></a>해상도

구독 수준에서 권한 있는 id 관리 서비스 사용자 이름 (MS – PIM)에 사용자 액세스 관리자 역할을 할당 합니다. 이 할당을 통해 권한 있는 id 관리 서비스에서 Azure 리소스에 액세스할 수 있습니다. 역할은 요구 사항에 따라 관리 그룹 수준 또는 구독 수준에서 할당 될 수 있습니다. 서비스 사용자에 대 한 자세한 내용은 [역할에 응용 프로그램 할당](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management 사용할 라이선스 요구 사항](subscription-requirements.md)
- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management 배포](pim-deployment-plan.md)
