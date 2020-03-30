---
title: 보안 웹 응용 프로그램 개발 | 마이크로 소프트 문서
description: 이 간단한 샘플 앱은 Azure에서 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선하는 보안 모범 사례를 구현합니다.
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 75890efebc42b74c56fb95ed1803152b516588b9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385217"
---
# <a name="develop-a-secure-web-app"></a>보안 웹앱 개발

이 샘플은 Azure에서 앱을 개발하기 위한 보안 리소스에 대한 링크가 포함된 웹 페이지를 표시하는 간단한 Python 앱입니다. 앱은 Azure에서 앱을 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선하는 데 도움이 되는 보안 모범 사례를 구현합니다.

응용 프로그램 구성 요소가 제대로 구성되었는지 확인하려면 이 문서에서 설명하는 단계를 순차적으로 수행해야 합니다. 데이터베이스, Azure 앱 서비스, Azure 키 볼트 인스턴스 및 Azure 응용 프로그램 게이트웨이 인스턴스는 서로 종속됩니다.

배포 스크립트는 인프라를 설정합니다. 배포 스크립트를 실행한 후 Azure Portal에서 구성 요소와 서비스를 함께 연결하기 위해 몇 가지 수동 구성을 수행해야 합니다.

샘플 앱은 응용 프로그램에서 보안 조치를 구현하려는 Azure에서 응용 프로그램을 개발하는 초보자를 대상으로 합니다.

이 앱을 개발하고 배포할 때 다음 방법을 알아봅니다.

