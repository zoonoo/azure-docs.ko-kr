---
title: "Azure Site Recovery를 사용하여 Azure로 Hyper-V(System Center VMM 없음) 복제를 위한 원본 및 대상 설정 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Azure Storage에 Hyper-V VM의 복제를 위한 원본 및 대상 설정을 설정하는 단계를 요약"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d2010d85-77fd-4dea-84f3-1c960ed4c63f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: b38eb3a011d46f2239891ea1d1bcac2a4059a866
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-replication-to-azure"></a>8단계: Azure에 Hyper-V 복제를 위한 원본 및 대상

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure에 온-프레미스 Hyper-V 가상 컴퓨터(System Center VMM 없음)를 복제할 때 원본 및 대상 설정을 구성하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

Hyper-V 사이트를 설정하고, Hyper-V 호스트에 Azure Site Recovery 공급자와 Azure Recovery Services 에이전트를 설치하고, 자격 증명 모음에 사이트를 등록합니다.

1. **인프라 준비**에서 **원본**을 클릭합니다. Hyper-V 호스트 또는 클러스터의 컨테이너로 새 Hyper-V 사이트를 추가하려면 **+Hyper-V 사이트**를 클릭합니다.

    ![원본 설정](./media/hyper-v-site-walkthrough-source-target/set-source1.png)
2. **Hyper-V 사이트 만들기**에서 사이트의 이름을 지정합니다. 그런 후 **OK**를 클릭합니다. 앞에서 만든 사이트를 선택하고 **+Hyper-V 서버**를 클릭하여 사이트에 서버를 추가합니다.

    ![원본 설정](./media/hyper-v-site-walkthrough-source-target/set-source2.png)

3. **서버 추가** > **서버 형식**에 **Hyper-V 서버**가 표시되는지 확인합니다.

    - 추가하려는 Hyper-V 서버가 [필수 구성 요소](#on-premises-prerequisites)를 충족하며 지정된 URL에 액세스할 수 있는지 확인합니다.
    - Azure Site Recovery 공급자 설치 파일을 다운로드합니다. 이 파일을 실행하여 공급자와 Recovery Services 에이전트를 각 Hyper-V 호스트에 설치합니다.

    ![원본 설정](./media/hyper-v-site-walkthrough-source-target/set-source3.png)


## <a name="install-the-provider-and-agent"></a>공급자 및 에이전트를 설치

1. Hyper-V 사이트를 추가할 각 호스트에서 공급자 설치 파일을 실행합니다. Hyper-V 클러스터에 설치하는 경우 각 클러스터 노드에서 설치 프로그램을 실행합니다. 각 Hyper-V 클러스터 노드를 설치하고 등록하면 노드 간 마이그레이션에서도 VM이 안전하게 보호됩니다.
2. Microsoft Update 정책에 따라 공급자 업데이트가 설치되도록 **Microsoft Update**에서 업데이트를 선택할 수 있습니다.
3. **설치**에서 기본 공급자 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다.
4. **자격 증명 모음 설정**에서 **찾아보기**를 클릭하고 다운로드한 자격 증명 모음 키 파일을 선택합니다. Azure Site Recovery 구독, 자격 증명 모음 이름 및 Hyper-V 서버가 속한 Hyper-V 사이트를 지정합니다.

    ![서버 등록](./media/hyper-v-site-walkthrough-source-target/provider3.png)

5. **프록시 설정**에서, Hyper-V 호스트에서 실행 중인 공급자가 인터넷을 통해 Azure Site Recovery에 연결하는 방법을 지정합니다.

    * 공급자를 직접 연결하려면 **프록시 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
    * 기존 프록시에 인증이 필요하거나 공급자 연결에 사용자 지정 프록시를 사용하려면 **프록시 서버를 사용하여 Azure Site Recovery에 연결**을 선택합니다.
    * 프록시를 사용하는 경우:
        - 주소, 포트 및 자격 증명을 지정합니다.
        - [필수 구성 요소](#prerequisites)에 설명된 URL이 프록시를 통과할 수 있는지 확인합니다.

    ![인터넷](./media/hyper-v-site-walkthrough-source-target/provider7.png)

6. 설치가 완료되면 **등록**을 클릭하여 자격 증명 모음에 서버를 등록합니다.

    ![설치 위치](./media/hyper-v-site-walkthrough-source-target/provider2.png)

7. 등록이 완료되면 Azure Site Recovery에서 Hyper-V 서버의 메타데이터가 검색되며 서버가 **Site Recovery 인프라** > **Hyper-V 호스트**에 표시됩니다.


## <a name="set-up-the-target-environment"></a>대상 환경 설정

복제에 사용할 Azure 저장소 계정과 장애 조치(failover) 후 Azure VM이 연결될 Azure 네트워크를 지정합니다.

1. **인프라 준비** > **대상**을 클릭합니다.
2. 장애 조치(failover) 후 Azure VM을 만들 구독 및 리소스 그룹을 선택합니다. Azure(클래식 또는 리소스 관리)에서 VM에 사용할 배포 모델을 선택합니다.

3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

    - 저장소 계정이 없으면 **+저장소**를 클릭하여 Resource Manager 기반 계정 인라인을 만듭니다. 
    - Azure 네트워크가 없으면 **+네트워크**를 클릭하여 Resource Manager 기반 네트워크 인라인을 만듭니다.






## <a name="next-steps"></a>다음 단계

[9단계: 복제 정책 설정](hyper-v-site-walkthrough-replication.md)으로 이동합니다.
