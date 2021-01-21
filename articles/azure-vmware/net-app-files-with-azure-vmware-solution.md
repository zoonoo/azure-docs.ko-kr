---
title: Azure VMware 솔루션으로 Azure NetApp Files
description: Azure VMware 솔루션 Vm과 Azure NetApp Files를 사용 하 여 온-프레미스 서버, Azure VMware 솔루션 Vm 및 클라우드 인프라에서 데이터를 마이그레이션하고 동기화 합니다.
ms.topic: how-to
ms.date: 01/20/2021
ms.openlocfilehash: 97a9172dfbed2722d4af0abbd6231a0367dc4e3c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634152"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure VMware 솔루션으로 Azure NetApp Files

이 문서에서는 Azure VMware 솔루션 기반 워크 로드와 Azure NetApp Files를 통합 하는 단계를 안내 합니다. 게스트 운영 체제는 Azure NetApp Files 볼륨에 액세스 하는 Vm (가상 컴퓨터) 내에서 실행 됩니다. 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files 개요

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) 는 데이터베이스, SAP 및 고성능 컴퓨팅 응용 프로그램을 비롯 하 여 클라우드에서 가장 까다로운 엔터프라이즈 파일 작업을 마이그레이션 및 실행 하는 Azure 자사 서비스로, 코드를 변경 하지 않습니다.

### <a name="features"></a>기능
Azure NetApp Files 사용 되는 서비스입니다.

- **Active Directory 연결**: Azure NetApp Files는 [Active Directory Domain Services 및 Azure Active Directory Domain Services](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use)를 지원 합니다.

- **프로토콜 공유**: AZURE NETAPP FILES은 SMB (서버 메시지 블록) 및 NFS (네트워크 파일 시스템) 프로토콜을 지원 합니다. 이 지원 이란 볼륨을 Linux 클라이언트에 탑재할 수 있으며 Windows 클라이언트에서 매핑될 수 있음을 의미 합니다.

- **Azure Vmware 솔루션**: Azure vmware 솔루션 환경에서 만든 vm에서 Azure NetApp Files 공유를 탑재할 수 있습니다.

Azure NetApp Files은 많은 Azure 지역에서 사용할 수 있으며 지역 간 복제를 지원 합니다. Azure NetApp Files 구성 방법에 대 한 자세한 내용은 [Azure NetApp Files 저장소 계층 구조](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)를 참조 하세요.

## <a name="reference-architecture"></a>참조 아키텍처

다음 다이어그램에서는 azure Express 경로를 통해 Azure VMware 솔루션 사설 클라우드로 연결 하는 방법을 보여 줍니다. Azure vmware 솔루션 Vm에 탑재 된 Azure NetApp Files 공유를 사용 하 여 Azure VMware 솔루션 환경에서 액세스 하는 방법을 보여 줍니다.

![Azure VMware 솔루션 아키텍처용 NetApp 파일을 보여 주는 다이어그램입니다.](media/net-app-files/net-app-files-topology.png)

이 문서에서는 Azure VMware 솔루션 Vm에 대 한 파일 공유로 Azure NetApp Files 볼륨을 설정, 테스트 및 확인 하는 지침을 설명 합니다. 이 시나리오에서는 NFS 프로토콜을 사용 했습니다. Azure NetApp Files와 Azure VMware 솔루션은 동일한 Azure 지역에 생성 됩니다.

## <a name="prerequisites"></a>필수 구성 요소 

> [!div class="checklist"]
> * Azure NetApp Files 사용 하도록 설정 된 Azure 구독
> * Azure NetApp Files에 대 한 서브넷
> * Azure VMware 솔루션의 Linux VM
> * Azure VMware 솔루션의 Windows Vm

## <a name="regions-supported"></a>지원 되는 지역

지원 되는 지역 목록은 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)에서 찾을 수 있습니다.

## <a name="verify-pre-configured-azure-netapp-files"></a>미리 구성 된 Azure NetApp Files 확인 

다음 문서의 단계별 지침에 따라 Azure NetApp Files 볼륨을 만들고 Azure VMware 솔루션 Vm에 탑재 합니다.

