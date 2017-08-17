---
title: "Azure에서 VM 및 기타 인프라를 프로비전하기 위해 Terraform 설치 및 구성 | Microsoft Docs"
description: "Terraform을 설치하고 구성하여 Azure 리소스를 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: 8727e15448a74b68277c5bdd82c573e817254f80
ms.openlocfilehash: da567097be38ac649c6bf1de1508de24d21cb877
ms.contentlocale: ko-kr
ms.lasthandoff: 08/04/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Azure에 VM 및 기타 인프라를 프로비전하기 위해 Terraform 설치 및 구성 
이 문서에서는 가상 컴퓨터와 같은 리소스를 Azure에 프로비전하기 위해 Terraform를 설치 및 구성하는 필수 단계를 설명합니다. Azure 자격 증명을 만들고 사용하여 Terraform에서 클라우드 리소스를 안전하게 프로비전하도록 하는 방법에 대해 알아봅니다.

HashiCorp Terraform은 HCL(HashiCorp configuration language)이라는 사용자 지정 템플릿 언어를 사용하여 클라우드 인프라를 정의 및 배포하는 쉬운 방법을 제공합니다. 이 사용자 지정 언어는 [작성하고 이해하기 쉽습니다](terraform-create-complete-vm.md). 또한 `terraform plan` 명령을 사용하여 커밋하기 전에 인프라에 대한 변경 내용을 시각화할 수 있습니다. 다음 단계를 수행하여 Azure에서 Terraform을 사용하기 시작합니다.

