---
title: PaaS 리소스에 대한 액세스 제한 - 자습서 - Azure Portal
description: 이 자습서에서는 Azure Portal을 사용하여 가상 네트워크 서비스 엔드포인트로 Azure Storage 및 Azure SQL Database와 같은 Azure 리소스에 대한 네트워크 액세스를 제한하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: cb3a4b6a726ee9163582b15586c65fc750712c63
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368296"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 가상 네트워크 서비스 엔드포인트로 PaaS 리소스에 대한 네트워크 액세스 제한

가상 네트워크 서비스 엔드포인트를 사용하면 일부 Azure 서비스 리소스에 대한 네트워크 액세스를 가상 네트워크 서브넷으로 제한할 수 있습니다. 리소스에 대한 인터넷 액세스를 제거할 수도 있습니다. 서비스 엔드포인트는 가상 네트워크에서 지원되는 Azure 서비스로의 직접 연결을 제공하므로 가상 네트워크의 프라이빗 주소 공간을 사용하여 Azure 서비스에 액세스할 수 있습니다. 서비스 엔드포인트를 통해 Azure 리소스에 도달하는 트래픽은 항상 Microsoft Azure 백본 네트워크에 유지됩니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 하나의 서브넷이 있는 가상 네트워크 만들기
> * 서브넷을 추가하고 서비스 엔드포인트를 사용하도록 설정
> * Azure 리소스를 만들고 서브넷에서만 네트워크 액세스 허용
> * 각 서브넷에 VM(가상 머신) 배포
> * 서브넷에서 리소스에 대한 액세스 확인
> * 서브넷 및 인터넷에서 리소스에 대한 액세스가 거부되는지 확인

