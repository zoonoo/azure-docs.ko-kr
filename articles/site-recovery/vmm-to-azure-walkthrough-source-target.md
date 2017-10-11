---
title: "Azure Site Recovery를 사용하여 Azure로의 Hyper-V(System Center VMM 포함) 복제를 위한 원본 및 대상 설정 | Microsoft 문서"
description: "Azure Site Recovery를 사용하여 VMM 클라우드의 Hyper-V VM을 Azure Storage로 복제하기 위한 원본 및 대상 설정을 지정하는 단계를 요약하여 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>8단계: Azure로의 Hyper-V(VMM 포함) 복제를 위한 원본 및 대상 설정

[자격 증명 모음 만들기](vmm-to-azure-walkthrough-create-vault.md)와 복제할 항목 지정을 수행한 후 이 문서의 내용을 참조하여 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용해 System Center Virtual Machine Manager(VMM) 클라우드의 온-프레미스 Hyper-V 가상 컴퓨터를 Azure로 복제할 때 사용할 원본 및 대상 설정을 구성할 수 있습니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

S1. **인프라 준비** > **원본**을 클릭합니다.

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. **소스 준비**에서 **+VMM**을 클릭하여 VMM 서버를 추가합니다.

    ![원본 설정](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. **서버 추가**에서 **System Center VMM 서버**가 **서버 형식**에 표시되고 해당 VMM 서버가 [필수 조건 및 URL 요구 사항](#prerequisites)을 만족하는지 확인합니다.
4. Azure Site Recovery 공급자 설치 파일을 다운로드합니다.
5. 등록 키를 다운로드합니다. 설정을 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

    ![원본 설정](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>VMM 서버에 공급자 설치

1. VMM 서버에서 공급자 설치 파일을 실행합니다.
2. Microsoft Update 정책에 따라 공급자 업데이트가 설치되도록 **Microsoft Update**에서 업데이트를 선택할 수 있습니다.
3. **설치**에서 기본 공급자 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다.

    ![설치 위치](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. 설치가 완료되면 **등록**을 클릭하여 자격 증명 모음에 VMM 서버를 등록합니다.
5. **자격 증명 모음 설정** 페이지에서 **찾아보기**를 클릭하고 자격 증명 모음 키 파일을 선택합니다. Azure Site Recovery 구독 및 자격 증명 모음 이름을 지정합니다.

    ![서버 등록](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. VMM 서버에서 실행 중인 공급자가 인터넷을 통해 Site Recovery에 연결하는 방법을 **인터넷 연결**에서 지정합니다.

   * 공급자를 직접 연결하려면 **프록시 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
   * 기존 프록시에 인증이 필요하거나 사용자 지정 프록시를 사용하려면 **프록시 서버를 사용하여 Azure Site Recovery에 연결**을 선택합니다.
   * 사용자 지정 프록시를 사용하는 경우 주소, 포트 및 자격 증명을 지정합니다.
   * 프록시를 사용하는 경우 [필수 조건](#on-premises-prerequisites)에 설명된 URL을 이미 허용했어야 합니다.
   * 사용자 지정 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. VMM 콘솔에서 VMM 실행 계정 설정을 수정할 수 있습니다. **설정**에서 **보안** > **실행 계정**을 차례로 확장한 다음 DRAProxyAccount의 암호를 수정합니다. 이 설정이 적용되도록 VMM 서비스를 다시 시작해야 합니다.

     ![인터넷](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. 데이터 암호화를 위해 자동으로 생성되는 SSL 인증서를 저장할 위치를 수락하거나 지정합니다. 이 인증서는 Azure Site Recovery 포털에서 Azure가 보호하는 클라우드에 대해 데이터 암호화를 사용하도록 설정하는 경우 사용되므로 안전하게 보관해야 합니다. 데이터 암호화가 활성화된 경우 Azure에 장애 조치(failover)를 실행할 때 암호 해독에 이 인증서가 필요합니다.
8. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다.
9. VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화하려면 **클라우드 메타데이터 동기화**를 활성화합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다. **등록**을 클릭하여 프로세스를 완료합니다.

    ![서버 등록](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. 등록이 시작됩니다. 등록이 완료되면 **Site Recovery 인프라** > **VMM 서버**에 해당 서버가 표시됩니다.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Hyper-V 호스트에 Azure 복구 서비스 에이전트 설치

1. 공급자를 설치한 후에는 Azure 복구 서비스 에이전트 설치 파일을 다운로드해야 합니다. VMM 클라우드의 각 Hyper-V 서버에서 설치 프로그램을 실행합니다.

    ![Hyper-V 사이트](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. **필수 구성 요소 확인**에서 **다음**을 클릭합니다. 누락된 필수 구성 요소는 자동으로 설치됩니다.

    ![필수 구성 요소 복구 서비스 에이전트](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. **설치 설정**에서 설치 위치 및 캐시 위치를 수락하거나 수정합니다. 최소 5GB의 저장 공간이 있는 드라이브에 캐시를 구성할 수 있지만 600GB 이상의 여유 공간이 있는 캐시 드라이브에 구성하는 것이 좋습니다. **설치**를 클릭합니다.
4. 설치가 완료되면 **닫기** 를 클릭하여 완료합니다.

    ![MARS 에이전트 등록](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>명령줄 설치
다음 명령을 사용하여 명령줄에서 Microsoft Azure 복구 서비스 에이전트를 설치할 수 있습니다.

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Hyper-V 호스트에서 Site Recovery에 대한 인터넷 프록시 액세스를 설정합니다.

VM을 복제하려면 Hyper-V 호스트에서 실행되는 복구 서비스 에이전트가 인터넷을 통해 Azure에 액세스할 수 있어야 합니다. 프록시를 통해 인터넷에 액세스하는 경우 다음과 같이 프록시를 설정합니다.

1. Hyper-V 호스트에서 Microsoft Azure 백업 MMC 스냅인을 엽니다. 기본적으로 Microsoft Azure Backup에 대한 바로 가기가 바탕 화면 또는 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin에 있습니다.
2. 스냅인에서 **속성 변경**을 클릭합니다.
3. **프록시 구성** 탭에서 프록시 서버 정보를 지정합니다.

    ![MARS 에이전트 등록](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. 에이전트가 [필수 조건](#on-premises-prerequisites)에 설명된 URL에 연결할 수 있는지 확인합니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정
복제에 사용될 Azure 저장소 계정과 장애 조치(failover) 후 Azure VM이 연결될 Azure 네트워크를 지정합니다.

1. **인프라 준비** > **대상**을 클릭하고 장애 조치 가상 컴퓨터를 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 가상 컴퓨터에 대해 Azure(클래식 또는 리소스 관리)에서 사용하려는 배포 모델을 선택합니다.

    ![창고](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

    ![저장소](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. 아직 저장소 계정을 만들지 않았으며 Resource Manager를 사용하여 계정을 만들려면 **+저장소 계정**을 클릭하여 인라인에서 계정을 만듭니다.  **저장소 계정 만들기** 블레이드에서 계정 이름, 형식, 구독 및 위치를 지정합니다. 계정이 복구 서비스 자격 증명 모음과 같은 위치에 있어야 합니다.

   ![저장소](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * 클래식 모델을 사용하여 저장소 계정을 만들려면 Azure 포털에서 만들면 됩니다. [자세히 알아보기](../storage/common/storage-create-storage-account.md)
   * 복제된 데이터에 프리미엄 저장소 계정을 사용하는 경우 온-프레미스 데이터에 지속적인 변화를 캡처하는 복제 로그를 저장하는 추가 표준 저장소 계정을 설정합니다.
5. 아직 Azure 네트워크를 만들지 않았으며 Resource Manager를 사용하여 네트워크를 만들려면 **+네트워크**를 클릭하여 인라인에서 네트워크를 만듭니다. **가상 네트워크 만들기** 블레이드에서 네트워크 이름, 주소 범위, 서브넷 세부 정보, 구독 및 위치를 지정합니다. 네트워크가 복구 서비스 자격 증명 모음과 같은 위치에 있어야 합니다.

   ![네트워크](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   클래식 모델을 사용하여 네트워크를 만들려면 Azure 포털에서 만들면 됩니다. [자세히 알아보기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).





## <a name="next-steps"></a>다음 단계

[9단계: 네트워크 매핑 구성](vmm-to-azure-walkthrough-network-mapping.md)으로 이동합니다.
