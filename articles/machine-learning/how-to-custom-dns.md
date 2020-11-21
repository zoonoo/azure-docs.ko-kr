---
title: 사용자 지정 DNS 서버 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 작업 영역 및 개인 끝점에서 작동 하도록 사용자 지정 DNS 서버를 구성 하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c67dcbbe2ca6dea533260f59831556c4338374ba
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012998"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>사용자 지정 DNS 서버에서 작업 영역을 사용하는 방법

개인 끝점과 함께 Azure Machine Learning 작업 영역을 사용 하는 경우 [여러 가지 방법으로 DNS 이름 확인을 처리할](../private-link/private-endpoint-dns.md)수 있습니다. 기본적으로 Azure는 작업 영역 및 개인 끝점에 대 한 이름 확인을 자동으로 처리 합니다. _사용자 고유의 사용자 지정 DNS 서버_ _를 대신 사용 하는 경우 작업 영역에 대 한 DNS 항목을 수동으로 만들어야 합니다.

> [!IMPORTANT]
> 이 문서에서는 이러한 항목에 대 한 DNS 레코드를 구성 하는 방법에 대 한 정보를 제공 하지 않으므로 이러한 항목에 대 한 FQDN (정규화 된 도메인 이름) 및 IP 주소를 찾는 방법에 대해서만 설명 합니다. 레코드를 추가 하는 방법에 대 한 자세한 내용은 DNS 소프트웨어에 대 한 설명서를 참조 하십시오.

## <a name="prerequisites"></a>필수 조건

- [자체 DNS 서버](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)를 사용 하는 Azure Virtual Network입니다.

- 개인 끝점을 사용 하는 Azure Machine Learning 작업 영역입니다. 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

- [학습 & 유추 중에 네트워크 격리](./how-to-network-security-overview.md)를 사용 하는 것에 대해 잘 알고 있어야 합니다.

- [Azure 개인 끝점 DNS 영역 구성](../private-link/private-endpoint-dns.md) 에 대 한 지식

- 필요에 따라 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)합니다.

## <a name="find-the-ip-addresses"></a>IP 주소 찾기

다음 목록에는 작업 영역 및 개인 끝점에서 사용 되는 FQDN (정규화 된 도메인 이름)이 포함 되어 있습니다.

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* 계산 인스턴스를 만들 경우 `<instance-name>.<region>.instances.azureml.ms` 작업 영역 개인 끝점의 개인 IP를 사용 하 여에 대 한 항목을 추가 해야 합니다.

    > [!NOTE]
    > 계산 인스턴스는 가상 네트워크 내 에서만 액세스할 수 있습니다.

VNet에서 Fqdn의 내부 IP 주소를 찾으려면 다음 방법 중 하나를 사용 합니다.

> [!NOTE]
> 정규화 된 도메인 이름 및 IP 주소는 구성에 따라 달라 집니다. 예를 들어 도메인 이름의 GUID 값은 작업 영역에만 적용 됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning __작업 영역__ 을 선택 합니다.
1. __설정__ 섹션에서 __개인 끝점 연결__ 을 선택 합니다.
1. 표시 되는 __개인 끝점__ 열에서 링크를 선택 합니다.
1. 작업 영역 개인 끝점의 FQDN (정규화 된 도메인 이름) 및 IP 주소 목록은 페이지 맨 아래에 있습니다.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="포털의 Fqdn 목록":::

---

모든 메서드에서 반환 된 정보는 동일 합니다. 리소스의 FQDN 및 개인 IP 주소 목록입니다.

| FQDN | IP 주소 |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> 일부 Fqdn은 개인 끝점의 목록에 표시 되지 않지만 작업 영역에 필요 합니다. 이러한 Fqdn은 다음 표에 나열 되어 있으며 DNS 서버에도 추가 해야 합니다.
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * 계산 인스턴스가 있는 경우 `<instance-name>.<region>.instances.azureml.ms` 를 사용 `<instance-name>` 합니다. 여기서은 계산 인스턴스의 이름입니다. 작업 영역 개인 끝점의 개인 IP 주소를 사용 하세요. 계산 인스턴스는 가상 네트워크 내 에서만 액세스할 수 있습니다.
>
> 이러한 모든 IP 주소에 대해 `*.api.azureml.ms` 이전 단계에서 반환 된 항목과 동일한 주소를 사용 합니다.

## <a name="next-steps"></a>다음 단계

가상 네트워크에서 Azure Machine Learning를 사용 하는 방법에 대 한 자세한 내용은 [virtual network 개요](how-to-network-security-overview.md)를 참조 하세요.