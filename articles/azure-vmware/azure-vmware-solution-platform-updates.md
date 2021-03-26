---
title: Azure VMware 솔루션에 대 한 플랫폼 업데이트
description: Azure VMware 솔루션에 대 한 플랫폼 업데이트에 대해 알아봅니다.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045215"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 플랫폼 업데이트

Azure VMware 솔루션은 3 월 2021부터 중요 업데이트를 적용 합니다. 유지 관리 타임 라인을 포함 하는 Azure Service Health를 통해 알림을 받게 됩니다. 자세한 내용은 [Azure VMware 솔루션 사설 클라우드 업데이트 및 업그레이드](concepts-upgrades.md)를 참조 하세요.

## <a name="march-24-2021"></a>2021 년 3 월 24 일
모든 새 Azure VMware 솔루션 사설 클라우드는 VMware vCenter 버전 6.7 U3l 및 NSX 버전 3.1.1를 사용 하 여 배포 됩니다. 모든 기존 사설 클라우드는 **6 월 2021까지** 위에서 언급 한 릴리스에 업데이트 및 업그레이드 됩니다.

계획 된 유지 관리 날짜 및 시간이 포함 된 전자 메일을 받게 됩니다. 업그레이드를 다시 예약할 수 있습니다. 또한 전자 메일은 업그레이드 된 구성 요소에 대 한 세부 정보, 워크 로드에 대 한 영향, 사설 클라우드 액세스 및 기타 Azure 서비스를 제공 합니다.  업그레이드 하기 전에 1 시간 후에 알림이 표시 되 고 완료 되 면 다시 알림이 수신 됩니다.

## <a name="march-15-2021"></a>2021 년 3 월 15 일 

- Azure VMware Solution service는 **2021 년 3 월 19 일까** 지 유지 관리 작업을 수행 하 여 사설 클라우드의 vCenter server를 VCenter Server 6.7 업데이트 3l 버전으로 업데이트 합니다.

- 이 시간 동안에는 VMware vCenter를 사용할 수 없습니다.  따라서 Vm (중지, 시작, 만들기, 삭제) 또는 사설 클라우드 크기 조정 (서버 및 클러스터 추가/제거)을 관리할 수 없습니다. 그러나 VMware HA (고가용성)는 기존 Vm을 보호 하기 위해 계속 작동 합니다. 
 
이 vCenter 버전에 대 한 자세한 내용은 [VMware vCenter Server 6.7 업데이트 3l 릴리스 정보](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)를 참조 하세요.

## <a name="march-4-2021"></a>2021 년 3 월 4 일

- Azure VMware 솔루션은 [VMware ESXi 6.7, Patch Release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) 을 기존 privates에 **년 3 월 15 2021 일까지** 적용 합니다.

- [Vsphere-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)에 따라 vsphere 스택에 대 한 문서화 된 해결 방법은 **2021 15,까지** 적용 됩니다.

>[!NOTE]
>이는 중단 되지 않으며 Azure VMware 서비스 또는 워크 로드에 영향을 주지 않습니다. 유지 관리 중에는 유지 관리가 진행 됨에 따라 _DVPorts의 네트워크 연결 끊김_ 및 _Dvports의 손실 된 업링크 중복성_ 과 같은 다양 한 VMware 경고가 vCenter에 표시 되 고 자동으로 제거 됩니다.

## <a name="post-update"></a>업데이트 후
완료 되 면 새 버전의 VMware 구성 요소가 표시 됩니다. 문제가 있거나 질문이 있는 경우 지원 티켓을 열어 지원 팀에 문의 하세요.