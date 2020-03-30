---
title: Azure 프라이빗 엔드포인트를 사용하여 스토리지 계정에 비공개로 연결
description: 프라이빗 엔드포인트를 사용하여 Azure의 저장소 계정에 개인적으로 연결하는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8c76333d5a2be8a2c589dbe54389b023fef34854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252523"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Azure 프라이빗 엔드포인트를 사용하여 스토리지 계정에 비공개로 연결
Azure 개인 끝점은 Azure에서 개인 링크에 대 한 기본 구성 요소입니다. 이를 통해 가상 시스템(VM)과 같은 Azure 리소스가 개인 링크 리소스와 비공개로 통신할 수 있습니다.

이 빠른 시작에서는 Azure 포털을 사용하는 개인 끝점이 있는 저장소 계정인 Azure 가상 네트워크에서 VM을 만드는 방법을 배웁니다. 그런 다음 VM에서 저장소 계정에 안전하게 액세스할 수 있습니다.

> [!NOTE]
> 프라이빗 엔드포인트는 동일한 서브넷에 있는 서비스 엔드포인트와 함께 허용되지는 않습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vm"></a>VM 만들기
이 섹션에서는 개인 링크 리소스(이 예제의 저장소 계정)에 액세스하는 데 사용되는 VM을 호스트하기 위해 가상 네트워크와 서브넷을 만듭니다.

## <a name="virtual-network-and-parameters"></a>가상 네트워크 및 매개 변수

이 섹션에서는 개인 링크 리소스에 액세스하는 데 사용되는 VM을 호스트하는 가상 네트워크와 서브넷을 만듭니다.

이 섹션에서는 단계의 다음 매개 변수를 아래 정보로 바꿔야 합니다.

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<리소스 그룹 이름>**  | myResourceGroup |
| **\<가상 네트워크 이름>** | myVirtualNetwork          |
| **\<지역 이름>**          | 미국 중서부      |
| **\<IPv4 주소 공간>**   | 10.1.0.0\16          |
| **\<서브넷 이름>**          | mySubnet        |
| **\<서브넷 주소 범위>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>가상 머신 만들기

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
    ||

1. **검토 + 만들기를**선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 유효성 검사 전달 메시지가 표시되면 **에서 만들기를** **선택합니다.**

## <a name="create-your-private-endpoint"></a>프라이빗 엔드포인트 만들기
이 섹션에서는 개인 끝점을 사용하여 개인 저장소 계정을 만듭니다. 

1. Azure 포털화면 왼쪽 에서 > 리소스**저장소** > **저장소 계정** **만들기를**선택합니다.

1. 저장소 계정 만들기 - 기본 사항에서 다음 정보를 입력하거나 **선택합니다.**

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup을**선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 세부 정보** |  |
    | 스토리지 계정 이름  | *mystorage계정 .* 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    | 지역 | **WestCentralUS**를 선택합니다. |
    | 성능| 기본값인 **표준**을 그대로 둡니다. |
    | 계정 종류 | 기본 **저장소(범용 v2)를**그대로 둡니다. |
    | 복제 | **읽기 액세스 지리적 중복 저장소(RA-GRS)를**선택합니다. |
    |||
  
3. **다음 을 선택합니다.**
4. **저장소 계정 만들기 - 네트워킹,** 연결 방법에서 개인 **끝점을**선택합니다.
5. **저장소 계정 만들기 - 네트워킹에서**개인 **끝점 추가를**선택합니다. 
6. **비공개 끝점 만들기에서**다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup을**선택합니다. 이전 섹션에서 만든 것입니다.|
    |위치|**WestCentralUS**를 선택합니다.|
    |이름|*myPrivateEndpoint*를 입력합니다.  |
    |저장소 하위 리소스|기본 **Blob을**그대로 둡니다. |
    | **네트워킹** |  |
    | 가상 네트워크  | 리소스 그룹 *myResourceGroup에서 마이가상* *네트워크를* 선택합니다. |
    | 서브넷 | *mySubnet*을 선택합니다. |
    | **프라이빗 DNS 통합**|  |
    | 프라이빗 DNS 영역과 통합  | 기본 **예**그대로 둡니다. |
    | 프라이빗 DNS 영역  | **기본값(새로**privatelink.blob.core.windows.net. |
    |||
7. **확인**을 선택합니다. 
8. **검토 + 만들기를**선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
9. 유효성 검사 전달 메시지가 표시되면 **에서 만들기를** **선택합니다.** 
10. 방금 만든 저장소 계정 리소스로 이동합니다.
11. 왼쪽 콘텐츠 메뉴에서 **액세스 키를** 선택합니다.
12. key1에 대한 연결 문자열에서 **복사를** 선택합니다.
 
## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 *myVm* VM에 연결합니다.

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

## <a name="access-storage-account-privately-from-the-vm"></a>VM에서 비공개로 저장소 계정에 액세스

이 섹션에서는 비공개 끝점을 사용하여 저장소 계정에 개인적으로 연결합니다.

1. *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.
2. Enter `nslookup mystorageaccount.blob.core.windows.net` 다음과 유사한 메시지가 나타납니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)를 설치합니다.
4. 마우스 오른쪽 단추로 클릭한 **저장소 계정을** 선택합니다.
5. **Azure 저장소에 연결 을**선택합니다.
6. **연결 문자열 사용을**선택합니다.
7. **다음**을 선택합니다.
8. 이전에 복사한 정보를 붙여넣은 연결 문자열을 입력합니다.
9. **다음**을 선택합니다.
10. **연결**을 선택합니다.
11. mystorage계정에서 Blob 컨테이너 찾아보기 
12. (선택 사항) 폴더를 만들거나 *mystorageaccount에*파일을 업로드합니다. 
13. *myVM에*원격 데스크톱 연결을 닫습니다. 

저장소 계정에 액세스하는 추가 옵션:
- 마이크로소프트 Azure 스토리지 탐색기는 Windows, macOS 및 Linux에서 Azure 저장소 데이터로 시각적으로 작업할 수 있는 Microsoft의 독립 실행형 무료 앱입니다. 응용 프로그램을 설치하여 저장소 계정 콘텐츠를 개인적으로 탐색할 수 있습니다. 
 
- AzCopy 유틸리티는 Azure 저장소에 대한 고성능 스크립팅 가능한 데이터 전송을 위한 또 다른 옵션입니다. AzCopy를 사용하여 Blob, File 및 Table Storage 간에 데이터를 전송합니다. 


## <a name="clean-up-resources"></a>리소스 정리 
개인 엔드포인트, 저장소 계정 및 VM을 사용하여 완료되면 리소스 그룹과 포함된 모든 리소스를 삭제합니다. 
1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력하고 검색 결과에서 *myResourceGroup*을 선택합니다. 
2. **리소스 그룹 삭제**를 선택합니다. 
3. **리소스 그룹 이름을 입력하기** 위해 *myResourceGroup을* 입력하고 **삭제를**선택합니다. 

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 가상 네트워크 및 저장소 계정과 개인 엔드포인트에 VM을 만들었습니다. 인터넷에서 하나의 VM에 연결하고 개인 링크를 사용하여 저장소 계정에 안전하게 통신합니다. 개인 끝점에 대한 자세한 내용은 [Azure 개인 끝점이란 무엇입니까?](private-endpoint-overview.md)
