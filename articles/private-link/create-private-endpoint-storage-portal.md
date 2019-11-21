---
title: Connect privately to a storage account using Azure Private Endpoint
description: Learn how to connect privately to a storage account in Azure using a Private Endpoint.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfe0caaf199821358f8a66ac65ae75c38336c725
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228085"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connect privately to a storage account using Azure Private Endpoint
Azure Private Endpoint is the fundamental building block for Private Link in Azure. It enables Azure resources, like virtual machines (VMs), to communicate privately with Private Link resources.

In this Quickstart, you will learn how to create a VM on an Azure virtual network, a storage account with a Private Endpoint using the Azure portal. Then, you can securely access the storage account from the VM.


## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vm"></a>VM 만들기
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link Resource (a storage account in this example).

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource.

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


### <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

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
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
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

## <a name="create-your-private-endpoint"></a>Create your Private Endpoint
In this section, you will create a private storage account using a Private Endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Storage** > **Storage account**.

1. In **Create storage account - Basics**, enter or select this information:

    | 설정 | Value |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 정보** |  |
    | Storage 계정 이름  | Enter *mystorageaccount*. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    | 지역 | **WestCentralUS**를 선택합니다. |
    | 성능 중심| 기본값인 **표준**을 그대로 둡니다. |
    | 계정 종류 | Leave the default **Storage (general purpose v2)** . |
    | 복제 | Select **Read-access geo-redundant storage (RA-GRS)** . |
    |||
  
3. Select **Next: Networking**.
4. In **Create a storage account - Networking**, connectivity method, select **Private Endpoint**.
5. In **Create a storage account - Networking**, select **Add Private Endpoint**. 
6. In **Create Private Endpoint**, enter or select this information:

    | 설정 | Value |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    |위치|**WestCentralUS**를 선택합니다.|
    |name|Enter *myPrivateEndpoint*.  |
    |Storage sub-resource|Leave the default **Blob**. |
    | **네트워킹** |  |
    | 가상 네트워크  | Select *MyVirtualNetwork* from resource group *myResourceGroup*. |
    | 서브넷 |  *mySubnet*을 선택합니다. |
    | **프라이빗 DNS 통합**|  |
    | 프라이빗 DNS 영역과 통합  | Leave the default **Yes**. |
    | 프라이빗 DNS 영역  | Leave the default ** (New) privatelink.blob.core.windows.net**. |
    |||
7. **확인**을 선택합니다. 
8. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
9. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다. 
10. Browse to the storage account resource that you just created.
11. Select **Access Keys** from the left content menu.
12. Select **Copy** on the connection string for key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

Connect to the VM *myVm* from the internet as follows:

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고 컴퓨터에 다운로드합니다.

1. 다운로드 한 .rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-storage-account-privately-from-the-vm"></a>Access storage account privately from the VM

In this section, you will connect privately to the storage account using the Private Endpoint.

> [!IMPORTANT]
> DNS configuration for storage needs a manual modification on the hosts file to include the FQDN of the specific account Please modify the following file using administrator permissions on Windows: c:\Windows\System32\Drivers\etc\hosts or Linux /etc/hosts Include the DNS information for the account from previous step in the following format [Private IP Address] myaccount.blob.core.windows.net

1.  *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.
2. Enter `nslookup mystorageaccount.blob.core.windows.net` You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)를 설치합니다.
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. **다음**을 선택합니다.
8. Enter the connection string by pasting the information previously copied.
9. **다음**을 선택합니다.
10. **연결**을 선택합니다.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. AzCopy를 사용하여 Blob, File 및 Table Storage 간에 데이터를 전송합니다. 


## <a name="clean-up-resources"></a>리소스 정리 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. 포털 맨 위에 있는 **검색** 상자에  *myResourceGroup* 을 입력하고 검색 결과에서  *myResourceGroup* 을 선택합니다. 
2. **리소스 그룹 삭제**를 선택합니다. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## <a name="next-steps"></a>다음 단계
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
