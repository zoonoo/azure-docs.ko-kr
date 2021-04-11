---
title: 자습서 - 프라이빗 클라우드에 액세스
description: Azure VMware Solution 프라이빗 클라우드에 액세스하는 방법 알아보기
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f2af1cffda08bf4b9c62e63f32d36cc9bbd7024a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494396"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>자습서: Azure VMware Solution 프라이빗 클라우드에 액세스

Azure VMware Solution을 사용하면 온-프레미스 vCenter에서 프라이빗 클라우드를 관리할 수 없습니다. 점프 박스를 통해 Azure VMware Solution vCenter 인스턴스에 연결해야 합니다. 

이 자습서에서는 [이전 자습서](tutorial-configure-networking.md)에서 만든 리소스 그룹에 점프 상자를 만들고 Azure VMware Solution vCenter에 로그인합니다. 이 점프 상자는 만든 것과 동일한 가상 네트워크에 있는 Windows VM(가상 머신)입니다.  vCenter와 NSX Manager 모두에 액세스할 수 있습니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure VMware Solution vCenter에 액세스할 수 있는 Windows 가상 머신을 만듭니다.
> * 이 가상 머신에서 vCenter에 로그인

## <a name="create-a-new-windows-virtual-machine"></a>새 Windows 가상 머신 만들기

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>프라이빗 클라우드의 로컬 vCenter에 연결

1. 점프 상자에서 클라우드 관리자 사용자 이름을 사용하여 VMware vCenter SSO로 vSphere 클라이언트에 로그인하고 사용자 인터페이스가 성공적으로 표시되는지 확인합니다.

1. Azure Portal에서 프라이빗 클라우드를 선택한 다음, **관리** > **ID** 를 선택합니다. 

   프라이빗 클라우드 vCenter 및 NSX-T Manager에 대한 URL 및 사용자 자격 증명이 표시됩니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="프라이빗 클라우드 vCenter와 NSX 관리자의 URL 및 자격 증명을 표시합니다." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. 이전 단계에서 만든 VM으로 이동하여 가상 머신에 연결합니다. 

   VM에 연결하는 데 도움이 필요한 경우 자세한 내용은 [가상 머신에 연결](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)을 참조하세요.

1. Windows VM에서 브라우저를 열고, 두 개의 탭에서 vCenter 및 NSX-T 관리자 URL로 이동합니다. 

1. vCenter 탭에서 이전 단계의 `cloudadmin@vmcp.local` 사용자 자격 증명을 입력합니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="프라이빗 클라우드 vCenter에 로그인합니다." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter 포털입니다." border="true":::

1. 브라우저의 두 번째 탭에서 NSX-T 관리자에 로그인합니다.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="브라우저의 두 번째 탭에서 NSX-T 관리자에 로그인합니다." border="true":::



## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * vCenter에 연결하는 데 사용할 Windows 가상 머신 만들기
> * 가상 머신에서 vCenter에 로그인

가상 네트워크를 만들어 프라이빗 클라우드 클러스터에 대한 로컬 관리를 설정하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Virtual Network 만들기](tutorial-configure-networking.md)


