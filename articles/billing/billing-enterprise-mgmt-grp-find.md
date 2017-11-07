---
title: "Azure 구독 또는 관리 그룹 찾기 - Azure | Microsoft Docs"
description: "여러 디렉터리 간에 이동하여 관리 그룹 및 구독을 확인하는 방법"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Azure 구독 또는 관리 그룹 찾기

Azure에서 구독 또는 관리 그룹을 찾는 데 문제가 있는 경우 잘못된 디렉터리에서 찾고 있을 수 있습니다. 이런 상황은 계정이 여러 Azure Active Directory에 있는 경우에 발생할 수 있습니다. 각 [Active Directory는 독립적](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence)이며 디렉터리 간에 액세스 권한이 상속되지 않습니다.      

![디렉터리 메뉴 전환](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>디렉터리 전환 
Azure Portal에서 디렉터리를 쉽게 전환할 수 있습니다.
1.  [Azure 포털](https://portal.azure.com)에 로그인합니다.
2.  화면 오른쪽 상단에서 이름을 선택합니다. 
3.  메뉴의 아래쪽에 액세스 권한이 있는 모든 디렉터리가 나열됩니다.
4.  액세스할 디렉터리 이름을 선택합니다. 

![디렉터리 메뉴 전환](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>자산을 사용할 수 없으신가요? 
구독 또는 관리 그룹에 액세스하려고 할 때 "이 자산을 사용할 수 없습니다."라는 오류 메시지가 표시되는 경우에는 이 항목을 볼 수 있는 올바른 역할이 없는 것입니다.  

![자산을 찾을 수 없음](media/billing-enterprise-mgmt-groups/asset-not-found.png)

액세스 권한을 받으려면 구독 또는 관리 그룹의 관리자에게 문의합니다.  
* 구독의 경우 필요한 역할에 대한 자세한 내용은 [Azure RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) 문서를 참조하세요.
* 관리 그룹의 경우 RBAC 액세스를 사용할 수 없으며 곧 제공될 예정입니다. 액세스 권한을 받으려면 Enterprise Portal 관리자에게 문의합니다.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>관리 그룹 및 구독이 동일한 디렉터리에 있도록 환경 개선 
구독 또는 관리 그룹이 모두 같은 위치에 있도록 선택한 디렉터리로 전송할 수 있습니다.  구독 및 관리 그룹을 동일한 디렉터리로 통합하면 디렉터리를 전환해야 하는 필요성이 줄어들고 정책을 상속할 수 있습니다.  


### <a name="transfer-your-subscriptions"></a>구독 전송 
구독을 관리 그룹과 연결된 디렉터리로 이동할 수 있습니다. 이렇게 이동하려면 등록 관리자가 대상 디렉터리의 계정으로 구독을 전송하면 됩니다. 자세한 내용을 보려면 [Enterprise Portal](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)에 로그인합니다.


 






