---
title: 개인 링크 - Azure 포털 - MySQL용 Azure 데이터베이스
description: Azure 포털에서 MySQL용 Azure 데이터베이스에 대한 개인 링크를 구성하는 방법에 대해 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4a4824a9f8340b12bca7e18562d723eb24e58b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371922"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-portal"></a>포털을 사용하여 MySQL용 Azure 데이터베이스에 대한 개인 링크 만들기 및 관리

프라이빗 엔드포인트는 Azure에서 프라이빗 링크를 만드는 데 사용되는 기본 구성 요소입니다. 프라이빗 엔드포인트는 VM(Virtual Machines) 같은 Azure 리소스가 프라이빗 링크 리소스와 비공개로 통신할 수 있게 해줍니다. 이 문서에서는 Azure 포털을 사용하여 Azure 가상 네트워크에서 VM을 만들고 Azure 개인 끝점이 있는 MySQL 서버에 대한 Azure 데이터베이스를 만드는 방법을 배웁니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!NOTE]
> 이 기능은 MySQL용 Azure 데이터베이스가 범용 및 메모리 최적화가격 책정 계층을 지원하는 모든 Azure 리전에서 사용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[Azure 포털에](https://portal.azure.com)로그인합니다.

## <a name="create-an-azure-vm"></a>Azure VM 만들기

이 섹션에서는 개인 링크 리소스(Azure의 MySQL 서버)에 액세스하는 데 사용되는 VM을 호스트하는 가상 네트워크및 서브넷을 만듭니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기
이 섹션에서는 Private Link 리소스에 액세스하는 데 사용되는 VM을 호스트하는 Virtual Network와 서브넷을 만듭니다.

1. 화면 왼쪽 상단에서 > 리소스**네트워킹** > **가상 네트워크** **만들기를**선택합니다.
2. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | 마이가상 네트워크 를 *입력합니다.* |
    | 주소 공간 | *10.1.0.0/16*을 입력합니다. |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **새로 만들기**를 선택하고 *myResourceGroup*을 입력한 다음, **확인**을 선택합니다. |
    | 위치 | **서유럽**를 선택합니다.|
    | 서브넷 - 이름 | *mySubnet*을 입력합니다. |
    | 서브넷 - 주소 범위 | *10.1.0.0/24*를 입력합니다. |
    |||
3. 나머지 항목은 기본값으로 유지하고 **만들기**를 선택합니다.

### <a name="create-virtual-machine"></a>Virtual Machine 만들기

1. Azure 포털화면 왼쪽 상단에서 > 리소스**계산** > **가상 컴퓨터** **만들기를**선택합니다.

2. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup을**선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | *myVm*을 입력합니다. |
    | 지역 | **서유럽**를 선택합니다. |
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

1. **다음 을 선택합니다: 디스크**.

1. **가상 컴퓨터 만들기 - 디스크에서**기본값을 그대로 두고 **다음: 네트워킹을**선택합니다.

1. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | 기본 **MyVirtualNetwork**.  |
    | 주소 공간 | 기본값인 **10.1.0.0/24**를 그대로 둡니다.|
    | 서브넷 | 기본값인 **mySubnet(10.1.0.0/24)** 을 그대로 둡니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip**를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
    | 인바운드 포트 선택 | **HTTP** 및 **RDP**를 선택합니다.|
    |||


1. **검토 + 만들기를**선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 유효성 검사 전달 메시지가 표시되면 **에서 만들기를** **선택합니다.**

## <a name="create-an-azure-database-for-mysql"></a>Azure Database for MySQL 만들기

이 섹션에서는 Azure에서 MySQL 서버에 대한 Azure 데이터베이스를 만듭니다. 

1. Azure 포털화면 왼쪽 상단에서**MySQL에 대한**리소스 > **데이터베이스** > Azure 데이터베이스 **만들기를**선택합니다.

1. **MySQL용 Azure 데이터베이스에서** 다음 정보를 제공합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup을**선택합니다. 이전 섹션에서 만든 것입니다.|
    | **서버 세부 정보** |  |
    |서버 이름  | *myServer를*입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다.|
    | 관리자 사용자 이름| 원하는 관리자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 8자 이상이어야 하며 정의된 요구 사항을 충족해야 합니다. |
    | 위치 | MySQL Server가 상주할 Azure 지역을 선택합니다. |
    |버전  | 필요한 MySQL 서버의 데이터베이스 버전을 선택합니다.|
    | 컴퓨팅 + 스토리지| 워크로드에 따라 서버에 필요한 가격 책정 계층을 선택합니다. |
    |||
 
7. **확인**을 선택합니다. 
8. **검토 + 만들기를**선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
9. 유효성 검사 전달 메시지가 표시되면 **에서 만들기를**선택합니다. 
10. 유효성 검사 통과 메시지가 표시되면 [만들기]를 선택합니다. 

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 MySQL 서버를 만들고 개인 끝점을 추가합니다. 

1. Azure 포털화면 왼쪽 상단에서 > 리소스**네트워킹** > **개인 링크** **만들기를**선택합니다.

2. **Private Link 센터 - 개요**의 **서비스에 대한 프라이빗 연결 설정** 옵션에서 **시작**을 선택합니다.

    ![비공개 링크 개요](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. 개인 끝점 만들기 - 기본 사항에서 다음 정보를 입력하거나 **선택합니다.**

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup을**선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 세부 정보** |  |
    | 이름 | *myPrivateEndpoint*를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    |지역|**서유럽**를 선택합니다.|
    |||

5. **다음 을 선택합니다.**
6. **프라이빗 엔드포인트 만들기 - 리소스**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | 내 디렉터리의 Azure 리소스에 연결하도록 선택합니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **마이크로 소프트.DBforMySQL / 서버를 선택합니다.** |
    | 리소스 |*myServer*를 선택합니다.|
    |대상 하위 리소스 |*마이sql서버* 선택|
    |||
7. **다음 을 선택합니다: 구성.**
8. **개인 끝점 만들기 - 구성에서**다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| *내 가상 네트워크*를 선택합니다. |
    | 서브넷 |  *mySubnet*을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. |
    |프라이빗 DNS 영역 |*(새)개인 링크.mysql.database.azure.com을* 선택합니다. |
    |||

1. **검토 + 만들기를**선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
2. 유효성 검사 전달 메시지가 표시되면 **에서 만들기를** **선택합니다.** 

    ![만든 비공개 링크](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

    > [!NOTE] 
    > 고객 DNS 설정의 FQDN이 구성된 개인 IP로 확인되지 않습니다. [여기에](../dns/dns-operations-recordsets-portal.md)표시된 대로 구성된 FQDN에 대한 DNS 영역을 설정해야 합니다.

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>RDP(원격 데스크톱)를 사용하여 VM에 연결


**myVm**을 만든 후 다음과 같이 인터넷을 통해 연결합니다. 

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드를**선택합니다. Azure에서 원격 데스크톱 프로토콜(*.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. *downloaded.rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 을** > 선택해야 할 수**있습니다다른 계정 사용**, VM을 만들 때 입력한 자격 증명을 지정합니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.

## <a name="access-the-mysql-server-privately-from-the-vm"></a>VM에서 개인적으로 MySQL 서버에 액세스

1.  *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.

2.  `nslookup  myServer.privatelink.mysql.database.azure.com`을 입력합니다. 

    다음과 유사한 메시지가 표시됩니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. 사용 가능한 클라이언트를 사용하여 MySQL 서버의 개인 링크 연결을 테스트합니다. 아래 예제에서는 [MySQL 워크벤치를](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) 사용하여 작업을 수행했습니다.

4. **새 연결에서**다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 서버 유형| **MySQL을**선택합니다.|
    | 서버 이름| *myServer.privatelink.mysql.database.azure.com* 선택 |
    | 사용자 이름 | MySQL 서버 username@servername 를 만드는 동안 제공되는 사용자 이름을 입력합니다. |
    |암호 |MySQL 서버 를 만드는 동안 제공된 암호를 입력합니다. |
    |SSL|**필수**를 선택합니다.|
    ||

5. 연결을 선택합니다.

6. 왼쪽 메뉴에서 데이터베이스를 찾아봅니다.

7. (선택 사항) MySQL 서버에서 정보를 만들거나 쿼리합니다.

8. myVm에 대한 원격 데스크톱 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리
개인 끝점, MySQL 서버 및 VM을 사용하여 완료되면 리소스 그룹과 포함된 모든 리소스를 삭제합니다.

1. 포털 상단의 **검색** 상자에 *myResourceGroup을* 입력하고 검색 결과에서 *myResourceGroup을* 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름을 입력하기** 위해 myResourceGroup을 입력하고 **삭제를**선택합니다.

## <a name="next-steps"></a>다음 단계

이 방법에서는 가상 네트워크에서 VM, MySQL용 Azure 데이터베이스 및 개인 액세스를 위한 개인 끝점을 만들었습니다. 인터넷에서 하나의 VM에 연결 하 고 개인 링크를 사용 하 여 MySQL 서버에 보안 통신. 개인 끝점에 대한 자세한 내용은 [Azure 개인 끝점입니다.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
