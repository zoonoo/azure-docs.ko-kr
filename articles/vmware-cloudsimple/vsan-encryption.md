---
title: 클라우드간이별 Azure VMware 솔루션 - 프라이빗 클라우드용 vSAN 암호화 구성
description: CloudSimple 프라이빗 클라우드가 Azure 가상 네트워크에서 실행되는 키 관리 서버와 함께 작동할 수 있도록 vSAN 소프트웨어 암호화 기능을 구성하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 638b60bd3612fa25350ecef0a738fea75c2f53d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020644"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>클라우드단순 프라이빗 클라우드를 위한 vSAN 암호화 구성

CloudSimple 프라이빗 클라우드가 Azure 가상 네트워크에서 실행되는 키 관리 서버와 함께 작동하도록 vSAN 소프트웨어 암호화 기능을 구성할 수 있습니다.

VMware는 vSAN 암호화를 사용할 때 외부 KMIP 1.1 호환 타사 키 관리 서버(KMS) 도구를 사용해야 합니다. VMware에서 인증하고 Azure에서 사용할 수 있는 지원되는 KMS를 활용할 수 있습니다.

이 가이드에서는 Azure 가상 네트워크에서 실행되는 HyTrust KeyControl KMS를 사용하는 방법에 대해 설명합니다. vSAN에 대해 인증된 다른 타사 KMS 솔루션에도 유사한 접근 방식을 사용할 수 있습니다.

이 KMS 솔루션에는 다음을 수행해야 합니다.

* Azure 가상 네트워크에서 VMware 인증 타사 KMS 도구를 설치, 구성 및 관리합니다.
* KMS 도구에 대한 사용자 고유의 라이선스를 제공합니다.
* Azure 가상 네트워크에서 실행되는 타사 KMS 도구를 사용하여 프라이빗 클라우드에서 vSAN 암호화를 구성하고 관리합니다.

## <a name="kms-deployment-scenario"></a>KMS 배포 시나리오

KMS 서버 클러스터는 Azure 가상 네트워크에서 실행되며 구성된 Azure ExpressRoute 연결을 통해 사설 클라우드 vCenter에서 IP에 연결할 수 있습니다.

