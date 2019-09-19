---
title: Azure 개인 끝점을 사용 하 여 전용 저장소 계정에 연결
description: 개인 끝점을 사용 하 여 Azure에서 저장소 계정에 개인적으로 연결 하는 방법을 알아봅니다.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: bb0c6e9d20c12df3532a52df1fe4d9574344d4b3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104725"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Azure 개인 끝점을 사용 하 여 전용 저장소 계정에 연결
Azure 개인 끝점은 Azure의 개인 링크에 대 한 기본 빌딩 블록입니다. Vm (가상 머신)과 같은 Azure 리소스가 개인 링크 리소스와 개인적으로 통신할 수 있도록 합니다.

이 빠른 시작에서는 Azure Portal를 사용 하 여 개인 끝점이 있는 저장소 계정인 Azure virtual network에서 VM을 만드는 방법에 대해 알아봅니다. 그런 다음 VM에서 저장소 계정에 안전 하 게 액세스할 수 있습니다.


## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vm"></a>VM 만들기
이 섹션에서는 개인 링크 리소스 (이 예제에서는 저장소 계정)에 액세스 하는 데 사용 되는 VM을 호스트 하는 가상 네트워크 및 서브넷을 만듭니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 개인 링크 리소스에 액세스 하는 데 사용 되는 VM을 호스트 하는 가상 네트워크 및 서브넷을 만듭니다.

1. 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.
1. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | *MyVirtualNetwork*를 입력 합니다. |
    | 주소 공간 | *10.1.0.0/16*을 입력합니다. |
    | 구독 | 구독을 선택합니다.|
    | 리소스 그룹 | **새로 만들기**를 선택하고 *myResourceGroup*을 입력한 다음, **확인**을 선택합니다. |
    | 위치 | **WestCentralUS**를 선택 합니다.|
    | 서브넷 - 이름 | *Mysubnet*을 입력 합니다. |
    | 서브넷 - 주소 범위 | *10.1.0.0/24*를 입력합니다. |
    |||
1. 나머지 항목은 기본값으로 유지하고 **만들기**를 선택합니다.


### <a name="create-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 화면의 왼쪽 위에서 **리소스** > 만들기**계산** > **가상 머신**를 선택 합니다.

1. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | 구독 | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 정보** |  |
    | 가상 머신 이름 | *Myvm*을 입력 합니다. |
    | Region | **WestCentralUS**를 선택 합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다**를 그대로 둡니다. |
    | 이미지 | **Windows Server 2019 Datacenter**를 선택 합니다. |
    | Size | 기본값인 **표준 DS1 v2**를 그대로 둡니다. |
    | **관리자 계정** |  |
    | Username | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | 기본값인 **없음**을 그대로 둡니다. |
    | **비용 절감** |  |
    | 이미 Windows 라이선스가 있나요? | 기본값인 **아니요**를 그대로 둡니다. |
    |||

1. 완료되면 **다음: 디스크**를 선택합니다.

1. **가상 머신 만들기-디스크**에서 기본값을 그대로 두고 다음을 선택 **합니다. 네트워킹**을 선택합니다.

1. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | 기본 **MyVirtualNetwork**을 그대로 둡니다.  |
    | 주소 공간 | 기본 **10.1.0.0/24**를 그대로 둡니다.|
    | Subnet | 기본 **Mysubnet (10.1.0.0/24)** 을 그대로 둡니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip**를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
    | 인바운드 포트 선택 | **HTTP** 및 **RDP**를 선택합니다.|
    ||

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

## <a name="create-your-private-endpoint"></a>개인 끝점 만들기
이 섹션에서는 개인 끝점을 사용 하 여 개인 저장소 계정을 만듭니다. 

1. Azure Portal 화면의 왼쪽 위에서 **리소스** > 만들기**저장소** > **저장소 계정**을 선택 합니다.

1. **저장소 계정 만들기-기본 사항**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | 구독 | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 정보** |  |
    | 저장소 계정 이름  | *Mystorageaccount*를 입력 합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    | Region | **WestCentralUS**를 선택 합니다. |
    | 성능| 기본값인 **표준**을 그대로 둡니다. |
    | 계정 종류 | 기본 **저장소 (범용 v2)** 를 그대로 둡니다. |
    | 복제 | **읽기 액세스 지역 중복 저장소 (RA-GRS)** 를 선택 합니다. |
    |||
  
3. 다음 **을 선택 합니다. 네트워킹**을 선택합니다.
4.  **저장소 계정 만들기-네트워킹**, 연결 방법에서 **개인 끝점**을 선택 합니다.
5.  **저장소 계정 만들기-네트워킹**에서 **개인 끝점 추가**를 선택 합니다. 
6.  **개인 끝점 만들기**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | 구독 | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    |위치|**WestCentralUS**를 선택 합니다.|
    |이름| *MyPrivateEndpoint*를 입력 합니다.  |
    |저장소 하위 리소스|기본 **Blob**을 그대로 둡니다. |
    | **LAN** |  |
    | 가상 네트워크  | 리소스 그룹 *Myresourcegroup*에서 *MyVirtualNetwork* 을 선택 합니다. |
    | Subnet |  *Mysubnet*을 선택 합니다. |
    | **개인 DNS 통합**|  |
    | 사설 DNS 영역과 통합  | 기본값 **예**를 그대로 둡니다. |
    | 사설 DNS 영역  | **기본값 (New) privatelink.blob.core.windows.net**을 그대로 둡니다. |
    |||
7.  **확인을**선택 합니다. 
8.  **검토 + 만들기**를 선택 합니다. Azure에서 구성의 유효성을 검사 하는 **검토 + 만들기** 페이지로 이동 합니다. 
9.  **유효성 검사 통과** 메시지가 표시 되 면 **만들기**를 선택 합니다. 
10. 방금 만든 저장소 계정 리소스를 찾습니다.
11. 왼쪽 콘텐츠 메뉴에서 **액세스 키** 를 선택 합니다.
12. Key1에 대 한 연결 문자열에서 **복사** 를 선택 합니다.
 
## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 VM *myvm* 에 연결 합니다.

1. 포털의 검색 창에서 *Myvm*을 입력 합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. 다운로드 한 .rdp * 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-storage-account-privately-from-the-vm"></a>VM에서 개인적으로 저장소 계정 액세스

이 섹션에서는 개인 끝점을 사용 하 여 저장소 계정에 비공개로 연결 합니다.

> [!IMPORTANT]
> 저장소에 대 한 DNS 구성에는 특정 계정의 FQDN을 포함 하기 위해 호스트 파일을 수동으로 수정 해야 합니다. Windows에 대 한 관리자 권한을 사용 하 여 다음 파일을 수정 하십시오. c:\Windows\System32\Drivers\etc\hosts 또는 Linux/etc/hosts 이전 단계의 계정에 대 한 DNS 정보를 다음 형식 [개인 IP 주소] myaccount.blob.core.windows.net 포함 합니다.

1.  *Myvm*의 원격 데스크톱에서 PowerShell을 엽니다.
2. Enter `nslookup mystorageaccount.blob.core.windows.net` 키를 누르면 다음과 비슷한 메시지가 표시 됩니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
