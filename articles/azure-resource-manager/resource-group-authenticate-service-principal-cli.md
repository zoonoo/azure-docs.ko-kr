---
title: "Azure CLI를 사용하여 Azure 앱에 대한 ID 만들기 | Microsoft Docs"
description: "Azure CLI를 사용하여 Azure Active Directory 응용 프로그램 및 서비스 주체를 만들고 역할 기반 액세스 제어를 통해 리소스에 대한 액세스를 부여하는 방법을 설명합니다. 암호 또는 인증서를 사용하여 응용 프로그램을 인증하는 방법을 보여 줍니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: fe05b9fd3bc2737ba157f620f91c25654114b480
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기

리소스에 액세스해야 하는 앱 또는 스크립트가 있는 경우 앱에 대한 ID를 설정하고 자체 자격 증명으로 앱을 인증할 수 있습니다. 이 ID를 서비스 주체라고 합니다. 이 접근 방법을 사용하면 다음을 수행할 수 있습니다.

* 자체 사용 권한과 다른 앱 ID에 대한 사용 권한을 할당합니다. 일반적으로 이러한 권한은 정확히 앱 실행에 필요한 것으로 제한됩니다.
* 무인 스크립트를 실행할 때 인증을 위해 인증서를 사용합니다.

이 문서에서는 [Azure CLI 1.0](../cli-install-nodejs.md)을 사용하여 응용 프로그램을 자체 자격 증명 및 ID로 실행하도록 설정하는 방법을 보여 줍니다. 최신 버전의 [Azure CLI 1.0](../cli-install-nodejs.md)을 설치하여 환경이 이 문서의 예제와 일치하도록 합니다.

## <a name="required-permissions"></a>필요한 사용 권한
이 항목을 완료하려면 Azure Active Directory와 Azure 구독에 대한 충분한 권한이 있어야 합니다. 특히, Azure Active Directory에서 앱을 만들고 역할에 서비스 주체를 할당할 수 있어야 합니다. 

