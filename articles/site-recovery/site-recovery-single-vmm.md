
---
title: 'Azure Site Recovery: 단일 VMM 서버에 Hyper-V 가상 컴퓨터 복제 | Microsoft Docs'
description: 이 문서에서는 단일 VMM 서버만 있는 경우 Hyper-V 가상 컴퓨터를 복제하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 08/24/2016
ms.author: raynew

---
# 단일 VMM 서버에서 Hyper-V 가상 컴퓨터 복제
이 문서에서는 배포에 VMM 서버가 하나만 있는 경우 VMM 클라우드에서 Hyper-V 호스트 서버에 있는 Hyper-V 가상 컴퓨터를 복제하는 방법을 설명합니다.

Azure에는 리소스를 만들고 작업하기 위한 두 가지 [배포 모델](../resource-manager-deployment-model.md)인 Azure Resource Manager 모델과 클래식 모델이 있습니다. 또한 Azure에는 두 가지 포털이 있는데, 하나는 클래식 배포 모델을 지원하는 Azure 클래식 포털이고 다른 하나는 두 가지 배포 모델을 모두 지원하는 Azure 포털입니다. 이 문서에는 Azure 포털에서 복제를 설정하기 위한 지침이 나와 있습니다.

이 문서를 읽은 후 질문이 있다면 문서 맨 아래 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 Disqus 의견을 게시해 주세요.

## 개요
VMM의 Hyper-V 호스트에 있는 Hyper-V VM을 복제하려고 하며 VMM 서버가 하나만 있는 경우 [Azure에 복제](site-recovery-vmm-to-azure.md)하거나 단일 VMM 서버의 클라우드 간에 복제할 수 있습니다.

클라우드 간에 복제할 때는 장애 조치(failover) 및 복구가 원활하지 않고 많은 수동 단계가 필요하므로 Azure로 복제하는 것이 좋습니다. VMM 서버만 사용하여 복제하려는 경우 다음을 수행할 수 있습니다.

* 단일 독립 실행형 VMM 서버로 복제
* 확장된 Windows 클러스터에 배포된 단일 VMM 서버로 복제

## 단일 독립 실행형 VMM 서버로 사이트 간 복제
![독립 실행형 가상 VMM 서버](./media/site-recovery-single-vmm/single-vmm-standalone.png)

이 시나리오에서는 단일 VMM 서버를 기본 사이트에서 가상 컴퓨터로 배포하고 Site Recovery 및 Hyper-V 복제본을 사용하여 이 VM을 보조 사이트로 복제합니다.

1. Hyper-V VM에 VMM을 설정합니다. 시간을 절약하려면 VMM에 사용되는 SQL Server 인스턴스를 동일한 VM에 설치하는 것이 좋습니다. SQL Server의 원격 인스턴스를 사용하려는 경우 중단이 발생하면 VMM을 복구하기 전에 해당 인스턴스를 복구해야 합니다.
2. VMM 서버에 클라우드가 2개 이상 구성되도록 해야 합니다. 하나의 클라우드는 복사할 VM을 포함하고, 다른 클라우드는 보조 위치로 사용됩니다. 보호하려는 VM이 포함된 클라우드에는 다음이 있어야 합니다.
   
   * 각 호스트 그룹에 하나 이상의 Hyper-V 호스트 서버가 포함된 하나 이상의 VMM 호스트 그룹
   * 각 Hyper-V 호스트 서버에 있는 하나 이상의 Hyper-V 가상 컴퓨터
3. 복구 서비스 자격 증명 모음을 만들고, 자격 증명 모음 등록 키를 생성하여 다운로드 하고, 자격 증명 모음에 포함된 VMM 서버를 등록합니다. 등록 중에 VMM 서버에 Azure Site Recovery 공급자를 설치합니다.
4. VMM VM에 하나 이상의 클라우드를 설정하고, 이러한 클라우드에 Hyper-V 호스트를 추가합니다.
5. 클라우드에 대한 보호 설정을 구성합니다. 원본 및 대상 위치로 단일 VMM 서버의 이름을 지정합니다. 네트워크 매핑을 구성하려면 보호할 VM을 포함하는 클라우드에 대한 VM 네트워크를 복제 클라우드에 대한 VM 네트워크에 매핑합니다.
6. 두 개의 클라우드가 모두 동일한 서버에 위치하기 때문에, 네트워크를 통해 보호하려는 VM에 대한 초기 복제를 활성화합니다.
7. Hyper-V 관리자 콘솔에서 VMM VM을 포함하는 Hyper-V 호스트의 Hyper-V 복제본을 사용하도록 설정하고 VM의 복제를 활성화합니다. Site Recovery에 의해 보호되는 클라우드에는 VMM VM을 추가하지 마세요. 이렇게 해야 Hyper-V 복제본 설정이 Site Recovery에 의해 재정의되지 않습니다.
8. 복구 계획을 생성하려면 원본과 대상에 동일한 VMM 서버를 지정합니다.

