---
title: 빠른 시작 - Azure에서 프라이빗 엔드포인트 관리
description: 이 빠른 시작에서 Azure Portal를 사용하여 프라이빗 엔드포인트를 만드는 방법 알아보기
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1bdc089bb89a732e329bf7d3ffd3d5b5c09ba408
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637254"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>빠른 시작: Azure Portal를 사용하여 프라이빗 엔드포인트 만들기

프라이빗 엔드포인트는 Azure에서 프라이빗 링크를 만드는 데 사용되는 기본 구성 요소입니다. 프라이빗 엔드포인트는 VM(Virtual Machines) 같은 Azure 리소스가 프라이빗 링크 리소스와 비공개로 통신할 수 있게 해줍니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure 프라이빗 엔드포인트가 있는 SQL Database Server인 Azure Virtual Network에서 VM을 만드는 방법에 대해 알아봅니다. 그러면 VM에서 SQL Database Server에 안전하게 액세스할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vm"></a>VM 만들기
이 섹션에서는 Private Link 리소스(이 예제에서는 Azure의 SQL 서버)에 액세스하는 데 사용되는 VM을 호스트하는 가상 네트워크와 서브넷을 만듭니다.

## <a name="virtual-network-and-parameters"></a>가상 네트워크 및 매개 변수

이 섹션에서는 Private Link 리소스에 액세스하는 데 사용되는 VM을 호스트하는 Virtual Network와 서브넷을 만듭니다.

이 섹션에서는 단계의 다음 매개 변수를 아래 정보로 바꾸어야 합니다.

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | 미국 중서부    |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Virtual Machine 만들기

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신**을 선택합니다.

1. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
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

1. 완료되면 **다음: 디스크**를 선택합니다.

1. **가상 머신 만들기 - 디스크**에서 기본값을 그대로 두고 **다음: 네트워킹**을 선택합니다.

1. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | 기본값인 **MyVirtualNetwork**를 그대로 둡니다.  |
    | 주소 공간 | 기본값인 **10.1.0.0/24**를 그대로 둡니다.|
    | 서브넷 | 기본값인 **mySubnet(10.1.0.0/24)** 을 그대로 둡니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip**를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
    | 인바운드 포트 선택 | **HTTP** 및 **RDP**를 선택합니다.|
    |||


1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

## <a name="create-a-sql-database-server"></a>SQL 데이터베이스 서버 만들기
이 섹션에서는 Azure에서 SQL 데이터베이스 서버를 만듭니다. 

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **SQL 데이터베이스**를 선택합니다.

1. **SQL 데이터베이스 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **데이터베이스 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 정보** |  |
    | 데이터베이스 이름  | *mydatabase*를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    |||
5. **서버**에서 **새로 만들기**를 선택합니다. 
6. **새 서버**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |서버 이름  | *myserver*를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다.|
    | 서버 관리자 로그인| 원하는 관리자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 8자 이상이어야 하며 정의된 요구 사항을 충족해야 합니다. |
    | 위치 | SQL Server가 상주할 Azure 지역을 선택합니다. |
    
7. **확인**을 선택합니다. 
8. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
9. 유효성 검사 통과 메시지가 표시되면 **만들기**를 선택합니다. 
10. 유효성 검사 통과 메시지가 표시되면 만들기를 선택합니다. 

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 SQL 서버를 만들고 프라이빗 엔드포인트를 추가합니다. 

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **Private Link 센터(미리 보기)** 를 선택합니다.
2. **Private Link 센터 - 개요**의 **서비스에 대한 프라이빗 연결 설정** 옵션에서 **시작**을 선택합니다.
1. **프라이빗 엔드포인트 만들기(미리 보기) - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 정보** |  |
    | 속성 | *myPrivateEndpoint*를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    |지역|**WestCentralUS**를 선택합니다.|
    |||
5. 완료되면 **다음: 리소스**를 선택합니다.
6. **프라이빗 엔드포인트 만들기 - 리소스**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | 내 디렉터리의 Azure 리소스에 연결하도록 선택합니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft.Sql/servers**를 선택합니다. |
    | 리소스 |*myServer*를 선택합니다.|
    |대상 하위 리소스 |*sqlServer*를 선택합니다.|
    |||
7. 완료되면 **다음: 구성**을 선택합니다.
8. **프라이빗 엔드포인트 만들기(미리 보기) - 구성**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| *MyVirtualNetwork*를 선택합니다. |
    | 서브넷 | *mySubnet*을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. |
    |프라이빗 DNS 영역 |*(New)privatelink.database.windows.net*을 선택합니다. |
    |||

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
2. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>RDP(원격 데스크톱)를 사용하여 VM에 연결


**myVm**을 만든 후 다음과 같이 인터넷을 통해 연결합니다. 

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. *downloaded.rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>VM에서 비공개로 SQL 데이터베이스 서버에 액세스

1. *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.

2. `nslookup myserver.database.windows.net`를 입력합니다. 

    다음과 유사한 메시지가 표시됩니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)를 설치합니다.

4. **서버에 연결**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 서버 유형| **데이터베이스 엔진**을 선택합니다.|
    | 서버 이름| *myserver.database.windows.net*을 선택합니다. |
    | 사용자 이름 | SQL 서버 생성 중에 제공되는 username@servername으로 사용자 이름을 입력합니다. |
    |암호 |SQL Server를 만들 때 입력한 암호를 입력합니다. |
    |암호 기억|**예**를 선택합니다.|
    |||
1. **연결**을 선택합니다.
2. 왼쪽 메뉴에서 데이터베이스를 찾아봅니다.
3. (선택 사항) Mydatabase에서 정보를 만들거나 쿼리합니다.
4. *myVm*에 대한 원격 데스크톱 연결을 닫습니다. 

## <a name="clean-up-resources"></a>리소스 정리 
프라이빗 엔드포인트, SQL Server 및 VM을 다 사용했으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다. 
1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력하고 검색 결과에서 *myResourceGroup*을 선택합니다. 
2. **리소스 그룹 삭제**를 선택합니다. 
3. **리소스 그룹 이름 입력**에 대해 myResourceGroup을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 가상 네트워크, SQL 데이터베이스 서버 및 프라이빗 액세스를 위한 프라이빗 엔드포인트에서 VM을 만들었습니다. 인터넷에서 하나의 VM에 연결하고 프라이빗 링크를 사용하여 SQL 데이터베이스 서버에 안전하게 전달했습니다. 프라이빗 엔드포인트에 대해 자세히 알아보려면 [Azure 프라이빗 엔드포인트란?](private-endpoint-overview.md)을 참조하세요.
