---
title: VM에 대한 라우팅 기본 설정 구성 - Azure Portal
description: Azure Portal을 통해 라우팅 기본 설정을 선택하여 공용 IP 주소가 있는 VM을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: af3d9e9fcf0dad6a5e51a3db87b63567d701970e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84687993"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Azure Portal을 사용하여 VM에 대한 라우팅 기본 설정 구성

이 문서에서는 가상 머신의 라우팅 기본 설정을 구성하는 방법을 보여줍니다. 라우팅 기본 설정 옵션으로 **인터넷**을 선택하면 VM의 인터넷 바인딩 트래픽이 ISP 네트워크를 통해 라우팅됩니다. 기본 라우팅은 Microsoft 글로벌 네트워크를 통해 전달됩니다.

이 문서에서는 Azure Portal을 사용하여 공용 인터넷을 통해 트래픽을 라우팅하도록 설정된 공용 IP로 가상 머신을 만드는 방법을 보여줍니다.

> [!IMPORTANT]
> 라우팅 기본 설정은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="register-the-feature-for-your-subscription"></a>구독에 대한 기능 등록
라우팅 기본 설정 기능은 현재 미리 보기로 제공됩니다. 다음과 같이 Azure PowerShell을 사용하여 구독에 대한 기능을 등록해야 합니다.
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature ProviderNamespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://preview.portal.azure.com/)에 로그인합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위 모서리에 있는 **+ 리소스 만들기**를 선택합니다.
2. **컴퓨팅**을 선택한 다음, **Windows Server 2016 VM** 또는 다른 운영 체제를 선택합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |속성|myVM|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    |Subscription| 구독을 선택합니다.|
    |Resource group| **기존 항목 사용**을 선택하고, **myResourceGroup**을 선택합니다.|
    |위치| **미국 동부**를 선택합니다.|

4. VM에 대한 크기를 선택한 다음, **선택**을 선택합니다.
5. **네트워킹** 탭에서 **공용 IP 주소**에 대해 **새로 만들기**를 클릭합니다.
6. 다음 그림과 같이 *myPublicIpAddress*를 입력하고 SKU를 **표준**으로 선택한 후, 라우팅 기본 설정 **인터넷**을 선택한 다음, **확인**을 누릅니다.

   ![고정 선택](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. **공용 인바운드 포트 선택** 아래에서 포트를 선택하거나 선택하지 않습니다. 인터넷에서 Windows Server 가상 머신에 원격 액세스할 수 있도록 포털 3389가 선택됩니다. 인터넷에서 포트 3389를 여는 것은 프로덕션 워크로드에 권장되지 않습니다.

   ![포트 선택](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. 나머지 기본 설정을 수락하고 **확인**을 선택합니다.
8. **요약** 페이지에서 **만들기**를 선택합니다. 가상 머신을 배포하는 데 몇 분 정도 걸립니다.
9. 가상 머신이 배포되면 포털의 맨 위에 있는 검색 상자에 *myPublicIpAddress*를 입력합니다. 검색 결과에 **myPublicIpAddress**가 표시되면 선택합니다.
10. 할당된 공용 IP 주소 및 다음 그림에 나와 있는 것처럼 **myVM** 가상 머신에 주소가 할당된 것을 볼 수 있습니다.

    ![공용 IP 주소 보기](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. **네트워킹**을 선택한 후, NIC **mynic**를 클릭한 다음, 공용 IP 주소를 선택하여 라우팅 기본 설정이 **인터넷**으로 할당되었는지 확인합니다.
    ![공용 IP 주소 보기](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계
- [라우팅 기본 설정을 사용하는 공용 IP](routing-preference-overview.md)에 대해 자세히 알아봅니다.
- Azure에서 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
