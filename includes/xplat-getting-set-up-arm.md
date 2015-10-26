<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## ARM(Azure 리소스 관리자)에서 Azure CLI 사용

리소스 관리자 명령 및 템플릿에서 Azure CLI를 사용하여 리소스 그룹을 통해 Azure 리소스 및 워크로드를 배포하려면 먼저 Azure 계정이 필요합니다. 계정이 없는 경우 [여기에서 무료 Azure 평가판](http://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.

> [AZURE.NOTE]Azure 계정이 아직 없지만 MSDN 구독이 있는 경우 [여기에서 MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하여 무료 Azure 크레딧을 받거나 무료 계정을 사용할 수 있습니다. 둘 중 하나가 Azure 액세스에 작동합니다.

### 1단계: Azure CLI 버전 확인

명령 및 ARM 템플릿에 Azure CLI를 사용하려면 0.8.17 이상 버전이 있어야 합니다. 사용 중인 버전을 확인하려면 `azure --version`을 입력합니다. 다음과 유사한 결과가 표시됩니다.

    $ azure --version
    0.8.17 (node: 0.10.25)

Azure CLI의 버전을 업데이트해야 하는 경우 [Azure CLI](https://github.com/Azure/azure-xplat-cli)를 참조하세요.

### 2단계: Azure에서 회사 또는 학교 ID를 사용 중인지 확인

[Azure Active Directory 테넌트](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) 또는 [서비스 사용자 이름](https://msdn.microsoft.com/library/azure/dn132633.aspx)을 사용하는 경우에만 ARM 명령 모드를 사용할 수 있습니다. 이를 *조직 ID*라고도 합니다.

있는지 확인하려면 `azure login`을 입력하고 메시지가 표시되면 회사 또는 학교 사용자 이름 및 암호를 사용하여 로그인합니다. 있으면 다음과 유사한 결과가 표시됩니다.

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

이러한 결과가 표시되지 않으면 Microsoft 계정 ID를 사용하여 새 테넌트(또는 서비스 사용자)를 만들어야 합니다. 이는 개인 MSDN 구독 또는 무료 평가판 구독에서 일반적인 경우입니다. Microsoft ID를 사용하여 만든 Azure 계정에서 회사 또는 학교 ID를 만들려면 [새 Azure 구독에 Azure AD Directory 연결](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)을 참조하세요. 조직 ID가 이미 있다고 생각되는 경우 계정을 만든 사람에게 문의해야 할 수 있습니다.

### 3단계: Azure 구독 선택

Azure 계정에 구독이 하나뿐인 경우 Azure CLI 자체가 기본적으로 해당 구독에 연결됩니다. 구독이 두 개 이상인 경우에는 `azure account set <subscription id or name> true`(여기서 _subscription id or name_은 현재 세션에서 사용할 구독 ID 또는 구독 DLFMA)를 입력하여 사용할 구독을 선택해야 합니다.

다음과 유사한 출력이 표시됩니다.

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### 4단계: ARM 모드에서 Azure CLI 배치

ARM(Azure 리소스 관리) 모드에서 Azure CLI를 사용하려면 `azure config mode arm`을(를) 입력합니다. 다음과 유사한 출력이 표시됩니다.

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE]`azure config mode asm`을 입력하여 Azure 서비스 관리 명령을 사용하도록 다시 전환할 수 있습니다.

<!---HONumber=Oct15_HO3-->