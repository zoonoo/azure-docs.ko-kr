---
title: Azure 스택 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정 | Microsoft Docs
description: 스택에서 Azure 구독에 액세스 하려면 서비스 공급자를 사용 하도록 설정 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Azure 스택 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정

*적용 대상: Azure 스택 시스템 통합*

Azure 스택은 클라우드 서비스 공급자 (CSP) 사용 중인 경우 공급자가 Azure 스택 및 Azure 구독에서 리소스에 대 한 액세스를 관리할 수 있습니다. 또는 사용자의 구독을 관리할 수 있습니다. 이 문서에서 사용자 대신 구독에 액세스 하거나 서비스 공급자는 서비스를 관리할 수 있도록 하려면 서비스 공급자를 설정 하는 방법을 살펴봅니다.

> [!Note]  
>  다음 단계를 건너뛰고 CSP 없는 이미 계정을 관리 하는 경우 다음 CSP 됩니다 사용자 대신 Azure 스택 구독을 관리할 수 있습니다.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>클라우드 서비스 공급자에 구독 관리

1. 테 넌 트 디렉터리에 사용자 역할이 있는 게스트 사용자로 CSP를 추가 합니다.  사용자를 추가 하는 방법에 대 한 단계를 참조 하세요. [Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP에서 로컬 Azure 스택 구독을 만듭니다.
3. Azure 스택을 사용 하 여 시작할 준비가 되었습니다.
3. CSP 해야 그런 다음 리소스를 관리할 수도 있습니다를 확인 하기 위해 구독에 리소스를 만듭니다. 예를 들어, 수행할 수 있습니다 [스택 Azure 포털과 Windows 가상 컴퓨터를 만들](azure-stack-quick-windows-portal.md)합니다.

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>RBAC 권한을 사용 하 여 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정

구독에 소유자와 CSP를 추가 합니다. 

1. CSP 게스트 사용자로 추가 합니다. 테 넌 트 디렉터리에 소유자 역할을 지정 합니다.  사용자를 추가 하는 방법에 대 한 단계를 참조 하세요. [Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. CSP 게스트 사용자에 게 소유자 역할을 추가 합니다. CSP 사용자 구독에 추가 하는 방법에 대 한 단계를 참조 하십시오. [Azure 구독 리소스에 대 한 액세스를 관리 하려면 Use Role-Based 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. CSP에서 로컬 Azure 스택 구독을 만듭니다.
4. Azure 스택을 사용 하 여 시작할 준비가 되었습니다.
5. CSP 해야 그런 다음 리소스를 관리할 수 있는지 확인 하기 위해 구독에 리소스를 만듭니다. 

## <a name="next-steps"></a>다음 단계

  - 스택에서 Azure 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 참조 [사용 및 청구 Azure 스택의](../azure-stack-billing-and-chargeback.md)합니다.
