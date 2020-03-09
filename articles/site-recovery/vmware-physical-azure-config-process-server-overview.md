---
title: Azure Site Recovery 구성/프로세스/마스터 대상 서버 정보
description: 이 문서에서는를 사용 하 여 Azure에 대 한 온-프레미스 VMware Vm의 재해 복구를 설정 하는 경우를 사용 하 여 구성, 프로세스 및 마스터 대상 서버에 대 한 개요를 제공 Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ad816f39dd4182dfa41fca975c99824a5d77f860
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395017"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Site Recovery 구성 요소 정보 (구성, 프로세스, 마스터 대상)

이 문서에서는 [Site Recovery](site-recovery-overview.md) 서비스를 사용 하 여 VMware vm 및 물리적 서버를 Azure에 복제할 때 사용 되는 구성, 프로세스 및 마스터 대상 서버에 대해 설명 합니다.

## <a name="configuration-server"></a>구성 서버

온-프레미스 VMware Vm 및 물리적 서버의 재해 복구를 위해 온-프레미스에 배포 된 Site Recovery 구성 서버가 필요 합니다.

**설정** | **세부 정보** | **연결**
--- | --- | ---
**Components**  | 구성 서버 컴퓨터는 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함 하는 모든 온-프레미스 Site Recovery 구성 요소를 실행 합니다.<br/><br/> 구성 서버를 설정 하면 모든 구성 요소가 자동으로 설치 됩니다. | 구성 서버 FAQ를 [읽습니다](vmware-azure-common-questions.md#configuration-server) .
**역할** | 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다. | Azure에 대 한 [VMware](vmware-azure-architecture.md) 및 [물리적 서버](physical-azure-architecture.md) 재해 복구를 위한 아키텍처에 대해 자세히 알아보세요.
**VMware 요구 사항** | 온-프레미스 VMware Vm의 재해 복구를 위해 온-프레미스, 항상 사용 가능한 VMware VM으로 구성 서버를 설치 하 고 실행 해야 합니다. | 필수 구성 요소 [에 대해 알아봅니다](vmware-azure-deploy-configuration-server.md#prerequisites) .
**VMware 배포** | 다운로드 한 OVA 템플릿을 사용 하 여 구성 서버를 배포 하는 것이 좋습니다. 이 방법은 모든 요구 사항 및 필수 구성 요소를 준수 하는 구성 서버를 설정 하는 간단한 방법을 제공 합니다.<br/><br/> 어떤 이유로 든 OVA 템플릿을 사용 하 여 VMware VM을 배포할 수 없는 경우 물리적 컴퓨터 재해 복구를 위해 아래에 설명 된 대로 구성 서버 컴퓨터를 수동으로 설정할 수 있습니다. | OVA 템플릿을 사용 하 여 [배포](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) 합니다.
**물리적 서버 요구 사항** | 온-프레미스 물리적 서버에서 재해 복구를 위해 수동으로 구성 서버를 배포 합니다. | 필수 구성 요소 [에 대해 알아봅니다](physical-azure-set-up-source.md#prerequisites) .
**물리적 서버 배포** | VMware VM으로 설치할 수 없는 경우 물리적 서버에 설치할 수 있습니다. | 구성 서버를 수동으로 [배포](physical-azure-set-up-source.md#set-up-the-source-environment) 합니다.


## <a name="process-server"></a>프로세스 서버

**설정** | **세부 정보** | **연결**
--- | --- | ---
**배포**  | 온-프레미스 VMware Vm 및 물리적 서버에 대 한 재해 복구 및 복제의 경우 온-프레미스 프로세스 서버가 필요 합니다. 기본적으로 프로세스 서버는 배포할 때 구성 서버에 설치 됩니다. | [자세히 알아봅니다](vmware-azure-architecture.md?#architectural-components).
**역할 (온-프레미스** | -복제를 사용 하도록 설정 된 컴퓨터에서 복제 데이터를 수신 합니다.<br/> -캐싱, 압축 및 암호화를 사용 하 여 복제 데이터를 최적화 하 고 Azure Storage 보냅니다.<br/> -복제 하려는 온-프레미스 VMware Vm 및 물리적 서버에서 Site Recovery 모바일 서비스의 푸시 설치를 수행 합니다.<br/> -온-프레미스 컴퓨터의 자동 검색을 수행 합니다. | [자세히 알아봅니다](vmware-physical-azure-config-process-server-overview.md#process-server). 
**역할 (Azure에서 장애 복구)** | 온-프레미스 사이트에서 장애 조치 (failover) 후 azure VM으로 azure에서 프로세스 서버를 설정 하 여 온-프레미스 위치에 장애 복구를 처리 합니다.<br/><br/> Azure의 프로세스 서버는 임시입니다. 장애 복구 (failback)가 완료 된 후 Azure VM을 삭제할 수 있습니다. | [자세히 알아봅니다](vmware-azure-set-up-process-server-azure.md).
**크기 조정** | 대규모 배포의 경우 온-프레미스에서 추가 스케일 아웃 프로세스 서버를 설정할 수 있습니다. 추가 서버는 많은 수의 복제 컴퓨터를 처리 하 고 더 많은 복제 트래픽을 처리 하 여 용량을 확장 합니다.<br/><br/> 복제 트래픽 부하를 분산 하기 위해 두 프로세스 서버 간에 컴퓨터를 이동할 수 있습니다. | [자세한 정보](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>마스터 대상 서버

마스터 대상 서버는 Azure에서 장애 복구 중 복제 데이터를 처리합니다.

- 구성 서버에 기본적으로 설치 됩니다.
- 대규모 배포의 경우 장애 복구를 위해 추가적인 별도의 마스터 대상 서버를 추가할 수 있습니다.


## <a name="next-steps"></a>다음 단계
- VMware Vm 및 물리적 서버의 재해 복구에 대 한 [아키텍처](vmware-azure-architecture.md) 를 검토 합니다.
- Azure에 대 한 VMware Vm 및 물리적 서버 재해 복구를 위한 [요구 사항 및 필수 구성 요소](vmware-physical-azure-support-matrix.md) 를 검토 합니다. 
