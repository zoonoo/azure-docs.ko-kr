---
title: 가상 머신 컴퓨팅 노드에서 Linux 실행
description: Azure Batch에서 Linux virtual machines 풀의 병렬 계산 워크 로드를 처리 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: 0a9c801a13af05f077b87f296992da7f50742e4b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533500"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Batch 풀에서 Linux 컴퓨팅 노드 프로비전

Azure Batch를 사용하여 Linux 및 Windows 가상 머신에서 병렬 컴퓨팅 워크로드를 실행할 수 있습니다. 이 문서에서는 [Batch Python](https://pypi.python.org/pypi/azure-batch) 및 [Batch .NET](/dotnet/api/microsoft.azure.batch) 클라이언트 라이브러리를 모두 사용하여 Batch 서비스에서 Linux 컴퓨팅 노드 풀을 만드는 방법에 대해 자세히 설명합니다. 

## <a name="virtual-machine-configuration"></a>가상 머신 구성

Batch에서 컴퓨팅 노드 풀을 만들 때는 노드 크기와 운영 체제를 선택할 수 있는 두 가지 옵션인 Cloud Services 구성 및 Virtual Machine 구성이 있습니다. 대부분의 Windows 계산 노드 풀에는 풀이 Azure Cloud Services 노드로 구성 되도록 지정 하는 [Cloud Services 구성이](nodes-and-pools.md#cloud-services-configuration)사용 됩니다. 이러한 풀은 Windows 계산 노드만 제공 합니다.

반면, [가상 컴퓨터 구성](nodes-and-pools.md#virtual-machine-configuration) 에서는 풀이 Azure vm으로 구성 됩니다 .이는 Linux 또는 Windows 이미지에서 만들 수 있습니다. 가상 컴퓨터 구성을 사용 하 여 풀을 만드는 경우 [사용 가능한 계산 노드 크기](../virtual-machines/sizes.md), 가상 컴퓨터 이미지 참조 및 Batch 노드 에이전트 SKU (각 노드에서 실행 되 고 노드와 Batch 서비스 간의 인터페이스를 제공 하는 프로그램) 및 노드에 설치 될 가상 컴퓨터 이미지 참조를 지정 해야 합니다.

### <a name="virtual-machine-image-reference"></a>가상 머신 이미지 참조

Batch 서비스는 [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)을 사용하여 가상 머신 구성에서 컴퓨팅 노드를 제공합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)에서 이미지를 지정 하거나 [공유 이미지 갤러리를 사용 하 여 사용자 지정 이미지를 준비할](batch-sig-images.md)수 있습니다.

가상 컴퓨터 이미지 참조를 만들 때 다음 속성을 지정 해야 합니다.

| **이미지 참조 속성** | **예제** |
| --- | --- |
| 게시자 |Canonical |
| 제안 |UbuntuServer |
| SKU |18.04-LTS |
| 버전 |최신 |

> [!TIP]
> 이러한 속성 및 Azure CLI를 사용 하 여 [Azure Marketplace에서 LINUX VM 이미지 찾기](../virtual-machines/linux/cli-ps-findimage.md)에서 Marketplace 이미지를 지정 하는 방법에 대해 자세히 알아볼 수 있습니다. 일부 Marketplace 이미지가 현재 Batch와 호환되지 않습니다.

### <a name="node-agent-sku"></a>노드 에이전트 SKU

[Batch 노드 에이전트](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) 는 풀의 각 노드에서 실행 되 고 노드와 Batch 서비스 간에 명령 및 컨트롤 인터페이스를 제공 하는 프로그램입니다. SKU라고 하는 노드 에이전트의 구현은 서로 다른 운영 체제에 대해 여러 가지가 있습니다. 기본적으로 Virtual Machine 구성을 만들 때 먼저 가상 머신 이미지 참조를 지정한 다음 이미지에 설치할 노드 에이전트를 지정합니다. 일반적으로 각 노드 에이전트 SKU는 여러 가상 머신 이미지와 호환됩니다. 다음은 노드 에이전트 SKU의 몇 가지 예입니다.

- batch.node.ubuntu 18.04
- batch.node.centos 7
- batch.node.windows amd64

### <a name="list-of-virtual-machine-images"></a>가상 머신 이미지 목록

일부 마켓플레이스 이미지는 현재 사용 가능한 Batch 노드 에이전트와 호환 되지 않습니다. Batch 서비스 및 해당 노드 에이전트 Sku에 대해 지원 되는 모든 Marketplace 가상 머신 이미지를 나열 하려면 Python ( [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) ), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (batch .net) 또는 다른 언어 SDK의 해당 API를 사용 합니다.

## <a name="create-a-linux-pool-batch-python"></a>Linux 풀 만들기: Batch Python

다음 코드 조각은 [Python용 Microsoft Azure Batch 클라이언트 라이브러리](https://pypi.python.org/pypi/azure-batch)를 사용하여 Ubuntu Server 컴퓨팅 노드의 풀을 만드는 방법의 예를 보여 줍니다. Batch Python 모듈에 대 한 자세한 내용은 [참조 설명서를 참조](/python/api/overview/azure/batch)하세요.

이 코드 조각은 명시적으로 [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference)를 만들고 각 속성(게시자, 제품, SKU, 버전)을 지정합니다. 그러나 프로덕션 코드에서는 [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) 메서드를 사용 하 여 런타임 시 사용 가능한 이미지 및 노드 에이전트 SKU 조합에서 선택 하는 것이 좋습니다.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

앞서 설명한 것 처럼 [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) 메서드를 사용 하 여 현재 지원 되는 노드 에이전트/Marketplace 이미지 조합 (명시적으로 [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) 을 만드는 대신)에서 동적으로 선택 하는 것이 좋습니다. 다음 Python 코드 조각에서는 이 메서드의 사용 방법을 보여 줍니다.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Linux 풀 만들기: Batch .NET

다음 코드 조각은 [Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) 클라이언트 라이브러리를 사용하여 Ubuntu Server 컴퓨팅 노드의 풀을 만드는 방법의 예를 보여 줍니다. Batch .NET에 대 한 자세한 내용은 [참조 설명서를 참조](/dotnet/api/microsoft.azure.batch)하세요.

다음 코드 조각에서는 [PoolOperations ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) 메서드를 사용 하 여 현재 지원 되는 Marketplace 이미지 및 노드 에이전트 SKU 조합의 목록에서 선택 합니다. 지원 되는 조합 목록이 시간에서 변경 될 수 있으므로이 기법은 권장 됩니다. 가장 일반적으로 지원되는 조합을 추가합니다.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

이전 코드 조각은 [PoolOperations istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) 메서드를 사용 하 여 지원 되는 이미지와 노드 에이전트 SKU 조합 (권장)을 동적으로 나열 하 고 선택 하지만 [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) 를 명시적으로 구성할 수도 있습니다.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>SSH를 사용하여 Linux 노드에 연결

개발 또는 문제 해결 동안 풀의 노드에 로그인할 필요가 있을 수 있습니다. Windows 계산 노드와 달리 원격 데스크톱 프로토콜 (RDP)를 사용 하 여 Linux 노드에 연결할 수 없습니다. 대신, Batch 서비스는 원격 연결을 위해 각 노드에서 SSH 액세스를 사용하도록 설정합니다.

다음 Python 코드 조각에서는 풀의 각 노드에서 사용자를 만들며 이는 원격 연결에 필요합니다. 그런 다음 각 노드에 대한 SSH(secure shell) 연결 정보를 인쇄합니다.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

이 코드는 다음 예제와 유사 하 게 출력 됩니다. 이 경우 풀은 4 개의 Linux 노드를 포함 합니다.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

노드에 사용자를 만들 때 암호 대신 SSH 공개 키를 지정할 수 있습니다. Python SDK에서는 [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser)에 **ssh_public_key** 매개 변수를 사용합니다. .NET에서는 [ComputeNodeUser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) 속성을 사용 합니다.

## <a name="pricing"></a>가격 책정

Azure Batch는 Azure Cloud Services 및 Azure Virtual Machines 기술을 기반으로 빌드됩니다. Batch 서비스 자체는 무료로 제공됩니다. 즉, Batch 솔루션에서 사용하는 컴퓨팅 리소스(및 이에 따른 관련 비용)에 대해서만 요금이 청구됩니다. **Virtual Machine 구성** 을 선택하는 경우 [Virtual Machines 가격](https://azure.microsoft.com/pricing/details/virtual-machines/) 구조에 따라 요금이 청구됩니다.

[애플리케이션 패키지](batch-application-packages.md)를 사용하여 Batch 노드에 애플리케이션을 배포하는 경우에도 애플리케이션 패키지에서 사용하는 Azure Storage 리소스에 대한 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

- 풀, 작업 및 태스크 작성과 같은 일반적인 배치 작업을 수행 하는 방법을 보려면 [azure-batch 샘플 GitHub 리포지토리의](https://github.com/Azure/azure-batch-samples) [Python 코드 샘플](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) 을 탐색 하세요. Python 샘플과 함께 제공되는 [추가 정보](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md)에는 필요한 패키지를 설치하는 방법에 대한 세부 정보가 있습니다.
- Batch로 [우선 순위가 낮은 vm](batch-low-pri-vms.md) 을 사용 하는 방법에 대해 알아봅니다.
