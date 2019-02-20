---
title: Azure Key Vault에 대한 액세스 보안 - Azure Key Vault | Microsoft Docs
description: Azure Key Vault, 키 및 비밀에 대한 액세스 권한을 관리합니다. Key Vault의 인증 및 권한 부여 모델과 키 자격 증명 모음을 보호하는 방법을 설명합니다.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 320a23e425ecb11e36af3efe988b25e598948132
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118516"
---
# <a name="secure-access-to-a-key-vault"></a>Key vault에 대한 액세스 보안

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 이 데이터는 민감하고 업무상 중요하기 때문에 권한이 부여된 애플리케이션과 사용자만 허용하여 Key Vault에 대한 액세스를 보호해야 합니다. 이 문서에서는 Key Vault 액세스 모델에 대한 개요를 제공합니다. 여기서는 인증 및 권한 부여에 대해 설명하고 Key Vault 액세스의 보안을 유지하는 방법을 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>액세스 모델 개요

Key Vault에 대한 액세스는 두 인터페이스, 즉 *관리 평면* 및 *데이터 평면*을 통해 제어됩니다. 관리 평면에서는 Key Vault 자체를 관리합니다. 이 평면의 작업에는 Key Vault 만들기와 삭제, Key Vault 속성 검색 및 액세스 정책 업데이트가 포함됩니다. 데이터 평면에서는 Key Vault에 저장된 데이터로 작업을 수행합니다. 키, 비밀 및 인증서를 추가, 삭제 및 수정할 수 있습니다.

두 평면에서 key vault에 액세스하려면 모든 호출자(사용자 또는 애플리케이션)에게 적절한 인증 및 권한이 있어야 합니다. 인증은 호출자의 ID를 설정합니다. 권한은 호출자가 실행할 수 있는 작업을 결정합니다. 

두 평면은 인증을 위해 Azure AD(Azure Active Directory)를 사용합니다. 권한 부여를 위해 관리 평면에서는 RBAC(역할 기반 액세스 제어)를 사용하고, 데이터 평면에서는 Key Vault 액세스 정책을 사용합니다.

## <a name="active-directory-authentication"></a>Active Directory 인증

Azure 구독에 Key Vault을 만들 때 해당 구독의 Azure AD 테넌트에 자동으로 연결됩니다. 두 평면의 모든 호출자가 이 테넌트에 등록해야 하고, 해당 Key Vault에 액세스하기 위해 인증을 받아야 합니다. 두 경우 모두 애플리케이션에서 다음과 같은 두 가지 방법으로 Key Vault에 액세스할 수 있습니다.

- **사용자 및 애플리케이션 액세스**: 로그인한 사용자를 대신하여 애플리케이션에서 Key Vault에 액세스합니다. Azure PowerShell과 Azure Portal이 이러한 액세스 유형의 예제입니다. 사용자 액세스는 두 가지 방법으로 부여됩니다. 사용자는 모든 애플리케이션에서 Key Vault에 액세스할 수 있거나 특정 애플리케이션만 사용해야 합니다(_복합 ID_라고도 함).
- **애플리케이션 전용 액세스**: 애플리케이션은 데몬 서비스 또는 백그라운드 작업으로 실행됩니다. Key Vault에 대한 액세스 권한이 애플리케이션의 ID에 부여됩니다.

두 유형의 액세스에서 애플리케이션은 Azure AD에서 인증을 받습니다. 애플리케이션은 애플리케이션 유형에 따라 [지원되는 인증 방법](../active-directory/develop/authentication-scenarios.md)을 사용합니다. 애플리케이션은 액세스 권한을 부여하기 위해 평면의 리소스에 대해 토큰을 획득합니다. 리소스는 Azure 환경에 따라 관리 또는 데이터 평면의 엔드포인트입니다. 애플리케이션은 해당 토큰을 사용하고 REST API 요청을 Key Vault에 보냅니다. 자세한 내용은 [전체 인증 흐름](../active-directory/develop/v1-protocols-oauth-code.md)을 참조하세요.

두 평면에 대한 인증에 단일 메커니즘을 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

- 조직에서는 조직의 모든 Key Vault에 대한 액세스를 중앙에서 제어할 수 있습니다.
- 사용자가 떠나는 경우 곧바로 조직의 모든 Key Vault에 대한 액세스 권한을 잃게 됩니다.
- 조직은 Azure AD에 있는 옵션(예: 추가 보안을 위해 다단계 인증 사용)을 사용하여 인증을 사용자 지정할 수 있습니다.

