---
title: 관리형 HSM에 대한 보안 액세스 - Azure Key Vault 관리형 HSM
description: Azure RBAC 및 Managed HSM 로컬 RBAC를 사용하여 Managed HSM에 대한 액세스를 보호하는 방법을 알아봅니다.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 99918d039052c9913400b85ac3caa4a1a5481155
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445322"
---
# <a name="secure-access-to-your-managed-hsms"></a>관리형 HSM에 대한 보안 액세스

Azure Key Vault 관리형 HSM은 암호화 키를 보호하는 클라우드 서비스입니다. 이 데이터는 민감하고 업무상 중요하므로 권한이 부여된 애플리케이션과 사용자만 관리형 HSM에 액세스할 수 있도록 허용하여 이러한 관리형 HSM에 대한 액세스를 보호해야 합니다. 이 문서에는 관리형 HSM 액세스 제어 모델에 대한 개요를 제공합니다. 인증, 권한 부여 및 관리형 HSM에 대한 액세스를 보호하는 방법에 대해 설명합니다.

이 자습서에서는 Azure RBAC 및 Managed HSM 로컬 RBAC를 사용하여 업무 분리 및 액세스 제어를 달성하는 방법을 보여주는 간단한 예제를 안내합니다. 관리형 HSM 액세스 제어 모델에 대한 자세한 내용은 [관리형 HSM 액세스 제어](access-control.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 항목이 있어야 합니다.

* Microsoft Azure에 대한 구독. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* Azure CLI 버전 2.12.0 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
* 구독의 관리형 HSM. [빠른 시작: Azure CLI를 사용하여 관리형 HSM 프로비저닝 및 활성화](quick-create-cli.md)를 참조하여 관리형 HSM을 프로비저닝하고 활성화합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

CLI를 통한 로그인 옵션에 대한 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)을 참조하세요.

## <a name="example"></a>예제

이 예제에서는 2,048비트 RSA 키를 서명 작업에 사용하는 애플리케이션을 개발합니다. 애플리케이션은 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 Azure VM(가상 머신)에서 실행됩니다. 서명에 사용되는 두 가지 RSA 키는 모두 관리형 HSM에 저장됩니다.

애플리케이션을 관리, 배포 및 감사하는 다음과 같은 역할이 확인되었습니다.

- **보안 팀**: CSO(최고 보안 책임자) 사무실 IT 직원 또는 비슷한 참가자입니다. 이 보안 팀은 키를 적절하게 보호해야 합니다. 서명에 사용하는 RSA 또는 EC 키 및 데이터 암호화에 사용하는 RSA 또는 AES 키가 있습니다.
- **개발자 및 운영자**: 애플리케이션을 개발하고 Azure에 배포하는 직원입니다. 이 팀의 멤버는 보안 담당자에 속하지 않습니다. RSA 키와 같은 중요한 데이터에는 액세스할 수 없습니다. 이 중요한 데이터는 배포하는 애플리케이션에서만 액세스할 수 있어야 합니다.
- **감사자**: 이 역할은 개발 팀의 멤버 또는 일반 IT 직원이 아닌 참가자입니다. 이들은 인증서, 키 및 비밀의 사용 및 유지 관리를 검토하여 보안 표준을 준수하도록 합니다.

애플리케이션의 범위 외부에 있는 다른 역할, 즉 구독(또는 리소스 그룹) 관리자 역할이 있습니다. 구독 관리자는 보안 팀의 초기 액세스 권한을 설정합니다. 이들은 애플리케이션에 필요한 리소스가 있는 리소스 그룹을 사용하여 보안 팀에 액세스 권한을 부여합니다.

역할에 대해 다음과 같은 작업 권한을 부여해야 합니다.

**보안 팀**
- 관리형 HSM을 만듭니다.
- 재해 복구를 위해 관리형 HSM 보안 도메인을 다운로드합니다.
- 로깅을 설정합니다.
- 키를 생성하거나 가져옵니다.
- 재해 복구를 위해 관리형 HSM 백업을 만듭니다.
- 특정 작업에 대한 권한을 사용자와 애플리케이션에 부여하도록 관리형 HSM 로컬 RBAC를 설정합니다.
- 키를 주기적으로 롤링합니다.

**개발자 및 운영자**
- 보안 팀에서 서명에 사용되는 RSA 키에 대한 참조(키 URI)를 가져옵니다.
- 키에 프로그래밍 방식으로 액세스하는 애플리케이션을 개발하고 배포합니다.