계정에 적절한 사용 권한이 있는지를 확인하는 가장 쉬운 방법은 포털을 통하는 것입니다. [포털에서 필요한 사용 권한 확인](resource-group-create-service-principal-portal.md#required-permissions)을 참조하세요.

이제 [암호](#create-service-principal-with-password) 또는 [인증서](#create-service-principal-with-certificate) 인증에 대한 섹션을 계속 진행합니다.

## <a name="create-service-principal-with-password"></a>암호를 사용하여 서비스 주체 만들기
이 섹션에서는 암호를 사용하여 AD 응용 프로그램을 만드는 단계를 수행하고 읽기 권한자 역할을 서비스 주체에 할당합니다.

1. 계정에 로그인합니다.
   
   ```azurecli
   azure login
   ```
2. 앱 ID를 만들려면 다음 명령을 표시된 것처럼 앱의 이름과 암호를 제공합니다.
     
   ```azurecli
   azure ad sp create -n exampleapp -p {your-password}
   ```
     
   새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다. 로그인할 때 서비스 주체 이름으로 나열된 GUID가 필요합니다. 이 GUID는 앱 ID와 동일한 값입니다. 샘플 응용 프로그램에서 이 값은 `Client ID`라고 합니다. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating application exampleapp
     / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
     data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
     data:    Display Name:            exampleapp
     data:    Service Principal Names:
     data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
     data:                             https://www.contoso.org/example
     info:    ad sp create command OK
   ```

3. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 예제에서는 구독에서 모든 리소스를 읽을 수 있는 권한이 부여된 읽기 권한자 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요. objectid 매개 변수의 경우 응용 프로그램을 만들 때 사용한 개체 ID를 제공합니다. 이 명령을 실행하기 전에 새 서비스 주체가 Azure Active Directory 전체에 전파될 어느 정도의 시간을 허용해야 합니다. 이러한 명령을 수동으로 실행할 때 작업 간에 충분 한 시간이 경과되었습니다. 스크립트에 명령 사이에 절전 모드로 전환하는 단계를 추가해야 합니다(예시 `sleep 15`). 주체가 디렉터리에 존재하지 않는다는 오류가 표시되는 경우 명령을 반환합니다.
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
이것으로 끝입니다. AD 응용 프로그램 및 서비스 주체가 설정되었습니다. 다음 섹션에서는 Azure CLI를 통해 자격 증명을 사용하여 로그인하는 방법을 보여 줍니다. 코드 응용 프로그램에서 자격 증명을 사용하려는 경우 이 항목을 진행할 필요가 없습니다. [샘플 응용 프로그램](#sample-applications) 으로 이동하여 응용 프로그램 ID 및 암호를 사용하여 로그인하는 예제를 참조할 수 있습니다. 

### <a name="provide-credentials-through-azure-cli"></a>Azure CLI를 통해 자격 증명 제공
이제 응용 프로그램으로 로그인하여 작업을 수행해야 합니다.

1. 서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Azure Active Directory의 인스턴스입니다. 현재 인증된 구독에 대한 테넌트 ID를 검색하려면 다음을 사용합니다.
   
   ```azurecli
   azure account show
   ```
   
   반환하는 내용은 다음과 같습니다.
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     다른 구독의 테넌트 ID를 가져와야 하는 경우 다음 명령을 사용하세요.
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 로그인에 사용할 클라이언트 ID를 검색해야 하는 경우 다음 명령을 사용합니다.
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     로그인에 사용할 값은 서비스 주체 이름에 나열된 GUID입니다.
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. 서비스 주체로 로그인합니다.
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    암호를 입력하라는 메시지가 나타납니다. AD 응용 프로그램을 만들 때 지정한 암호를 제공합니다.
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

이제 사용자는 작성한 서비스 주체에 대한 서비스 주체로 인증됩니다.

또는 명령줄에서 REST 작업을 호출하여 로그인할 수 있습니다. 인증 응답에서 다른 작업에 사용할 액세스 토큰을 검색할 수 있습니다. REST 작업을 호출하여 액세스 토큰을 검색하는 예제는 [액세스 토큰 생성하기](resource-manager-rest-api.md#generating-an-access-token)를 참조하세요.

## <a name="create-service-principal-with-certificate"></a>인증서를 사용하여 서비스 주체 만들기
이 섹션에서 수행하는 단계는 다음과 같습니다.

* 자체 서명된 인증서 만들기
* 인증서를 사용하는 AD 응용 프로그램과 서비스 주체 만들기
* 읽기 권한자 역할을 서비스 주체에 할당

다음 단계를 완료하려면 [OpenSSL](http://www.openssl.org/) 을 설치해야 합니다.

1. 자체 서명된 인증서를 만듭니다.
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```

2. 앞의 두 단계에서 2개의 파일 privkey.pem 및 cert.pem이 생성되었습니다. 공용 및 개인 키를 단일 파일로 결합합니다.

   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```

3. **examplecert.pem** 파일을 열고 **-----BEGIN CERTIFICATE-----**와 **-----END CERTIFICATE-----** 사이의 긴 시퀀스 문자를 찾습니다. 인증서 데이터를 복사합니다. 서비스 주체를 만들 때 이 데이터를 매개 변수로 전달합니다.

4. 계정에 로그인합니다.

   ```azurecli
   azure login
   ```
5. 서비스 주체를 만들려면 다음 명령과 같이 앱 이름과 인증서 데이터를 제공합니다.
     
   ```azurecli
   azure ad sp create -n exampleapp --cert-value {certificate data}
   ```
     
   새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다. 로그인할 때 서비스 주체 이름으로 나열된 GUID가 필요합니다. 이 GUID는 앱 ID와 동일한 값입니다. 샘플 응용 프로그램에서 이 값을 클라이언트 ID라고 합니다. 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
     data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
     data:    Display Name:     exampleapp
     data:    Service Principal Names:
     data:                      4fd39843-c338-417d-b549-a545f584a745
     data:                      https://www.contoso.org/example
     info:    ad sp create command OK
   ```
6. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 예제에서는 구독에서 모든 리소스를 읽을 수 있는 권한이 부여된 읽기 권한자 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요. objectid 매개 변수의 경우 응용 프로그램을 만들 때 사용한 개체 ID를 제공합니다. 이 명령을 실행하기 전에 새 서비스 주체가 Azure Active Directory 전체에 전파될 어느 정도의 시간을 허용해야 합니다. 이러한 명령을 수동으로 실행할 때 작업 간에 충분 한 시간이 경과되었습니다. 스크립트에 명령 사이에 절전 모드로 전환하는 단계를 추가해야 합니다(예시 `sleep 15`). 주체가 디렉터리에 존재하지 않는다는 오류가 표시되는 경우 명령을 반환합니다.
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
  
### <a name="provide-certificate-through-automated-azure-cli-script"></a>자동화된 Azure CLI 스크립트를 통해 인증서 제공
이제 응용 프로그램으로 로그인하여 작업을 수행해야 합니다.

1. 서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Azure Active Directory의 인스턴스입니다. 현재 인증된 구독에 대한 테넌트 ID를 검색하려면 다음을 사용합니다.
   
   ```azurecli
   azure account show
   ```
   
   반환하는 내용은 다음과 같습니다.
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
   다른 구독의 테넌트 ID를 가져와야 하는 경우 다음 명령을 사용하세요.
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 인증서 지문을 검색하고 불필요한 문자를 제거하려면 다음을 사용합니다.
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
   다음과 유사한 지문 값을 반환합니다.
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. 로그인에 사용할 클라이언트 ID를 검색해야 하는 경우 다음 명령을 사용합니다.
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
   로그인에 사용할 값은 서비스 주체 이름에 나열된 GUID입니다.
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. 서비스 주체로 로그인합니다.
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

이제 사용자는 작성한 Azure Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

## <a name="change-credentials"></a>자격 증명 변경

보안 침해 또는 자격 증명 만료 때문에 AD 앱에 대한 자격 증명을 변경하려면 `azure ad app set`을 사용합니다.

암호를 변경하려면 다음을 사용합니다.

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

인증서 값을 변경하려면 다음을 사용합니다.

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```

## <a name="debug"></a>디버그

서비스 주체를 만들 때 다음과 같은 오류가 발생할 수 있습니다.

* **"Authentication_Unauthorized"** 또는 **"컨텍스트에서 구독을 찾을 수 없습니다."** - 계정에 Azure Active Directory에서 앱을 등록하는 데 [필요한 권한](#required-permissions)이 없으면 이 오류가 발생합니다. 일반적으로 Azure Active Directory의 관리 사용자만 앱을 등록할 수 있고 사용자 계정은 관리자가 아니면 이 오류가 표시됩니다. 관리자 역할에 사용자를 할당하거나 사용자가 앱을 등록할 수 있게 설정하도록 관리자에게 요청합니다.

* 계정에 **"'/subscriptions/{guid}' 범위에 대해 'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 수 있는 권한이 없습니다."** - 계정에 ID에 역할을 할당할 수 있는 충분한 권한이 없을 때 이 오류가 표시됩니다. 구독 관리자에게 사용자 액세스 관리자 역할에 사용자를 추가할 것을 요청합니다.

## <a name="sample-applications"></a>샘플 응용 프로그램
다음 예제 응용 프로그램에서는 서비스 주체로 로그인하는 방법을 보여 줍니다.

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
* 리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.
* 인증서 및 Azure CLI 사용에 대한 자세한 내용은 [Linux 명령줄에서 Azure 서비스 보안 주체로 인증서 기반 인증](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)을 참조하세요. 
* 권한이 부여되거나 사용자에 대해 거부될 수 있는 작업 목록은 [Azure Resource Manager 리소스 공급자 작업](../active-directory/role-based-access-control-resource-provider-operations.md)을 참조하세요.