원하는 경우 [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) 또는 [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md)을 사용하여 이 자습서를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal ( https://portal.azure.com ) 에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기** 를 선택합니다.
2. **네트워킹** 을 선택한 다음, **가상 네트워크** 를 선택합니다.
3. **+ 추가** 를 클릭하고, 다음 정보를 입력합니다. 

   |설정|값|
   |----|----|
   |구독| 구독 선택|
   |Resource group | **새로 만들기** 를 선택하고 *myResourceGroup* 을 입력합니다.|
   |이름| *myVirtualNetwork* 를 입력합니다. |
   |지역| **(미국) 미국 동부** 를 선택합니다. |

   ![가상 네트워크에 대한 기본 정보 입력](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. **다음: IP 주소 >** 를 클릭합니다.
   
   |설정|값|
   |----|----|
   |IPv4 주소 공간| 기본값으로 둡니다. |
   |서브넷 이름| **기본값** 을 클릭하고, 이름을 "기본값"에서 "퍼블릭"으로 변경합니다.|
   |서브넷 주소 범위| 기본값으로 둡니다.|

5. **다음: 보안 >** 을 클릭합니다. 
   
   |설정|값|
   |----|----|   
   |BastionHost| 사용 안 함|
   |DDoS 보호| 사용 안 함|
   |방화벽| 사용 안 함|

6. 완료되면 **검토 및 만들기** 를 클릭합니다.
7. 유효성 검사가 통과되면 **만들기** 를 클릭합니다.
8. 배포가 완료될 때까지 기다린 다음, **리소스로 이동** 을 클릭하거나 다음 섹션으로 이동합니다. 

## <a name="enable-a-service-endpoint"></a>서비스 엔드포인트 사용

서비스 엔드포인트는 서비스 단위, 서브넷 단위로 활성화됩니다. 서브넷을 만들고 서비스 엔드포인트를 서브넷에 사용하도록 설정하려면 다음을 수행합니다.

1. 아직 가상 네트워크 리소스 페이지에 있지 않은 경우 포털 위쪽의 **리소스, 서비스 및 문서 검색** 상자에서 새로 만든 네트워크를 검색하고, *myVirtualNetwork* 를 입력하고, 목록에서 선택할 수 있습니다.
2. 다음과 같이 **설정** 메뉴(왼쪽)에서 **서브넷** 을 선택한 다음, **+ 서브넷** 을 선택합니다.

    ![서브넷 추가](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. **서브넷 추가** 에서 다음 정보를 선택하거나 입력한 다음, **확인** 을 선택합니다.

    |설정|값|
    |----|----|
    |속성| 프라이빗 |
    |주소 범위| 기본값으로 둡니다.|
    |서비스 엔드포인트| **Microsoft.Storage** 를 선택합니다.|
    |서비스 엔드포인트 정책 | 기본값을 0으로 둡니다. |

> [!CAUTION]
> 리소스를 포함한 기존 서브넷에 서비스 엔드포인트를 사용하도록 설정하기 전에 [서브넷 설정 변경](virtual-network-manage-subnet.md#change-subnet-settings)을 참조하세요.

4. **저장** 을 클릭한 다음, 오른쪽에 있는 [서브넷] 창을 닫습니다. 새로 만든 서브넷이 목록에 표시됩니다.

## <a name="restrict-network-access-for-a-subnet"></a>서브넷에 대한 네트워크 액세스 제한

기본적으로 서브넷의 모든 가상 머신 인스턴스는 모든 리소스와 통신할 수 있습니다. 네트워크 보안 그룹을 만들고 서브넷에 연결하여 서브넷에 있는 모든 리소스 간의 통신을 제한할 수 있습니다.

1. Azure Portal의 왼쪽 위 모서리에서 **모든 서비스** 를 선택합니다.
2. **네트워킹** 을 선택한 다음, **네트워크 보안 그룹** 을 선택(또는 검색)합니다.
3. **네트워크 보안 그룹** 페이지에서 **+ 추가** 를 클릭합니다.
4. 다음 정보를 입력합니다. 

    |설정|값|
    |----|----|
    |구독| 구독 선택|
    |리소스 그룹 | 목록에서 *myResourceGroup* 을 선택합니다.|
    |이름| **myNsgPrivate** 을 입력합니다. |
    |위치| **미국 동부** 를 선택합니다. |

5. **검토 + 만들기** 를 클릭하고, 유효성 검사가 통과되면 **만들기** 를 클릭합니다.
6. 네트워크 보안 그룹이 만들어지면 **리소스로 이동** 을 클릭하거나 *myNsgPrivate* 을 검색합니다.
7. 왼쪽의 **설정** 아래에서 **아웃바운드 보안 규칙** 을 선택합니다.
8. **+추가** 를 선택합니다.
9. Azure Storage 서비스에 대해 아웃바운드 통신을 허용하는 규칙을 만듭니다. 다음 정보를 입력하거나 선택한 다음, **추가** 를 선택합니다.

    |설정|값|
    |----|----|
    |원본| **VirtualNetwork** 를 선택합니다. |
    |원본 포트 범위| * |
    |대상 | **서비스 태그** 를 선택합니다.|
    |대상 서비스 태그 | **스토리지** 를 선택합니다.|
    |대상 포트 범위| 기본값을 *8080* 으로 둡니다. |
    |프로토콜|모두|
    |작업|허용|
    |우선 순위|100|
    |이름|이름을 **Allow-Storage-All** 로 바꿉니다.|

10. 인터넷에 대한 통신을 거부하는 다른 아웃바운드 보안 규칙을 만듭니다. 이 규칙은 아웃바운드 인터넷 통신을 허용하는 모든 네트워크 보안 그룹의 기본 규칙을 재정의합니다. 다음 값을 사용하여 위의 6~9단계를 완료합니다.

    |설정|값|
    |----|----|
    |원본| **VirtualNetwork** 를 선택합니다. |
    |원본 포트 범위| * |
    |대상 | **서비스 태그** 를 선택합니다.|
    |대상 서비스 태그| **인터넷** 을 선택합니다.|
    |대상 포트 범위| * |
    |프로토콜|모두|
    |작업|**기본값을 *거부* 로 변경합니다.** |
    |우선 순위|110|
    |속성|*Deny-Internet-All* 로 변경합니다.|

11. 어디서나 서브넷에 대한 RDP(원격 데스크톱 프로토콜) 트래픽을 허용하는 *인바운드 보안 규칙* 을 만듭니다. 이 규칙은 인터넷의 모든 인바운드 트래픽을 거부하는 기본 보안 규칙을 재정의합니다. 이후 단계에서 연결을 테스트할 수 있도록 서브넷에대 한 원격 데스크톱 연결이 허용됩니다. 
12. **설정** 아래에서 **인바운드 보안 규칙** 을 선택합니다.
13. **+ 추가** 를 선택하고, 다음 값을 사용합니다.

    |설정|값|
    |----|----|
    |원본| 모두 |
    |원본 포트 범위| * |
    |대상 | **VirtualNetwork** 를 선택합니다.|
    |대상 포트 범위| *3389* 로 변경합니다. |
    |프로토콜|모두|
    |작업|허용|
    |우선 순위|120|
    |속성|*Allow-RDP-All* 로 변경합니다.|

   >[!WARNING] 
   > 3389 RDP 포트는 인터넷에 공개되어 있습니다. 이는 테스트용으로만 추천됩니다. *프로덕션 환경* 의 경우 VPN 또는 프라이빗 연결을 사용하는 것이 좋습니다.

1.  **설정** 에서 **서브넷** 을 선택합니다.
2.  **+ 연결** 을 클릭합니다.
3.  **가상 네트워크** 아래에서 **myVirtualNetwork** 를 선택합니다.
4.  **서브넷** 아래에서 **프라이빗** 을 선택한 다음, **확인** 을 선택합니다.

## <a name="restrict-network-access-to-a-resource"></a>리소스에 대한 네트워크 액세스 제한

서비스 엔드포인트에 사용하도록 설정된 Azure 서비스를 통해 만든 리소스에 대한 네트워크 액세스를 제한하는 데 필요한 단계는 서비스에 따라 달라집니다. 각 서비스에 대한 특정 단계는 개별 서비스의 설명서를 참조하세요. 이 자습서의 나머지 부분에는 Azure Storage 계정에 대한 네트워크 액세스를 제한하는 단계가 예제로 포함되어 있습니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

1. Azure Portal의 왼쪽 위에서 **+ 리소스 만들기** 를 선택합니다.
2. 검색 창에서 "스토리지 계정"을 입력하고, 드롭다운 메뉴에서 선택합니다.
3. **+ 추가** 를 클릭합니다.
4. 다음 정보를 입력합니다.

    |설정|값|
    |----|----|
    |구독| 구독 선택|
    |리소스 그룹| *myResourceGroup* 을 선택합니다.|
    |스토리지 계정 이름| 모든 Azure 위치에서 고유한 이름을 입력합니다. 이름은 3~24자 사이여야 하고 숫자와 소문자만 사용할 수 있습니다.|
    |위치| **(미국) 미국 동부** 를 선택합니다. |
    |성능|표준|
    |계정 종류| StorageV2(범용 v2)|
    |복제| LRS(로컬 중복 스토리지)|

5. **만들기 + 검토** 를 선택하고, 유효성 검사가 통과되면 **만들기** 를 클릭합니다. 

>[!NOTE] 
> 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

6. 스토리지 계정이 만들어지면 **리소스로 이동** 을 클릭합니다.

### <a name="create-a-file-share-in-the-storage-account"></a>스토리지 계정에 파일 공유 만들기

1. 스토리지 계정 개요 페이지로 이동합니다.
2. **파일 공유** 앱 아이콘을 선택한 다음, **+ 파일 공유** 를 클릭합니다.

    |설정|값|
    |----|----|
    |이름| my-file-share|
    |할당량| '최댓값으로 설정' |

   ![스토리지 계정](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. **만들기** 를 클릭합니다.
4. Azure 창에 파일 공유가 표시됩니다. 그렇지 않으면 **새로 고침** 을 클릭합니다.

### <a name="restrict-network-access-to-a-subnet"></a>서브넷에 대한 네트워크 액세스 제한

기본적으로 스토리지 계정은 인터넷을 비롯하여 네트워크에 있는 클라이언트의 네트워크 연결을 허용합니다. 인터넷 및 모든 가상 네트워크의 다른 모든 서브넷(*myVirtualNetwork* 가상 네트워크에 있는 *프라이빗* 서브넷 제외)으로부터의 네트워크 액세스를 제한할 수 있습니다. 서브넷에 대한 네트워크 액세스를 제한하려면 다음을 수행합니다.

1. 고유 이름의 스토리지 계정에 대한 **설정** 아래에서 **네트워킹** 을 선택합니다.
2. **선택한 네트워크** 를 선택합니다.
3. **+ 기존 가상 네트워크 추가** 를 선택합니다.
4. **네트워크 추가** 에서 다음 값을 선택한 다음, **추가** 를 선택합니다.

    |설정|값|
    |----|----|
    |구독| 구독 선택|
    |가상 네트워크| **myVirtualNetwork**|
    |서브넷| **개인**|

    ![스크린샷은 지정된 값을 입력할 수 있는 네트워크 추가 창을 보여줍니다.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. **추가** 를 클릭한 다음,즉시 **저장** 아이콘을 클릭하여 변경 내용을 저장합니다.
6. 다음 이미지와 같이 스토리지 계정에 대한 **설정** 아래에서 **액세스 키** 를 선택합니다.

      ![스크린샷은 키를 가져올 수 있는 설정에서 선택한 액세스 키를 보여줍니다.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. 이후 단계에서 파일 공유를 VM의 드라이브 문자에 매핑할 때 key1을 수동으로 입력해야 하므로 **키 표시** 를 클릭하고 **키** 값을 적어둡니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

스토리지 계정에 대한 네트워크 액세스를 테스트하려면 각 서브넷에 VM을 배포합니다.

### <a name="create-the-first-virtual-machine"></a>첫 번째 가상 머신 만들기

1. "리소스 검색 . . ." 창에서 **가상 머신** 을 검색합니다.
2. **+ 추가 > 가상 머신** 을 차례로 선택합니다. 
3. 다음 정보를 입력합니다.

   |설정|값|
   |----|----|
   |구독| 구독 선택|
   |리소스 그룹| 이전에 만든 **myResourceGroup을 선택합니다.|
   |가상 머신 이름| *myVmPublic* 을 입력합니다.|
   |지역 | (미국) 미국 동부
   |가용성 옵션| 가용성 영역|
   |가용성 영역 | 1 |
   |이미지 | Windows Server 2019 Datacenter – 1세대 |
   |크기 | 사용하려는 VM 인스턴스 크기를 선택합니다. |
   |사용자 이름|선택한 사용자 이름을 입력합니다.|
   |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
   |공용 인바운드 포트 | 선택한 포트 허용 |
   |인바운드 포트 선택 | *RDP(3389)* 로 설정된 기본값을 그대로 둡니다. |

   ![가상 네트워크 선택](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. **네트워킹** 탭을 선택한 다음, **myVirtualNetwork** 를 선택합니다. 
5. *퍼블릭* 서브넷을 선택합니다.
6. **NIC 네트워크 보안 그룹** 아래에서 **고급** 을 선택합니다. 포털에서 3389 포트를 허용하는 네트워크 보안 그룹을 자동으로 만듭니다. 3389 포트는 이후 단계에서 가상 머신에 연결하기 위해 열어야 합니다. 

   ![가상 머신에 대한 기본 정보 입력](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. **검토 및 만들기** 를 선택하고, **만들기** 를 선택한 다음, 배포가 완료될 때까지 기다립니다.
8. **리소스로 이동** 을 클릭하거나 **홈 > 가상 머신** 페이지를 열고, 방금 만든 VM(*myVmPublic*)을 선택합니다. 그러면 해당 VM이 시작됩니다.

### <a name="create-the-second-virtual-machine"></a>두 번째 가상 머신 만들기

1. 1~8단계를 다시 완료하지만, 3단계에서 가상 머신의 이름을 *myVmPrivate* 으로 지정하고 **퍼블릭 인바운드 포트** 를 "없음"으로 설정합니다. 
2. 4~5단계에서 **프라이빗** 서브넷을 선택합니다.

>[!NOTE]
> **NIC 네트워크 보안 그룹**  및 **퍼블릭 인바운드 포트** 설정은 "모든 퍼블릭 인터넷 트래픽이 기본적으로 차단됩니다."라고 시작하는 파란색 확인 창을 포함하여 아래에 표시된 이미지를 미러링해야 합니다.

   ![프라이빗 가상 머신 만들기](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. **검토 및 만들기** 를 선택하고, **만들기** 를 선택한 다음, 배포가 완료될 때까지 기다립니다. 

>[!WARNING]
> 배포가 완료될 때까지 다음 단계로 진행하지 마세요.

4. 아래에 표시된 확인 창이 표시될 때까지 기다린 다음, **리소스로 이동** 을 클릭합니다.

   ![프라이빗 가상 머신 만들기 확인 창](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>스토리지 계정에 대한 액세스 확인

1. *myVmPrivate* VM이 만들어지면 **리소스로 이동** 을 클릭합니다. 
2. **연결 > RDP** 를 차례로 선택하여 VM에 연결합니다.
3. **연결** 단추를 선택하면 원격 데스크톱 프로토콜(.rdp) 파일이 만들어집니다. **RDP 파일 다운로드** 를 클릭하여 컴퓨터에 다운로드합니다.  
4. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결** 을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **다른 옵션 선택**, **다른 계정 사용** 을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수 있습니다. 이메일 필드에 대해 이전에 지정한 "관리자 계정: 사용자 이름" 자격 증명을 입력합니다. 
5. **확인** 을 선택합니다.
6. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속** 을 선택하여 연결을 계속합니다. 다음과 같이 VM이 시작되는 것을 볼 수 있습니다.

   ![실행 중인 프라이빗 가상 머신 표시](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. VM 창에서 PowerShell CLI 인스턴스를 엽니다.
8. 아래 스크립트를 사용하여 PowerShell을 통해 Azure 파일 공유를 Z 드라이브에 매핑합니다. 다음 명령을 실행하기 전에, `<storage-account-key>` 및 두 개의 `<storage-account-name>` 필드를 제공한 값 및 이전 [스토리지 계정 만들기](#create-a-storage-account)의 "d로 바꿉니다.

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell에서 다음 예제 출력과 비슷한 출력을 반환합니다.

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Azure 파일 공유가 Z 드라이브에 매핑되었습니다.

9.   *myVmPrivate* VM에 대한 원격 데스크톱 세션을 닫습니다.

## <a name="confirm-access-is-denied-to-storage-account"></a>스토리지 계정에 대한 액세스가 거부되는지 확인

1. 포털 맨 위에 있는 *리소스, 서비스 및 문서 검색* 상자에 **myVirtualNetwork** 를 입력합니다.
2. 검색 결과에 **myVmPublic** 이 표시되면 선택합니다.
3. *myVmPublic* VM에 대한 [스토리지 계정에 대한 액세스 확인](#confirm-access-to-storage-account)에서 위의 1~8단계를 완료합니다.

   잠시 기다리면 `New-PSDrive : Access is denied` 오류가 발생합니다. *myVmPublic* VM이 *Public* 서브넷에 배포되었으므로 액세스가 거부되었습니다. *공용* 서브넷에는 Azure Storage에 사용하도록 설정된 서비스 엔드포인트가 없습니다. 스토리지 계정은 *공용* 서브넷이 아닌 *프라이빗* 서브넷으로부터의 네트워크 액세스만을 허용합니다.

4. *myVmPublic* VM에 대한 원격 데스크톱 세션을 닫습니다.
5. Azure Portal로 돌아가 이전에 만든 고유 이름의 스토리지 계정으로 이동합니다.
6. [파일 서비스] 아래에서 이전에 만든 **파일 공유**(*my-file-share*)를 선택합니다.
7. 다음과 같은 오류 메시지가 나타납니다.

   ![액세스가 거부됨 오류](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> 컴퓨터가 *MyVirtualNetwork* 가상 네트워크의 *프라이빗* 서브넷에 없기 때문에 액세스가 거부되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우, 리소스 그룹 및 이 그룹에 포함된 리소스를 모두 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup* 을 입력합니다. 검색 결과에 **myResourceGroup** 이 보이면 선택합니다.
2. **리소스 그룹 삭제** 를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *myResourceGroup* 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 네트워크 서브넷에 대해 서비스 엔드포인트를 사용하도록 설정했습니다. 여러 Azure 서비스에서 배포된 리소스에 서비스 엔드포인트를 사용하도록 설정할 수 있음을 알았습니다. Azure Storage 계정을 만들고 스토리지 계정에 대한 네트워크 액세스를 가상 네트워크 서브넷 내의 리소스로만 제한했습니다. 서비스 엔드포인트에 대한 자세한 내용은 [서비스 엔드포인트 개요](virtual-network-service-endpoints-overview.md) 및 [서브넷 관리](virtual-network-manage-subnet.md)를 참조하세요.

계정에 여러 개의 가상 네트워크가 있는 경우, 각 가상 네트워크 내의 리소스가 서로 통신할 수 있도록 두 개의 가상 네트워크를 함께 연결하는 것이 좋습니다. 가상 네트워크를 연결하는 방법을 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [가상 네트워크 연결](./tutorial-connect-virtual-networks-portal.md)
