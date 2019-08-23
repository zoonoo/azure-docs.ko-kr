---
title: CloudSimple의 Azure VMware 솔루션-사설 클라우드의 vSAN 암호화 구성
description: CloudSimple 사설 클라우드가 Azure virtual network에서 실행 되는 키 관리 서버와 함께 작동할 수 있도록 vSAN 소프트웨어 암호화 기능을 구성 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972326"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드에 대 한 vSAN 암호화 구성

Azure 가상 네트워크에서 실행 되는 키 관리 서버에서 CloudSimple 사설 클라우드를 사용할 수 있도록 vSAN 소프트웨어 암호화 기능을 구성할 수 있습니다.

VSAN 암호화를 사용 하는 경우 VMware는 외부 KMIP 1.1 호환 타사 KMS (키 관리 서버) 도구를 사용 해야 합니다. VMware에서 인증 되었으며 Azure에 사용할 수 있는 모든 지원 되는 KMS를 활용할 수 있습니다.

이 가이드에서는 Azure 가상 네트워크에서 실행 되는 HyTrust KeyControl KMS를 사용 하는 방법을 설명 합니다. VSAN에 대해 인증 된 다른 타사 KMS 솔루션에도 유사한 접근 방식을 사용할 수 있습니다.

이 KMS 솔루션을 사용 하려면 다음을 수행 해야 합니다.

* Azure virtual network에 VMware 인증 타사 KMS 도구를 설치, 구성 및 관리 합니다.
* KMS 도구에 사용할 라이선스를 제공 합니다.
* Azure 가상 네트워크에서 실행 되는 타사 KMS 도구를 사용 하 여 사설 클라우드에서 vSAN 암호화를 구성 하 고 관리 합니다.

## <a name="kms-deployment-scenario"></a>KMS 배포 시나리오

KMS 서버 클러스터는 Azure virtual network에서 실행 되며, 구성 된 Azure Express 경로 연결을 통해 사설 클라우드 vCenter에서 연결할 수 있습니다.

![.. Azure virtual network의/Sv/dv/KMS 클러스터](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>솔루션을 배포 하는 방법

배포 프로세스에는 다음 단계가 포함 됩니다.

1. [필수 구성 요소가 충족 되는지 확인](#verify-prerequisites-are-met)
2. [CloudSimple 포털: Express 경로 피어 링 정보 가져오기](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure Portal: 사설 클라우드에 가상 네트워크 연결](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure Portal: 가상 네트워크에 HyTrust KeyControl 클러스터 배포](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: KMIP 서버 구성](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI: Azure virtual network에서 KMS 클러스터를 사용 하도록 vSAN 암호화 구성](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>필수 구성 요소가 충족 되는지 확인

배포 전에 다음을 확인 합니다.

* 선택한 KMS 공급 업체, 도구 및 버전은 vSAN 호환성 목록에 있습니다.
* 선택한 공급 업체는 Azure에서 실행 되는 버전의 도구를 지원 합니다.
* Azure 버전의 KMS 도구는 KMIP 1.1을 준수 합니다.
* Azure Resource Manager 및 가상 네트워크가 이미 생성 되었습니다.
* CloudSimple 사설 클라우드가 이미 만들어졌습니다.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple 포털: Express 경로 피어 링 정보 가져오기

설치를 계속 하려면 Express 경로에 대 한 인증 키 및 피어 회로 URI와 Azure 구독에 대 한 액세스 권한이 필요 합니다. 이 정보는 CloudSimple 포털의 Virtual Network 연결 페이지에서 사용할 수 있습니다. 지침은 [사설 클라우드에 대 한 가상 네트워크 연결 설정](virtual-network-connection.md)을 참조 하세요. 정보를 얻는 데 문제가 있는 경우 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 여세요.

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure Portal: 사설 클라우드에 가상 네트워크 연결

1. [Azure Portal를 사용 하 여 express 경로에 대 한 가상 네트워크 게이트웨이 구성](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)의 지침에 따라 가상 네트워크에 대 한 가상 네트워크 게이트웨이를 만듭니다.
2. [포털을 사용 하 여 가상 네트워크를 express 경로 회로에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)의 지침에 따라 Cloudsimple express 경로 회로에 가상 네트워크를 연결 합니다.
3. CloudSimple의 환영 메일에서 받은 CloudSimple Express 경로 회로 정보를 사용 하 여 가상 네트워크를 Azure의 CloudSimple Express 경로 회로에 연결 합니다.
4. 권한 부여 키 및 피어 회로 URI를 입력 하 고, 연결 이름을 지정 하 고, **확인**을 클릭 합니다.

![가상 네트워크를 만들 때 CS 피어 회로 URI 제공](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure Portal: 가상 네트워크의 Azure Resource Manager에 HyTrust KeyControl 클러스터 배포

가상 네트워크의 Azure Resource Manager에 HyTrust KeyControl 클러스터를 배포 하려면 다음 작업을 수행 합니다. 자세한 내용은 [HyTrust 설명서](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) 를 참조 하세요.

1. HyTrust 설명서의 지침에 따라 지정 된 인바운드 규칙을 사용 하 여 Azure 네트워크 보안 그룹 (nsg-hytrust)을 만듭니다.
2. Azure에서 SSH 키 쌍을 생성 합니다.
3. Azure Marketplace에서 이미지의 초기 KeyControl 노드를 배포 합니다.  생성 된 키 쌍의 공개 키를 사용 하 고 **hytrust** 를 keycontrol 노드에 대 한 네트워크 보안 그룹으로 선택 합니다.
4. KeyControl의 개인 IP 주소를 고정 IP 주소로 변환 합니다.
5. 공용 IP 주소와 이전에 언급 한 키 쌍의 개인 키를 사용 하 여 KeyControl VM에 대 한 SSH를 사용 합니다.
6. SSH 셸에서 메시지가 표시 되 면를 선택 `No` 하 여 노드를 초기 keycontrol 노드로 설정 합니다.
7. 이 절차의 3-5 단계를 반복 하 고 기존 클러스터에 추가 하 `Yes` 라는 메시지가 표시 되 면 선택 하 여 추가 keycontrol 노드를 추가 합니다.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: KMIP 서버 구성

Https://*공용*ip로 이동 합니다. 여기서 *공용 Ip* 는 keycontrol 노드 VM의 공용 ip 주소입니다. [HyTrust 설명서](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)에서 다음 단계를 수행 합니다.

1. [KMIP 서버 구성](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [VMware 암호화를 위한 인증서 번들 만들기](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI: Azure virtual network에서 KMS 클러스터를 사용 하도록 vSAN 암호화 구성

HyTrust 지침에 따라 [vCenter에서 KMS 클러스터를 만듭니다](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![VCenter에서 KMS 클러스터 세부 정보 추가](media/vsan-config01.png)

VCenter에서 **클러스터 > 구성** 으로 이동 하 고 vsan에 대 한 **일반** 옵션을 선택 합니다. 암호화를 사용 하도록 설정 하 고 이전에 vCenter에 추가 된 KMS 클러스터를 선택 합니다.

![VCenter에서 vSAN 암호화를 사용 하도록 설정 하 고 KMS 클러스터 구성](media/vsan-config02.png)

## <a name="references"></a>참조 항목

### <a name="azure"></a>Azure

[Azure Portal를 사용 하 여 Express 경로에 대 한 가상 네트워크 게이트웨이 구성](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[포털을 사용하여 ExpressRoute 회로에 가상 네트워크 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl 및 Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[KMPI 서버 구성](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[VMware 암호화를 위한 인증서 번들 만들기](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[VSphere KMS 클러스터 만들기](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
