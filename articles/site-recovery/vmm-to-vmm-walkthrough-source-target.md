---
title: "Azure Site Recovery를 사용하여 보조 사이트로의 Hyper-V 복제를 위한 원본 및 대상 설정 | Microsoft 문서"
description: "Azure Site Recovery를 사용하여 보조 VMM 사이트로 Hyper-V VM을 복제할 때 원본 및 대상을 설정하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.contentlocale: ko-kr
ms.lasthandoff: 08/01/2017

---
# <a name="step-6-set-up-the-replication-source-and-target"></a>6단계: 복제 원본 및 대상 설정


[Azure Site Recovery](site-recovery-overview.md)를 사용하여 보조 VMM 사이트로의 Hyper-V 복제를 위한 Recovery Services 자격 증명 모음을 만든 후 이 문서를 참조하여 원본 및 대상 복제 위치를 설정할 수 있습니다. 

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.




## <a name="set-up-the-source-environment"></a>원본 환경 설정

VMM 서버에 Azure Site Recovery 공급자를 설치하고 서버를 검색하여 자격 증명 모음에 등록합니다.

1. **1단계: 인프라 준비** > **원본**을 클릭합니다.

    ![원본 설정](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. **소스 준비**에서 **+VMM**을 클릭하여 VMM 서버를 추가합니다.

    ![원본 설정](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. **서버 추가**에서 **System Center VMM 서버**가 **서버 형식**에 표시되고 해당 VMM 서버가 [필수 조건](#prerequisites)을 만족하는지 확인합니다.
4. Azure Site Recovery 공급자 설치 파일을 다운로드합니다.
5. 등록 키를 다운로드합니다. 설정을 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

    ![원본 설정](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. VMM 서버에 Azure Site Recovery 공급자를 설치합니다. Hyper-V 호스트 서버에 명시적으로 설치할 필요는 없습니다.


## <a name="install-the-azure-site-recovery-provider"></a>Azure Site Recovery 공급자 설치

1. 각 VMM 서버에서 공급자 설치 파일을 실행합니다. VMM을 클러스터에 배포하는 경우 처음에 설치할 때 다음을 수행합니다.
    -  활성 노드에 공급자를 설치하고 자격 증명 모음에서 해당 VMM 서버를 등록하는 것으로 설치를 마칩니다.
    - 그런 후에 다른 노드에 공급자를 설치합니다. 모든 클러스터 노드는 동일한 버전의 공급자를 실행해야 합니다.
2. 설치 중에 몇 가지 필수 조건 검사가 실행되며 VMM 서비스를 중지하기 위한 권한이 요청됩니다. 설정이 완료되면 VMM 서비스가 자동으로 다시 시작됩니다. VMM 클러스터에 설치하는 경우 클러스터 역할을 중지하라는 메시지가 표시됩니다.
3. **Microsoft Update**에서 Microsoft Update 정책에 따라 공급자 업데이트가 설치되도록 지정할 수 있습니다.
4. **설치**에서 기본 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다.

    ![설치 위치](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. 설치가 완료되면 **등록**을 클릭하여 자격 증명 모음에 서버를 등록합니다.

    ![설치 위치](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. **자격 증명 모음 이름**에서 서버를 등록할 자격 증명 모음의 이름을 확인합니다. *다음*을 클릭합니다.

    ![서버 등록](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. VMM 서버에서 실행 중인 공급자를 Azure에 연결하는 방법을 **인터넷 연결**에서 지정합니다.

    ![인터넷 설정](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - 공급자가 인터넷에 직접 또는 프록시를 통해 연결되도록 지정할 수 있습니다.
   - 필요한 경우 프록시 설정을 지정합니다.
   - 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. 실행 계정 설정은 VMM 콘솔 > **설정** > **보안** > **실행 계정**에서 수정할 수 있습니다. 변경 내용을 업데이트하려면 VMM 서비스를 다시 시작합니다.
8. **등록 키**에서 Azure Site Recovery에서 다운로드하고 VMM 서버에 복사한 키를 선택합니다.
9. 암호화 설정은 VMM 클라우드의 Hyper-V VM을 Azure에 복제하는 경우에 사용됩니다. 보조 사이트에 복제하는 경우 사용되지 않습니다.
10. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다.
11. **클라우드 메타데이터 동기화**에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화할 것인지를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다.
12. **다음** 을 클릭하여 프로세스를 완료합니다. 등록 후에 VMM 서버의 메타데이터가 Azure Site Recovery에 의해 검색됩니다. 자격 증명 모음의 **서버** 페이지에 있는 **VMM 서버** 탭에 해당 서버가 표시됩니다.

    ![서버](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. Site Recovery 콘솔에서 서버를 사용할 수 있게 되면 **원본** > **소스 준비** 에서 VMM 서버를 선택하고 Hyper-V 호스트가 있는 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.

명령줄에서 공급자를 설치할 수도 있습니다.

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 VMM 서버 및 클라우드를 선택합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 대상 VMM 서버를 선택합니다.
2. Site Recovery와 동기화된 서버의 클라우드가 표시됩니다. 대상 클라우드를 선택합니다.

   ![대상](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>다음 단계

[7단계: 네트워크 매핑 구성](vmm-to-vmm-walkthrough-network-mapping.md)으로 이동합니다.

