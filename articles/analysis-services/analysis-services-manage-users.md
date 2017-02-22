---
title: "Azure Analysis Services의 사용자 관리 | Microsoft Docs"
description: "Azure에서 Analysis Services 사용자를 관리하는 방법을 알아봅니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/18/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 8725687a5700df72026f9154659758b9280872f4
ms.openlocfilehash: 35247a68588ab8593c40ed0d62f37062350f9aec


---
# <a name="manage-users-in-azure-analysis-services"></a>Azure Analysis Services의 사용자 관리
Azure Analysis Services에서는 두 가지 유형의 사용자, 서버 관리자 및 데이터베이스 사용자가 있습니다. 

## <a name="server-administrators"></a>서버 관리자
Azure Portal 또는 SSMS의 서버 속성에서 서버에 대한 제어 블레이드의 **Analysis Services 관리자**를 사용하면 서버 관리자를 관리할 수 있습니다. Analysis Services 관리자는 데이터베이스 추가 및 제거, 사용자 관리와 같은 일반 데이터베이스 관리 작업에 대한 권한을 가진 데이터베이스 서버 관리자입니다. 기본적으로 Azure 포털에서 서버를 만드는 사용자는 Analysis Services 관리자로 자동 추가됩니다.

![Azure Portal의 서버 관리자](./media/analysis-services-manage-users/aas-manage-users-admins.png)

또한 다음 항목을 알고 있어야 합니다.

* Windows Live ID는 Azure Analysis Services에서 지원하지 않는 ID 유형입니다.  
* Analysis Services 관리자는 유효한 Azure Active Directory 사용자여야 합니다.
* Azure Resource Manager 템플릿을 통해 Azure Analysis Services 서버를 만드는 경우 Analysis Services 관리자에는 관리자로 추가해야 하는 사용자 JSON 배열이 사용됩니다.

Analysis Services 관리자는 Azure 구독에 대한 리소스를 관리할 수 있는 Azure 리소스 관리자와 다를 수 있습니다. 이렇게 하면 Analysis Services의 기존 XMLA과 TMSL 관리 동작과의 호환성이 유지되며 Azure 리소스 관리와 Analysis Services 데이터베이스 관리 간 업무를 구분할 수 있습니다. Azure Analysis Services 리소스의 역할과 액세스 형식을 모두 보려면 제어 블레이드에서 액세스 제어(IAM)를 사용합니다.

### <a name="to-add-administrators-using-azure-portal"></a>Azure Portal을 사용하여 관리자를 추가하려면 다음을 수행합니다.
1. 서버에 대한 제어 블레이드에서 **Analysis Services 관리자**를 클릭합니다.
2. **\<서버 이름> - Analysis Services 관리자** 블레이드에서 **추가**를 클릭합니다.
3. **서버 관리자 추가** 블레이드에서 추가할 사용자 계정을 선택합니다.

## <a name="database-users"></a>데이터베이스 사용자
데이터베이스 사용자는 데이터베이스 역할에 추가되어야 합니다. 역할은 데이터베이스에 대한 동일한 권한이 있는 사용자 및 그룹을 정의합니다. 기본적으로 테이블 형식 모델 데이터베이스는 읽기 권한을 가진 기본 사용자 역할을 적용합니다. 자세한 내용은 [테이블 형식 모델의 역할](https://msdn.microsoft.com/library/hh213165.aspx)을 참조하세요.

Azure Analysis Services 모델 데이터베이스 사용자는 *Azure Active Directory에 있어야 합니다*. 지정된 사용자 이름은 조직의 전자 메일 주소 또는 UPN을 기준으로 작성되어야 합니다. 이는 Windows 도메인 사용자 이름으로 사용자를 지원하는 온-프레미스 테이블 형식 모델 데이터베이스와 다릅니다. 

데이터베이스 역할을 만들고, 사용자 및 그룹을 역할에 추가하고, SSDT(SQL Server Data Tools) 또는 SSMS(SQL Server Management Studio)에서 행 수준 보안을 구성할 수 있습니다. [Analysis Services PowerShell cmdlets](https://msdn.microsoft.com/library/hh758425.aspx) 또는 TMSL([테이블 형식 모델 스크립팅 언어](https://msdn.microsoft.com/library/mt614797.aspx))를 사용하여 사용자를 역할에 추가하거나 제거할 수 있습니다.

**샘플 TMSL 스크립트**

이 샘플에서 사용자 및 그룹은 SalesBI 데이터베이스의 사용자 역할에 추가됩니다.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>다음 단계
아직 테이블 형식 모델을 새 서버에 배포하지 않았다면 지금이야말로 좋은 기회입니다. 자세한 내용은 [Azure Analysis Services에 배포](analysis-services-deploy.md)를 참조하세요.

서버에 모델을 배포한 경우에는 클라이언트 또는 브라우저를 통해 연결할 준비가 된 것입니다. 자세한 내용은 [Azure Analysis Services 서버에서 데이터 가져오기](analysis-services-connect.md)를 참조하세요.




<!--HONumber=Jan17_HO3-->


