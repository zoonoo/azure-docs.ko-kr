---
title: "Azure Site Recovery를 사용하여 Azure에 복제하기 위한 VMware의 필수 조건 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 VMware VM에서 실행되는 워크로드를 Azure에 복제하기 위한 필수 조건을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 7a82e58d9ff9208130c43fcd11d03dcc3238696a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-vmware-to-azure-replication"></a>2단계: Azure에 복제하기 위한 VMware의 필수 조건 검토

다음 표에 요약된 필수 조건을 참고하세요.

**필수 요소** | **세부 정보**
--- | ---
**Azure** | [Azure 요구 사항](site-recovery-prereq.md#azure-requirements)에 대해 알아봅니다.
**온-프레미스 구성 서버** | Windows Server 2012 R2 이상을 실행하는 VMware VM이 필요합니다. Site Recovery를 배포하는 동안 이 서버를 설정합니다.<br/><br/> 기본적으로 프로세스 서버와 마스터 대상 서버도 이 VM에 설치됩니다. 확장하는 경우 별도 프로세스 서버가 필요하며 구성 서버와 동일한 요구 사항을 포함합니다.<br/><br/> [여기](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)에서 이러한 구성 요소에 대해 자세히 알아보기
**온-프레미스 VMware 서버** | 최신 업데이트가 포함된 6.5, 6.0, 5.5, 5.1을 실행하는 하나 이상의 VMware vSphere 서버. 서버는 구성 서버(또는 별도의 프로세스 서버)와 동일한 네트워크에 있어야 합니다.<br/><br/> 호스트를 관리하는 vCenter 서버를 두는 것이 좋습니다(최신 업데이트가 포함된 6.5, 6.0 또는 5.5 실행).
**온-프레미스 VM** | 복제하려는 VM은 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)를 실행하고 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다. VM에서 VMware 도구가 실행되어야 합니다.
**URL** | 구성 서버는 다음 URL에 액세스해야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/></br> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/></br> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).<br/><br/> MySQL을 다운로드할 URL인 http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi를 허용합니다.
**모바일 서비스** | 모든 복제된 VM에 설치




## <a name="limitations"></a>제한 사항

배포하기 전에 테이블에 요약된 제한 사항을 이해해야 합니다.

**제한 사항** | **세부 정보**
--- | ---
**Azure** | 저장소 및 네트워크 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다.<br/><br/> 프리미엄 저장소 계정을 사용하는 경우 복제 로그를 저장할 표준 저장소 계정도 필요합니다.<br/><br/> 중부 및 남부 인도에서는 프리미엄 계정에 복제할 수 없습니다.
**온-프레미스 구성 서버** | VMware VM 어댑터 유형은 VMXNET3이어야 합니다. 그렇지 않은 경우 [이 업데이트를 설치하세요.](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> vSphere PowerCLI 6.0을 설치해야 합니다.<br/><br> 컴퓨터는 도메인 컨트롤러가 아니어야 합니다. 컴퓨터에는 고정 IP 주소가 있어야 합니다.<br/><br/> 호스트 이름은 15자 이하여야 하고 운영 체제에서는 영어를 사용해야 합니다.
**VMware** | Site Recovery에서는 교차 vCenter vMotion, 가상 볼륨 및 저장소 DRS와 같은 새로운 vCenter 및 vSphere 6.5 및 6.0 기능을 지원하지 않습니다.
**VM** | [Azure VM 제한 사항](site-recovery-prereq.md#azure-requirements) 확인<br/><br/> 암호화된 디스크를 사용하는 VM 또는 UEFI/EFI 부팅을 사용하는 VM은 복제할 수 없습니다.<br/><br> 공유 디스크 클러스터는 지원되지 않습니다. 원본 VM에 NIC 팀이 있는 경우 장애 조치 후 단일 NIC로 변환됩니다.<br/><br/> VM에 iSCSI 디스크가 있는 경우 Site Recovery는 장애 조치 후 이를 VHD 파일로 변환합니다. Azure VM에서 iSCSI 대상에 연결할 수 있으면 연결하고 해당 대상과 VHD를 모두 표시합니다. 이런 경우 iSCSI 대상의 연결을 끊습니다.<br/><br/> 일관된 데이터 지점으로 함께 복구할 동일한 워크로드를 실행하는 VM을 활성화하는 다중 VM 일관성을 사용하도록 설정하려면 VM에서 20004 포트를 엽니다.<br/><br/> Windows는 C 드라이브에 설치해야 합니다. OS 디스크는 동적 디스크가 아닌 기본 디스크여야 합니다. 데이터 디스크는 동적일 수 있습니다.<br/><br/> VM의 Linux /etc/hosts 파일은 로컬 호스트 이름을 모든 네트워크 어댑터와 연관된 IP 주소에 매핑하는 항목을 포함해야 합니다. 호스트 이름, 마운트 지점, 장치 이름, 시스템 경로 및 파일 이름(/etc/, /usr)에는 영어만 사용해야 합니다.<br/><br/> 특정 유형의 [Linux 저장소](site-recovery-support-matrix-to-azure.md#support-for-storage)가 지원됩니다.<br/><br/>VM 설정에서 **disk.enableUUID=true**를 만들거나 설정합니다. 이렇게 하면 VMDK에 일관된 UUID가 제공되므로 올바르게 탑재할 수 있으며 전체 복제하지 않고 장애 복구 시 델타 변경 사항만 온-프레미스로 전송되도록 할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 전체 배포를 수행하는 경우 [3단계: 용량 계획](vmware-walkthrough-capacity.md)으로 이동합니다.
- 간단한 테스트 배포를 수행하는 경우 [4단계: 네트워킹 계획](vmware-walkthrough-network.md)으로 이동합니다.