## <a name="resource-endpoints"></a>리소스 엔드포인트

애플리케이션은 엔드포인트를 통해 평면에 액세스합니다. 두 평면에 대한 액세스 제어는 독립적으로 작동합니다. 애플리케이션에 Key Vault의 키를 사용하기 위한 액세스 권한을 부여하려면 Key Vault 액세스 정책을 사용하여 데이터 평면 액세스 권한을 부여합니다. 사용자에게 Key Vault 속성 및 태그에 대한 액세스 권한을 부여하려고 하지만 데이터(키, 비밀 또는 인증서)에 대한 액세스 권한은 부여하지 않으려면 RBAC를 사용하여 관리 평면 액세스 권한을 부여합니다.

다음 표에서는 관리 및 데이터 평면에 대한 엔드포인트를 보여 줍니다.

| 액세스&nbsp;평면 | 액세스 엔드포인트 | 작업 | 액세스&nbsp;제어 메커니즘 |
| --- | --- | --- | --- |
| 관리 평면 | **전역:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure 미국 정부:**<br> management.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> management.microsoftazure.de:443 | Key Vault 만들기, 읽기, 업데이트 및 삭제<br><br>Key Vault 액세스 정책 설정<br><br>Key Vault 태그 설정 | Azure Resource Manager RBAC |
| 데이터 평면 | **전역:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure 미국 정부:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 키: 암호 해독, 암호화,<br> 래핑 취소, 래핑, 확인, 서명,<br> 가져오기, 나열, 업데이트, 만들기,<br> 가져오기, 삭제, 백업, 복원<br><br> 비밀: 가져오기, 나열, 설정, 삭제 | Key Vault 액세스 정책 |

## <a name="management-plane-and-rbac"></a>관리 평면 및 RBAC

관리 평면에서 RBAC를 사용하여 호출자가 실행할 수 있는 작업에 대해 권한을 부여합니다. RBAC 모델에서 각 Azure 구독에는 Azure AD의 인스턴스가 있습니다. 이 디렉터리에서 사용자, 그룹 및 애플리케이션에 대해 액세스 권한을 부여합니다. Resource Manager 배포 모델을 사용하는 Azure 구독의 리소스를 관리할 수 있는 액세스 권한을 부여합니다. 이 액세스 권한을 부여하려면 [Azure Portal](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) 또는 [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx)를 사용합니다.

Azure AD를 사용하여 리소스 그룹에 key vault를 만들고 액세스를 관리합니다. 사용자 또는 그룹에 리소스 그룹에서 key vault를 관리하는 기능을 부여합니다. 적절한 RBAC 역할을 할당하여 특정 범위 수준에서 액세스 권한을 부여합니다. key vault를 관리하기 위해 사용자에게 액세스 권한을 부여하려면 특정 범위에 속한 사용자에게 미리 정의된 `key vault Contributor` 역할을 할당합니다. 다음 범위 수준을 RBAC 역할에 할당할 수 있습니다.

- **구독**: 구독 수준에서 할당된 RBAC 역할은 해당 구독 내 모든 리소스 그룹 및 리소스에 적용됩니다.
- **리소스 그룹**: 리소스 그룹 수준에서 할당된 RBAC 역할은 해당 리소스 그룹의 모든 리소스에 적용됩니다.
- **특정 리소스**: 특정 리소스에 할당된 RBAC 역할이 해당 리소스에 적용됩니다. 이 경우 리소스는 특정 Key Vault입니다.