![.. /미디어/KMS Azure 가상 네트워크의 클러스터](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>솔루션을 배포하는 방법

배포 프로세스에는 다음 단계가 있습니다.

1. [필수 구성 조건이 충족되는지 확인](#verify-prerequisites-are-met)
2. [CloudSimple 포털: 익스프레스루트 피어링 정보 얻기](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure 포털: 가상 네트워크를 프라이빗 클라우드에 연결](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure 포털: 가상 네트워크에 HyTrust 키컨트롤 클러스터 배포](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [하이트러스트 웹UI: KMIP 서버 구성](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI: Azure 가상 네트워크에서 KMS 클러스터를 사용하도록 vSAN 암호화 구성](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>필수 구성 조건이 충족되는지 확인

배포 하기 전에 다음을 확인 합니다.

* 선택한 KMS 공급업체, 도구 및 버전은 vSAN 호환성 목록에 있습니다.
* 선택한 공급업체는 Azure에서 실행할 도구 버전을 지원합니다.
* KMS 도구의 Azure 버전은 KMIP 1.1을 준수합니다.
* Azure 리소스 관리자와 가상 네트워크가 이미 만들어졌습니다.
* 클라우드심플 프라이빗 클라우드가 이미 생성되었습니다.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple 포털: 익스프레스루트 피어링 정보 얻기

설정을 계속하려면 ExpressRoute에 대한 권한 부여 키 및 피어 회로 URI와 Azure 구독에 대한 액세스권한이 필요합니다. 이 정보는 CloudSimple 포털의 가상 네트워크 연결 페이지에서 확인할 수 있습니다. 지침은 프라이빗 [클라우드에 대한 가상 네트워크 연결 설정을](virtual-network-connection.md)참조하십시오. 정보를 가져오는 데 문제가 있는 경우 [지원 요청을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)엽니다.

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure 포털: 가상 네트워크를 프라이빗 클라우드에 연결

1. Azure 포털을 [사용하여 ExpressRoute에 대한 가상 네트워크 게이트웨이 구성의](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)지침에 따라 가상 네트워크에 대한 가상 네트워크 게이트웨이를 만듭니다.
2. 포털을 사용하여 가상 네트워크 연결의 지침에 따라 가상 네트워크를 CloudSimple ExpressRoute [회로에 연결합니다.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)
3. CloudSimple에서 환영 이메일로 받은 CloudSimple ExpressRoute 회로 정보를 사용하여 가상 네트워크를 Azure의 CloudSimple ExpressRoute 회로에 연결합니다.
4. 권한 부여 키와 피어 회로 URI를 입력하고 연결에 이름을 지정하고 **확인을**클릭합니다.

![가상 네트워크를 만들 때 CS 피어 회로 URI 제공](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure 포털: 가상 네트워크의 Azure 리소스 관리자에 HyTrust 키컨트롤 클러스터 배포

가상 네트워크의 Azure 리소스 관리자에 HyTrust 키컨트롤 클러스터를 배포하려면 다음 작업을 수행합니다. 자세한 내용은 [HyTrust 문서를](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) 참조하십시오.

1. HyTrust 설명서의 지침에 따라 지정된 인바운드 규칙이 있는 Azure 네트워크 보안 그룹(nsg-hytrust)을 만듭니다.
2. Azure에서 SSH 키 쌍을 생성합니다.
3. Azure 마켓플레이스의 이미지에서 초기 KeyControl 노드를 배포합니다.  생성된 키 쌍의 공개 키를 사용하고 키컨트롤 노드의 네트워크 보안 그룹으로 **nsg-hytrust를** 선택합니다.
4. KeyControl의 개인 IP 주소를 정적 IP 주소로 변환합니다.
5. SSH는 공개 IP 주소와 앞에서 언급한 키 쌍의 개인 키를 사용하여 KeyControl VM에 전달됩니다.
6. SSH 셸에서 메시지가 표시되면 노드를 초기 KeyControl 노드로 설정하도록 선택합니다. `No`
7. 이 절차의 3-5단계를 반복하고 기존 클러스터에 `Yes` 추가하는 메시지가 표시될 때를 선택하여 KeyControl 노드를 추가합니다.

### <a name="hytrust-webui-configure-the-kmip-server"></a>하이트러스트 웹UI: KMIP 서버 구성

https://*공용 IP로*이동하여 공용 *IP가* KeyControl 노드 VM의 공용 IP 주소입니다. [HyTrust 설명서의](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)다음 단계를 따르십시오.

1. [KMIP 서버 구성](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [VMware 암호화를 위한 인증서 번들 만들기](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI: Azure 가상 네트워크에서 KMS 클러스터를 사용하도록 vSAN 암호화 구성

HyTrust 지침에 따라 [vCenter에서 KMS 클러스터를 만듭니다.](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)

![vCenter에 KMS 클러스터 세부 정보 추가](media/vsan-config01.png)

vCenter에서 클러스터 **> 구성하고** vSAN에 대한 **일반** 옵션을 선택합니다. 암호화를 활성화하고 이전에 vCenter에 추가된 KMS 클러스터를 선택합니다.

![vSAN 암호화 활성화 및 vCenter에서 KMS 클러스터 구성](media/vsan-config02.png)

## <a name="references"></a>참조

### <a name="azure"></a>Azure

[Azure Portal을 사용하여 ExpressRoute에 대한 가상 네트워크 게이트웨이 구성](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[포털을 사용하여 ExpressRoute 회로에 가상 네트워크 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>하이트러스트

[하이트러스트 데이터 컨트롤 및 마이크로소프트 Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[KMPI 서버 구성](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[VMware 암호화를 위한 인증서 번들 만들기](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[vSphere에서 KMS 클러스터 만들기](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
