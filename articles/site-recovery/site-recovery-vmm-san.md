---
title: "Azure Site Recovery를 사용하여 SAN으로 VMM에서 Hyper-V VM 복제 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery에서 SAN 복제를 사용하여 두 사이트 간에 Hyper-V 가상 컴퓨터를 복제하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 75653b84d6ccbefe7d5230449bea81f498e10a98
ms.openlocfilehash: 09fb09bfdea2e18384851bb8ed9a4f8c08466dd2


---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-using-san"></a>SAN을 사용하여 Azure Site Recovery로 보조 사이트에 VMM 클라우드의 Hyper-V VM 복제


보조 VMM 사이트에 Hyper-V VM(System Center VMM 클라우드에서 관리됨) 복제를 관리하기 위해 [Site Recovery](site-recovery-overview.md)를 배포하려는 경우 이 문서를 사용합니다.


여기에는 VMM에 SAN 저장소 구성 및 Site Recovery 포털에서 복제를 설정하기 위한 시나리오 개요, 지침이 포함됩니다. 끝으로, 장애 조치(Failover)를 테스트하여 모두 예상대로 작동하는지 확인합니다.

하단에 의견을 남겨주세요. 기술 관련 질문에 대한 답변은 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 얻으세요.


## <a name="why-replicate-with-san-and-site-recovery"></a>SAN 및 Site Recovery를 사용하여 복제하는 이유는 무엇입니까?

* SAN은 엔터프라이즈 수준의 확장성 있는 복제 솔루션을 제공하기 때문에 SAN과 Hyper-V를 포함하는 기본 사이트에서 SAN을 사용하여 LUN을 보조 사이트에 복제할 수 있습니다. 저장소는 VMM에서 관리되며 복제 및 장애 조치(Failover)는 Site Recovery를 통해 오케스트레이션됩니다.
* Site Recovery는 몇 가지 [SAN 저장소 파트너](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)와 작동하여 파이버 채널 및 iSCSI 저장소에 복제를 제공합니다.  
* 기존 SAN 인프라를 활용하여 Hyper-V 클러스터에 배포된 중요 업무용 앱을 보호합니다. VM은 그룹으로 복제될 수 있기 때문에 N계층 앱이 지속적으로 장애 조치(Failover)될 수 있습니다.
* SAN 복제는 저장소 배열 기능에 따라 낮은 RTO 및 RPO에 대해 동기화된 복제와 뛰어난 유연성을 위해 비동기화된 복제를 사용하여 다양한 응용프로그램 계층 간의 복제 일관성을 보장합니다.  
* VMM 패브릭에서 SAN 저장소를 관리할 수 있고 VMM에서 SMI-S를 사용하여 기존 저장소를 검색할 수 있습니다.  

다음 사항에 유의하세요.
- SAN을 사용한 사이트 간 복제는 Azure Portal에서 사용할 수 없습니다. 클래식 포털에서만 사용할 수 있습니다. 새 자격 증명 모음은 클래식 포털에서 만들 수 없습니다. 기존 자격 증명 모음은 유지 관리될 수 있습니다.
- SAN에서 Azure로 복제는 지원되지 않습니다.
- iSCSI 또는 파이버 채널을 통해 VM에 직접 연결된 공유 VHDX 또는 논리적 단위(LUN)를 복제할 수 없습니다. 게스트 클러스터는 복제될 수 있습니다.


## <a name="architecture"></a>아키텍처

![SAN 아키텍처](./media/site-recovery-vmm-san/architecture.png)

