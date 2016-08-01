<properties
   pageTitle="Azure CLI에서 서비스 주체 만들기 | Microsoft Azure"
   description="Azure CLI를 사용하여 Active Directory 응용 프로그램 및 서비스 주체를 만들고 역할 기반 액세스 제어를 통해 리소스에 대한 액세스를 부여하는 방법을 설명합니다. 암호 또는 인증서를 사용하여 응용 프로그램을 인증하는 방법을 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [포털](resource-group-create-service-principal-portal.md)

리소스에 액세스해야 하는 응용 프로그램이나 스크립트가 있는 경우 이 프로세스를 사용자의 자격 증명에 따라 실행하지 않으려고 할 수 있습니다. 해당 사용자가 프로세스에 할당하려고 하는 권한과 다른 권한을 가질 수 있으며 사용자의 역할 책임도 달라질 수 있기 때문입니다. 대신 인증 자격 증명 및 역할 할당을 포함하는 응용 프로그램에 대한 ID를 만들 수 있습니다. 응용 프로그램은 실행될 때마다 이 ID로 로그인합니다. 이 항목에서는 [Mac, Linux 및 Windows용 Azure CLI](xplat-cli-install.md)를 사용하여 응용 프로그램을 자체 자격 증명 및 ID로 실행하는 데 필요한 모든 항목을 설정하는 방법을 보여 줍니다.

이 문서에서는 AD(Active Directory) 응용 프로그램 및 서비스 주체라는 두 개체를 만듭니다. AD 응용 프로그램에는 자격 증명(응용 프로그램 ID 및 암호 또는 인증서)이 포함되어 있습니다. 서비스 주체에는 역할 할당이 포함되어 있습니다. AD 응용 프로그램에서 많은 서비스 주체를 만들 수 있습니다. 이 항목에서는 응용 프로그램을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 응용 프로그램을 중점적으로 다룹니다. 일반적으로 단일 조직 내에서 실행되는 LOB(기간 업무) 응용 프로그램에 대해 단일 테넌트 응용 프로그램을 사용하게 됩니다. 많은 조직에서 응용 프로그램을 실행해야 하는 경우 다중 테넌트 응용 프로그램을 만들 수도 있습니다. 일반적으로 SaaS(software-as-a-service) 응용 프로그램에 대해 다중 테넌트 응용 프로그램을 사용합니다. 다중 테넌트 응용 프로그램을 설정하려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

Active Directory를 사용할 때 이해해야 할 여러 가지 개념이 있습니다. 응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](./active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](./active-directory/active-directory-authentication-scenarios.md)를 참조하세요.

Azure CLI를 사용하는 경우 AD 응용 프로그램을 인증하기 위한 다음 2가지 옵션이 있습니다.

 - password
 - 인증서

