---
title: 아키텍처-Azure Site Recovery를 사용 하 여 보조 사이트로의 VMware/물리적 재해 복구
description: 이 문서에서는 Azure Site Recovery를 사용하여 온-프레미스 VMware VM 또는 실제 Windows/Linux 서버를 보조 VMware 사이트로 재해 복구하는 동안 사용되는 구성 요소 및 아키텍처 개요를 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 7c884ce839523706e67e4278f43e237e1a2b0580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87496970"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>VMware용 아키텍처/보조 온-프레미스 사이트에 물리적 서버 복제

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 VMware VM(가상 머신) 또는 실제 Windows/Linux 서버를 보조 VMware 사이트로 재해 복구 복제, 장애 조치(failover) 및 복구를 설정할 때 사용되는 아키텍처와 프로세스에 대해 설명합니다.


## <a name="architectural-components"></a>아키텍처 구성 요소

**영역** | **구성 요소** | **세부 정보**
--- | --- | ---
**Azure** | InMage Scout를 사용하여 이 시나리오를 배포합니다. | InMage Scout를 얻으려면 Azure 구독이 필요합니다.<br/><br/> Recovery Services 자격 증명 모음을 만든 후에 InMage Scout를 다운로드하고 최신 업데이트를 설치하여 배포를 설정합니다.
**프로세스 서버** | 기본 사이트에 있음 | 프로세스 서버를 배포하여 캐시, 압축 및 데이터 최적화를 처리합니다.<br/><br/> 또한 보호하려는 컴퓨터에 대해 통합 에이전트의 푸시 설치를 처리합니다.
**구성 서버** | 보조 사이트에 있음 | 구성 서버는 관리 웹 사이트나 vContinuum 콘솔을 사용하여 배포를 관리, 구성 및 모니터링합니다.
**vContinuum 서버** | (선택 사항) 구성 서버와 동일한 위치에 설치됩니다. | 보호되는 환경을 관리 및 모니터링하기 위한 콘솔을 제공합니다.
**마스터 대상 서버** | 보조 사이트에 있음 | 마스터 대상 서버는 복제된 데이터를 보유합니다. 프로세스 서버로부터 데이터를 수신하고 보조 사이트에 복제본 컴퓨터를 만들며 데이터 보존 지점을 유지합니다.<br/><br/> 필요한 마스터 대상 서버의 수는 보호하는 컴퓨터의 수에 따라 다릅니다.<br/><br/> 기본 사이트로 다시 장애 복구할 경우 해당 기본 사이트에도 마스터 대상 서버가 필요합니다. 통합 에이전트는 이 서버에 설치됩니다.
**VMware ESX/ESXi 및 vCenter 서버** |  ESX/ESXi 호스트에서 호스트되는 VM. vCenter 서버로 관리되는 호스트. | VMware VM을 복제하려면 VMware 인프라가 필요합니다.
**VM/물리적 서버** |  복제하려는 VMware VM 또는 물리적 서버에 설치되는 통합 에이전트. | 에이전트는 모든 구성 요소 간의 통신 공급자 역할을 합니다.

## <a name="set-up-outbound-network-connectivity"></a>아웃 바운드 네트워크 연결 설정

Site Recovery가 예상 대로 작동 하려면 아웃 바운드 네트워크 연결을 수정 하 여 환경을 복제할 수 있도록 해야 합니다.

> [!NOTE]
> Site Recovery는 인증 프록시를 사용하여 네트워크 연결을 제어하도록 지원하지 않습니다.

### <a name="outbound-connectivity-for-urls"></a>URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음 URL에 대한 액세스를 허용합니다.

| **이름**                  | **상업용**                               | **정부**                                 | **설명** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 스토리지                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 합니다. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery 서비스 URL에 대한 권한 부여 및 인증을 제공합니다. |
| 복제               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | VM이 Site Recovery 서비스와 통신할 수 있도록 합니다. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM이 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 합니다. |

## <a name="replication-process"></a>복제 프로세스

1. 각 사이트(구성, 프로세스, 마스터 대상)에 구성 요소 서버를 설정하고 복제할 컴퓨터에 통합 에이전트를 설치합니다.
2. 초기 복제 후 각 컴퓨터에서 에이전트는 델타 복제 변경을 프로세스 서버로 보냅니다.
3. 프로세스 서버가 데이터를 최적화하고 보조 사이트의 마스터 대상 서버로 보냅니다. 구성 서버는 복제 프로세스를 관리합니다.

![VMware Vm 및 물리적 서버를 보조 데이터 센터에 복제 하는 방법을 보여 주는 다이어그램](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>다음 단계

VMware VM 및 물리적 서버를 보조 사이트로 재해 복구하도록 [설정](vmware-physical-secondary-disaster-recovery.md)합니다.
