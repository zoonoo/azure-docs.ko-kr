---
title: '자습서: Azure Bastion 호스트 만들기: Windows VM: 포털'
description: 이 문서에서는 Azure Bastion 호스트를 만들고 Windows VM에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: daf3b6c36b191f52f0d7ac7638ef59695325f541
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078547"
---
# <a name="tutorial-create-an-azure-bastion-host-and-connect-to-a-windows-vm-through-a-browser"></a>자습서: Azure Bastion 호스트를 만들고 브라우저를 통해 Windows VM에 연결

이 자습서에서는 Azure Bastion 및 Azure Portal을 사용하여 브라우저를 통해 가상 머신에 연결하는 방법을 보여줍니다. Azure Portal에서 Bastion을 가상 네트워크에 배포합니다. Bastion을 배포한 후에는 Azure Portal을 사용하여 개인 IP 주소를 통해 VM에 연결합니다. VM에는 공용 IP 주소 또는 특정 소프트웨어가 필요하지 않습니다. 서비스가 프로비저닝되면 동일한 가상 네트워크의 모든 가상 머신에서 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion에 대한 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * VNet에 대한 베스천 호스트 만들기
> * Windows 가상 머신에 연결

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* 가상 네트워크
* 가상 네트워크의 Windows 가상 머신
* 필요한 역할은 다음과 같습니다.
  * 가상 머신에 대한 읽기 권한자 역할
  * 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
  * Azure Bastion 리소스에 대한 읽기 권한자 역할

* 포트: Windows VM에 연결하려면 Windows VM에서 다음 포트가 열려 있어야 합니다.
  * 인바운드 포트: RDP(3389)

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion 호스트 만들기

이 섹션은 VNet에서 베스천 개체를 만드는 데 도움이 됩니다. VNet의 VM에 안전하게 연결하려면 다음을 수행해야 합니다.

1. **홈** 페이지에서 **+ 리소스 만들기**를 선택합니다.
1. **새로 만들기** 페이지에서 검색 상자에 **Bastion**을 입력한 다음, **Enter** 키를 선택하여 검색 결과로 이동합니다. **Bastion**에 대한 검색 결과에서 게시자가 Microsoft인지 확인합니다.
1. **만들기**를 선택합니다.
1. **Bastion 만들기** 페이지에서 새 Bastion 리소스를 구성합니다.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="베스천 호스트 만들기" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **구독**: 새 Bastion 리소스를 만드는 데 사용할 Azure 구독입니다.
    * **리소스 그룹**: 새 Bastion 리소스가 만들어질 Azure 리소스 그룹입니다. 기존 리소스 그룹이 없는 경우 새 리소스 그룹을 만들 수 있습니다.
    * **이름**: 새 Bastion 리소스의 이름입니다.
    * **지역**: 리소스가 만들어질 Azure 공용 지역입니다.
    * **가상 네트워크**: Bastion 리소스를 만들 가상 네트워크입니다. 이 과정에서 포털에서 새 가상 네트워크를 만들어도 되고, 아니면 기존 가상 네트워크를 사용해도 됩니다. 기존 가상 네트워크를 사용하는 경우 기존 가상 네트워크에 Bastion 서브넷 요구 사항을 수용하기에 충분한 여유 주소 공간이 있는지 확인합니다. 드롭다운에 가상 네트워크가 보이지 않는 경우 올바른 리소스 그룹을 선택했는지 확인합니다.
    * **서브넷**: 가상 네트워크를 만들거나 선택하면 서브넷 필드가 나타납니다. 가상 네트워크의 이 서브넷에 새 Bastion 호스트 리소스가 배포됩니다. 이 서브넷은 Bastion 호스트 전용입니다. **서브넷 구성 관리**를 선택하고 Azure Bastion 서브넷을 만듭니다. **+서브넷**을 선택하고 다음 지침에 따라 서브넷을 만듭니다.

         * 서브넷 이름은 **AzureBastionSubnet**이어야 합니다.
         * 서브넷은 /27 이상이어야 합니다.

      추가 필드는 입력하지 않아도 됩니다. **확인**을 선택한 다음, 페이지 맨 위에서 **Bastion 만들기**를 선택하여 Bastion 구성 페이지로 돌아갑니다.
    * **공용 IP 주소**: 포트 443을 통해 RDP/SSH에 액세스할 Bastion 리소스의 공용 IP입니다. 새 공용 IP를 만듭니다. 공용 IP 주소는 만들려는 Bastion 리소스와 동일한 지역에 있어야 합니다. 이 IP 주소는 우리가 연결하려는 VM과 아무 관련이 없습니다. Bastion 호스트 리소스에 사용할 공용 IP입니다.
    * **공용 IP 주소 이름**: 공용 IP 주소 리소스의 이름입니다. 이 자습서에서는 기본 설정을 그대로 사용해도 됩니다.
    * **공용 IP 주소 SKU**: 이 설정은 기본적으로 **표준**으로 미리 채워져 있습니다. Azure Bastion은 표준 공용 IP SKU만 사용/지원합니다.
    * **할당**: 이 설정은 기본적으로 **정적**으로 미리 채워져 있습니다.

1. 설정 지정을 마쳤으면, **검토 + 만들기**를 선택합니다. 그러면 값의 유효성이 검사됩니다. 유효성 검사를 통과하면 Bastion 리소스를 만들 수 있습니다.
1. **만들기**를 선택합니다.
1. 배포가 진행되고 있음을 알리는 메시지가 표시됩니다. 리소스가 생성되면 이 페이지에 상태가 표시됩니다. Bastion 리소스가 생성되고 배포될 때까지 약 5분 정도 걸립니다.

## <a name="connect-to-a-vm"></a>VM에 연결

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 해당 리소스 그룹이 보이면 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다.
1. **리소스 그룹 이름 입력**에 해당 리소스 그룹의 이름을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Bastion 호스트를 만들고 가상 네트워크에 연결한 다음, Windows VM에 연결했습니다. Azure Bastion 서브넷에서 네트워크 보안 그룹을 사용하도록 선택할 수 있습니다. 이렇게 하려면 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [NSG 사용](bastion-nsg.md)