**감사자**
- 키 만료 날짜를 검토하여 키가 최신 상태인지 확인합니다.
- 역할 할당을 모니터링하여 권한 있는 사용자/애플리케이션만 키에 액세스할 수 있도록 합니다.
- 관리형 HSM 로그를 검토하여 데이터 보안 표준에 따라 키를 적절하게 사용하는지 확인합니다.

다음 표에는 관리형 HSM에 액세스할 수 있도록 팀 및 리소스에 할당되는 역할이 요약되어 있습니다.

| 역할 | 관리 평면 역할 | 데이터 평면 역할 |
| --- | --- | --- |
| 보안 팀 | 관리형 HSM 기여자 | 관리형 HSM 관리자 |
| 개발자 및 운영자 | None | None |
| 감사자 | None | 관리형 HSM 암호화 감사자 |
| 애플리케이션에서 사용하는 VM의 관리 ID| None | 관리형 HSM 암호화 사용자 |
| 애플리케이션에서 사용하는 스토리지 계정의 관리 ID| None| 관리형 HSM 서비스 암호화 |

이러한 세 가지 팀 역할에는 관리형 HSM 권한과 함께 다른 리소스에 대한 액세스 권한도 필요합니다. VM(또는 Azure App Service의 Web Apps 기능)을 배포하려면 개발자와 운영자는 해당 리소스 형식에 대한 `Contributor` 액세스 권한이 필요합니다. 감사자에게는 관리형 HSM 로그가 저장되는 스토리지 계정에 대한 읽기 액세스 권한이 필요합니다.

관리 평면 역할(Azure RBAC)을 할당하려면 Azure Portal 또는 다른 관리 인터페이스(예: Azure CLI 또는 Azure PowerShell)를 사용할 수 있습니다. 관리형 HSM 데이터 평면 역할을 할당하려면 Azure CLI를 사용해야 합니다.

이 섹션의 Azure CLI 코드 조각은 다음과 같은 가정을 사용하여 빌드되었습니다.

- Azure Active Directory 관리자가 세 가지 역할, 즉 Contoso Security Team, Contoso App DevOps 및 Contoso App Auditors를 나타내는 보안 그룹을 만들었습니다. 관리자가 해당 그룹에 사용자를 추가했습니다.
- 모든 리소스는 **ContosoAppRG** 리소스 그룹에 있습니다.
- 관리형 HSM 로그는 **contosologstorage** 스토리지 계정에 저장됩니다.
- **ContosoMHSM** 관리형 HSM 및 **contosologstorage** 스토리지 계정은 동일한 Azure 위치에 있습니다.

구독 관리자는 보안 팀에 `Managed HSM Contributor` 역할을 할당합니다. 이 역할을 통해 보안 팀은 기존 관리형 HSM을 관리하고 새 HSM을 만들 수 있습니다. 기존 관리형 HSM이 있는 경우 이를 관리할 수 있도록 "관리형 HSM 관리자" 역할을 할당받아야 합니다.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

보안 팀은 로깅을 설정하고, 역할을 감사자 및 VM 애플리케이션에 할당합니다.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

이 자습서에서는 대부분의 액세스 제어와 관련된 작업만 보여 줍니다. 여기서는 액세스 제어 및 역할 관리에 초점을 맞춘 예제를 유지하기 위해 애플리케이션을 VM에 배포하고, 스토리지 계정에 대한 고객 관리형 키를 사용하여 암호화를 설정하며, 관리형 HSM을 만드는 것과 관련된 다른 동작과 작업은 보여 주지 않습니다.

이 예제에서는 간단한 시나리오를 설명합니다. 실제 시나리오는 더 복잡할 수 있습니다. 필요에 따라 key vault에 대한 사용 권한을 조정할 수 있습니다. 보안 팀에서 DevOps 직원이 애플리케이션에서 사용하는 키 및 비밀 참조(URI 및 지문)를 제공한다고 가정합니다. 개발자 및 운영자에게는 데이터 평면 액세스가 필요하지 않습니다. Key Vault를 보호하는 방법을 집중적으로 살펴보았습니다. [VM](https://azure.microsoft.com/services/virtual-machines/security/), [스토리지 계정](../../storage/blobs/security-recommendations.md) 및 기타 Azure 리소스를 보호할 때도 비슷한 사항을 고려해야 합니다.

## <a name="resources"></a>리소스

- [Azure RBAC 설명서](../../role-based-access-control/overview.md)
- [Azure RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)
- [Azure CLI를 사용하여 Azure RBAC 관리](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>다음 단계

관리자를 위한 시작 자습서는 [관리형 HSM이란?](overview.md)을 참조하세요.

관리형 HSM 로깅의 사용량 로깅에 대한 자세한 내용은 [관리형 HSM 로깅](logging.md)을 참조하세요.
