---
title: "포털에서 Azure 앱에 대한 ID 만들기 | Microsoft Docs"
description: "Azure Resource Manager에서 리소스에 대한 액세스를 관리하기 위해 역할 기반 액세스 제어와 함께 사용할 수 있는 새 Azure Active Directory 응용 프로그램 및 서비스 주체를 만드는 방법을 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c66c895eb9b905ea265341783c153a9554bd1259
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 응용 프로그램 및 서비스 주체 만들기

리소스를 액세스하거나 수정해야 하는 응용 프로그램이 있는 경우 Azure AD(Active Directory) 응용 프로그램을 설정하고 필수 사용 권한을 할당해야 합니다. 이 방법은 다음의 이유로 사용자 고유의 자격 증명을 사용하여 앱을 실행하는 데 좋습니다.

* 자체 사용 권한과 다른 앱 ID에 대한 사용 권한을 할당할 수 있습니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.
* 책임이 변경되면 앱의 자격 증명을 변경할 필요가 없습니다. 
* 무인 스크립트를 실행할 때 인증서를 사용하여 인증을 자동화할 수 있습니다.

이 토픽에서는 포털을 통해 이러한 단계를 수행하는 방법을 보여 줍니다. 여기서는 응용 프로그램을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 응용 프로그램을 중점적으로 다룹니다. 일반적으로 단일 조직 내에서 실행되는 LOB(기간 업무) 응용 프로그램에 대해 단일 테넌트 응용 프로그램을 사용하게 됩니다.
 
## <a name="required-permissions"></a>필요한 사용 권한
이 토픽을 완료하려면 Azure AD 테넌트에 응용 프로그램을 등록하고 Azure 구독의 역할에 응용 프로그램을 할당하기 위한 충분한 권한이 있어야 합니다. 이러한 단계를 수행하기 위한 올바른 권한이 있는지 확인해보겠습니다.

