---
title: Azure Analysis Services의 서버 관리자 관리 | Microsoft Docs
description: Azure에서 Analysis Services 서버 관리자를 관리하는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d8f74bd66fc7c980c4fc5f83492aad7d8a4aa5c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866970"
---
# <a name="manage-server-administrators"></a>서버 관리자 관리
서버 관리자는 서버가 상주하는 테넌트의 Azure AD(Azure Active Directory)에서 유효한 사용자 또는 보안 그룹이어야 합니다. Azure Portal 또는 SSMS의 서버 속성에서 서버에 대한 **Analysis Services 관리자**를 사용하여 서버 관리자를 관리할 수 있습니다. 

> [!NOTE]
> 보안 그룹에는 `True`로 설정된 `MailEnabled` 속성이 있어야 합니다.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Azure Portal을 사용하여 서버 관리자를 추가하려면
1. 포털에서 서버에 대해 **Analysis Services 관리자**를 클릭합니다.
2. **\<서버 이름> - Analysis Services 관리자**에서 **추가**를 클릭합니다.
3. **서버 관리자 추가**에서 Azure AD의 사용자 계정을 선택하거나 이메일 주소를 통해 외부 사용자를 초대합니다.

    ![Azure Portal의 서버 관리자](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>SSMS를 사용하여 서버 관리자를 추가하려면
1. 서버를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
2. **Analysis Server 속성**에서 **보안**을 클릭합니다.
3. **추가**를 클릭한 다음 Azure AD에 있는 사용자 또는 그룹의 이메일 주소를 입력합니다.
   
    ![SSMS에서 서버 관리자 추가](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>다음 단계 
[인증 및 사용자 권한](analysis-services-manage-users.md)  
[데이터베이스 역할 및 사용자 관리](analysis-services-database-users.md)  
[역할 기반 Access Control](../role-based-access-control/overview.md)  

