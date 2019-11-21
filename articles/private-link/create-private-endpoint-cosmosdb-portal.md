---
title: Connect to an Azure Cosmos account with Azure Private Link
description: Learn how to securely access the Azure Cosmos account from a VM by creating a Private Endpoint.
author: asudbring
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 90710176ec16d1c565e24ff7df56b0b838f2699e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229417"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Connect privately to an Azure Cosmos account using Azure Private Link

Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this article, you will learn how to create a VM on an Azure virtual network and an Azure Cosmos account with a Private Endpoint using the Azure portal. Then, you can securely access the Azure Cosmos account from the VM.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Sign in to the [Azure portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>VM 만들기

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

In this section, you will create a virtual network and the subnet to host the VM that is used to access your Private Link resource (an Azure Cosmos account in this example).

1. 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.

1. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | Value |
    | ------- | ----- |
    | name | *MyVirtualNetwork*를 입력합니다. |
    | 주소 공간 | *10.1.0.0/16*을 입력합니다. |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **새로 만들기**를 선택하고 *myResourceGroup*을 입력한 다음, **확인**을 선택합니다. |
    | 위치 | **WestCentralUS**를 선택합니다.|
    | 서브넷 - 이름 | *mySubnet*을 입력합니다. |
    | 서브넷 - 주소 범위 | *10.1.0.0/24*를 입력합니다. |
    |||

1. 나머지 항목은 기본값으로 유지하고 **만들기**를 선택합니다.

### <a name="create-the-virtual-machine"></a>가상 머신 만들기

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual machine**.

1. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | Value |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 정보** |  |
    | 가상 머신 이름 | *myVm*을 입력합니다. |
    | 지역 | **WestCentralUS**를 선택합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다**를 그대로 둡니다. |
    | 이미지 | **Windows Server 2019 Datacenter**를 선택합니다. |
    | 크기 | 기본값인 **표준 DS1 v2**를 그대로 둡니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | Enter a username of your choice. |
    | 암호 | 사용자가 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | Reenter the password. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | 기본값인 **없음**을 그대로 둡니다. |
    | **비용 절감** |  |
    | 이미 Windows 라이선스가 있나요? | 기본값인 **아니요**를 그대로 둡니다. |
    |||

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | Value |
    | ------- | ----- |
    | 가상 네트워크 | 기본값인 **MyVirtualNetwork**를 그대로 둡니다.  |
    | 주소 공간 | 기본값인 **10.1.0.0/24**를 그대로 둡니다.|
    | 서브넷 | 기본값인 **mySubnet(10.1.0.0/24)** 을 그대로 둡니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip**를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
    | 인바운드 포트 선택 | **HTTP** 및 **RDP**를 선택합니다.|
    ||

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos 계정 만들기

Create an [Azure Cosmos SQL API account](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). For simplicity, you can create the Azure Cosmos account in the same region as the other resources (that is "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Create a Private Endpoint for your Azure Cosmos account

Create a Private Link for your Azure Cosmos account as described in the [Create a Private Link using the Azure portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) section of the linked article.

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

Connect to the VM *myVm* from the internet as follows:

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고 컴퓨터에 다운로드합니다.

1. 다운로드한 *.rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Access the Azure Cosmos account privately from the VM

In this section, you will connect privately to the Azure Cosmos account using the Private Endpoint. 

> [!IMPORTANT]
> The DNS configuration for the Azure Cosmos account needs a manual modification on the hosts file to include the FQDN of the specific account. In production scenarios you will configure the DNS server to use the private IP addresses. However for the demo purpose, you can use administrator permissions on the VM and modify the `c:\Windows\System32\Drivers\etc\hosts` file (on Windows) or `/etc/hosts` file (on Linux) to include the IP address and DNS mapping.

1. To include the IP address and DNS mapping, sign into your Virtual machine *myVM*, open the `c:\Windows\System32\Drivers\etc\hosts` file and include the DNS information from previous step in the following format:

   [Private IP Address] [Account endpoint].documents.azure.com

   **예제:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. In the Remote Desktop of *myVM*, install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Select **Cosmos DB Accounts (Preview)** with the right-click.

1. Select **Connect to Cosmos DB**.

1. **API**를 선택합니다.

1. Enter the connection string by pasting the information previously copied.

1. **다음**을 선택합니다.

1. **연결**을 선택합니다.

1. Browse the Azure Cosmos databases and containers from *mycosmosaccount*.

1. (Optionally) add new items to *mycosmosaccount*.

1. Close the remote desktop connection to *myVM*.

## <a name="clean-up-resources"></a>리소스 정리

When you're done using the Private Endpoint, Azure Cosmos account and the VM, delete the resource group and all of the resources it contains: 

1. 포털 맨 위에 있는 **검색** 상자에  *myResourceGroup* 을 입력하고 검색 결과에서  *myResourceGroup* 을 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>다음 단계

In this article, you created a VM on a virtual network, an Azure Cosmos account and a Private Endpoint. You connected to the VM from the internet and securely communicated to the Azure Cosmos account using Private Link.

* To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).

* To learn more about limitation of Private Endpoint when using with Azure Cosmos DB, see [Azure Private Link with Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) article.