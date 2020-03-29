---
title: Azure 사이트 복구 구성/프로세스/마스터 대상 서버 소개
description: 이 문서에서는 온-프레미스 VM웨어 VM의 재해 복구를 Azure 사이트 복구를 사용하여 Azure에 설정할 때 사용하는 구성, 프로세스 및 마스터 대상 서버에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062085"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>사이트 복구 구성 요소 정보(구성, 프로세스, 마스터 대상)

이 문서에서는 VMware VM 및 물리적 서버를 Azure에 복제하는 데 [사이트 복구](site-recovery-overview.md) 서비스에서 사용하는 구성, 프로세스 및 마스터 대상 서버에 대해 설명합니다.

## <a name="configuration-server"></a>구성 서버

온-프레미스 VMware VM 및 물리적 서버의 재해 복구를 위해 온-프레미스 사이트 복구 구성 서버를 배포합니다.

**설정** | **세부 정보** | **링크**
--- | --- | ---
**구성 요소**  | 구성 서버 컴퓨터는 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함하는 모든 온-프레미스 사이트 복구 구성 요소를 실행합니다.<br/><br/> 구성 서버를 설정하면 모든 구성 요소가 자동으로 설치됩니다. | 구성 서버 FAQ를 [읽어보십시오.](vmware-azure-common-questions.md#configuration-server)
**Role** | 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다. | Azure에 대한 [VMware](vmware-azure-architecture.md) 및 [물리적 서버](physical-azure-architecture.md) 재해 복구에 대한 아키텍처에 대해 자세히 알아봅니다.
**VMware 요구 사항** | 온-프레미스 VMware VM의 재해 복구를 위해 구성 서버를 온-프레미스, 가용성이 높은 VMware VM으로 설치하고 실행해야 합니다. | 필수 구성 방법에 [대해 알아봅니다.](vmware-azure-deploy-configuration-server.md#prerequisites)
**VM웨어 배포** | 다운로드한 OVA 템플릿을 사용하여 구성 서버를 배포하는 것이 좋습니다. 이 메서드는 모든 요구 사항 및 필수 구성 구성 을 준수 하는 구성 서버를 설정 하는 간단 하 게 방법을 제공 합니다.<br/><br/> 어떤 이유로 OVA 템플릿을 사용하여 VMware VM을 배포할 수 없는 경우 물리적 컴퓨터 재해 복구를 위해 아래에 설명된 대로 구성 서버 컴퓨터를 수동으로 설정할 수 있습니다. | OVA 템플릿을 통해 [배포합니다.](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)
**물리적 서버 요구 사항** | 온-프레미스 물리적 서버에서 재해 복구를 위해 구성 서버를 수동으로 배포합니다. | 필수 구성 방법에 [대해 알아봅니다.](physical-azure-set-up-source.md#prerequisites)
**물리적 서버 배포** | VMware VM으로 설치할 수 없는 경우 실제 서버에 설치할 수 있습니다. | 구성 서버를 수동으로 [배포합니다.](physical-azure-set-up-source.md#set-up-the-source-environment)

## <a name="process-server"></a>프로세스 서버

프로세스 서버는 장애 조치 및 장애 조치 중에 복제 데이터를 처리하고 온-프레미스 VM웨어 VM 및 물리적 서버에 대한 Mobility 서비스를 설치합니다.

**설정** | **세부 정보** | **링크**
--- | --- | ---
**배포**  | 기본적으로 구성 서버가 배포될 때 프로세스 서버가 설치됩니다. <br/><br/> 온-프레미스 프로세스 서버는 온-프레미스 VM웨어 VM 및 물리적 서버의 재해 복구 및 복제를 위해 필요합니다. | [자세히 알아봅니다](vmware-azure-architecture.md#architectural-components).
**역할(온-프레미스)** | 복제에 사용하도록 설정된 컴퓨터에서 복제 데이터를 받습니다. <br/><br/> 캐싱, 압축 및 암호화를 사용하여 복제 데이터를 최적화하고 Azure Storage로 보냅니다. <br/><br/> 복제하려는 온-프레미스 VMware VM 및 물리적 서버에서 사이트 복구 모빌리티 서비스의 푸시 설치를 수행합니다. <br/><br/> 온-프레미스 시스템의 자동 검색을 수행합니다. | [자세히 알아봅니다](vmware-azure-enable-replication.md).
**역할(Azure에서 장애 조치)** | 온-프레미스 사이트에서 장애 조치 후 AzureVM으로 Azure에서 프로세스 서버를 설정하여 온-프레미스 위치로 장애 복구를 처리합니다.<br/><br/> Azure의 프로세스 서버는 일시적입니다. 장애 복구가 완료된 후 Azure VM을 삭제할 수 있습니다. | [자세히 알아봅니다](vmware-azure-set-up-process-server-azure.md).
**스케일링** | 대규모 배포의 경우 온-프레미스에서 추가 확장 프로세스 서버를 설정할 수 있습니다. 추가 서버는 더 많은 수의 복제 컴퓨터와 더 많은 양의 복제 트래픽을 처리하여 용량을 확장합니다.<br/><br/> 복제 트래픽의 균형을 맞추기 위해 두 프로세스 서버 간에 컴퓨터를 이동할 수 있습니다. | [자세히 알아봅니다](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>마스터 대상 서버

마스터 대상 서버는 Azure에서 장애 복구 중 복제 데이터를 처리합니다.

- 기본적으로 마스터 대상 서버는 구성 서버에 설치됩니다.
- 대규모 배포의 경우 장애 복구를 위해 추가적인 별도의 마스터 대상 서버를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- VMware VM 및 물리적 서버의 재해 복구를 위한 [아키텍처를](vmware-azure-architecture.md) 검토합니다.
- Azure에 대한 VMware VM 및 물리적 서버의 재해 복구에 대한 요구 사항 및 [필수 구성 조건을](vmware-physical-azure-support-matrix.md) 검토합니다.