### <a name="check-azure-active-directory-permissions"></a>Azure Active Directory 권한 확인
1. [Azure Portal](https://portal.azure.com)을 통해 Azure 계정에 로그인합니다.
2. **Azure Active Directory**를 선택합니다.

     ![Azure Active Directory 선택](./media/resource-group-create-service-principal-portal/select-active-directory.png)
3. Azure Active Directory에서 **사용자 설정**을 선택합니다.

     ![사용자 설정 선택](./media/resource-group-create-service-principal-portal/select-user-settings.png)
4. **앱 등록** 설정을 확인합니다. **예**로 설정된 경우 관리자가 아닌 사용자가 AD 앱을 등록할 수 있습니다. 이 설정에서는 Azure AD 테넌트의 모든 사용자가 앱을 등록할 수 있습니다. [Azure 구독 권한 확인](#check-azure-subscription-permissions)을 계속 진행할 수 있습니다.

     ![앱 등록 보기](./media/resource-group-create-service-principal-portal/view-app-registrations.png)
5. 앱 등록 설정이 **아니요**로 설정되어 있으면 관리자만 앱을 등록할 수 있습니다. 사용자 계정이 Azure AD 테넌트에 대한 관리자인지 확인해야 합니다. 빠른 작업에서 **개요** 및 **사용자 찾기**를 선택합니다.

     ![사용자 찾기](./media/resource-group-create-service-principal-portal/find-user.png)
6. 사용자 계정을 검색하고, 찾으면 선택합니다.

     ![사용자 검색](./media/resource-group-create-service-principal-portal/show-user.png)
7. 사용자 계정에 대해 **디렉터리 역할**을 선택합니다. 

     ![디렉터리 역할](./media/resource-group-create-service-principal-portal/select-directory-role.png)
8. Azure AD에서 할당된 디렉터리 역할을 봅니다. 계정이 사용자 역할에 할당되어 있으나 앱 등록 설정(이전 단계)이 관리자로 제한되어 있으면 관리자에게 사용자를 관리자 역할로 할당하거나 사용자가 앱을 등록할 수 있게 설정하도록 요청합니다.

     ![역할 보기](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Azure 구독 권한 확인
Azure 구독에서 사용자 계정에 AD 앱을 역할에 할당할 수 있는 `Microsoft.Authorization/*/Write` 권한이 있어야 합니다. 이 작업에 대한 권한은 [소유자](../active-directory/role-based-access-built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](../active-directory/role-based-access-built-in-roles.md#user-access-administrator) 역할을 통해 부여됩니다. 계정이 **참가자** 역할에 할당된 경우 적절한 사용 권한이 없습니다. 서비스 주체를 역할에 할당하려고 하면 오류가 발생합니다. 

Azure 구독 권한을 확인하려면

1. 이전 단계에서 Azure AD 계정을 아직 확인하지 않은 경우 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

2. Azure AD 계정을 찾습니다. 빠른 작업에서 **개요** 및 **사용자 찾기**를 선택합니다.

     ![사용자 찾기](./media/resource-group-create-service-principal-portal/find-user.png)
2. 사용자 계정을 검색하고, 찾으면 선택합니다.

     ![사용자 검색](./media/resource-group-create-service-principal-portal/show-user.png) 
     
3. **Azure 리소스**를 선택합니다.

     ![리소스 선택](./media/resource-group-create-service-principal-portal/select-azure-resources.png) 
3. 할당된 사용자 역할을 확인하고 AD 앱을 역할에 할당하기 위한 적절한 권한이 있는지 확인합니다. 이러한 권한이 없으면 구독 관리자에게 사용자 액세스 관리자 역할에 사용자를 추가할 것을 요청합니다. 다음 그림에서 사용자에게는 2개의 구독에 대한 소유자 역할이 할당됩니다. 즉, 사용자에게는 적절한 권한이 있습니다. 

     ![권한 표시](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 응용 프로그램 만들기
1. [Azure Portal](https://portal.azure.com)을 통해 Azure 계정에 로그인합니다.
2. **Azure Active Directory**를 선택합니다.

     ![Azure Active Directory 선택](./media/resource-group-create-service-principal-portal/select-active-directory.png)

4. **앱 등록**을 선택합니다.   

     ![앱 등록 선택](./media/resource-group-create-service-principal-portal/select-app-registrations.png)
5. **추가**를 선택합니다.

     ![앱 추가](./media/resource-group-create-service-principal-portal/select-add-app.png)

6. 응용 프로그램에 대한 이름 및 URL을 제공합니다. 만들려는 응용 프로그램 유형으로 **웹앱/API** 또는 **네이티브**를 선택합니다. 값을 설정한 후 **만들기**를 선택합니다.

     ![응용 프로그램 이름 지정](./media/resource-group-create-service-principal-portal/create-app.png)

응용 프로그램이 만들어졌습니다.

## <a name="get-application-id-and-authentication-key"></a>응용 프로그램 ID 및 인증 키 가져오기
프로그래밍 방식으로 로그인하는 경우 응용 프로그램에 대한 ID 및 인증 키가 필요합니다. 이러한 값을 가져오려면 다음 단계를 사용합니다.

1. Azure Active Directory의 **앱 등록**에서 응용 프로그램을 선택합니다.

     ![응용 프로그램 선택](./media/resource-group-create-service-principal-portal/select-app.png)
2. **응용 프로그램 ID**를 복사하고 응용 프로그램 코드에 저장합니다. [샘플 응용 프로그램](#sample-applications) 섹션의 응용 프로그램은 이 값을 클라이언트 ID로 참조합니다.

     ![클라이언트 ID](./media/resource-group-create-service-principal-portal/copy-app-id.png)
3. 인증 키를 생성하려면 **키**를 선택합니다.

     ![키 선택](./media/resource-group-create-service-principal-portal/select-keys.png)
4. 키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.

     ![키 저장](./media/resource-group-create-service-principal-portal/save-key.png)

     키를 저장하면 키 값이 표시됩니다. 나중에 키를 검색할 수 없으므로 이 값을 복사해둡니다. 응용 프로그램으로 로그인하려면 응용 프로그램 ID와 함께 키 값을 제공합니다. 응용 프로그램에서 검색할 수 있는 위치에 키 값을 저장합니다.

     ![공유 키](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>테넌트 ID 가져오기
프로그래밍 방식으로 로그인하는 경우 인증 요청과 함께 테넌트 ID를 전달해야 합니다. 

1. 테넌트 ID를 가져오려면 Azure AD 테넌트에 대한 **속성**을 선택합니다. 

     ![Azure AD 속성 선택](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

2. **디렉터리 ID**를 복사합니다. 이 값은 테넌트 ID입니다.

     ![테넌트 ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>응용 프로그램을 역할에 할당
구독의 리소스에 액세스하려면 역할에 응용 프로그램을 할당해야 합니다. 응용 프로그램에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. 응용 프로그램을 할당하려는 범위 수준으로 이동합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다. 대신 리소스 그룹 또는 리소스를 선택할 수 있습니다.

     ![구독 선택](./media/resource-group-create-service-principal-portal/select-subscription.png)

2. 응용 프로그램을 할당할 특정 구독(리소스 그룹 또는 리소스)을 선택합니다.

     ![할당을 위한 구독 선택](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

3. **액세스 제어(IAM)**를 선택합니다.

     ![액세스 선택](./media/resource-group-create-service-principal-portal/select-access-control.png)

4. **추가**를 선택합니다.

     ![추가 선택](./media/resource-group-create-service-principal-portal/select-add.png)
6. 응용 프로그램에 할당할 역할을 선택합니다. 다음 이미지에서는 **읽기 권한자** 역할을 보여 줍니다.

     ![역할 선택](./media/resource-group-create-service-principal-portal/select-role.png)

8. 응용 프로그램을 검색하고 선택합니다.

     ![앱 검색](./media/resource-group-create-service-principal-portal/search-app.png)
9. **확인**을 선택하여 역할 할당을 완료합니다. 목록에서 해당 범위에 대한 역할에 할당된 사용자 목록에 응용 프로그램이 표시될 것입니다.

## <a name="log-in-as-the-application"></a>응용 프로그램으로 로그인

응용 프로그램은 이제 Azure Active Directory에 설정되어 있습니다. 응용 프로그램으로 로그인하는 데 사용할 ID와 키가 있습니다. 응용 프로그램은 수행할 수 있는 특정 작업을 제공하는 역할에 할당됩니다. 

PowerShell을 통해 로그인하려면 [PowerShell을 통해 자격 증명 제공](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell)을 참조하세요.

Azure CLI를 통해 로그인하려면 [Azure CLI를 통해 자격 증명 제공](resource-group-authenticate-service-principal-cli.md#provide-credentials-through-azure-cli)을 참조하세요.

REST 작업에 대한 액세스 토큰을 얻으려면 [요청 만들기](/rest/api/#create-the-request)를 참조하세요.

응용 프로그램 코드를 통한 로그인에 대해 알아보려면 다음 샘플 응용 프로그램을 살펴보세요.

### <a name="sample-applications"></a>샘플 응용 프로그램
다음 예제 응용 프로그램에서는 AD 응용 프로그램으로 로그인하는 방법을 보여 줍니다.

**.NET**

* [.NET에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [.NET을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [리소스 사용 시작 - Azure Resource Manager 템플릿을 사용하여 배포 - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [리소스 사용 시작 - 리소스 그룹 관리 - Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Python에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Python을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Node.js에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Node.js를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Ruby에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Ruby를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>다음 단계
* 다중 테넌트 응용 프로그램을 설정하려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.
* 보안 정책 지정에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.  
* 권한이 부여되거나 사용자에 대해 거부될 수 있는 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](../active-directory/role-based-access-control-resource-provider-operations.md)을 참조하세요.

