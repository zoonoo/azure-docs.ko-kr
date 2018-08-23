---
title: Azure Stack 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정 | Microsoft Docs
description: Azure Stack에서 구독에 액세스 하려면 서비스 공급자를 사용 하도록 설정 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: f309b86578f340040927735c067656158f3198fc
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139385"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Azure Stack 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정

*적용 대상: Azure Stack 통합 시스템*

Azure Stack 클라우드 서비스 공급자 (CSP) 사용 하 여 사용 중인 경우에 Azure 및 Azure Stack에서 리소스에 액세스 하려면 사용자 고유의 구독 관리를 위해 선택할 수 있습니다. 공급자를 구독 관리를 할 수도 있습니다. 이 문서에서는 다음 방법을 안내합니다.

 * 구독에 서비스 공급자 액세스를 제공 합니다.
 * 서비스 공급자는 서비스를 관리할 수 있는지 확인 합니다.

> [!Note]
>  CSP 계정에 관리 되지 않습니다. 다음 단계를 건너뛰고를 CSP를 Azure Stack 구독을 관리할 수 없습니다.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>클라우드 서비스 공급자를 사용 하 여 구독 관리

CSP로 추가 **사용자** 를 구독 합니다.

1. 사용자 역할을 사용 하 여 게스트 사용자로 테 넌 트 디렉터리에 CSP가 사용자를 추가 합니다.  사용자를 추가 하는 단계를 참조 하세요. [Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP를 로컬 Azure Stack 구독을 만듭니다.
3. Azure Stack을 사용 하 여 시작할 준비가 되었습니다.
4. CSP은 리소스를 관리할 수도 있습니다 있는지 확인 하기 위해 구독에서 리소스를 만들어야 합니다. 예를 들어 할 [Azure Stack 포털을 사용 하 여 Windows 가상 머신 만들기](azure-stack-quick-windows-portal.md)합니다.

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>RBAC 권한을 사용 하 여 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정

CSP로 추가 **소유자** 를 구독 합니다.

1. CSP 프로그램을 테 넌 트 디렉터리에 게스트 사용자로 추가 합니다.  사용자를 추가 하는 단계를 참조 하세요. [Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP 게스트 사용자에 게 소유자 역할을 추가 합니다. 구독을 CSP 사용자를 추가 하는 단계를 참조 하세요. [Azure 구독 리소스에 대 한 액세스 관리에 관한 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. CSP를 로컬 Azure Stack 구독을 만듭니다.
4. Azure Stack을 사용 하 여 시작할 준비가 되었습니다.
5. CSP은 리소스를 관리할 수 있다는 확인 하기 위해 구독에서 리소스를 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Stack에서 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 내용은 참조 하세요 [사용 및 Azure Stack에서 청구](../azure-stack-billing-and-chargeback.md)합니다.