- **Azure**: Azure Portal에서 Site Recovery 자격 증명 모음을 설정합니다.
- **SAN 저장소**: SAN 저장소는 VMM 패브릭에서 관리됩니다. 저장소 공급자를 추가하고, 저장소 분류를 만들고, 저장소 풀을 설정합니다.
- **VMM 및 Hyper-V**: 각 사이트에 VMM 서버를 사용하는 것이 좋습니다. VMM 사설 클라우드를 설정하고 클라우드에 Hyper-V 클러스터를 배치합니다. 배포 중에 Azure Site Recovery 공급자가 각 VMM 서버에 설치되고 서버가 자격 증명 모음에 등록됩니다. 공급자는 복제, 장애 조치(failover) 및 장애 복구(failback)를 관리하기 위해 Site Recovery 서비스와 통신합니다.
- **복제**: VMM에서 저장소를 설정하고 Site Recovery에서 복제를 구성한 후 기본 및 보조 SAN 저장소 간에 복제가 발생합니다. 복제 데이터는 Site Recovery에 전송되지 않습니다.
- **장애 조치(failover)**: 장애 조치(failover)는 Site Recovery 포털에서 사용하도록 설정합니다. 복제가 동기 상태이기 때문에 장애 조치(failover) 중에 데이터가 손실되지 않습니다.
- **장애 복구(failback)**: 장애 복구(failback)를 수행하려면 보조 사이트에서 기본 사이트로 델타 변경을 전송하기 위해 역방향 복제를 사용하도록 설정합니다. 역방향 복제가 완료된 후 보조 사이트에서 기본 사이트로 계획된 장애 조치(failover)를 실행합니다. 계획된 장애 조치(failover)는 복제본 VM을 보조 사이트에서 중지하고 기본 사이트에서 시작합니다.


## <a name="before-you-start"></a>시작하기 전에


