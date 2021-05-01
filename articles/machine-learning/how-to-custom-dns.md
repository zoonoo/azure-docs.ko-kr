---
title: 사용자 지정 DNS 서버 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 작업 영역 및 프라이빗 엔드포인트와 함께 작동하도록 사용자 지정 DNS 서버를 구성하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 04/01/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q3
ms.openlocfilehash: 9021c3f70c9fc053998d1b31271a1ca3b0124b4d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169541"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>사용자 지정 DNS 서버에서 작업 영역을 사용하는 방법

프라이빗 엔드포인트와 함께 Azure Machine Learning 작업 영역을 사용하는 경우 [DNS 이름 확인을 처리하는 여러 방법](../private-link/private-endpoint-dns.md)이 있습니다. 기본적으로 Azure는 작업 영역 및 프라이빗 엔드포인트에 대한 이름 확인을 자동으로 처리합니다. __자체 사용자 지정 DNS 서버__ 를 대신 사용하는 경우 수동으로 DNS 항목을 만들거나 작업 영역에 대해 조건부 전달자를 사용해야 합니다.

> [!IMPORTANT]
> 이 문서에서는 이러한 항목에 대한 FQDN(정규화된 도메인 이름) 및 IP 주소를 찾는 방법만 다루며 해당 항목에 대한 DNS 레코드 구성에 대한 정보는 제공하지 않습니다. 레코드를 추가하는 방법에 대한 자세한 내용은 DNS 소프트웨어에 대한 문서를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- [자체 DNS 서버](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)를 사용하는 Azure Virtual Network입니다.

- 프라이빗 엔드포인트를 사용하는 Azure Machine Learning 작업 영역입니다. 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

- [학습 및 유추 중 네트워크 격리](./how-to-network-security-overview.md)를 사용하는 방법 숙지

- [Azure 프라이빗 엔드포인트 DNS 영역 구성](../private-link/private-endpoint-dns.md) 방법 숙지

- 선택적으로는 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)이 있습니다.

## <a name="public-regions"></a>공용 지역

다음 목록에는 공용 지역에 있는 경우 작업 영역에서 사용하는 FQDN (정규화된 도메인 이름)이 포함되어 있습니다.

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > 이 FQDN의 작업 영역 이름은 잘릴 수 있습니다. 잘림은 `ml-<workspace-name, truncated>-<region>-<workspace-guid>` 63자를 유지하기 위해 수행됩니다.
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * 컴퓨팅 인스턴스는 가상 네트워크 내에서만 액세스할 수 있습니다.
    > * 이 FQDN의 IP 주소가 컴퓨팅 인스턴스의 IP가 **아닙니다**. 대신 작업 영역 프라이빗 엔드포인트(`*.api.azureml.ms` 항목의 IP)의 개인 IP 주소를 사용합니다.

## <a name="azure-china-21vianet-regions"></a>Azure 중국 21Vianet 지역

다음 FQDN은 Azure 중국 21Vianet 지역을 위한 것입니다.

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > 이 FQDN의 작업 영역 이름은 잘릴 수 있습니다. 잘림은 `ml-<workspace-name, truncated>-<region>-<workspace-guid>` 63자를 유지하기 위해 수행됩니다.
* `<instance-name>.<region>.instances.ml.azure.cn`
## <a name="find-the-ip-addresses"></a>IP 주소 찾기

VNet에서 FQDN의 내부 IP 주소를 찾으려면 다음 방법 중 하나를 사용합니다.

> [!NOTE]
> 정규화된 도메인 이름 및 IP 주소는 구성에 따라 달라집니다. 예를 들어 도메인 이름의 GUID 값은 작업 영역에만 적용됩니다.

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

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning __작업 영역__ 을 선택합니다.
1. __설정__ 섹션에서 __프라이빗 엔드포인트 연결__ 을 선택합니다.
1. 표시되는 __프라이빗 엔드포인트__ 열에서 링크를 선택합니다.
1. 작업 영역 프라이빗 엔드포인트에 대한 FQDN(정규화된 도메인 이름) 및 IP 주소 목록은 페이지 하단에 있습니다.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="포털의 FQDN 목록":::

---

모든 메서드에서 반환된 정보는 동일합니다. 리소스의 FQDN 및 개인 IP 주소 목록입니다. 다음 예제는 글로벌 Azure 지역에서 가져온 것입니다.

| FQDN | IP 주소 |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> 일부 FQDN은 프라이빗 엔드포인트에 표시 및 나열되지 않지만 eastus, southcentralus 및 westus2의 작업 영역에 필요합니다. 이러한 FQDN은 다음 표에 나와 있으며 DNS 서버 및/또는 Azure 프라이빗 DNS 영역에도 추가해야 합니다.
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * 컴퓨팅 인스턴스가 있는 경우 `<instance-name>.<region>.instances.azureml.ms`를 사용합니다. 여기서, `<instance-name>`은 컴퓨팅 인스턴스의 이름입니다. 작업 영역 프라이빗 엔드포인트의 개인 IP 주소를 사용합니다. 컴퓨팅 인스턴스는 가상 네트워크 내에서만 액세스할 수 있습니다.
>
> 이러한 모든 IP 주소에 대해 이전 단계에서 반환된 `*.api.azureml.ms` 항목과 동일한 주소를 사용합니다.

다음 표에는 Azure 중국 21Vianet 지역의 예제 IP가 나와 있습니다.

| FQDN | IP 주소 |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |
## <a name="next-steps"></a>다음 단계

가상 네트워크에서 Azure Machine Learning을 사용하는 방법에 대한 자세한 내용은 [가상 네트워크 개요](how-to-network-security-overview.md)를 참조하세요.

프라이빗 엔드포인트를 DNS 구성에 통합하는 방법에 대한 자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)을 참조하세요.
