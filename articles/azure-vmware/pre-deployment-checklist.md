---
title: Azure VMware 솔루션 배포 전 검사 목록
description: 배포 전 계획 프로세스의 일부로이 검사 목록을 사용 합니다.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580022"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Azure VMware 솔루션 배포 전 검사 목록
[계획 단계](production-ready-deployment-steps.md)에서이 배포 전 검사 목록을 사용 합니다.

## <a name="success-criteria"></a>성공 조건
실행할 테스트 및 예상 결과를 정의 합니다.

| 기본 정보 필요 | 사용자의 응답 |
| ----------- | ------------- |
| SDDC 배포 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 가상 네트워크 만들기 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 점프 상자 만들기 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 가상 네트워크 게이트웨이 만들기 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Express 경로 Global Reach 연결 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| NSX Manager 및 vCenter에 로그인 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| 기본 정보 필요 | 사용자의 응답 |
| --------| --------------|
| DHCP 서버 만들기 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 네트워크 세그먼트 만들기 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 크기 조정 (노드 추가 또는 삭제) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX 배포 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Vm (가상 컴퓨터) 만들기 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| 인터넷 사용 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 온-프레미스에서 사설 클라우드로 VM 마이그레이션 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| 추가 정보 필요 | 사용자의 응답 |
| --------| --------------|
| VM 스냅숏 작업 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| NSX-T 부하 분산 장치 배포 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure 통합<br><ul><li>공유 콘텐츠 라이브러리</li><li>보안 통합</li><li>ISO 업로드</li><li>ISO에서 빌드</li><li>Azure Backup</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 마이크로 분할 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 라우팅 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Azure VMware 솔루션 정보

#### <a name="azure-subscription"></a>Azure 구독
Azure VMware 솔루션에 대 한 구독 이름 및 구독 ID를 제공 합니다. 구독은 신규 또는 기존 구독 일 수 있습니다. 개발/테스트 구독을 사용 하지 마십시오.

| 필요한 정보  | 사용자의 응답 |
| ------------| ------------- |
| 구독 및 ID | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| EA 구독 | ☐ 예 &nbsp; &nbsp; ☐ 아니요  |
| 리소스 그룹 이름 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 위치 | 미국 동부 ☒ |
| Azure 관리자<br><br>관리자의 이름 및 연락처 제공<br>marketplace에서 서비스를 사용 하도록 할당 됩니다.<br>참가자는 다음에 필요한 최소한의 역할입니다. <br>[Azure VMware 솔루션 리소스 공급자를 등록](tutorial-create-private-cloud.md#register-the-resource-provider)합니다. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>온-프레미스 VMware 정보

| 필요한 정보  | 사용자의 응답 |
| ------------| --------------|
| vSphere 버전 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| vCenter 버전 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vCenter 관리자 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| L2 확장<br><br>VMware HCX를 사용 하 여 L2 네트워크를 확장 하는 경우<br>확장할 온-프레미스 네트워크를 제공 합니다. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>환경 전체에서 사용 되는 vSwitch 유형을 제공 합니다. | ☐ Standard &nbsp; &nbsp; ☐ Distributed<br><br>Standard를 사용 하는 경우 VMware HCX를 사용할 수 없습니다. |
| DNS 및 DHCP<br><br>적절 한 DNS 및 DHCP 인프라가 필요 합니다. <br>에 기본 제공 된 DHCP 서비스를 사용 하는 것이 좋습니다. <br>사설 클라우드에서 로컬 DHCP 서버 NSX 또는 사용 <br>를 통해 브로드캐스트 DHCP 트래픽을 라우팅하는 대신 <br>온-프레미스로 다시 WAN. 자세한 내용은, <br>[Azure VMWare 솔루션에서 DHCP를 만들고 관리 하는 방법을](manage-dhcp.md)참조 하세요. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>네트워킹-Azure VMware 솔루션 인프라 
필요한 데이터는 Azure VMware 솔루션 네트워킹 요구를 충족 하 고, 초기 네트워크 테스트를 수행 하는 데 도움이 됩니다. 

| 필요한 정보 | 사용자의 응답 |
| ----------- | ------------- |
|Azure VMware 솔루션 CIDR<br><br>VSphere 호스트, Vsphere 및 관리에 필요 합니다. <br>Azure VMware 솔루션의 네트워크. 자세한 정보 <br>자세한 내용은 [라우팅 및 서브넷 고려 사항](tutorial-network-checklist.md#routing-and-subnet-considerations)을 참조 하세요. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure VMware 솔루션 워크 로드 CIDR (선택 사항)<br><br>Azure VMware에서 사용할 네트워크 할당<br>초기 테스트를 위한 솔루션입니다. 가상 머신<br>네트워크 연결의 유효성을 검사 하기 위해 배포 됩니다. <br>from/to Azure VMware 솔루션. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>네트워킹-azure Virtual Network에 Azure VMware 솔루션 연결
Azure VMware 솔루션 클러스터가 구현 된 후에 필요한 데이터는 Azure VMware 솔루션 서비스의 일부인 Express 경로 회로를 통해 Azure에 연결할 수 있습니다.

| 필요한 정보 | 사용자의 응답 |
| ------------------ | ------------- |
| 점프 상자-Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 가상 네트워크 만들기 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 게이트웨이 서브넷 만들기 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 가상 네트워크 게이트웨이 만들기<br><br>자세한 내용은 [가상 네트워크 게이트웨이 만들기](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)를 참조 하세요. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>네트워킹-온-프레미스 데이터 센터에 Azure VMware 솔루션 연결

| 필요한 정보 | 사용자의 응답 |
| ------------------ | ------------- |
| Express 경로 피어 링 CIDR<br><br>`/29`CIDR 주소 블록입니다. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Express 경로 권한 부여 키 및 리소스 ID<br><br>권한 부여 키 및 리소스 ID를 제공 합니다. <br>현재 Express 경로에서 생성 된 <br>온-프레미스 데이터 센터에 연결 하는 회로입니다.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 기본 경로 방향<br><br>Azure VMware 솔루션의 가상 머신 <br>제공 된 Azure VMware 솔루션을 통해 인터넷에 액세스 <br>인터넷으로 이동 하거나 Express 경로 회로를 통해 <br>기본 경로에 대 한 온-프레미스 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 서비스와 통신하는 데 필요한 네트워크 포트<br><br>자세한 내용은 [필수 네트워크 포트](tutorial-network-checklist.md#required-network-ports)를 참조 하세요. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>네트워킹-VMware HCX

| 필요한 정보 | 사용자의 응답 |
| ------------------ | ------------- |
| 네트워크 포트<br><br>방화벽이 있는 경우 필요한 네트워크 포트를 확인 합니다. <br>온-프레미스와 Azure VMware 솔루션 간에 열립니다. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>DNS를 구성 하는 방법에 대 한 자세한 내용은 <br>[네트워킹-Azure VMware 솔루션 인프라](#networking---azure-vmware-solution-infrastructure)를 참조 하세요. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX CIDRs<br><br>에 사용 되는 두 `/29` CIDR 블록이 필요 합니다. <br>VMware HCX 인프라 온-프레미스 구성 요소입니다.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