- Azure Key Vault 인스턴스를 만들고 비밀에서 저장 및 검색합니다.
- PostgreSQL용 Azure 데이터베이스 배포, 보안 암호 설정 및 액세스 권한 부여
- Linux용 Azure 웹 앱에서 알파인 Linux 컨테이너를 실행하고 Azure 리소스에 대해 관리되는 ID를 사용하도록 설정합니다.
- [OWASP 상위 10개 규칙 집합을](https://coreruleset.org/)사용하는 방화벽을 사용하여 Azure 응용 프로그램 게이트웨이 인스턴스를 만들고 구성합니다.
- Azure 서비스를 사용하여 전송 중 및 미사용 데이터의 암호화를 사용하도록 설정합니다.

이 앱을 개발하고 배포한 후에는 설명된 구성 및 보안 조치와 함께 다음 샘플 웹 앱을 설정하게 됩니다.

![샘플 웹 앱](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architecture

응용 프로그램은 세 계층과 일반적인 n 계층 응용 프로그램입니다. 모니터링 및 비밀 관리 구성 요소가 통합된 프런트 엔드, 백 엔드 및 데이터베이스 계층은 다음과 같습니다.

![앱 아키텍처](./media/secure-web-app/architecture.png)

아키텍처는 다음과 같은 구성 요소로 구성됩니다.

- [Azure Application Gateway](../../application-gateway/index.yml). 응용 프로그램 아키텍처에 대한 게이트웨이 및 방화벽을 제공합니다.
- [리눅스에 Azure 웹 애플 리 케이 션](../../app-service/containers/app-service-linux-intro.md). Linux 환경에서 Python 앱을 실행하는 컨테이너 런타임을 제공합니다.
- [Azure 키 볼트](../../key-vault/index.yml). 앱의 비밀을 저장하고 암호화하고 앱 주변의 액세스 정책 생성을 관리합니다.
- [PostgreSQL에 대한 Azure 데이터베이스](https://azure.microsoft.com/services/postgresql/). 앱의 데이터를 안전하게 저장합니다.
- [Azure 보안 센터](../../security-center/index.yml) 및 [Azure 응용 프로그램 인사이트](../../azure-monitor/app/app-insights-overview.md). 우리의 응용 프로그램의 작동에 대한 모니터링 및 경고를 제공합니다.

## <a name="threat-model"></a>위협 모델

위협 모델링은 비즈니스 및 응용 프로그램에 대한 잠재적보안 위협을 식별한 다음 적절한 완화 계획을 수립하는 프로세스입니다.

이 샘플에서는 [Microsoft 위협 모델링 도구를](threat-modeling-tool.md) 사용하여 보안 샘플 앱에 대한 위협 모델링을 구현했습니다. 구성 요소와 데이터 흐름을 다이어그램으로 만들어 개발 프로세스 초기에 문제와 위협을 식별할 수 있습니다. 이렇게 하면 나중에 시간과 비용을 절약할 수 있습니다.

샘플 앱의 위협 모델입니다.

![위협 모델](./media/secure-web-app/threat-model.png)

위협 모델링 도구가 생성하는 일부 샘플 위협 및 잠재적 취약점은 다음 스크린샷에 나와 있습니다. 위협 모델은 노출된 공격 노출 에 대한 개요를 제공하고 개발자가 문제를 완화하는 방법에 대해 생각하라는 메시지를 표시합니다.

![위협 모델 출력](./media/secure-web-app/threat-model-output.png)

예를 들어, 이전 위협 모델 출력의 SQL 주입은 사용자 입력을 삭제하고 PostgreSQL용 Azure 데이터베이스에 저장된 함수를 사용하여 완화됩니다. 이 완화는 데이터 읽기 및 쓰기 중에 쿼리를 임의로 실행하지 못하게 합니다.

개발자는 위협 모델 출력의 각 위협을 완화하여 시스템의 전반적인 보안을 향상시킵니다.

## <a name="deployment"></a>배포

다음 옵션을 사용하면 Azure 앱 서비스에서 Linux를 실행할 수 있습니다.

- 지원 기술(파이썬, 루비, PHP, Java, Node.js, .NET Core)으로 만든 Azure에서 미리 빌드된 Microsoft 컨테이너 목록에서 컨테이너를 선택합니다.
- 사용자 지정 빌드 컨테이너를 사용합니다. 사용자 고유의 컨테이너 레지스트리를 이미지의 소스로 선택하고 HTTP를 지원하는 사용 가능한 많은 기술을 기반으로 합니다.

이 예제에서는 앱 서비스에 웹랩을 배포하고 리소스를 만드는 배포 스크립트를 실행합니다.

앱은 아래와 같은 다양한 배포 모델을 사용할 수 있습니다.

![배포 데이터 흐름 다이어그램](./media/secure-web-app/deployment.png)

Azure에서 앱을 배포하는 방법에는 다음과 같은 여러 가지 방법이 있습니다.

- Azure 리소스 관리자 템플릿
- PowerShell
- Azure CLI
- Azure portal
- Azure DevOps

이 응용 프로그램은 사용 :

- [Docker를](https://docs.docker.com/) 사용하여 컨테이너 이미지를 만들고 빌드합니다.
- 배포를 위한 [Azure CLI입니다.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [도커 허브를](https://hub.docker.com/) 컨테이너 레지스트리로 합니다.

## <a name="security-considerations"></a>보안 고려 사항

### <a name="network"></a>네트워크

샘플 앱은 네트워크 안팎으로 흐르는 전송 중 데이터에 대해 종단 간 SSL 암호화를 사용합니다. 게이트웨이는 자체 서명된 인증서로 구성됩니다.
> [!IMPORTANT]
> 이 데모에서는 자체 서명된 인증서가 사용됩니다. 프로덕션 환경에서는 확인된 인증 기관(CA)에서 인증서를 가져와야 합니다.

또한 응용 프로그램 방화벽은 들어오는 트래픽을 검사하고 네트워크 트래픽에서 악성 트래픽이 감지되면 관리자에게 경고합니다.
응용 프로그램 게이트웨이는 위협 모델에서 발견된 DDoS 및 SQL 주입 위협의 가능성을 완화합니다.

### <a name="identity"></a>Identity

포털에 로그인하기 위해 샘플 앱은 리소스에 대한 액세스 권한을 할당받은 Azure Active Directory(Azure AD) 관리자에 대한 다단계 인증을 사용합니다.
샘플 앱은 관리되는 ID를 사용하여 Azure Key Vault에서 암호를 읽고 검색할 수 있는 권한을 확보하여 앱이 암호를 읽기 위해 자격 증명 및 토큰을 하드 코드할 필요가 없도록 합니다. Azure AD는 앱에서 읽어야 하는 서비스 주체를 자동으로 만들고 관리되는 ID를 사용할 때 비밀을 수정합니다.

Azure 리소스 및 MFA에 대한 관리되는 ID를 통해 적이 권한을 얻고 시스템에서 권한을 에스컬레이션하기가 더 어려워집니다. 이 위협은 위협 모델에서 지적되었습니다.
응용 프로그램은 OAuth를 사용하여 OAuth 응용 프로그램에 등록 된 사용자가 앱에 로그인 할 수 있습니다.

### <a name="storage"></a>스토리지

PostgreSQL 데이터베이스의 데이터는 PostgreSQL용 Azure 데이터베이스에 의해 미사용 으로 자동으로 암호화됩니다. 데이터베이스는 배포된 App Service 웹 앱만 올바른 인증 자격 증명으로 데이터베이스 리소스에 액세스할 수 있도록 앱 서비스 IP 주소를 승인합니다.

### <a name="logging-and-auditing"></a>로깅 및 감사

앱은 응용 프로그램 인사이트를 사용하여 발생하는 메트릭, 로그 및 예외를 추적하여 로깅을 구현합니다. 이 로깅은 개발자 및 운영 팀 구성원에게 앱 상태를 알리기에 충분한 앱 메타데이터를 제공합니다. 또한 보안 인시던트의 경우 역추적할 수 있는 충분한 데이터를 제공합니다.

## <a name="cost-considerations"></a>비용 고려 사항

Azure 계정이 아직 없는 경우 무료 계정을 만들 수 있습니다. [무료 계정 페이지로](https://azure.microsoft.com/free/) 이동하여 시작하고, 무료 Azure 계정으로 수행할 수 있는 작업을 확인하고, 12개월 동안 어떤 제품이 무료인지 알아보세요.

보안 기능을 사용하여 샘플 앱에 리소스를 배포하려면 일부 프리미엄 기능에 대한 비용을 지불해야 합니다. 응용 프로그램 확장 및 Azure에서 제공하는 프리 계층 및 평가판을 응용 프로그램 요구 사항을 충족하도록 업그레이드해야 하므로 비용이 증가할 수 있습니다. Azure [가격 계산기를](https://azure.microsoft.com/pricing/calculator/) 사용하여 비용을 예측합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

### <a name="prerequisites"></a>사전 요구 사항

응용 프로그램을 실행하려면 다음 도구를 설치해야 합니다.

- 응용 프로그램 코드를 수정하고 볼 수 있는 코드 편집기입니다. [비주얼 스튜디오 코드는](https://code.visualstudio.com/) 오픈 소스 옵션입니다.
- 개발 컴퓨터에서 [Azure CLI를](/cli/azure/install-azure-cli) 제공합니다.
- 시스템에 [Git.](https://git-scm.com/) Git은 소스 코드를 로컬로 복제하는 데 사용됩니다.
- [jq](https://stedolan.github.io/jq/), 사용자 친화적 인 방법으로 JSON을 쿼리하기위한 UNIX 도구입니다.

샘플 앱의 리소스를 배포하려면 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [무료 계정을 만들어](https://azure.microsoft.com/free/) 샘플 앱을 테스트할 수 있습니다.

이러한 도구를 설치한 후 Azure에서 앱을 배포할 준비가 된 것입니다.

### <a name="environment-setup"></a>환경 설정

배포 스크립트를 실행하여 환경 및 구독을 설정합니다.

1. 소스 코드 리포지토리를 복제하려면 다음 Git 명령을 사용합니다.

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. 디렉터리로 이동하려면 다음 명령을 사용합니다.

   ```shell
   cd tutorial-project/scripts
   ```

3. 스크립트 폴더에 사용 중인 플랫폼(Windows 또는 Linux)과 관련된 파일이 있습니다. Azure CLI가 이미 설치되어 있는 경우 다음 Azure CLI 명령을 실행하여 명령 프롬프트에서 Azure 계정에 로그인합니다.

   ```azurecli-interactive
   az login
   ```

브라우저가 열리고 자격 증명으로 로그인합니다. 로그인한 후 명령 프롬프트에서 리소스 배포를 시작할 수 있습니다.

배포 `deploy-powershell.ps1` 스크립트및 `deploy-bash.sh` 전체 응용 프로그램을 배포하는 코드를 포함합니다.
솔루션을 배포하려면 다음을 수행하십시오.

1. PowerShell에 있는 경우 `deploy-powershell.ps1` 지역 및 `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` 리소스 그룹 이름을 적합한 Azure 지역 및 리소스 그룹에 대한 이름으로 대체하여 파일을 실행합니다.
2. 리눅스에 있는 경우 `deploy-bash.sh` 입력 하 `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`여 파일을 실행 하려면 파일을 입력 하 여 파일을 만들어야 할 수 있습니다.`chmod +x deploy-bash.sh`

다음 예제에서는 주요 구성 요소의 조각을 보여 주며 있습니다. 배포 파일을 실행하여 예제를 개별적으로 또는 나머지 구성 요소와 함께 배포할 수 있습니다.

### <a name="implementation-guidance"></a>구현 지침

배포 스크립트는 네 단계로 나눌 수 있는 하나의 스크립트입니다. 각 단계는 [아키텍처 다이어그램에](#architecture)있는 Azure 리소스를 배포하고 구성합니다.

네 단계는 다음과 같습니다.

- Azure 키 자격 증명 모음을 배포합니다.
- PostgreSQL용 Azure 데이터베이스 배포.
- Linux에 Azure 웹 앱을 배포합니다.
- 웹 응용 프로그램 방화벽을 통해 응용 프로그램 게이트웨이를 배포합니다.

각 단계는 이전에 배포된 리소스의 구성을 사용하여 이전 단계에 기반을 두고 있습니다.

구현 단계를 완료하려면 [필수 구성 조건](#prerequisites)아래에 나열된 도구를 설치했는지 확인합니다.

#### <a name="deploy-azure-key-vault"></a>Azure 키 볼트 배포

이 섹션에서는 비밀 및 인증서를 저장하는 데 사용되는 Azure Key Vault 인스턴스를 만들고 배포합니다.

배포를 완료하면 Azure에 배포된 Azure 키 자격 증명 모음 인스턴스가 있습니다.

Azure CLI를 사용하여 Azure 키 자격 증명 모음을 배포하려면 다음을 수행합니다.

1. Azure 키 자격 증명 모음에 대한 변수를 선언합니다.
2. Azure 키 볼트 공급자를 등록합니다.
3. 인스턴스에 대한 리소스 그룹을 만듭니다.
4. 3단계에서 만든 리소스 그룹에 Azure 키 볼트 인스턴스를 만듭니다.

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

Key Vault를 사용하여 리소스에 액세스하는 앱의 Azure 리소스에 관리되는 ID를 사용하는 것이 좋습니다. Key Vault에 대한 액세스 키가 코드 또는 구성에 저장되지 않을 때 보안 태세가 증가합니다.

#### <a name="deploy-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스 배포

PostgreSQL용 Azure 데이터베이스는 먼저 데이터베이스 서버를 만든 다음 스키마와 데이터를 저장할 데이터베이스를 만듭니다.

배포를 완료한 후에는 PostgreSQL 서버와 데이터베이스가 Azure에서 실행됩니다.

Azure CLI를 사용하여 PostgreSQL용 Azure 데이터베이스를 배포하려면 다음을 수행합니다.

1. Azure CLI 및 Azure 구독 설정으로 터미널을 엽니다.
2. 데이터베이스에 액세스하는 데 사용되는 보안 사용자 이름과 암호 조합을 생성합니다. (이러한 앱은 Azure Key Vault에 저장해야 합니다.)
3. PostgreSQL 서버 인스턴스를 만듭니다.
4. 3단계에서 만든 서버 인스턴스에 데이터베이스를 만듭니다.
5. PostgreSQL 인스턴스에서 PostgreSQL 스크립트를 실행합니다.

아래 코드는 위의 키볼트 배포 단계에서 Azure KeyVault에 저장된 PGUSERNAME 및 PGPASSWORD 암호를 기반으로 합니다.

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

데이터베이스를 배포한 후 Azure Key Vault에 자격 증명 및 연결 문자열을 저장해야 합니다.
스크립트 폴더에는 저장된 함수를 `functions.sql` 실행할 때 만드는 PL/pgSQL 코드가 포함된 파일이 있습니다. 이 파일을 실행하면 SQL 주입을 제한하기 위해 입력을 매개 변수화합니다.

PostgreSQL은 데이터베이스에 연결하는 데 `psql` 사용되는 도구와 함께 번들로 제공됩니다. 실행하려면 `functions.sql`로컬 컴퓨터에서 PostgreSQL 인스턴스에 대한 Azure 데이터베이스에 연결하고 거기에서 실행해야 합니다. psql 도구의 설치는 각 운영 체제에서 PostgreSQL의 기본 설치에 포함되어 있습니다.
자세한 내용은 [psql 설명서를](https://www.postgresql.org/docs/9.3/app-psql.html)참조하십시오.

Azure 클라우드 셸에는 `psql` 도구도 포함되어 있습니다. 클라우드 셸 아이콘을 선택 하여 Azure 포털에서 직접 클라우드 셸을 사용할 수 있습니다.

PostgreSQL 인스턴스에 대한 원격 액세스를 사용하려면 PostgreSQL에서 IP 주소를 승인해야 합니다.
**연결 보안** 탭으로 이동하여 **클라이언트 IP 추가를**선택하고 새 설정을 저장하여 이 액세스를 사용하도록 설정합니다.

![클라이언트 IP 권한 부여](./media/secure-web-app/add-client-ip-postgres.png)

로컬 psql 도구 대신 Cloud Shell을 사용하는 경우 **Azure 서비스에 대한 액세스 허용을** 선택하고 해당 값을 **ON으로** 변경하여 클라우드 셸 액세스를 허용합니다.

그런 다음 Azure 포털의 PostgreSQL 인스턴스의 연결 문자열 탭에서 연결 문자열 매개 변수를 사용하여 아래 psql 명령을 실행하여 인스턴스에 **연결합니다.**
빈 중괄호를 데이터베이스의 연결 문자열 블레이드의 매개 변수로 바꾸고 암호를 Azure Key Vault의 암호로 바꿉습니다.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

데이터베이스에 연결되어 있는지 확인한 후 다음 PL/pgSQL 스크립트를 실행합니다. 스크립트는 데이터베이스에 데이터를 삽입하는 데 사용되는 저장된 함수를 만듭니다.

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

PostgreSQL에 대한 SSL 및 인증 기관(CA) 확인을 설정하는 방법에 대한 자세한 내용은 [PostgreSQL에 대한 Azure 데이터베이스의 SSL 연결 구성을](/azure/postgresql/concepts-ssl-connection-security)참조하십시오.

루트 인증서가 컨테이너에 포함됩니다. 인증서를 얻기 위해 수행된 단계는 다음과 같습니다.

1. 인증서 기관에서 인증서 파일을 [다운로드합니다.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. [컴퓨터에 OpenSSL을 다운로드하여 설치합니다.](/azure/postgresql/concepts-ssl-connection-security)
3. 인증서 파일 디코딩:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

PostgreSQL에 대한 SSL 보안을 구성하는 방법에 대한 자세한 내용은 여기에서 [SSL 연결 보안을 구성합니다.](/azure/postgresql/concepts-ssl-connection-security)

#### <a name="deploy-azure-web-apps-on-linux"></a>Linux에 Azure 웹 앱 배포

Azure는 Python, Ruby, C#및 Java와 같이 널리 사용되는 언어에 대해 미리 빌드된 컨테이너 및 이미지 집합을 제공하므로 Azure 앱 서비스 위에 Linux 서비스를 쉽게 빌드할 수 있습니다. 또한 Azure는 거의 모든 프로그래밍 언어를 Azure App Service 플랫폼에서 실행할 수 있는 사용자 지정 컨테이너도 지원합니다.

배포 되는 응용 프로그램은 최신 우분투 리눅스 배포판에서 실행 되는 간단한 파이썬 애플 리 케이 션. 자격 증명 관리 및 데이터 저장소를 위해 이전 섹션에서 만든 Azure 키 볼트 및 PostgreSQL 인스턴스에 각각 연결합니다.

다음 Docker 파일은 앱의 루트 폴더에 제공됩니다.

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

위의 Dockerfile은 에서 Azure 컨테이너 레지스트리에서 `mcr.microsoft.com/samples/basic-linux-app`호스팅되는 컨테이너를 빌드하는 데 사용됩니다.

아래 코드는 다음과 같습니다.

1. 앱 서비스 인스턴스의 변수 및 이름을 선언합니다.
2. 앱 서비스 계획에 대한 리소스 그룹을 만듭니다.
3. Linux 컨테이너 인스턴스에 Azure 웹 앱을 프로비전합니다.
4. 웹 앱 컨테이너에 대한 로깅을 활성화합니다.
5. 컨테이너의 앱 설정에서 일부 앱 구성을 설정합니다.

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

이 스크립트는 MSI와 함께 코드 또는 구성에서 하드 코딩 암호 없이 Azure Key Vault와 상호 작용하는 데 사용할 수 있는 앱 서비스 인스턴스에 대해 할당된 ID를 만듭니다.

포털의 Azure Key Vault 인스턴스로 이동하여 액세스 정책 탭에서 할당된 ID를 **승인합니다.** **선택 보안 주체에서**만든 앱 서비스 인스턴스의 이름과 유사한 응용 프로그램 이름을 검색합니다.
응용 프로그램에 연결된 서비스 주체가 표시되어야 합니다. 다음 스크린샷과 같이 액세스 정책 페이지를 선택하고 저장합니다.

응용 프로그램은 키만 검색하기만 하면 되므로 비밀 옵션에서 권한 **받기를** 선택하여 권한을 부여받은 권한을 줄이면서 액세스를 허용합니다.

![Key Vault 액세스 정책](./media/secure-web-app/kv-access-policy.png)

*키 볼트 액세스 정책 만들기*

액세스 정책을 저장한 다음 **액세스 정책** 탭에 새 변경 된 변경 을 저장 하여 정책을 업데이트합니다.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>웹 응용 프로그램 방화벽을 사용하도록 설정한 응용 프로그램 게이트웨이 배포

웹 앱에서는 인터넷에서 외부 세계에 직접 서비스를 노출하지 않는 것이 좋습니다.
로드 밸런싱 및 방화벽 규칙은 들어오는 트래픽을 보다 세밀하고 제어하고 관리하는 데 도움을 줍니다.

응용 프로그램 게이트웨이 인스턴스를 배포하려면 다음을 수행합니다.

1. 응용 프로그램 게이트웨이를 수용할 리소스 그룹을 만듭니다.
2. 게이트웨이에 연결할 가상 네트워크를 프로비전합니다.
3. 가상 네트워크에서 게이트웨이용 서브넷을 만듭니다.
4. 공용 IP 주소를 프로비전합니다.
5. 응용 프로그램 게이트웨이를 프로비전합니다.
6. 게이트웨이에서 웹 응용 프로그램 방화벽을 활성화합니다.

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

위의 스크립트:

1. Azure에서 새 자체 서명된 인증서를 만듭니다.
2. 자체 서명된 인증서를 base64 인코딩된 파일로 다운로드합니다.
3. 자체 서명된 인증서에 대한 암호를 생성합니다.
4. 인증서를 암호로 서명된 PFX 파일로 내보전합니다.
5. 인증서의 암호를 Azure 키 자격 증명 모음에 저장합니다.

이 섹션에서는 응용 프로그램 게이트웨이를 배포합니다.

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

배포를 완료한 후에는 웹 응용 프로그램 방화벽이 활성화된 응용 프로그램 게이트웨이가 있습니다.

게이트웨이 인스턴스는 HTTPS에 대한 포트 443을 노출합니다. 이 구성을 통해 HTTPS를 통해 포트 443에서만 앱에 액세스할 수 있습니다.

사용하지 않는 포트를 차단하고 공격 노출을 제한하는 것이 보안 모범 사례입니다.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>앱 서비스 인스턴스에 네트워크 보안 그룹 추가

앱 서비스 인스턴스를 가상 네트워크와 통합할 수 있습니다. 이 통합을 통해 앱의 수신 및 나가는 트래픽을 관리하는 네트워크 보안 그룹 정책으로 구성할 수 있습니다.

1. 이 기능을 사용하려면 Azure 앱 서비스 인스턴스 블레이드에서 **설정에서** **네트워킹을**선택합니다. 오른쪽 창에서 **VNet 통합**에서 **을 클릭하여 구성하려면 여기를**클릭하십시오.

   ![새로운 가상 네트워크 통합](./media/secure-web-app/app-vnet-menu.png)

    *앱 서비스를 위한 새로운 가상 네트워크 통합*

1. 다음 페이지에서 **VNET 추가(미리 보기)를**선택합니다.

1. 다음 메뉴에서 `hello-vnet`로 시작하는 배포에서 만든 가상 네트워크를 선택합니다. 새 서브넷을 만들거나 기존 서브넷을 선택할 수 있습니다.
   이 경우 새 서브넷을 만듭니다. 주소 **범위를** **10.0.3.0/24로** 설정하고 서브넷 **앱 서브넷의**이름을 지정합니다.

   ![앱 서비스 가상 네트워크 구성](./media/secure-web-app/app-vnet-config.png)

    *앱 서비스를 위한 가상 네트워크 구성*

이제 가상 네트워크 통합을 사용하도록 설정되었으므로 앱에 네트워크 보안 그룹을 추가할 수 있습니다.

1. 검색 상자를 사용하여 **네트워크 보안 그룹을**검색합니다. 결과에서 **네트워크 보안 그룹을** 선택합니다.

    ![네트워크 보안 그룹 검색](./media/secure-web-app/nsg-search-menu.png)

    *네트워크 보안 그룹 검색*

2. 다음 메뉴에서 **추가를**선택합니다. NSG의 **이름과** NSG가 있는 **리소스 그룹을** 입력합니다. 이 NSG는 응용 프로그램 게이트웨이의 서브넷에 적용됩니다.

    ![NSG 만들기](./media/secure-web-app/nsg-create-new.png)

    *NSG 만들기*

3. NSG를 만든 후 선택합니다. 블레이드에서 **설정에서** **인바운드 보안 규칙을**선택합니다. 포트 443을 통해 응용 프로그램 게이트웨이로 들어오는 연결을 허용하도록 이러한 설정을 구성합니다.

   ![NSG 구성](./media/secure-web-app/nsg-gateway-config.png)

   *NSG 구성*

4. 게이트웨이 NSG에 대한 아웃바운드 규칙에서 서비스 태그를 `AppService`대상으로 하는 규칙을 만들어 앱 서비스 인스턴스에 아웃바운드 연결을 허용하는 규칙을 추가합니다.

   ![NSG에 대한 아웃바운드 규칙 추가](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *NSG에 대한 아웃바운드 규칙 추가*

    게이트웨이가 아웃바운드 규칙을 가상 네트워크에 보낼 수 있도록 다른 아웃바운드 규칙을 추가합니다.

   ![다른 아웃바운드 규칙 추가](./media/secure-web-app/nsg-outbound-vnet.png)

    *다른 아웃바운드 규칙 추가*

5. NSG의 서브넷 블레이드에서 연결 **,** 배포에서 만든 가상 네트워크를 선택하고 **gw-서브넷이라는**게이트웨이 서브넷을 선택합니다. NSG는 서브넷에 적용됩니다.

6. 앱 서비스 인스턴스에 대해 이전 단계와 마찬가지로 이번에는 다른 NSG를 만듭니다. 이름을 지정합니다. 응용 프로그램 게이트웨이 NSG에 대해했던 것처럼 포트 443에 대한 인바운드 규칙을 추가합니다.

   이 앱의 경우와 는 달리 앱 서비스 환경 인스턴스에 배포된 앱 서비스 인스턴스가 있는 경우 앱 서비스 NSG의 인바운드 보안 그룹에 포트 454-455를 열어 Azure Service 상태 프로브를 허용하는 인바운드 규칙을 추가할 수 있습니다. 구성은 다음과 같습니다.

   ![Azure 서비스 상태 프로브에 대한 규칙 추가](./media/secure-web-app/nsg-create-healthprobes.png)

    *Azure 서비스 상태 프로브에 대한 규칙 추가(앱 서비스 환경만)*

7. 아웃바운드 보안 규칙에서 앱 서비스 인스턴스가 PostgreSQL 데이터베이스와 통신할 수 있도록 하는 새 아웃바운드 보안 규칙을 만듭니다. 다음과 같이 구성하십시오.

   ![아웃바운드 PostgreSQL 연결을 허용하는 규칙](./media/secure-web-app/nsg-outbound-postgresql.png)

   *아웃바운드 PostgreSQL 연결을 허용하는 규칙 추가*

공격 표면을 제한하려면 응용 프로그램 게이트웨이만 응용 프로그램에 액세스할 수 있도록 App Service 네트워크 설정을 수정합니다.
앱 서비스 네트워크 탭으로 이동하여 IP **제한** 탭을 선택하고 응용 프로그램 게이트웨이의 IP만 서비스에 직접 액세스할 수 있도록 허용하는 규칙을 만듭니다.

개요 페이지에서 게이트웨이의 IP 주소를 검색할 수 있습니다. IP **주소 CIDR** 탭에서 이 형식으로 IP `<GATEWAY_IP_ADDRESS>/32`주소를 입력합니다.

![게이트웨이만 허용](./media/secure-web-app/app-allow-gw-only.png)

*게이트웨이 IP만 앱 서비스에 액세스할 수 있도록 허용*

#### <a name="implement-azure-active-directory-oauth"></a>Azure Active 디렉터리 OAuth 구현

샘플 웹 앱 페이지에 배포된 Azure 문서는 보호가 필요할 수 있는 앱의 리소스입니다. Azure Active Directory(Azure AD)를 사용하여 다른 인증 흐름을 사용하여 웹, 데스크톱 및 모바일 앱에 대한 인증을 구현할 수 있습니다.
이 앱은 **Microsoft와 로그인을**사용하여 앱이 단일 테넌트 Azure AD 사용자 목록에 추가된 사용자의 프로필을 읽을 수 있도록 합니다.

Azure 포털에서 필요한 자격 증명을 사용하도록 앱을 구성합니다.

1. **Azure Active Directory를**선택하거나 검색 상자를 사용하여 검색합니다.

2. **새 등록**선택 :

   ![등록 만들기](./media/secure-web-app/ad-auth-create.png)

   *Azure AD 앱 등록 만들기*

3. 다음 페이지에서 앱 이름을 입력합니다. **지원되는 계정 유형에서** **이 조직 디렉터리에서 만 계정을**선택합니다.
    **URI 리디렉션에서**앱이 실행될 기본 도메인과 토큰 끝점을 가진 도메인을 입력합니다. 예: *GATEWAY_HASH*.cloudapp.net/token.

   ![Azure AD 앱 등록 구성](./media/secure-web-app/ad-auth-type.png)

   *Azure AD 앱 등록 구성*

4. 등록된 앱과 해당 정보를 표시하는 화면이 표시됩니다. 이 정보를 Azure 키 자격 증명 모음 인스턴스에 추가해야 합니다.
   1. 응용 프로그램(클라이언트) ID를 복사하여 키 `CLIENTID`볼트에 저장합니다.
   2. 이전 단계에서 입력한 리디렉션 URI를 복사하여 `REDIRECTURI`로 저장합니다.
   3. 형식 *이름이*있는 Azure AD 기본 디렉터리 이름을 복사합니다.microsoftonline.com 키 `TENANT`볼트에 저장합니다.
   4. 이전에 만든 Azure AD 앱의 **인증서 & 비밀** 탭으로 이동하여 다음 스크린샷과 같이 새 클라이언트 **비밀을**선택합니다. 만료 날짜를 설정한 다음 생성된 값을 복사하여 키 볼트에 `CLIENTSECRET`로 저장합니다.

      ![Azure AD 권한 부여 비밀](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD 권한 부여 비밀*

   5. 명령줄/온라인 도구를 사용하여 안전한 임의의 비밀 키를 생성합니다. 키 볼트에 저장합니다. `FLASKSECRETKEY` 응용 프로그램 프레임워크는 이 키를 사용하여 세션을 만듭니다.
        비밀 키를 생성하는 방법에 대한 자세한 내용은 [플라스크 세션을](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)참조하십시오.

5. 로그인을 구성한 후에는 사용자가 리소스에 로그인할 수 있도록 Azure AD 링크에 사용자를 추가해야 합니다. 이를 추가하려면 Azure AD의 **사용자** 탭으로 이동하여 **모든 사용자를**선택한 다음 **새 사용자** 또는 새 **게스트 사용자를**선택합니다. 테스트를 위해 게스트 사용자를 추가하고 사용자를 디렉터리로 초대할 수 있습니다. 또는 앱이 실행 중인 도메인의 유효성이 검사된 경우 새 사용자를 추가할 수 있습니다. 이 예제에서는 Azure AD 테넌트에 등록된 사용자만 액세스에 등록할 수 있습니다. 다중 테넌트 로그인 액세스에 대한 자세한 내용은 설명서를 참조하십시오.

   ![사용자를 기본 도메인에 추가](./media/secure-web-app/ad-auth-add-user.png)

   *기본 Azure Active Directory 도메인에 사용자 추가*

키 볼트에 Azure AD 구성 및 비밀을 추가한 후 Azure OAuth 인증을 사용하여 사용자를 앱에 인증할 수 있습니다.
앱 코드에서 이 문제는 ADAL(Azure Active Directory 인증 라이브러리)에서 처리합니다.

비밀이 Key Vault에 있고 응용 프로그램이 암호 및 데이터베이스에 액세스할 수 있게 되면 게이트웨이의 응용https://GATEWAY_HASH.cloudapp.net)프로그램 URL(블레이드에서 얻을 수 있는 응용 프로그램 URL)을 통해 응용 프로그램 서비스에 액세스할 수 있습니다.

Azure AD에 로그인할 때 "사용자가 로그인하려는 디렉터리에 등록되지 않았습니다"라는 오류가 발생하면 사용자를 추가해야 합니다. 사용자를 추가하려면 Azure AD의 **사용자** 탭으로 이동하여 세부 정보를 입력하여 사용자를 수동으로 추가하거나 **게스트 초대** 블레이드에서 Azure AD에 게스트 사용자로 전자 메일 주소를 입력하여 사용자를 초대합니다.

#### <a name="deploy-application-insights"></a>Application Insights 배포
이제 앱이 배포되고 작동되었으므로 로깅 및 추적 데이터 수집과 함께 앱 내에서 발생하는 오류를 처리해야 합니다.
로깅 및 추적 데이터 수집은 앱에서 발생하는 감사 이벤트에 대한 보기를 제공합니다.

응용 프로그램 인사이트는 사용자 또는 시스템에서 생성할 수 있는 로그를 수집하는 서비스입니다.

응용 프로그램 인사이트 인스턴스를 만들려면 다음을 수행합니다.

1. Azure 포털의 검색 상자를 사용하여 **응용 프로그램 정보를** 검색합니다.
2. **Application Insights**를 선택합니다. 인스턴스를 만들려면 여기에 표시된 세부 정보를 제공합니다.

   ![애플리케이션 인사이트 인스턴스 만들기](./media/secure-web-app/app-insights-data.png)

배포가 완료되면 응용 프로그램 인사이트 인스턴스가 있습니다.

Application Insights 인스턴스를 만든 후 클라우드로 로그를 보낼 수 있는 계측 키를 앱에 알려야 합니다. 응용 프로그램 인사이트 키를 검색 하 고 Azure 응용 프로그램 통찰력에 대 한 제공 하는 응용 프로그램 라이브러리 내에서 사용 하 여이 작업을 수행 합니다. 가장 좋은 방법은 키와 비밀을 Azure Key Vault에 저장하여 보안을 유지하는 것입니다.

기본 샘플 앱의 경우 Application Insights 인스턴스를 만든 후 클라우드로 로그를 보낼 수 있는 계측 키를 앱에 알려야 합니다.
키 볼트에서 `APPINSIGHTSKEY` 비밀을 설정하고 계측 키로 값을 설정합니다. 이렇게 하면 앱에서 응용 프로그램 인사이트로 로그 및 메트릭을 보낼 수 있습니다.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Azure Active 디렉터리용 다단계 인증 구현

관리자는 포털의 구독 계정을 보호해야 합니다. 구독은 생성한 리소스를 관리하기 때문에 공격에 취약합니다. 구독을 보호하려면 구독의 **Azure Active Directory** 탭에서 다단계 인증을 사용하도록 설정합니다.

Azure AD는 특정 기준에 맞는 사용자 또는 사용자 그룹에 적용되는 정책을 기반으로 작동합니다.
Azure는 관리자가 포털에 로그인하기 위해 2단계 인증이 필요하도록 지정하는 기본 정책을 만듭니다.
이 정책을 사용하도록 설정하면 Azure 포털에 로그아웃하고 다시 로그인하라는 메시지가 표시될 수 있습니다.

관리자 로그인에 대해 MFA를 사용하려면 다음단계를 수행합니다.

1. Azure 포털의 **Azure Active 디렉터리** 탭으로 이동
2. 보안 범주에서 조건부 액세스를 선택합니다. 다음 화면이 표시됩니다.

   ![조건부 액세스 - 정책](./media/secure-web-app/ad-mfa-conditional-add.png)

새 정책을 만들 수 없는 경우:

1. **MFA** 탭으로 이동합니다.
2. Azure AD **프리미엄 무료 평가판** 링크를 선택하여 무료 평가판에 가입합니다.

   ![Azure AD 프리미엄 무료 평가판](./media/secure-web-app/ad-trial-premium.png)

조건부 액세스 화면으로 돌아갑니다.

1. 새 정책 탭을 선택합니다.
2. 정책 이름을 입력합니다.
3. MFA를 사용하도록 설정할 사용자 또는 그룹을 선택합니다.
4. **액세스 컨트롤에서** **권한 부여** 탭을 선택한 다음 **다단계 인증 필요(원하는** 경우 다른 설정)를 선택합니다.

   ![MFA 요구](./media/secure-web-app/ad-mfa-conditional-add.png)

화면 상단의 확인란을 선택하거나 **조건부 액세스** 탭에서 정책을 활성화할 수 있습니다. 정책을 사용하도록 설정하면 사용자는 MFA가 포털에 로그인해야 합니다.

모든 Azure 관리자에 대해 MFA가 필요한 기준 정책이 있습니다. 포털에서 즉시 활성화할 수 있습니다. 이 정책을 사용하도록 설정하면 현재 세션이 무효화되고 다시 로그인해야 할 수 있습니다.

기준 정책이 활성화되지 않은 경우:

1. **관리자의 경우 MFA 필요를 선택합니다.**
2. **정책 사용을 즉시**선택합니다.

   ![즉시 정책 사용 선택](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel을 사용하여 앱 및 리소스 모니터링

응용 프로그램이 증가함에 따라 리소스에서 받은 모든 보안 신호와 메트릭을 집계하여 작업 지향적인 방식으로 유용하게 만들기가 어려워집니다.

Azure Sentinel은 데이터를 수집하고 가능한 위협 유형을 검색하며 보안 인시던트에 대한 가시성을 제공하도록 설계되었습니다.
수동 개입을 기다리는 동안 Azure Sentinel은 미리 작성된 플레이북에 의존하여 경고 및 인시던트 관리 프로세스를 시작할 수 있습니다.

샘플 앱은 Azure Sentinel이 모니터링할 수 있는 여러 리소스로 구성됩니다.
Azure Sentinel을 설정하려면 먼저 다양한 리소스에서 수집된 모든 데이터를 저장하는 Log Analytics 작업 영역을 만들어야 합니다.

이 작업 영역을 만들려면 다음을 수행합니다.

1. Azure 포털의 검색 상자에서 **로그 분석을**검색합니다. **Log Analytics 작업 영역**을 선택합니다.

   ![로그 분석 작업 영역 검색](./media/secure-web-app/sentinel-log-analytics.png)

    *로그 분석 작업 영역 검색*

2. 다음 페이지에서 **추가를** 선택한 다음 작업 공간에 대한 이름, 리소스 그룹 및 위치를 제공합니다.
   ![Log Analytics 작업 영역 만들기](./media/secure-web-app/sentinel-log-analytics-create.png)

   *로그 분석 작업 영역 만들기*

3. 검색 상자를 사용하여 **Azure Sentinel**을 검색합니다.

   ![Azure Sentinel 검색](./media/secure-web-app/sentinel-add.png)

    *Azure 센티넬 검색*

4. **추가를** 선택한 다음 이전에 만든 로그 분석 작업 영역을 선택합니다.

   ![로그 분석 작업 영역 추가](./media/secure-web-app/sentinel-workspace-add.png)

    *로그 분석 작업 영역 추가*

5. Azure **Sentinel - 데이터 커넥터** 페이지에서 **구성**에서 **데이터 커넥터를 선택합니다.** Azure Sentinel에서 분석을 위해 로그 애널리틱스 저장소 인스턴스에 연결할 수 있는 Azure 서비스 배열이 표시됩니다.

   ![로그 분석 데이터 커넥터](./media/secure-web-app/sentinel-connectors.png)

    *Azure Sentinel에 데이터 커넥터 추가*

   예를 들어 응용 프로그램 게이트웨이를 연결하려면 다음 단계를 수행합니다.

   1. Azure 응용 프로그램 게이트웨이 인스턴스 블레이드를 엽니다.
   2. **모니터링** 아래에서 **진단 설정**을 선택합니다.
   3. **진단 설정 추가를**선택합니다.

      ![응용 프로그램 게이트웨이 진단 추가](./media/secure-web-app/sentinel-gateway-connector.png)

      *응용 프로그램 게이트웨이 진단 추가*

   4. 진단 **설정** 페이지에서 사용자가 만든 로그 분석 작업 영역을 선택한 다음 수집하여 Azure Sentinel으로 보낼 모든 메트릭을 선택합니다. **저장**을 선택합니다.

        ![Azure 센티넬 커넥터 설정](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure 센티넬 커넥터 설정*

  리소스의 메트릭은 Azure Sentinel에 있으며, 이 메트릭을 쿼리하고 조사할 수 있습니다.

   Azure Key Vault의 진단 설정, 공용 IP 주소, PostgreSQL용 Azure 데이터베이스 및 계정의 진단 로그를 지원하는 모든 서비스에 동일한 메트릭을 추가합니다.

메트릭을 설정한 후 Azure Sentinel에는 분석할 데이터가 있습니다.

## <a name="evaluate-and-verify"></a>평가 및 확인

아키텍처를 개발하고 배포한 후에는 코드와 배포된 서비스가 보안 표준을 충족하는지 확인해야 합니다. 다음은 소프트웨어를 확인하기 위해 수행할 수 있는 몇 가지 단계입니다.

- 정적 코드 분석
- 취약성 검색
- 응용 프로그램 종속성의 취약점 찾기 및 수정

이러한 요소는 보안 개발의 모범 사례를 위한 기본 구성 요소입니다.

### <a name="static-code-analysis"></a>정적 코드 분석

샘플 앱의 경우 정적 분석 도구를 사용하여 확인하려면 오염 검사 및 데이터 흐름 분석과 같은 기술을 사용하여 앱 코드에서 취약점을 찾는 작업이 포함됩니다. 파이썬 정적 분석 도구는 앱이 안전하다는 확신을 줍니다.

**Linting**

파이썬 리딩 라이브러리인 PyFlakes는 다음과 같이 앱에서 죽은 코드와 사용되지 않는 함수를 제거하는 데 도움이 됩니다.

![파이플레이크](./media/secure-web-app/pyflakes.png)

Linting은 런닝 중에 코드를 더 깨끗하게 하고 오류가 발생하기 쉬운 힌트와 가능한 변경 사항을 제공합니다.

**PyLint**

PyLint는 이 프로젝트에 가장 큰 가치를 제공했습니다. 서버에서 실행되는 코드가 안전한지 확인하기 위해 코드 표준 검사, 오류 검사 및 리팩터링 팁을 수행합니다. PyLint를 사용하여 코드를 업데이트하면 다음 이미지와 같이 버그를 제거하고 PyLint 등급을 향상시킬 수 있습니다.

![필린트 이전](./media/secure-web-app/before-pylint.png)

*필린트 이전*

linting 도구에서 찾은 코드 오류 중 일부를 수정한 후에는 코드가 오류가 발생하기 쉽지 않다는 확신을 갖게 됩니다. 오류를 수정하면 코드가 프로덕션 환경에 배포될 때 발생할 수 있는 보안 위험이 크게 줄어듭니다.

![피를린트 이후](./media/secure-web-app/after-pylint.png)

*피린트 이후*

### <a name="vulnerability-scanning"></a>취약성 검색

[OWASP의 ZAP](https://www.zaproxy.org/) 도구는 샘플 앱에서 취약점을 확인하는 데 사용할 수 있는 오픈 소스 웹 응용 프로그램 취약성 스캐너입니다. 샘플 앱에서 도구를 실행하면 몇 가지 가능한 오류 및 공격 벡터가 표시됩니다.

![ZAP 도구](./media/secure-web-app/zap-tool.png)

*ZAP 도구*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>앱 종속성의 취약점 찾기 및 수정

응용 프로그램 종속성을 찾아 수정하려면 [OWASP의 종속성 검사를](https://www.owasp.org/index.php/OWASP_Dependency_Check)사용할 수 있습니다.

안전은 종속성을 확인하는 유사한 응용 프로그램입니다. [GitHub](https://github.com/pyupio/safety)에서 찾을 수 있습니다. 잘 알려진 취약점 데이터베이스에서 발견된 취약점에 대한 안전 검사

![안전](./media/secure-web-app/pysafety.png)

*안전*

## <a name="next-steps"></a>다음 단계

다음 문서에서는 보안 응용 프로그램을 설계, 개발 및 배포하는 데 도움이 될 수 있습니다.

- [디자인](secure-design.md)
- [개발](secure-develop.md)
- [배포](secure-deploy.md)
