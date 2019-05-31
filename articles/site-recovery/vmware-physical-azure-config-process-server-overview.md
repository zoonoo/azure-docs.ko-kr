---
title: Azure Site Recovery 구성, 프로세스 및 마스터 대상 서버에 대 한 | Microsoft Docs
description: 이 문서에서는 구성, 프로세스 및 Azure Site Recovery를 사용 하 여 Azure에 온-프레미스 VMware Vm의 재해 복구를 설정 하는 경우를 사용 하 여 마스터 대상 서버 개요
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8b3815fc9dc44484779a70b51ebff4802265d53a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417747"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Site Recovery 구성 요소 (구성, 프로세스, 마스터 대상)에 대 한

이 문서에서는 설명 구성, 프로세스 및 마스터 대상 서버를 사용 하 여 Azure에 VMware Vm 및 물리적 서버를 복제할 때 사용 합니다 [Site Recovery](site-recovery-overview.md) 서비스입니다.

## <a name="configuration-server"></a>구성 서버

Site Recovery는 온-프레미스 VMware Vm 및 물리적 서버의 재해 복구를 위해 필요한 구성 서버 온-프레미스를 배포 합니다.

**설정** | **세부 정보** | **연결**
--- | --- | ---
**구성 요소**  | 구성 서버 컴퓨터에는 모든 온-프레미스 Site Recovery 구성 요소를 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함 하는 실행 됩니다.<br/><br/> 구성 서버를 설정한 경우 모든 구성 요소가 자동으로 설치 됩니다. | [읽기](vmware-azure-common-questions.md#configuration-server) 구성 서버 FAQ.
**역할** | 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다. | 아키텍처에 자세히 알아보려면 [VMware](vmware-azure-architecture.md) 하 고 [물리적 서버](physical-azure-architecture.md) Azure로 재해 복구 합니다.
**VMware 요구 사항** | 온-프레미스 VMware Vm의 재해 복구를 위해 설치 하 고 구성 서버를 온-프레미스에서 항상 사용 가능한 VMware VM으로 실행 해야 합니다. | [에 대 한 자세한](vmware-azure-deploy-configuration-server.md#prerequisites) 필수 구성 요소입니다.
**VMware 배포** | 다운로드 한 OVA 템플릿을 사용 하 여 구성 서버를 배포 하는 것이 좋습니다. 이 메서드는 제공 된 단순히 모든 요구 사항 및 필수 구성 요소를 준수 하는 구성 서버를 설정 하는 방법입니다.<br/><br/> 어떤 이유로 모르는 경우 OVA 템플릿을 사용 하는 VMware VM을 배포할 수를 설정할 수 있습니다 구성 서버 컴퓨터를 수동으로 물리적 컴퓨터 재해 복구를 위해 아래 설명 된 대로. | [배포](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) OVA 템플릿을 사용 하 여 합니다.
**물리적 서버 요구 사항** | 온-프레미스 물리적 서버 재해 복구용 구성 서버 배포 수동으로. | [에 대 한 자세한](physical-azure-set-up-source.md#prerequisites) 필수 구성 요소입니다.
**물리적 서버 배포** | VMware VM으로 설치할 수 없거나, 물리적 서버에 설치할 수 있습니다. | [배포](physical-azure-set-up-source.md#set-up-the-source-environment) 구성 서버 수동으로.


## <a name="process-server"></a>프로세스 서버

**설정** | **세부 정보** | **연결**
--- | --- | ---
**배포**  | 프로세스 서버를 재해 복구 및 온-프레미스 VMware Vm 및 물리적 서버 복제의 경우 필요한 온-프레미스입니다. 기본적으로 프로세스 서버는 배포할 때 구성 서버에 설치 됩니다. | [자세히 알아보기](vmware-azure-architecture.md?#architectural-components).
**역할 (온-프레미스** | -복제용으로 설정 된 컴퓨터에서 복제 데이터를 수신 합니다.<br/> --캐싱, 압축 및 암호화를 사용 하 여 복제 데이터를 최적화 하는 중 하 고 Azure Storage로 보냅니다.<br/> -온-프레미스 VMware Vm 및 물리적 서버 복제를 Site Recovery 모바일 서비스의 강제 설치를 수행 합니다.<br/> -온-프레미스 컴퓨터의 자동 검색을 수행 합니다. | [자세히 알아보기](vmware-physical-azure-config-process-server-overview.md#process-server). 
**역할 (Azure에서 장애 복구)** | 온-프레미스 사이트에서 장애 조치 후 Azure에서 프로세스 서버도 설정한 Azure VM에 온-프레미스 위치로 장애 복구를 처리할 수 있습니다.<br/><br/> Azure에서 프로세스 서버는 일시적입니다. 장애 복구를 완료 한 후 Azure VM은 삭제할 수 있습니다. | [자세히 알아보기](vmware-azure-set-up-process-server-azure.md).
**크기 조정** | 대규모 배포의 경우 온-프레미스 추가, 스케일 아웃 프로세스 서버를 설정할 수 있습니다. 추가 서버 컴퓨터와 복제 트래픽을 더 큰 볼륨을 복제 하는 더 많은 처리 용량을 확장 합니다.<br/><br/> 복제 트래픽 분산을 로드 하기 위해 두 프로세스 서버 간에 컴퓨터를 이동할 수 있습니다. | [자세한](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>마스터 대상 서버

마스터 대상 서버는 Azure에서 장애 복구 중 복제 데이터를 처리합니다.

- 구성 서버에서 기본적으로 설치 됩니다.
- 대규모 배포의 경우 장애 복구를 위해 추가적인 별도의 마스터 대상 서버를 추가할 수 있습니다.


## <a name="next-steps"></a>다음 단계
- 검토 합니다 [아키텍처](vmware-azure-architecture.md) VMware Vm 및 물리적 서버의 재해 복구에 대 한 합니다.
- 검토 합니다 [요구 사항 및 필수 구성 요소](vmware-physical-azure-support-matrix.md) VMware Vm 및 물리적 서버에서 Azure로의 재해 복구에 대 한 합니다. 