- [NetApp 계정 만들기](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [용량 풀 설정](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files에 대한 SMB 볼륨 만들기](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Azure NetApp Files에 대한 NFS 볼륨 만들기](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Azure NetApp Files에 서브넷 위임](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

다음 단계에는 Azure NetApp Files Premium 서비스 수준에서 Azure에 만들어진 미리 구성 된 Azure NetApp Files 확인이 포함 됩니다.

1. Azure Portal의 **저장소** 에서 **Azure NetApp Files** 를 선택 합니다. 구성 된 Azure NetApp Files 목록에 표시 됩니다. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="미리 구성 된 Azure NetApp Files 목록을 보여 주는 스크린샷"::: 

2. 구성 된 NetApp 파일 계정을 선택 하 여 설정을 확인 합니다. 예를 들어 **Contoso-anf2** 을 선택 합니다. 

3. 구성 된 풀을 확인 하려면 **용량 풀** 을 선택 합니다. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="구성 된 NetApp 파일 계정의 용량 풀 및 볼륨을 볼 수 있는 옵션을 보여 주는 스크린샷":::

    용량 및 서비스 수준이 표시 된 용량 풀 페이지가 열립니다. 이 예제에서 저장소 풀은 프리미엄 서비스 수준으로 4 TiB 구성 됩니다.

4. **볼륨** 을 선택 하 여 용량 풀에서 만든 볼륨을 볼 수 있습니다. 앞의 스크린샷을 참조 하세요.

5. 볼륨을 선택 하 여 해당 구성을 확인 합니다.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="용량 풀에서 생성 된 볼륨을 보여 주는 스크린샷":::

    볼륨의 구성 세부 정보를 표시 하는 창이 열립니다.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="볼륨의 구성 세부 정보를 보여 주는 스크린샷":::

    크기가 200 GiB 볼륨 anfvolume이 용량 풀 anfpool1에 만들어지고 10.22.3.4:/ANFVOLUME.를 통해 NFS 파일 공유로 내보내집니다. Azure NetApp Files 및 VM에 탑재할 NFS 경로에 대해 Azure Virtual Network (VNet)에서 하나의 개인 IP를 만들었습니다. 크기 ("할당량")에 상대적인 Azure NetApp Files 볼륨 성능에 대 한 자세한 내용은 [Azure NetApp Files에 대 한 성능 고려 사항](../azure-netapp-files/azure-netapp-files-performance-considerations.md)을 참조 하세요. 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>미리 구성 된 Azure VMware 솔루션 VM 공유 매핑 확인

Azure NetApp Files의 접근성을 Azure VMware 솔루션 VM에 보여주는 하기 전에 SMB 및 NFS 공유 매핑을 이해 하는 것이 중요 합니다. SMB 또는 NFS 볼륨을 구성한 후에만 여기에 설명 된 대로 탑재할 수 있습니다.

- SMB 공유: SMB 볼륨을 배포 하기 전에 Active Directory 연결을 만듭니다. 연결에 성공 하려면 Azure NetApp Files의 위임 된 서브넷에서 지정 된 도메인 컨트롤러에 액세스할 수 있어야 합니다. Active Directory Azure NetApp Files 계정 내에서 구성 되 면 SMB 볼륨을 만드는 동안 선택 가능한 항목으로 표시 됩니다.

- NFS 공유: Azure NetApp Files NFS 또는 이중 프로토콜 (NFS 및 SMB)을 사용 하 여 볼륨을 만드는 데 기여 합니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. 명령줄 또는/etc/fstab 항목을 사용 하 여 Linux 서버에 NFS를 탑재할 수 있습니다.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Azure VMware 솔루션과 함께 Azure NetApp Files의 사용 사례

다음은 몇 가지 매력적인 Azure NetApp Files 사용 사례입니다. 
- 수평 프로필 관리
- Citrix 프로필 관리
- 원격 데스크톱 서비스 프로필 관리
- Azure VMware 솔루션의 파일 공유

## <a name="next-steps"></a>다음 단계
- [Azure NetApp Files에 대 한 리소스 제한](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)에 대해 알아봅니다.
- [Azure NetApp Files 네트워크 계획에 대 한 지침을](../azure-netapp-files/azure-netapp-files-network-topologies.md)참조 하세요.
- [Azure NetApp Files 볼륨의 지역 간 복제](../azure-netapp-files/cross-region-replication-introduction.md)에 대해 알아봅니다. 
- [Azure NetApp Files에 대 한 faq](../azure-netapp-files/azure-netapp-files-faqs.md)를 참조 하세요.
