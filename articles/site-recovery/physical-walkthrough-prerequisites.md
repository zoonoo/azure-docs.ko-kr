---
title: "Azure Site Recovery를 사용하여 Azure에 복제하기 위한 물리적 서버의 필수 조건 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 물리적 Windows/Linux 서버에서 실행되는 워크로드를 Azure에 복제하기 위한 필수 조건을 설명합니다."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 5f5cb4b9b6fcee6b56ccdcb6223afddd9de90fec
ms.contentlocale: ko-kr
ms.lasthandoff: 06/29/2017

---

# <a name="step-2-review-the-prerequisites-for-physical-server-to-azure-replication"></a>2단계: Azure에 복제하기 위한 물리적 서버의 필수 조건 검토

표에서 필수 구성 요소를 검토합니다.


**필수 요소** | **세부 정보**
--- | ---
**Azure** | [Azure 요구 사항](site-recovery-prereq.md#azure-requirements)에 대해 알아봅니다.
**온-프레미스 구성 서버** | Windows Server 2012 R2 이상이 실행되는 물리적 서버(또는 VMware VM)가 필요합니다. Site Recovery를 배포하는 동안 이 서버를 설정합니다.<br/><br/> 기본적으로 프로세스 서버와 마스터 대상 서버도 이 컴퓨터에 설치됩니다. 수직 확장하는 경우 별도의 프로세스 서버가 필요할 수 있습니다. 이런 경우 구성 서버와 동일한 요구 사항을 갖습니다.<br/><br/> [자세히 알아보기](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**온-프레미스 VM** | 복제하려는 컴퓨터는 [지원되는 운영 체제](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)를 실행하고 [Azure 필수 조건](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 준수해야 합니다.
**URL** | 구성 서버는 다음 URL에 액세스해야 합니다.<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP 주소 기반 방화벽 규칙이 있는 경우 해당 규칙이 Azure와의 통신을 허용하는지 확인합니다.<br/></br> [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트를 허용합니다.<br/></br> 구독하는 Azure 지역과 미국 서부에 해당하는 IP 주소 범위를 허용하세요(Access Control 및 ID 관리에 사용됨).<br/><br/> MySQL을 다운로드할 URL인 http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi를 허용합니다.
**모바일 서비스** | 모든 복제된 서버에 설치됩니다.




## <a name="limitations"></a>제한 사항

배포하기 전에 표에 요약된 제한 사항을 확인합니다.

**제한 사항** | **세부 정보**
--- | ---
**Azure** | 저장소 및 네트워크 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다.<br/><br/> 프리미엄 저장소 계정을 사용하는 경우 복제 로그를 저장할 표준 저장소 계정도 필요합니다.<br/><br/> 중부 및 남부 인도에서는 프리미엄 계정에 복제할 수 없습니다.
**온-프레미스 구성 서버** | VMware VM을 구성 서버 컴퓨터로 사용하는 경우 VMware VM 어댑터 유형은 VMXNET3이어야 합니다. 그렇지 않은 경우 [이 업데이트를 설치하세요.](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> VMware VM의 경우 vSphere PowerCLI 6.0을 설치해야 합니다.<br/><br> 컴퓨터는 도메인 컨트롤러가 아니어야 합니다.<br/><br/> 컴퓨터에는 고정 IP 주소가 있어야 합니다.<br/><br/> 호스트 이름은 15자 이하여야 하고 운영 체제에서는 영어를 사용해야 합니다.
**컴퓨터 복제** | [Azure VM 제한 사항](site-recovery-prereq.md#azure-requirements) 확인<br/><br/> 암호화된 디스크를 사용하는 VM 또는 UEFI/EFI 부팅을 사용하는 VM은 복제할 수 없습니다.<br/><br> 공유 디스크 클러스터는 지원되지 않습니다. 원본 VM에 NIC 팀이 있는 경우 장애 조치 후 단일 NIC로 변환됩니다.<br/><br/> VM에 iSCSI 디스크가 있는 경우 Site Recovery는 장애 조치 후 이를 VHD 파일로 변환합니다. Azure VM에서 iSCSI 대상에 연결할 수 있으면 연결하고 해당 대상과 VHD를 모두 표시합니다. 이런 경우 iSCSI 대상의 연결을 끊습니다.<br/><br/> 일관된 데이터 지점으로 함께 복구할 동일한 워크로드를 실행하는 VM을 활성화하는 다중 VM 일관성을 사용하도록 설정하려면 VM에서 20004 포트를 엽니다.<br/><br/> Windows는 C 드라이브에 설치해야 합니다. OS 디스크는 동적 디스크가 아닌 기본 디스크여야 합니다. 데이터 디스크는 동적일 수 있습니다.<br/><br/> VM의 Linux /etc/hosts 파일은 로컬 호스트 이름을 모든 네트워크 어댑터와 연관된 IP 주소에 매핑하는 항목을 포함해야 합니다. 호스트 이름, 마운트 지점, 장치 이름, 시스템 경로 및 파일 이름(/etc/, /usr)에는 영어만 사용해야 합니다.<br/><br/> 특정 유형의 [Linux 저장소](site-recovery-support-matrix-to-azure.md#support-for-storage)가 지원됩니다.<br/><br/>VM 설정에서 **disk.enableUUID=true**를 만들거나 설정합니다. 이렇게 하면 VMDK에 일관된 UUID가 제공되므로 올바르게 탑재할 수 있으며 전체 복제하지 않고 장애 복구 시 델타 변경 사항만 온-프레미스로 전송되도록 할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 전체 배포를 수행하는 경우 [3단계: 용량 계획](physical-walkthrough-capacity.md)으로 이동합니다.
- 간단한 테스트 배포를 수행하는 경우 [4단계: 네트워킹 계획](physical-walkthrough-network.md)으로 이동합니다.