**필수 구성 요소** | **세부 정보**
--- | ---
**Azure** |[Microsoft Azure](https://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. 사이트 복구 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/). 복제 및 장애 조치(failover)를 구성하고 관리할 Azure Site Recovery 자격 증명 모음을 만듭니다.
**VMM** | 단일 VMM 서버를 사용하고 다른 클라우드 간에 복제할 수 있지만 VMM을 기본 사이트에 두고 보조 사이트에도 두는 것이 좋습니다. VMM은 물리적 또는 가상 독립 실행형 서버나 클러스터로 배포될 수 있습니다. <br/><br/>VMM 서버는 최신 누적 업데이트를 설치한 System Center 2012 R2 이상을 실행해야 합니다.<br/><br/> 보호할 기본 VMM 서버에 클라우드가 하나 이상 구성되어야 하고 장애 조치(failover)에 사용할 보조 VMM 서버에 클라우드가 하나 구성되어 있어야 합니다.<br/><br/> 원본 클라우드에 하나 이상의 VMM 호스트 그룹이 있어야 합니다.<br/><br/> 모든 VMM 클라우드에 Hyper-V 용량 프로필이 설정되어 있어야 합니다.<br/><br/> VMM 클라우드 설정에 대해 자세히 알아보세요.(Https://technet.microsoft.com/en-us/system-center-docs/vmm/scenario/cloud-overview).
**Hyper-V** | 기본 및 보조 VMM 클라우드에 있는 하나 이상의 Hyper-V 클러스터가 있어야 합니다.<br/><br/> 원본 Hyper-V 클러스터는 하나 이상의 VM을 포함해야 합니다.<br/><br/> 기본 및 보조 사이트의 VMM 호스트 그룹은 Hyper-V 클러스터를 하나 이상 포함해야 합니다.<br/><br/>호스트 및 대상 Hyper-V 서버는 Hyper-V 역할로 Windows Server 2012 이상을 실행해야 하며 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> 클러스터에서 Hyper-V를 실행하고 있다면 고정 IP 주소 기반 클러스터가 있는 경우 클러스터 broker가 자동으로 만들어지지 않습니다. 수동으로 구성해야 합니다. Aidan Finn의 게시물에서 [자세히 알아보세요](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters).
**SAN 저장소** | 게스트 클러스터형 가상 컴퓨터를 iSCSI 또는 채널 저장소나 공유 가상 하드 디스크(vhdx)를 사용하여 복제할 수 있습니다.<br/><br/> SAN 배열이 기본 사이트와 보조 사이트에 하나씩 두 개 필요합니다.<br/><br/> 배열 간에 네트워크 인프라를 설정해야 합니다. 피어링 및 복제를 구성해야 합니다. 저장소 배열 요구 사항에 따라 복제 라이선스를 설정해야 합니다.<br/><br/>호스트가 ISCSI 또는 파이버 채널을 사용하여 저장소 LUN과 통신할 수 있도록 Hyper-V 호스트 서버와 저장소 배열 간에 네트워킹을 설정합니다.<br/><br/> [지원되는 저장소 배열](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)을 확인합니다.<br/><br/> 저장소 배열 제조업체를 통해 제공되는 SMI-S 공급자를 설치해야 하며 SAN 배열을 공급자를 통해 관리해야 합니다. 제조업체 지침에 따라 공급자를 설정합니다.<br/><br/>VMM 서버가 IP 주소 또는 FQDN을 사용하여 네트워크를 통해 액세스할 수 있는 서버에 배열의 SMI-S 공급자가 있는지 확인합니다.<br/><br/> 각 SAN 배열에 사용할 수 있는 저장소 풀이 하나 이상 있어야 합니다.<br/><br/> 기본 VMM 서버가 기본 배열을 관리하고 보조 VMM 서버가 보조 배열을 관리해야 합니다.
**네트워크 매핑** | 장애 조치(Failover) 후에 복제된 가상 컴퓨터가 보조 Hyper-V 호스트 서버에 최적으로 배치되고 적절한 VM 네트워크에 연결될 수 있도록 네트워크 매핑을 설정합니다. 네트워크 매핑을 구성하지 않으면 장애 조치(failover) 후 복제본 VM이 네트워크에 연결되지 않습니다.<br/><br/> Site Recovery 배포 중에 네트워크 매핑을 설정할 수 있도록 VMM 네트워크가 제대로 구성되었는지 확인해야 합니다. VMM에서 VM, 원본 Hyper-V 호스트가 VMM VM 네트워크에 연결되어야 합니다. 해당 네트워크가 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.<br/><br/> 대상 클라우드에 해당 VM 네트워크가 있어야 하고 이것은 대상 클라우드와 연결된 해당 논리 네트워크에 연결되어 있어야 합니다.<br/><br/>[자세히 알아봅니다](site-recovery-network-mapping.md) 를 확인해 보세요.

## <a name="step-1-prepare-the-vmm-infrastructure"></a>1단계: VMM 인프라 준비
다음과 같이 필요한 VMM 인프라 준비 방법:

1. VMM 클라우드 확인
2. VMM에서 SAN 저장소 통합 및 분류
3. LUN을 만들고 저장소 할당
4. 복제 그룹 만들기
5. VM 네트워크 설정

### <a name="verify-vmm-clouds"></a>VMM 클라우드 확인

Site Recovery 배포를 시작하기 전에 VMM 클라우드가 올바르게 설정되었는지 확인합니다.

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>VMM 패브릭에서 SAN 저장소 통합 및 분류

1. VMM 콘솔에서 **패브릭** > **저장소** > **리소스 추가** > **저장 장치**를 클릭합니다.
2. 저장 장치 추가 마법사 > **저장소 공급자 유형 선택**에서 **SMI-S 공급자에서 검색하고 관리하는 SAN 및 NAS 장치**를 선택합니다.


    ![공급자 유형](./media/site-recovery-vmm-san/provider-type.png)
3. **저장소 SMI-S 공급자의 프로토콜 및 주소 지정** 페이지에서 **SMI-S CIMXML**을 선택하고 공급자에 연결하기 위한 설정을 지정합니다.
4. **공급자 IP 주소 또는 FQDN** 및 **TCP/IP 포트**에서 공급자에 연결하기 위한 설정을 지정합니다. SMI-S CIMXML에 대해서만 SSL 연결을 사용할 수 있습니다.

    ![공급자 연결](./media/site-recovery-vmm-san/connect-settings.png)
5. **실행 계정** 에서 공급자에 액세스할 수 있는 VMM 실행 계정을 지정하거나 새 계정을 만듭니다.
6. **정보 수집** 페이지에서 VMM은 자동으로 저장소를 검색하고 가져오려고 합니다. 검색을 다시 시도하려면 **검색 공급자**를 클릭합니다. 검색 프로세스에 성공하면 검색된 저장소 배열, 저장소 풀, 제조업체, 모델 및 용량이 페이지에 나열됩니다.

    ![저장소 검색](./media/site-recovery-vmm-san/discover.png)
7. **관리할 저장소 풀을 선택한 후 분류 할당**에서 VMM이 관리할 저장소 풀을 선택한 후 분류를 할당합니다. LUN 정보는 저장소 풀에서 가져옵니다. 보호해야 하는 응용 프로그램, 응용 프로그램의 용량 요구 사항 및 무엇을 함께 복제해야 하는지에 대한 요구 사항을 기준으로 LUN을 만듭니다.

    ![저장소 분류](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>LUN을 만들고 저장소 할당

보호해야 하는 응용 프로그램, 용량 요구 사항 및 함께 복제할 항목을 기반으로 LUN을 만듭니다.

1. 저장소가 VMM 패브릭에 표시된 후, [LUN을 프로비전](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm)할 수 있습니다.

     > [!NOTE]
     > LUN에 복제에 사용하도록 설정된 VM용 VHD를 추가하지 마세요. (LUN이 Site Recovery 복제 그룹 내에 있지 않은 경우) 그렇게 하면 Site Recovery에서 검색되지 않습니다.
     >
     
2. 그런 다음 VMM이 프로비전된 저장소에 가상 컴퓨터 데이터를 배포할 수 있도록 Hyper-V 호스트에 저장소 용량을 할당합니다.

   * 클러스터에 저장소를 할당하려면 먼저 클러스터가 있는 VMM 호스트 그룹에 저장소를 할당해야 합니다. 호스트 그룹에 [저장소 풀](https://technet.microsoft.com/library/gg610635.aspx) 및 [저장소 논리 단위 할당](https://technet.microsoft.com/library/gg610686.aspx)에 대해 자세히 알아봅니다.</a>.
   * 그런 다음 [VMM에서 Hyper-V 호스트 클러스터에 저장소를 구성하는 방법](https://technet.microsoft.com/library/gg610692.aspx)에 설명된 대로 클러스터에 저장소 용량을 할당합니다.</a>.

    ![공급자 유형](./media/site-recovery-vmm-san/provider-type.png)
3. **저장소 SMI-S 공급자의 프로토콜 및 주소 지정**에서 **SMI-S CIMXML**을 선택합니다. 공급자에 연결하기 위한 설정을 지정합니다. SMI-S CIMXML에 대해서만 SSL 연결을 사용할 수 있습니다.

    ![공급자 연결](./media/site-recovery-vmm-san/connect-settings.png)
4. **실행 계정** 에서 공급자에 액세스할 수 있는 VMM 실행 계정을 지정하거나 새 계정을 만듭니다.
5. **정보 수집**에서 VMM은 저장 장치 정보를 자동으로 검색하고 가져오려고 합니다. 다시 시도하려면 **스캔 공급자**를 클릭합니다. 검색 프로세스에 성공하면 저장소 배열, 저장소 풀, 제조업체, 모델 및 용량이 페이지에 나열됩니다.

    ![저장소 검색](./media/site-recovery-vmm-san/discover.png)
7. **관리할 저장소 풀을 선택한 후 분류 할당**에서 VMM이 관리할 저장소 풀을 선택한 후 분류를 할당합니다. LUN 정보는 저장소 풀에서 가져옵니다.

    ![저장소 분류](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>복제 그룹 만들기

함께 복제해야 하는 LUN을 모두 포함하는 복제 그룹을 만듭니다.

1. VMM 콘솔에서 저장소 배열 속성의 **복제 그룹** 탭을 열고 **새로 만들기**를 클릭합니다.
2. 복제 그룹을 만듭니다.


    ![SAN 복제 그룹](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>네트워크 설정

네트워크 매핑을 구성하려면 다음을 수행합니다.

1. [네트워크 매핑](site-recovery-network-mapping.md) 에 대해 자세히 알아봅니다.
2. VMM에서 VM 네트워크를 준비합니다.

   * [논리 네트워크를 설정합니다](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks).
   * [VM 네트워크를 설정합니다](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks).


## <a name="step-2-create-a-vault"></a>2단계: 자격 증명 모음 만들기

1. 자격 증명 모음에 등록할 VMM 서버에서 [관리 포털](https://portal.azure.com) 에 로그인합니다.
2. **Data Services** > **Recovery Services**를 확장하고 **Site Recovery 자격 증명 모음**을 클릭합니다.
3. **새로 만들기** > **빠른 생성**을 클릭합니다.
4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.
5. **하위 지역** 에서 자격 증명 모음에 대한 지리적 하위 지역을 선택합니다. 지원되는 하위 지역을 확인하려면 [Azure Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하세요.
6. **자격 증명 모음 만들기**를 클릭합니다.

    ![새 자격 증명 모음](./media/site-recovery-vmm-san/create-vault.png)

상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 **Recovery Services** 페이지에서 **활성** 으로 나열됩니다.

### <a name="register-the-vmm-servers"></a>VMM 서버 등록

1. **복구 서비스** 페이지에서 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

    ![빠른 시작 아이콘](./media/site-recovery-vmm-san/quick-start-icon.png)
2. 드롭다운 목록에서 **배열 복제를 사용하여 Hyper-V 온-프레미스 사이트 간**을 선택합니다.

    ![등록 키](./media/site-recovery-vmm-san/select-san.png)
3. **VMM 서버 준비**에서 Azure Site Recovery 공급자 설치 파일의 최신 버전을 다운로드합니다.
4. 원본 VMM 서버에서 이 파일을 실행합니다. VMM이 클러스터에 배포되고 공급자를 처음 설치하는 경우 활성 노드에 공급자를 설치하고 설치를 완료하여 VMM 서버를 자격 증명 모음에 등록합니다. 그런 후에 다른 노드에 공급자를 설치합니다. 공급자를 업그레이드하는 경우 동일한 공급자 버전을 갖도록 모든 노드에서 업그레이드해야 합니다.
5. 설치 관리자는 요구 사항을 확인하고 공급자 설정을 시작하기 위해 VMM 서비스를 중지하는 권한을 요청합니다. 설정이 완료되면 서비스가 자동으로 다시 시작됩니다. VMM 클러스터에 클러스터 역할을 중지하라는 메시지가 표시됩니다.
6. **Microsoft 업데이트**에서 업데이트를 선택하면 공급자 업데이트가 Microsoft 업데이트 정책에 따라 설치됩니다.

    ![Microsoft 업데이트](./media/site-recovery-vmm-san/ms-update.png)
    
7. 기본적으로 공급자의 설치 위치는 <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin입니다. 시작하려면 **설치**를 클릭합니다.

    ![InstallLocation](./media/site-recovery-vmm-san/install-location.png)
    
8. 공급자가 설치된 후 **등록** 을 클릭하여 자격 증명 모음에 VMM 서버를 등록합니다.

    ![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)
    
9. **인터넷 연결**에서 공급자를 인터넷에 연결하는 방법을 지정합니다. 서버의 기본 인터넷 연결 설정을 사용하려면 **기본 시스템 프록시 설정 사용**을 선택합니다.

    ![인터넷 설정](./media/site-recovery-vmm-san/proxy-details.png)

   * 사용자 지정 프록시를 사용하려면 공급자를 설치하기 전에 설정합니다. 사용자 지정 프록시 설정을 구성하면 테스트가 실행되어 프록시 연결을 확인합니다.
   * 사용자 지정 프록시를 사용하거나 기본 프록시에 인증이 필요한 경우 주소와 포트를 비롯한 프록시 정보를 입력해야 합니다.
   * 다음 [URL](site-recovery-best-practices.md#url-access)은 VMM 서버에서 액세스할 수 있습니다.
   * 사용자 지정 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 인증할 수 있도록 프록시 서버를 구성합니다. 실행 계정 설정은 VMM 콘솔(**설정** > **보안** > **실행 계정** > **DRAProxyAccount**)에서 수정할 수 있습니다. 변경 내용이 적용되려면 VMM 서비스를 다시 시작해야 합니다.
10. **등록 키**에서 포털에서 다운로드하고 VMM 서버에 복사한 키를 선택합니다.
11. **자격 증명 모음 이름**에서 서버를 등록할 자격 증명 모음의 이름을 확인합니다.

    ![서버 등록](./media/site-recovery-vmm-san/vault-creds.png)
12. 암호화 설정은 VMM에서 Azure로 복제에만 사용됩니다. 무시해도 됩니다.

    ![서버 등록](./media/site-recovery-vmm-san/encrypt.png)
13. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다.
14. **초기 클라우드 메타데이터 동기화**에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 동기화할지 여부를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다.


    ![서버 등록](./media/site-recovery-vmm-san/friendly-name.png)
15. **다음** 을 클릭하여 프로세스를 완료합니다. 등록 후에 VMM 서버의 메타데이터가 Azure Site Recovery에 의해 검색됩니다. 서버가 자격 증명 모음의 **서버** > **VMM 서버**에 표시됩니다.

### <a name="command-line-installation"></a>명령줄 설치

다음 명령줄을 사용하여 Azure Site Recovery 공급자를 설치할 수도 있습니다. 이 방법은 Windows Server 2012 R2용 Server Core에 공급자를 설치하는 데 사용할 수 있습니다.

1. 공급자 설치 파일 및 등록 키를 폴더로 다운로드합니다. 예: C:\ASR.
2. VMM 서비스를 중지합니다.
3. 공급자 설치 관리자를 추출합니다. 관리자 권한으로 다음 명령을 실행합니다.

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. 공급자 설치:

        C:\ASR> setupdr.exe /i
5. 공급자 등록:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

매개 변수:

* **/Credentials**: 등록 키 파일이 있는 위치에 대한 필수 매개 변수입니다.  
* **/FriendlyName**: Azure Site Recovery 포털에 나타나는 Hyper-V 호스트 서버의 이름에 대한 필수 매개 변수입니다.
* **/EncryptionEnabled**: VMM에서 Azure로 복제할 때만 사용되는 선택적 매개 변수입니다.
* **/proxyAddress** : 프록시 서버의 주소를 지정하는 선택적 매개 변수입니다.
* **/proxyport** : 프록시 서버의 포트를 지정하는 선택적 매개 변수입니다.
* **/proxyUsername**: (프록시에 인증이 필요한 경우) 프록시 사용자 이름을 지정하는 선택적 매개 변수입니다.
* **/proxyPassword**: (프록시에 인증이 필요한 경우) 프록시 서버를 인증하기 위한 암호를 지정하는 선택적 매개 변수입니다.

## <a name="step-3-map-storage-arrays-and-pools"></a>3단계: 저장소 배열과 풀 매핑

기본 및 보조 배열을 매핑하여 기본 풀에서 복제 데이터를 수신하는 보조 저장소 풀을 지정합니다. 매핑 정보는 복제 그룹에 대한 보호를 사용하도록 설정할 때 사용되므로 복제를 구성하기 전에 저장소를 매핑합니다.

시작하기 전에 자격 증명 모음에서 VMM 클라우드가 표시되는지 확인합니다. 클라우드는 공급자 설치 중에 모든 클라우드를 동기화할 때 또는 VMM 콘솔에서 특정 클라우드를 동기화할 때 검색됩니다.

1. **리소스** > **서버 저장소** > **원본 및 대상 배열 매핑**을 클릭합니다.
    ![서버 등록](./media/site-recovery-vmm-san/storage-map.png)

2. 기본 사이트의 저장소 배열을 선택하고 보조 사이트의 저장소 배열에 매핑합니다. **저장소 풀** 에서 매핑할 원본 및 대상 저장소 풀을 선택합니다.


   ![서버 등록](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>4단계: 복제 설정 구성

VMM 서버가 등록된 후에 클라우드 보호 설정을 구성합니다.

1. 빠른 시작 페이지에서 **VMM 클라우드에 대해 보호 설정**을 클릭합니다.
2. **보호된 항목** 탭에서 클라우드 > **구성**을 선택합니다.
3. **대상**에서 **VMM**을 선택합니다.
4. **대상 위치**에서 복구에 사용할 클라우드를 관리하는 VMM 서버를 선택합니다.
5. **대상 클라우드**에서 VM 장애 조치(failover)에 사용할 대상 클라우드를 선택합니다.
   * 보호할 가상 컴퓨터의 복구 요구 사항을 충족하는 대상 클라우드를 선택하는 것이 좋습니다.
   * 클라우드는 단일 클라우드 쌍에 기본 클라우드 또는 대상 클라우드로만 속할 수 있습니다.
6. Site Recovery는 클라우드가 SAN 저장소에 액세스할 수 있는지, 스토리지 배열이 매핑되어 있는지 확인합니다.
7. 확인에 성공하면 **복제 유형**에서 **SAN**을 선택합니다.

설정을 저장한 후 작업이 만들어지고 **작업** 탭에서 모니터링될 수 있습니다. 설정은 **구성** 탭에서 수정될 수 있습니다. 대상 위치 또는 대상 클라우드를 수정하려면 클라우드 구성을 제거한 후 클라우드를 다시 구성해야 합니다.

## <a name="step-5-enable-network-mapping"></a>5단계: 네트워크 매핑 사용

1. 빠른 시작 페이지에서 **네트워크 매핑**을 클릭합니다.
2. 원본 VMM 서버를 선택한 다음 네트워크를 매핑할 대상 VMM 서버를 선택합니다. 원본 네트워크 및 연결된 대상 네트워크의 목록이 표시됩니다. 매핑되지 않은 네트워크에 대해서는 빈 값이 표시됩니다. 원본 및 대상 네트워크 이름 옆에 있는 정보 아이콘을 클릭하여 각 네트워크의 서브넷을 확인합니다.
3. **원본 네트워크**에서 네트워크를 선택하고 **매핑**을 클릭합니다. 서비스에서 대상 서버의 VM 네트워크를 감지하고 표시합니다.

    ![SAN 아키텍처](./media/site-recovery-vmm-san/network-map1.png)
4. 대상 VMM 서버의 VM 네트워크 중 하나를 선택합니다.


    ![SAN 아키텍처](./media/site-recovery-vmm-san/network-map2.png)
5. 대상 네트워크를 선택하면 원본 네트워크를 사용하는 보호된 클라우드가 표시됩니다. 사용 가능한 대상 네트워크도 표시됩니다. 복제에 사용 중인 모든 클라우드에 사용할 수 있는 대상 네트워크를 선택하는 것이 좋습니다.
6. 확인 표시를 클릭하여 매핑 프로세스를 완료합니다. 진행 상황을 추적하는 작업이 시작됩니다. **작업** 탭에서 작업을 확인할 수 있습니다.

## <a name="step-6-enable-replication-for-replication-groups"></a>6단계: 복제 그룹에 대해 복제 사용

가상 컴퓨터에 대해 보호를 사용하도록 설정하려면 먼저 저장소 복제 그룹에 대해 복제를 사용하도록 설정해야 합니다.

1. Site Recovery 포털에 있는 기본 클라우드의 속성 페이지에서 **Virtual Machines** 탭을 열고 **복제 그룹 추가**를 클릭합니다.
2. 클라우드와 연결된 VMM 복제 그룹을 하나 이상 선택하고 원본 및 대상 배열을 확인하고 복제 빈도를 지정합니다.

Site Recovery, VMM 및 SMI-S 공급자는 대상 사이트 저장소 LUN을 프로비전하고 저장소 복제를 사용하도록 설정합니다. 복제 그룹이 이미 복제된 경우 Site Recovery에서는 기존 복제 관계를 다시 사용하고 정보를 업데이트합니다.

## <a name="step-7-enable-protection-for-virtual-machines"></a>7단계: 가상 컴퓨터의 보호 활성화


저장소 그룹이 복제되면 다음 방법 중 하나를 사용하여 VMM 콘솔에서 VM에 대한 보호를 사용하도록 설정합니다.

* **새 가상 컴퓨터**—새 VM을 만들 때 복제를 사용하도록 설정하고 VM을 복제 그룹과 연결합니다.
  이 옵션을 사용하면 VMM은 지능형 배치를 사용하여 VM 저장소를 복제 그룹의 LUN에 최적으로 배치합니다. Site Recovery는 보조 사이트의 섀도 VM 생성을 오케스트레이션하고 장애 조치(Failover) 후 복제본 VM을 시작할 수 있도록 용량을 할당합니다.
* **기존 가상 컴퓨터**—가상 컴퓨터가 VMM에 이미 배포된 경우 복제를 사용하도록 설정하고 복제 그룹으로 저장소를 마이그레이션을 수행할 수 있습니다. 완료되면 VMM과 Site Recovery에서 새 VM을 검색하고 Site Recovery에서 VM을 관리하기 시작합니다. 섀도 VM은 보조 사이트에서 만들어지며 장애 조치(Failover) 후 복제본 VM이 시작될 수 있도록 용량이 할당됩니다.


    ![보호 사용](./media/site-recovery-vmm-san/enable-protect.png)

VM은 복제에 사용되도록 설정된 후 Site Recovery 콘솔에 표시됩니다. VM 속성을 보고, 상태를 추적하고, 여러 VM을 포함하는 복제 그룹을 장애 조치(Failover)할 수 있습니다. SAN 복제에서 복제 그룹과 연결된 모든 VM은 함께 장애 조치(Failover)되어야 합니다. 이는 장애 조치(Failover)가 먼저 저장소 계층에서 수행되기 때문입니다. 복제 그룹을 제대로 그룹화하고 연결된 VM만 함께 배치하는 것이 중요합니다.

> [!NOTE]
> VM에 대해 복제를 사용하도록 설정한 후에는 해당 컴퓨터에 대한 VHD를 Site Recovery 복제 그룹에 있지 않은 LUN에 추가하지 말아야 합니다. 그렇게 하면 Site Recovery에서 검색되지 않습니다.
>
>

**작업** 탭에서 초기 복제를 비롯한 진행 상태를 추적할 수 있습니다. 보호 완료 작업이 실행된 후에 가상 컴퓨터는 장애 조치(Failover)를 수행할 준비가 됩니다.

![가상 컴퓨터 보호 작업](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>8단계: 배포 테스트

배포를 테스트하여 VM이 예상대로 장애 조치(Failover)되는지 확인합니다. 이를 위해 복구 계획을 만들고 테스트 장애 조치(Failover)를 실행합니다.

1. **복구 계획** 탭에서 **복구 계획 만들기**를 클릭합니다.
2. 복구 계획 이름, 원본 및 대상 VMM 서버를 지정합니다. 원본 서버에 장애 조치(Failover) 및 복구를 사용하도록 설정된 VM이 있어야 합니다. **SAN** 을 선택하여 SAN 복제가 구성된 클라우드만 표시합니다.

    ![복구 계획 만들기](./media/site-recovery-vmm-san/r-plan.png)

3. **가상 컴퓨터 선택**에서 복제 그룹을 선택합니다. 그룹과 연결된 모든 VM이 복구 계획에 추가됩니다. 이러한 VM은 복구 계획 기본 그룹(그룹 1)에 추가됩니다. 필요한 경우 그룹을 더 추가할 수 있습니다. 복제 후 VM이 복구 계획 그룹의 순서에 따라 시작됩니다.

    ![가상 컴퓨터 추가](./media/site-recovery-vmm-san/r-plan-vm.png)
4. 복구 계획이 만들어지면 **복구 계획** 탭의 목록에 표시됩니다. 계획을 선택하고 **테스트 장애 조치(Failover)**를 클릭합니다.
5. **테스트 장애 조치(Failover) 확인** 페이지에서 **없음**을 선택합니다. 이 옵션을 사용하도록 설정하면 장애 조치(Failover)된 복제본 VM이 네트워크에 연결되지 않습니다. 이 테스트는 VM이 예상대로 장애 조치(Failover)되는지 테스트하지만 네트워크 환경은 테스트하지 않습니다. 다른 네트워킹 옵션에 대해 [자세히 알아보세요](site-recovery-failover.md#run-a-test-failover).

    ![테스트 네트워크 선택](./media/site-recovery-vmm-san/test-fail1.png)


1. 테스트 가상 컴퓨터는 복제본 가상 컴퓨터가 있는 호스트와 동일한 호스트에 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다.
6. 테스트 VM이 복제본 VM이 있는 호스트와 같은 호스트에 만들어집니다. 복제본 VM이 있는 클라우드에는 추가되지 않습니다.
2. 복제 후 복제본 VM은 기본 가상 컴퓨터의 IP 주소와 다른 IP 주소를 갖게 됩니다. DHCP에서 주소를 발급하는 경우 주소가 자동으로 업데이트됩니다. DHCP를 사용하지 않는 경우 동일한 주소를 원한다면 몇 가지 스크립트를 실행해야 합니다.
3. 다음 스크립트를 실행하여 IP 주소를 검색합니다.


       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  

4. 다음 예제 스크립트를 실행하여 DNS를 업데이트합니다. 검색한 IP 주소를 지정합니다.

       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## <a name="next-steps"></a>다음 단계

환경이 예상대로 작동 중인지 확인하기 위해 테스트 장애 조치(failover)를 실행한 후에 여러 형식의 장애 조치(failover)에 대해 [알아봅니다](site-recovery-failover.md) .




<!--HONumber=Jan17_HO5-->


