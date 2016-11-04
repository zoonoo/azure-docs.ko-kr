---
title: Azure Portal을 사용하여 가상 컴퓨터 크기 집합 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 크기 집합을 배포합니다.
keywords: 가상 컴퓨터 크기 집합
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: gatneil

---
# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 컴퓨터 규모 집합 만들기
이 자습서에서는 Azure Portal을 사용하여 가상 컴퓨터 크기 집합을 몇 분 이내에 간편하게 만드는 방법을 보여 줍니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="choose-the-vm-image-from-the-marketplace"></a>마켓플레이스에서 VM 이미지를 선택합니다.
포털에서 CentOS, CoreOS, Debian, Open Suse, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, Ubuntu Server 또는 Windows Server 이미지를 사용하여 크기 집합을 쉽게 배포할 수 있습니다.

먼저 웹 브라우저에서 [Azure Portal](https://portal.azure.com) 로 이동합니다. `New`를 클릭하고 `scale set`을 검색한 후 `Virtual machine scale set` 항목을 선택합니다.

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Linux 가상 컴퓨터 만들기
이제 기본 설정을 사용하여 신속하게 가상 컴퓨터를 만들 수 있습니다.

* `Basics` 블레이드에서 크기 집합의 이름을 입력합니다. 이 이름은 크기 집합 앞에 있는 부하 분산 장치 FQDN의 기반이 됩니다. 따라서 모든 Azure에서 고유해야 합니다.
* 원하는 OS 유형을 선택하고, 원하는 사용자 이름을 입력한 후 원하는 인증 유형을 선택합니다. 암호를 선택하는 경우 12자 이상이어야 하고 1개의 소문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자의 네 가지 복잡성 요구 사항 중 적어도 세 가지를 충족해야 합니다. 자세한 내용은 [사용자 이름 및 암호 요구 사항](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)을 참조하세요. `SSH public key`를 선택하는 경우 개인 키가 아니라 공개 키만 붙여넣어야 합니다.

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* 원하는 리소스 그룹 이름 및 위치를 입력한 다음 `OK`를 클릭합니다.
* `Virtual machine scale set service settings` 블레이드에서 원하는 도메인 이름 레이블(크기 집합 앞에 있는 부하 분산 장치 FQDN의 기준)을 입력합니다. 이 레이블은 모든 Azure에서 고유해야 합니다.
* 원하는 운영 체제 디스크 이미지, 인스턴스 수 및 컴퓨터 크기를 선택합니다.
* 자동 크기 조정을 사용하거나 사용하지 않도록 설정하고 사용하도록 설정하는 경우 다음을 구성합니다.

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* `Summary` 블레이드에서 유효성 검사 작업이 완료되면 `OK`를 클릭합니다.
* 마지막으로 `Purchase` 블레이드에서 `Purchase`를 클릭하여 크기 집합 배포를 시작합니다.

## <a name="connect-to-a-vm-in-the-scale-set"></a>크기 집합의 VM에 연결
크기 집합이 배포되면 크기 집합에 대한 부하 분산 장치의 `Inbound NAT Rules` 탭으로 이동합니다.

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

이러한 NAT 규칙을 사용하여 크기 집합의 각 VM에 연결할 수 있습니다. 예를 들어 Windows 크기 집합의 경우 수신 포트 50000에 대한 NAT 규칙이 있으면 `<load-balancer-ip-address>:50000`의 RDP를 통해 해당 컴퓨터에 연결할 수 있습니다. Linux 크기 집합의 경우 `ssh -p 50000 <username>@<load-balancer-ip-address>`명령을 사용하여 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계
CLI에서 크기 집합을 배포하는 방법에 대한 설명서를 보려면 [이 설명서](virtual-machine-scale-sets-cli-quick-create.md)를 참조하세요.

PowerShell에서 크기 집합을 배포하는 방법에 대한 설명서를 보려면 [이 설명서](virtual-machine-scale-sets-windows-create.md)를 참조하세요.

Visual Studio에서 크기 집합을 배포하는 방법에 대한 설명서를 보려면 [이 설명서](virtual-machine-scale-sets-vs-create.md)를 참조하세요.

일반 설명서는 [크기 집합에 대한 설명서 개요 페이지](virtual-machine-scale-sets-overview.md)를 참조하세요.

일반적인 정보는 [크기 집합에 대한 주 방문 페이지](https://azure.microsoft.com/services/virtual-machine-scale-sets/)를 확인하세요.

<!--HONumber=Oct16_HO2-->


