---
title: Azure VMware Solution에 대한 플랫폼 업데이트
description: Azure VMware Solution에 대한 플랫폼 업데이트에 대해 알아봅니다.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045215"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware Solution에 대한 플랫폼 업데이트

Azure VMware Solution은 2021년 3월부터 중요 업데이트를 적용합니다. 유지 관리 타임라인을 포함하는 Azure Service Health를 통해 알림을 받게 됩니다. 자세한 내용은 [Azure VMware Solution 프라이빗 클라우드 업데이트 및 업그레이드](concepts-upgrades.md)를 참조하세요.

## <a name="march-24-2021"></a>2021년 3월 24일
모든 새 Azure VMware Solution 프라이빗 클라우드는 VMware vCenter 버전 6.7 U3l 및 NSX-T 버전 3.1.1을 사용하여 배포됩니다. 모든 기존 프라이빗 클라우드는 **2021년 6월** 까지 위에서 언급한 릴리스로 업데이트 및 업그레이드됩니다.

계획된 유지 관리 날짜 및 시간이 포함된 메일을 받게 됩니다. 업그레이드를 다시 예약할 수 있습니다. 또한 메일은 업그레이드된 구성 요소에 대한 세부 정보, 워크로드에 대한 영향, 프라이빗 클라우드 액세스 및 기타 Azure 서비스를 제공합니다.  업그레이드하기 1시간 전에 알림이 표시되고 완료되면 다시 알림이 수신됩니다.

## <a name="march-15-2021"></a>2021년 3월 15일 

- Azure VMware Solution 서비스는 **2021년 3월 19일까지** 유지 관리 작업을 수행하여 프라이빗 클라우드의 vCenter Server를 vCenter Server 6.7 Update 3l 버전으로 업데이트합니다.

- 이 기간 동안에는 VMware vCenter를 사용할 수 없습니다.  따라서 VM(중지, 시작, 만들기, 삭제) 또는 프라이빗 클라우드 스케일링(서버 및 클러스터 추가/제거)을 관리할 수 없습니다. 그러나 VMware HA(고가용성)는 기존 VM을 보호하기 위해 계속 작동합니다. 
 
이 vCenter 버전에 대한 자세한 내용은 [VMware vCenter Server 6.7 Update 3l 릴리스 정보](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)를 참조하세요.

## <a name="march-4-2021"></a>2021년 3월 4일

- Azure VMware Solution은 [VMware ESXi 6.7, Patch Release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html)를 **2021년 3월 15일까지** 기본 프라이빗 환경에 적용합니다.

- [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)에 따라 vSphere 스택에 대한 문서화된 해결 방법 또한 **2021년 3월 15일까지** 적용됩니다.

>[!NOTE]
>이것은 무중단 방식으로 진행되며 Azure VMware 서비스 또는 워크로드에 영향을 주지 않습니다. 유지 관리 중에는 _DVPort의 네트워크 연결 끊김_ 및 _DVPort의 손실된 업링크 중복성_ 과 같은 다양한 VMware 경고가 vCenter에 표시되고 유지 관리가 진행되면서 자동으로 제거됩니다.

## <a name="post-update"></a>사후 업데이트
완료되면 새 버전의 VMware 구성 요소가 표시됩니다. 문제가 있거나 질문이 있는 경우 지원 티켓을 열어 지원 팀에 문의하세요.