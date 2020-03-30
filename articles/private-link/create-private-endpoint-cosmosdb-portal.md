---
title: Azure 개인 링크가 있는 Azure 코스모스 계정에 연결
description: 프라이빗 엔드포인트를 만들어 VM에서 Azure Cosmos 계정에 안전하게 액세스하는 방법을 알아봅니다.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252593"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Azure 개인 링크를 사용하여 Azure Cosmos 계정에 비공개로 연결

Azure 개인 끝점은 Azure에서 개인 링크에 대 한 기본 구성 요소입니다. 이를 통해 가상 시스템(VM)과 같은 Azure 리소스가 개인 링크 리소스와 비공개로 통신할 수 있습니다.

이 문서에서는 Azure 가상 네트워크에서 VM을 만드는 방법과 Azure 포털을 사용하여 개인 끝점을 사용하는 Azure Cosmos 계정을 만드는 방법을 알아봅니다. 그런 다음 VM에서 Azure Cosmos 계정에 안전하게 액세스할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure 포털에 로그인합니다.](https://portal.azure.com)

## <a name="create-a-vm"></a>VM 만들기

## <a name="virtual-network-and-parameters"></a>가상 네트워크 및 매개 변수

이 섹션에서는 개인 링크 리소스(이 예제의 Azure Cosmos 계정)에 액세스하는 데 사용되는 VM을 호스트하는 가상 네트워크와 서브넷을 만듭니다.

이 섹션에서는 단계의 다음 매개 변수를 아래 정보로 바꿔야 합니다.

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<리소스 그룹 이름>**  | myResourceGroup|
| **\<가상 네트워크 이름>** | myVirtualNetwork         |
| **\<지역 이름>**          | 미국 중서부     |
| **\<IPv4 주소 공간>**   | 10.1.0.0\16          |
| **\<서브넷 이름>**          | mySubnet        |
| **\<서브넷 주소 범위>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>가상 머신 만들기

1. Azure 포털에서 화면의 왼쪽 위쪽에서 > 리소스**계산** > **가상 컴퓨터** **만들기를**선택합니다.

1. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup을**선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | *myVm*을 입력합니다. |
    | 지역 | **WestCentralUS**를 선택합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다**를 그대로 둡니다. |
    | 이미지 | **Windows Server 2019 Datacenter**를 선택합니다. |
    | 크기 | 기본값인 **표준 DS1 v2**를 그대로 둡니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 사용자가 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
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
    ||

1. **검토 + 만들기를**선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 유효성 검사 전달 메시지가 표시되면 **에서 만들기를** **선택합니다.**

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos 계정 만들기

Azure [코스모스 SQL API 계정을](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)만듭니다. 간단히 하기 위해 다른 리소스("WestCentralUS")와 동일한 지역에 Azure Cosmos 계정을 만들 수 있습니다.

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Azure Cosmos 계정에 대한 비공개 엔드포인트 만들기

연결된 아티클의 Azure 포털 섹션을 사용하여 [개인 링크 만들기에](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) 설명된 대로 Azure Cosmos 계정에 대한 개인 링크를 만듭니다.

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 *myVm* VM에 연결합니다.

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드를**선택합니다. Azure에서 원격 데스크톱 프로토콜(*.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. 다운로드한 *.rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 을** > 선택해야 할 수**있습니다다른 계정 사용**, VM을 만들 때 입력한 자격 증명을 지정합니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>VM에서 Azure 코스모스 계정에 비공개로 액세스

이 섹션에서는 비공개 끝점을 사용하여 Azure Cosmos 계정에 개인적으로 연결합니다. 

1. IP 주소와 DNS 매핑을 포함하려면 가상 컴퓨터 *myVM에*로그인하여 `c:\Windows\System32\Drivers\etc\hosts` 파일을 열고 이전 단계의 DNS 정보를 다음 형식으로 포함합니다.

   [개인 IP 주소] [계정 끝점].documents.azure.com

   **예제:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. *myVM의*원격 데스크톱에서 [Microsoft Azure 저장소 탐색기를](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)설치합니다.

1. 마우스 오른쪽 버튼으로 **코스모스 DB 계정(미리 보기)을** 선택합니다.

1. **코스모스 DB에 연결 선택합니다.**

1. **API**를 선택합니다.

1. 이전에 복사한 정보를 붙여넣은 연결 문자열을 입력합니다.

1. **다음**을 선택합니다.

1. **연결**을 선택합니다.

1. *mycosmosaccount에서*Azure 코스모스 데이터베이스 및 컨테이너를 찾아보십시오.

1. (선택사항) *mycosmosaccount에*새 항목을 추가합니다.

1. *myVM에*원격 데스크톱 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

프라이빗 엔드포인트, Azure Cosmos 계정 및 VM을 사용하여 완료되면 리소스 그룹과 포함된 모든 리소스를 삭제합니다. 

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력하고 검색 결과에서 *myResourceGroup*을 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. **리소스 그룹 이름을 입력하기** 위해 *myResourceGroup을* 입력하고 **삭제를**선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크, Azure Cosmos 계정 및 개인 끝점에서 VM을 만들었습니다. 인터넷에서 VM에 연결하고 개인 링크를 사용하여 Azure Cosmos 계정에 안전하게 통신합니다.

* 개인 끝점에 대한 자세한 내용은 [Azure 개인 끝점이란 무엇입니까?](private-endpoint-overview.md)

* Azure Cosmos DB와 함께 사용할 때 개인 끝점의 제한에 대해 자세히 알아보려면 [Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) 와 함께 Azure 개인 링크를 참조하세요.