[이 문서](site-recovery-vmm-to-vmm.md)의 지침에 따라 자격 증명 모음을 만들고, 서버를 등록하고, 보호를 설정합니다.

### 가동 중단 시 수행할 작업
완전한 중단이 발생하여 보조 사이트에서 작동해야 하는 경우 다음을 수행합니다.

1. 보조 사이트의 Hyper-V 관리자 콘솔에서 계획되지 않은 장애 조치를 실행하여 기본 사이트에서 보조 사이트로 VMM VM을 장애 조치(failover)합니다.
2. 보조 사이트에서 VMM VM이 작동하고 있는지 확인합니다.
3. 복구 서비스 자격 증명 모음에는 계획되지 않은 장애 조치를 실행하여 기본 사이트에서 보조 사이트로 워크로드 VM을 장애 조치(failover)합니다. VM의 계획되지 않은 장애 조치(failover)를 완료하려면 장애 조치(failover)를 커밋하거나 필요에 따라 다른 복구 지점을 선택합니다.
4. 계획되지 않은 장애 조치(failover)가 완료되면 사용자는 보조 사이트의 워크로드 리소스에 액세스할 수 있습니다.

기본 사이트가 다시 정상적으로 작동하는 경우 다음을 수행합니다.

1. Hyper-V 관리자 콘솔에서 VMM VM에 대한 역방향 복제를 사용하도록 설정하여 보조 사이트에서 기본 사이트로 복제를 시작합니다.
2. Hyper-V 관리자 콘솔에서 계획된 장애 조치(failover)를 실행하여 VMM VM을 기본 사이트로 장애 복구(failback)합니다. 장애 조치(failover)를 커밋하여 완료합니다. 그런 후 역방향 복제를 사용하도록 설정하여 기본 사이트에서 보조 사이트로 VMM 복제를 시작합니다.
3. 복구 서비스 자격 증명 모음에서 워크로드 VM에 대한 역방향 복제를 사용하도록 설정하여 보조 복제본에서 주 복제본으로 복제를 시작합니다.
4. 복구 서비스 자격 증명 모음에서 계획된 장애 조치를 실행하여 워크로드 VM을 기본 사이트로 장애 복구(failback)합니다. 장애 조치(failover)를 커밋하여 완료합니다. 그런 후 역방향 복제를 사용하도록 설정하여 기본 사이트에서 보조 사이트로의 워크로드 VM 복제를 시작합니다.

## 늘어난(stretched) 클러스터의 단일 VMM 서버로 사이트 간 복제
![클러스터된 가상 VMM 서버](./media/site-recovery-single-vmm/single-vmm-cluster.png)

독립 실행형 VMM 서버를 보조 사이트로 복제되는 VM으로 배포하는 대신 VMM을 Windows 장애 조치(failover) 클러스터의 VM으로 배포하면 VMM의 가용성을 높일 수 있습니다. 그러면 워크로드 복원력이 제공되고 하드웨어 장애로부터 보호됩니다. Site Recovery와 함께 배포하기 위해서 VMM VM은 지리적으로 떨어져 있는 사이트에 걸친 확대 클러스터에 배포되어야 합니다. 다음을 수행합니다.

1. Windows 장애 조치(failover) 클러스터의 가상 컴퓨터에 VMM을 설치하고, 설치하는 동안 항상 서버를 실행할 수 있도록 옵션을 선택합니다.
2. 보조 사이트에 데이터베이스 복제본이 존재하도록 VMM에 사용되는 SQL Server 인스턴스는 SQL Server AlwaysOn 가용성 그룹과 함께 복제되어야 합니다.
3. [이 문서](site-recovery-vmm-to-vmm.md)의 지침에 따라 자격 증명 모음을 만들고, 서버를 등록하고, 보호를 설정합니다. 복구 서비스 자격 증명 모음에서 클러스터의 각 VMM 서버를 등록해야 합니다. 이를 위해 활성 노드에 공급자를 설치하고 VMM 서버를 등록합니다. 그런 다음 다른 노드에 공급자를 설치합니다.

### 가동 중단 시 수행할 작업
중단이 발생하면 VMM 서버 및 해당 SQL Server 데이터베이스에 장애 조치가 수행되고 보조 사이트에서 액세스됩니다.

## 다음 단계
VMM 간 복제에 대한 Site Recovery 배포에 대해 [자세히 알아봅니다](site-recovery-vmm-to-vmm.md).

<!---HONumber=AcomDC_0824_2016-->