## <a name="install-terraform"></a>Terraform 설치
Terraform를 설치하려면 운영 체제에 적합한 패키지를 별도의 설치 디렉터리에 [다운로드](https://www.terraform.io/downloads.html)합니다. 다운로드에는 전역 경로를 정의해야 하는 단일 실행 파일이 포함됩니다. Linux 및 Mac에서 경로를 설정하는 방법에 대한 지침은 [이 웹 페이지](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)로 이동합니다. Windows에서 경로를 설정하는 방법에 대한 지침은 [이 웹 페이지](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)로 이동합니다. 설치를 확인하려면 `terraform` 명령을 실행합니다. 출력으로 사용할 수 있는 Terraform 옵션 목록이 표시되어야 합니다.

다음으로, 인프라 프로비전을 수행하기 위해 Azure 구독에 대한 Terraform 액세스를 허용해야 합니다.

## <a name="set-up-terraform-access-to-azure"></a>Azure에 대한 Terraform 액세스 설정
Terraform에서 Azure에 리소스를 프로비전하도록 하려면 Azure AD(Azure Active Directory)에서 Azure AD 응용 프로그램 및 Azure AD 서비스 주체라는 두 개의 엔터티를 만들어야 합니다. 그런 다음 Terraform 스크립트에서 이러한 엔터티의 식별자를 사용합니다. 서비스 주체는 글로벌 Azure AD 응용 프로그램의 로컬 인스턴스입니다. 서비스 주체를 사용하면 전역 리소스에 대한 세분화된 로컬 액세스 제어가 가능합니다.

여러 가지 방법으로 Azure AD 응용 프로그램 및 Azure AD 서비스 주체를 만들 수 있습니다. 현재 가장 쉽고 빠른 방법은 Azure CLI 2.0을 사용하는 것이며 [Windows, Linux 또는 Mac에 다운로드하여 설치](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)할 수 있습니다. 또한 PowerShell 또는 Azure CLI 1.0을 사용하여 필요한 보안 인프라를 만들 수도 있습니다. 아래 지침은 해당하는 모든 방법을 사용하여 Azure에 대해 Terraform을 구성하는 방법을 보여줍니다.

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>Azure CLI 2.0 사용(Windows, Linux 또는 Mac 사용자의 경우) 
[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)을 다운로드 및 설치한 후에 다음 명령을 실행하여 Azure 구독을 관리하기 위해 로그인합니다.

```
az login
```

>[!NOTE]
>중국, Azure 독일 또는 Azure Government Clouds를 사용하는 경우 해당 클라우드에서 작동하도록 먼저 Azure CLI를 구성해야 합니다. 다음을 실행하여 이 작업을 수행할 수 있습니다.

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

여러 Azure 구독이 있는 경우 `az login` 명령을 사용하면 세부 정보가 반환됩니다. 사용하려는 구독에서 반환된 `id` 필드 값을 보유하도록 `SUBSCRIPTION_ID` 환경 변수를 설정합니다. 

이 세션에 사용할 구독을 설정합니다.

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

계정을 쿼리하여 구독 ID 및 테넌트 ID 값을 가져옵니다.

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

다음으로 Terraform에 대한 별도의 자격 증명을 만듭니다.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

appId, 암호, sp_name, 및 테넌트가 반환됩니다. appId 및 암호를 기록해 둡니다.

자격 증명(서비스 주체)을 확인하려면 새 셸을 열고 다음 명령을 실행합니다. sp_name, 암호 및 테넌트에 반환된 값으로 대체합니다.

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>PowerShell 사용(Windows 사용자의 경우) 
Windows 컴퓨터를 사용하여 Terraform 스크립트를 작성 및 실행하고 구성 작업에 PowerShell을 사용하려면 적절한 PowerShell 도구로 컴퓨터를 구성합니다. 

1. [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)의 단계에 따라 PowerShell 도구를 설치합니다. 

2. PowerShell 콘솔에서 [azure-setup.ps1 스크립트](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1)를 다운로드하여 실행합니다.

3. azure-setup.ps1 스크립트를 실행하려면 다운로드하여 PowerShell 콘솔에서 `./azure-setup.ps1 setup` 명령을 실행합니다. 관리자 권한으로 Azure 구독에 로그인합니다.

4. 메시지가 표시되면 응용 프로그램 이름(임의 문자열, 필수)을 제공합니다. 필요에 따라 메시지가 표시되면 강력한 암호를 입력합니다. 암호를 입력하지 않으면 .NET 보안 라이브러리를 사용하여 강력한 암호가 생성됩니다.

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>Azure CLI 1.0 사용(Linux 또는 Mac 사용자의 경우)
Azure CLI 1.0이 설치된 Linux 컴퓨터 또는 Mac에서 Terraform을 시작하려면 컴퓨터에 적절한 라이브러리를 설치합니다.  

1. [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)의 단계에 따라 Azure xPlat CLI 도구를 설치합니다. 

2. [jq 다운로드](https://stedolan.github.io/jq/download/)의 지침에 따라 JSON 프로세서를 다운로드하고 설치합니다.

3. 콘솔에서 [azure-setup.sh 스크립트](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) 일괄 처리를 다운로드하여 실행합니다.

4. azure-setup.sh 스크립트를 실행하려면 다운로드하여 콘솔에서 `./azure-setup setup` 명령을 실행합니다. 관리자 권한으로 Azure 구독에 로그인합니다.
 
5. 메시지가 표시되면 응용 프로그램 이름(임의 문자열, 필수)을 제공합니다. 필요에 따라 메시지가 표시되면 강력한 암호를 입력합니다. 암호를 입력하지 않으면 .NET 보안 라이브러리를 사용하여 강력한 암호가 생성됩니다.

모든 이전 스크립트는 Azure AD 응용 프로그램 및 서비스 주체를 만듭니다. 서비스 주체는 구독에서 참가자 또는 소유자 수준의 액세스를 가져옵니다. 높은 수준의 액세스 권한이 부여되므로 해당 스크립트에서 생성한 보안 정보를 항상 보호해야 합니다. 해당 스크립트에서 제공한 다음 4가지 모든 보안 정보(appId, 암호, subscription_id 및 tenant_id)를 기록해 둡니다.

## <a name="set-environment-variables"></a>환경 변수 설정
Azure AD 서비스 주체를 만들고 구성한 후에 Terraform이 사용할 테넌트 ID, 구독 ID, 클라이언트 ID 및 클라이언트 암호를 알 수 있도록 해야 합니다. [Terraform을 사용하여 기본 인프라 만들기](terraform-create-complete-vm.md)에서 설명한 대로 Terraform 스크립트에 해당 값을 포함하면 가능합니다. 또는 다음 환경 변수를 설정할 수 있습니다(따라서 실수로 체크 인하거나 자격 증명을 공유하지 않도록 방지할 수 있음).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

이 샘플 셸 스크립트를 사용하여 해당 변수를 설정할 수 있습니다.

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

또한 중국, Azure Government 또는 Azure Germany에서 Azure로 Terraform을 사용하는 경우 ENVIRONMENT 변수를 적절하게 설정해야 합니다.

## <a name="next-steps"></a>다음 단계
이제 Terraform을 설치했고 Azure 자격 증명을 구성했으므로 Azure 구독에 인프라를 배포하기 시작할 수 있습니다. 다음으로 [Terraform으로 인프라를 만드는 방법](terraform-create-complete-vm.md)에 대해 알아봅니다.

