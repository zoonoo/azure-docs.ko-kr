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
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [포털](resource-group-create-service-principal-portal.md)

이 항목에서는 [Mac, Linux 및 Windows용 Azure CLI](xplat-cli-install.md)을 자동화된 프로세스, 응용 프로그램 또는 서비스와 같이 다른 구독의 리소스에 액세스할 수 있는 AD(Active Directory) 응용 프로그램을 만드는 방법을 보여 줍니다. Azure Resource Manager에서 역할 기반 액세스 제어를 사용하여 응용 프로그램에 대해 허용된 작업 권한을 부여할 수 있습니다.

이 문서에서는 AD 응용 프로그램 및 서비스 주체라는 두 개체를 만듭니다. AD 응용 프로그램은 앱이 등록되는 테넌트에 상주하며 실행할 프로세스를 정의합니다. 서비스 주체는 AD 응용 프로그램의 ID를 포함하고 사용 권한을 할당하는 데 사용됩니다. AD 응용 프로그램에서 많은 서비스 주체를 만들 수 있습니다. 응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](./active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](./active-directory/active-directory-authentication-scenarios.md)를 참조하세요.

응용 프로그램을 인증하기 위한 2가지 옵션이 있습니다.

 - 암호 - 실행 중에 사용자가 대화형으로 로그인하는 경우 적합합니다.
 - 인증서 - 사용자 상호 작용 없이 인증해야 하는 무인 모드 스크립트에 적합합니다.
 
## 암호를 사용하여 AD 응용 프로그램 만들기

이 섹션에서는 암호를 사용하여 AD 응용 프로그램을 만드는 단계를 수행합니다.

1. Azure 리소스 관리자 모드로 전환한 다음 계정에 로그인합니다.

        azure config mode arm
        azure login

2. **Azure AD 앱 만들기** 명령을 실행하여 새 AD 응용 프로그램을 만듭니다. 응용 프로그램의 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크(이 링크는 확인되지 않음), 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    AD 응용 프로그램이 반환됩니다. 서비스 주체 만들기 및 액세스 토큰 획득을 위해서는 AppId 속성이 필요합니다.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### 서비스 주체를 만들고 역할에 할당합니다.

1. 응용 프로그램에 대한 서비스 주체를 만듭니다. 이전 단계에서 반환된 응용 프로그램 id를 제공합니다.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

2. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### Azure CLI를 통해 수동으로 자격 증명 제공

해당 응용 프로그램에 대한 AD 응용 프로그램 및 서비스 주체를 만들었습니다. 역할에 서비스 주체를 할당했습니다. 이제, 응용 프로그램으로 로그인하여 작업을 수행해야 합니다. 주문형 스크립트 또는 명령을 실행할 경우 응용 프로그램에 대한 자격 증명을 수동으로 제공할 수 있습니다.

1. 서비스 주체를 포함하는 구독에 대해 **TenantId**를 확인합니다. 현재 인증된 구독에 대한 테넌트 ID를 검색하는 경우 매개 변수로 구독 ID를 제공할 필요가 없습니다. **-r** 스위치는 따옴표 없이 값을 검색합니다.

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. 사용자 이름의 경우 서비스 주체를 만들 때 사용한 **AppId**를 사용합니다. 응용 프로그램 ID를 검색해야 할 경우 다음 명령을 사용합니다. **검색** 매개 변수에 Active Directory 응용 프로그램의 이름을 제공합니다.

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. 서비스 주체로 로그인합니다.

        azure login -u "$appId" --service-principal --tenant "$tenantId"

    암호를 입력하라는 메시지가 나타납니다. AD 응용 프로그램을 만들 때 지정한 암호를 제공합니다.

        info:    Executing command login
        Password: ********

이제 사용자는 작성한 AD 응용 프로그램에 대한 서비스 주체로 인증됩니다.

## 인증서를 사용하여 AD 응용 프로그램 만들기

이 섹션에서는 인증에 인증서를 사용하는 AD 응용 프로그램에 대한 서비스 주체를 만드는 단계를 수행합니다. 다음 단계를 완료하려면 [OpenSSL](http://www.openssl.org/)을 설치해야 합니다.

1. 자체 서명된 인증서를 만듭니다.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. 공개 및 개인 키를 결합합니다.

        cat privkey.pem cert.pem > examplecert.pem

3. **examplecert.pem** 파일을 열고 인증서 데이터를 복사합니다. **---BEGIN CERTIFICATE---**와 **---END CERTIFICATE---** 사이의 긴 시퀀스 문자를 찾습니다.

4. **azure ad app create** 명령을 실행하여 새 Active Directory 응용 프로그램을 만들고, 이전 단계에서 키 값으로 복사한 인증서 데이터를 제공합니다.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Active Directory 응용 프로그램이 반환됩니다. 서비스 주체 만들기 및 액세스 토큰 획득을 위해서는 AppId 속성이 필요합니다.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### 서비스 주체를 만들고 역할에 할당합니다.

1. 응용 프로그램에 대한 서비스 주체를 만듭니다. 이전 단계에서 반환된 응용 프로그램 id를 제공합니다.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. 서비스 주체에게 구독에 대한 권한을 부여합니다. 이 샘플에서는 서비스 주체에게 구독에서 모든 리소스를 읽을 수 있는 권한을 부여합니다. 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### 스크립트에 대한 값을 준비합니다.

스크립트에서 서비스 주체로 로그인하는 데 필요한 세 가지 값을 전달합니다. 다음 정보가 필요합니다.

- 응용 프로그램 ID
- 테넌트 ID
- 인증서 지문

이전 단계에서 응용 프로그램 ID 및 인증서 지문을 확인했습니다. 그러나 나중에 이러한 값을 검색해야 하는 경우 명령은 아래와 같이 테넌트 ID를 가져오는 명령과 함께 표시됩니다.

1. 인증서 지문을 검색하고 불필요한 문자를 제거하려면 다음을 사용합니다.

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     다음과 유사한 지문 값을 반환합니다.

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. 테넌트 ID를 검색하려면 다음을 사용합니다.

        azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId'

3. 응용 프로그램 ID를 검색하려면 다음을 사용합니다.

        azure ad app show --search exampleapp --json | jq -r '.[0].appId'

### 자동화된 Azure CLI 스크립트를 통해 인증서 제공

해당 응용 프로그램에 대한 Active Directory 응용 프로그램 및 서비스 주체를 만들었습니다. 역할에 서비스 주체를 할당했습니다. 이제 서비스 주체로 작업을 수행하는 서비스 주체로 로그인해야 합니다.

Azure CLI를 인증하려면 인증서 지문, 인증서 파일, 응용 프로그램 ID 및 테넌트 ID를 제공합니다.

        azure login --service-principal --tenant {tenant-id} -u {app-id} --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

## 다음 단계
  
- .NET 인증 예제의 경우 [.NET용 Azure Resource Manager SDK](resource-manager-net-sdk.md)를 참조하세요.
- Java 인증 예제의 경우 [Java용 Azure Resource Manager SDK](resource-manager-java-sdk.md)를 참조하세요.
- Python 인증 예제의 경우 [Python에 대한 리소스 관리 인증](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)을 참조하세요.
- REST 인증 예제의 경우 [REST APIs용 Resource Manager SDK](resource-manager-rest-api.md)를 참조하세요.
- 리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.
- 인증서 및 Azure CLI 사용에 대한 자세한 내용은 [Linux 명령줄에서 Azure 서비스 보안 주체로 인증서 기반 인증](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)을 참조하세요.

<!---HONumber=AcomDC_0629_2016-->