미리 정의된 몇 가지 역할이 있습니다. 미리 정의된 역할이 요구에 맞지 않는 경우 고유한 역할을 정의할 수 있습니다. 자세한 내용은 [RBAC: 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

> [!IMPORTANT]
> 사용자에게 Key Vault 관리 평면에 대한 `Contributor` 사용 권한이 있는 경우 이 사용자는 Key Vault 액세스 정책을 설정하여 스스로 데이터 평면에 대한 액세스 권한을 부여할 수 있습니다. Key Vault에 대한 `Contributor` 역할 액세스 권한이 있는 사용자를 긴밀하게 제어해야 합니다. 권한이 있는 사람만 키 자격 증명 모음, 키, 비밀 및 인증서에 액세스하고 관리할 수 있는지 확인합니다.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>데이터 평면 및 액세스 정책

Key Vault에 대한 Key Vault 액세스 정책을 설정하여 데이터 평면 액세스 권한을 부여합니다. 이러한 액세스 정책을 설정하려면 사용자, 그룹 또는 애플리케이션은 해당 Key Vault에 대한 관리 평면에서 `Contributor` 권한이 있어야 합니다.

Key Vault의 키 또는 비밀에 대해 특정 작업을 실행하기 위해 사용자, 그룹 또는 애플리케이션에 액세스 권한을 부여합니다. 각 Key Vault는 최대 1024개 액세스 정책 항목을 지원합니다. 여러 사용자에게 데이터 평면 액세스 권한을 부여하려면 Azure AD 보안 그룹을 만들고, 해당 그룹에 사용자를 추가합니다.

<a id="key-vault-access-policies"></a> Key Vault 액세스 정책은 키, 비밀 및 인증서에 대한 권한을 별도로 부여합니다. 비밀이 아닌 키에 대해서만 사용자 액세스 권한을 부여할 수 있습니다. 키, 비밀 및 인증서에 대한 액세스 권한은 자격 증명 모음 수준에서 지정됩니다. Key Vault 액세스 정책은 특정 키, 비밀 또는 인증서와 같은 세분화된 개체 수준 권한을 지원하지 않습니다. Key Vault에 대한 액세스 정책을 설정하려면 [Azure Portal](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) 또는 [Key Vault 관리 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)를 사용합니다.

> [!IMPORTANT]
> Key Vault 액세스 정책은 자격 증명 모음 수준에 적용됩니다. 사용자에게 키를 만들고 삭제하는 권한이 부여되면 해당 사용자는 Key Vault의 모든 키에 대해 이러한 작업을 수행할 수 있습니다.
>

[Azure Key Vault에 대한 가상 네트워크 서비스 엔드포인트](key-vault-overview-vnet-service-endpoints.md)를 사용하여 데이터 평면 액세스를 제어할 수 있습니다. 추가 보안 계층에 대한 [방화벽 및 가상 네트워크 규칙](key-vault-network-security.md)을 구성할 수 있습니다.

## <a name="example"></a>예

이 예제에서는 SSL에 인증서를, 데이터 저장에 Azure Storage를, 로그인 작업에 RSA 2048비트 키를 사용하는 애플리케이션을 개발합니다. 애플리케이션은 Azure VM(Virtual Machine)(또는 가상 머신 확장 집합)에서 실행됩니다. Key Vault를 사용하여 애플리케이션 비밀을 저장할 수 있습니다. 애플리케이션이 Azure AD에서 인증을 받는 데 사용하는 부트스트랩 인증서를 저장할 수 있습니다.

다음 저장된 키와 비밀에 액세스해야 합니다.
- **SSL 인증서**: SSL에 사용합니다.
- **스토리지 키**: 스토리지 계정에 액세스하는 데 사용합니다.
- **RSA 2048비트 키**: 서명 작업에 사용합니다.
- **부트스트랩 인증서**: Azure AD를 사용하여 인증하는 데 사용합니다. 액세스 권한이 부여되면 스토리지 키를 가져오고 서명을 위해 RSA 키를 사용할 수 있습니다.

애플리케이션을 관리, 배포 및 감사할 수 있는 사용자를 지정하려면 다음 역할을 정의해야 합니다.
- **보안 팀**: CSO(최고 보안 책임자) 사무실 IT 직원 또는 비슷한 참가자입니다. 이 보안 팀은 비밀의 적절한 보호를 담당합니다. 비밀에는 SSL 인증서, 서명용 RSA 키, 연결 문자열 및 스토리지 계정 키가 포함될 수 있습니다.
- **개발자 및 운영자**: 애플리케이션을 개발하고 Azure에 배포하는 직원입니다. 이 팀의 멤버는 보안 담당자에 속하지 않습니다. 이러한 직원은 SSL 인증서 및 RSA 키와 같은 중요한 데이터에 액세스할 수 없어야 합니다. 배포하는 애플리케이션에서만 중요한 데이터에 액세스할 수 있어야 합니다.
- **감사자**: 이 역할은 개발 팀의 멤버 또는 일반 IT 직원이 아닌 참가자입니다. 이들은 인증서, 키 및 비밀의 사용 및 유지 관리를 검토하여 보안 표준을 준수하도록 합니다. 

애플리케이션의 범위 외부에 있는 다른 역할, 즉 구독(또는 리소스 그룹) 관리자 역할이 있습니다. 구독 관리자는 보안 팀의 초기 액세스 권한을 설정합니다. 이들은 애플리케이션에 필요한 리소스가 있는 리소스 그룹을 사용하여 보안 팀에 액세스 권한을 부여합니다.

역할에 대해 다음과 같은 작업 권한을 부여해야 합니다.

**보안 팀**
- Key Vault를 만듭니다.
- Key Vault 로깅을 켭니다.
- 키 및 비밀을 추가합니다.
- 재해 복구를 위해 키 백업을 만듭니다.
- Key Vault 액세스 정책을 설정하여 사용자와 애플리케이션에 특정 작업에 대한 권한을 부여합니다.
- 키 및 비밀을 주기적으로 롤링합니다.

**개발자 및 운영자**
- 서명을 위해 보안 팀에서 부트스트랩 및 SSL 인증서(지문), 스토리지 키(비밀 URI) 및 RSA 키(키 URI)에 대한 참조를 가져옵니다.
- 프로그래밍 방식으로 키와 암호에 액세스하는 애플리케이션을 개발하고 배포합니다.

**감사자**
- Key Vault 로그를 검토하여 적절한 키/비밀 사용 및 데이터 보안 표준 규정 준수를 확인합니다.

다음 표에서는 역할과 애플리케이션에 대한 액세스 권한을 요약해서 설명합니다. 

| 역할 | 관리 평면 사용 권한 | 데이터 평면 사용 권한 |
| --- | --- | --- |
| 보안 팀 | Key Vault 참가자 | 키: 백업, 만들기, 삭제, 권한 가져오기, 가져오기, 목록 표시, 복원<br>비밀: 모든 작업 |
| 개발자 및&nbsp;운영자 | Key Vault 배포 권한<br><br> **참고**: 이 권한이 있으면 배포된 VM이 Key Vault에서 비밀을 가져올 수 있습니다. | 없음 |
| 감사자 | 없음 | 키: 목록 표시<br>암호: 목록 표시<br><br> **참고**: 이 권한이 있으면 감사자는 로그에서 내보내지 않은 키 및 비밀의 특성(태그, 활성화 날짜, 만료 날짜)을 검사할 수 있습니다. |
| 애플리케이션 | 없음 | 키: 로그인<br>암호: 권한 가져오기 |

이 세 가지 팀 역할은 Key Vault 사용 권한과 함께 다른 리소스에 대한 액세스 권한이 필요합니다. VM(또는 Azure App Service의 Web Apps 기능)을 배포하려면 개발자와 운영자는 해당 리소스 형식에 대한 `Contributor` 액세스 권한이 필요합니다. 감사자에게는 Key Vault 로그를 저장할 스토리지 계정에 대한 읽기 액세스 권한이 필요합니다.

프로그래밍 방식으로 인증서, 액세스 키 및 비밀을 배포하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.
- [고객 관리 Key Vault에서 VM에 인증서 배포](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)(블로그 게시물) 방법을 알아봅니다.
- [Azure Key Vault 클라이언트 샘플](https://www.microsoft.com/download/details.aspx?id=45343)을 다운로드합니다. 이 콘텐츠에서는 부트스트랩 인증서를 사용하여 Key Vault에 액세스하기 위해 Azure AD에서 인증을 받는 방법을 설명합니다.

Azure Portal을 사용하여 대부분의 액세스 권한을 부여할 수 있습니다. 세분화된 권한을 부여하려면 Azure PowerShell 또는 Azure CLI를 사용할 수 있습니다.

이 섹션의 PowerShell 코드 조각은 다음과 같은 가정에 따라 빌드됩니다.
- Azure AD 관리자가 세 가지 역할 Contoso Security Team, Contoso App DevOps 및 Contoso App Auditors를 나타내는 보안 그룹을 만들었습니다. 관리자가 해당 그룹에 사용자를 추가했습니다.
- 모든 리소스는 **ContosoAppRG** 리소스 그룹에 있습니다.
- Key Vault 로그는 **contosologstorage** 스토리지 계정에 저장됩니다. 
- **ContosoKeyVault** Key Vault 및 **contosologstorage** 스토리지 계정이 동일한 Azure 위치에 있습니다.

구독 관리자는 보안 팀에 `key vault Contributor` 및 `User Access Administrator` 역할을 할당합니다. 이러한 역할을 사용하여 보안 팀에서 **ContosoAppRG** 리소스 그룹에 있는 다른 리소스 및 Key Vault에 대한 액세스를 관리할 수 있습니다.

```PowerShell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

보안 팀은 key vault를 만들고 로깅 및 액세스 권한을 설정합니다. Key Vault 액세스 정책 권한에 대한 자세한 내용은 [Azure Key Vault 키, 비밀 및 인증서 정보](about-keys-secrets-and-certificates.md)를 참조하세요.

```PowerShell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

정의된 사용자 지정 역할은 **ContosoAppRG** 리소스 그룹을 만든 구독에만 할당할 수 있습니다. 다른 구독의 다른 프로젝트에 대한 사용자 지정 역할을 사용하려면 해당 역할의 범위에 다른 구독을 추가합니다.

DevOps 직원의 경우 Key Vault `deploy/action` 권한에 대한 사용자 지정 역할 할당 범위는 해당 리소스 그룹으로 지정됩니다. **ContosoAppRG**에서 만든 VM만 비밀(SSL 및 부트스트랩 인증서)에 액세스할 수 있습니다. DevOps 멤버가 다른 리소스 그룹에서 만든 VM은 비밀 URI가 있더라도 이러한 비밀에 액세스할 수 없습니다.

이 예제에서는 간단한 시나리오를 설명합니다. 실제 시나리오는 더 복잡할 수 있습니다. 필요에 따라 key vault에 대한 사용 권한을 조정할 수 있습니다. 보안 팀에서 DevOps 직원이 애플리케이션에서 사용하는 키 및 비밀 참조(URI 및 지문)를 제공한다고 가정합니다. 개발자 및 운영자에게는 데이터 평면 액세스가 필요하지 않습니다. Key Vault를 보호하는 방법을 집중적으로 살펴보았습니다. [VM](https://azure.microsoft.com/services/virtual-machines/security/), [스토리지 계정](../storage/common/storage-security-guide.md) 및 기타 Azure 리소스를 보호할 때도 비슷한 사항을 고려해야 합니다.

> [!NOTE]
> 이 예제에서는 프로덕션 환경에서 Key Vault 액세스를 잠그는 방법을 보여줍니다. 개발자는 애플리케이션을 개발하는 자격 증명 모음, VM 및 스토리지 계정을 관리할 수 있는 모든 권한을 갖춘 구독 또는 리소스 그룹을 가지고 있어야 합니다.

[Key Vault 방화벽 및 가상 네트워크를 구성](key-vault-network-security.md)하여 Key Vault에 대한 추가 보안 액세스를 설정하는 것이 좋습니다.

## <a name="resources"></a>리소스

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

* [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Azure PowerShell을 사용하여 RBAC 관리](../role-based-access-control/role-assignments-powershell.md) 

* [REST API를 사용하여 RBAC 관리](../role-based-access-control/role-assignments-rest.md)

* [Microsoft Azure용 RBAC](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    이 2015 Microsoft Ignite 컨퍼런스 동영상은 Azure의 액세스 관리 및 보고 기능에 대해 설명합니다. 또한 Azure AD를 사용하여 Azure 구독에 대한 액세스를 보호하는 모범 사례도 알아봅니다.

* [OAuth 2.0 및 Azure AD를 사용하여 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v1-protocols-oauth-code.md)

* [Key Vault 관리 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Key Vault 액세스 정책 설정을 포함하여 프로그래밍 방식으로 Key Vault를 관리하는 REST API에 대한 참조입니다.

* [Key Vault REST API](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [키 액세스 제어](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [암호 액세스 제어](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* PowerShell을 사용하여 Key Vault 액세스 정책 [설정](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) 및 [제거](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy)
  
## <a name="next-steps"></a>다음 단계

[Key Vault 방화벽 및 가상 네트워크를](key-vault-network-security.md) 구성합니다.

관리자를 위한 시작 자습서는 [Azure Key Vault란?](key-vault-overview.md)을 참조하세요.

키 자격 증명 모음에 사용 현황 로깅에 대한 자세한 내용은 [Azure Key Vault 로깅](key-vault-logging.md)을 참조하세요.

Azure Key Vault에서 키 및 비밀을 사용하는 방법에 대한 자세한 내용은 [키 및 비밀 정보](https://msdn.microsoft.com/library/azure/dn903623.aspx)를 참조하세요.

Key Vault에 대한 질문이 있으면 [포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)을 방문하세요.