AD 응용 프로그램을 설정한 다음 다른 프로그래밍 프레임워크(예: Python, Ruby 또는 Node.js)에서 Azure에 로그인하려는 경우 암호 인증이 가장 좋은 방법일 수 있습니다. 암호 또는 인증서 중 어떤 방법을 사용할지 결정하기 전에 [샘플 응용 프로그램](#sample-applications) 섹션에서 여러 다른 프레임워크에서 인증하는 방법의 예를 참조하세요.

## 테넌트 ID 가져오기

서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Active Directory의 인스턴스입니다. 암호 또는 인증서 인증을 위해 해당 값이 필요하므로 이제부터 해당 값을 구해 보겠습니다.

1. 계정에 로그인합니다.

        azure config mode arm
        azure login

1. 현재 인증된 구독에 대한 테넌트 ID를 검색하는 경우 매개 변수로 구독 ID를 제공할 필요가 없습니다. **-r** 스위치는 따옴표 없이 값을 검색합니다.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

이제 [암호](#create-service-principal-with-password) 또는 [인증서](#create-service-principal-with-certificate) 인증에 대한 아래 섹션을 계속 진행합니다.


## 암호를 사용하여 서비스 주체 만들기

이 섹션에서는 암호를 사용하여 서비스 주체를 만든 후 역할에 할당하는 단계를 수행합니다.

1. 응용 프로그램에 대한 서비스 주체를 만듭니다. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다. 이 명령은 AD 응용 프로그램 및 서비스 주체를 모두 만듭니다.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
        
    새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다. 서비스 주체 이름은 로그인할 때 필요합니다.
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요. 역할을 할당하려면 [소유자](./active-directory/role-based-access-built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) 역할을 통해 부여된 `Microsoft.Authorization/*/Write` 액세스 권한이 있어야 합니다.

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

이것으로 끝입니다. AD 응용 프로그램 및 서비스 주체가 설정되었습니다. 다음 섹션에서는 Azure CLI를 통해 자격 증명으로 로그인하는 방법을 보여 줍니다. 그러나 코드 응용 프로그램에서 자격 증명을 사용하려는 경우 이 항목을 계속 진행할 필요가 없습니다. [샘플 응용 프로그램](#sample-applications)으로 이동하여 응용 프로그램 ID 및 암호를 사용하여 로그인하는 예제를 참조할 수 있습니다.

### Azure CLI를 통해 자격 증명 제공

1. 사용자 이름으로 서비스 주체를 만들 때 반환된 서비스 주체 이름을 사용합니다. 응용 프로그램 ID를 검색해야 할 경우 다음 명령을 사용합니다. **검색** 매개 변수에 Active Directory 응용 프로그램의 이름을 제공합니다.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. 서비스 주체로 로그인합니다.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant "$tenantId"

    암호를 입력하라는 메시지가 나타납니다. AD 응용 프로그램을 만들 때 지정한 암호를 제공합니다.

        info:    Executing command login
        Password: ********

이제 사용자는 작성한 서비스 주체에 대한 서비스 주체로 인증됩니다.

## 인증서를 사용하여 서비스 주체 만들기

이 섹션에서는 인증에 인증서를 사용하는 서비스 주체에 대한 서비스 주체를 만드는 단계를 수행합니다. 다음 단계를 완료하려면 [OpenSSL](http://www.openssl.org/)을 설치해야 합니다.

1. 자체 서명된 인증서를 만듭니다.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. 공개 및 개인 키를 결합합니다.

        cat privkey.pem cert.pem > examplecert.pem

3. **examplecert.pem** 파일을 열고 인증서 데이터를 복사합니다. **---BEGIN CERTIFICATE---**와 **---END CERTIFICATE---** 사이의 긴 시퀀스 문자를 찾습니다.

1. 응용 프로그램에 대한 서비스 주체를 만듭니다. 이전 단계에서 반환된 응용 프로그램 id를 제공합니다.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
    새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요. 역할을 할당하려면 [소유자](./active-directory/role-based-access-built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) 역할을 통해 부여된 `Microsoft.Authorization/*/Write` 액세스 권한이 있어야 합니다.

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### 자동화된 Azure CLI 스크립트를 통해 인증서 제공

Azure CLI를 인증하려면 인증서 지문, 인증서 파일, 응용 프로그램 ID 및 테넌트 ID를 제공합니다.

    azure login --service-principal --tenant 00000 -u 000000 --certificate-file C:\certificates\examplecert.pem --thumbprint 000000

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

인증서 지문을 검색하고 불필요한 문자를 제거해야 할 경우 다음을 사용합니다.

    openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
다음과 유사한 지문 값을 반환합니다.

    30996D9CE48A0B6E0CD49DBB9A48059BF9355851

응용 프로그램 ID를 검색해야 할 경우 다음을 사용합니다.

    azure ad app show --search exampleapp --json | jq -r '.[0].appId'

나중에 테넌트 ID를 검색해야 할 경우 [테넌트 ID 가져오기](#get-tenant-id)를 참조하세요.


## 샘플 응용 프로그램

다음 예제 응용 프로그램에서는 서비스 주체로 로그인하는 방법을 보여 줍니다.

**.NET**

- [.NET에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [.NET을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [리소스 사용 시작 - Azure Resource Manager 템플릿을 사용하여 배포 - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [리소스 사용 시작 - 리소스 그룹 관리 - Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Python에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Python을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

- [Node.js에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Node.js를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Ruby에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Ruby를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## 다음 단계
  
- 리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.
- 인증서 및 Azure CLI 사용에 대한 자세한 내용은 [Linux 명령줄에서 Azure 서비스 보안 주체로 인증서 기반 인증](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)을 참조하세요.

<!---HONumber=AcomDC_0720_2016-->