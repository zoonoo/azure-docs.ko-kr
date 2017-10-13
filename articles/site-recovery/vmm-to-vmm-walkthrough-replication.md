---
title: "Azure Site Recovery를 사용하여 보조 사이트로의 Hyper-V 복제를 위한 복제 정책 설정 | Microsoft 문서"
description: "Azure Site Recovery를 사용하여 Hyper-V VM을 보조 VMM 사이트로 복제하기 위한 복제 정책을 설정하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5d9b79cf-89f2-4af9-ac8e-3a32ad8c6c4d
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: a3ecc555e049914d7bffdddce7b0522f09362d24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-a-replication-policy"></a>8단계: 복제 정책 설정

[네트워크 매핑](vmm-to-vmm-walkthrough-network-mapping.md)을 구성한 후 이 문서를 참조하여 [Azure Site Recovery](site-recovery-overview.md)를 사용해 Hyper-V VM(가상 컴퓨터)을 보조 사이트로 복제하기 위한 복제 정책을 설정할 수 있습니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.


## <a name="before-you-start"></a>시작하기 전에

- 복제 정책을 만들 경우 해당 정책을 사용하는 모든 호스트의 운영 체제가 동일해야 합니다. VMM 클라우드는 서로 다른 버전의 Windows Server를 실행하는 Hyper-V 호스트를 포함할 수 있지만 이런 경우 여러 복제 정책이 필요합니다.
- 초기 복제를 오프라인으로 수행할 수 있습니다.

## <a name="configure-replication-settings"></a>복제 설정 구성

1. 새 복제 정책을 만들려면 **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 차례로 클릭합니다.

    ![네트워크](./media/vmm-to-vmm-walkthrough-replication/gs-replication.png)
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다. 소스 및 대상 형식은 **Hyper-V**여야 합니다.
3. **Hyper-V 호스트 버전**에서 호스트에서 실행되는 운영 체제를 선택합니다.
4. **인증 형식** 및 **인증 포트**에서 기본 및 복구 Hyper-V 호스트 서버 간에 트래픽을 인증하는 방법을 지정합니다. 작동하는 Kerberos 환경이 구성되어 있지 않으면 **인증서** 를 선택합니다. Azure Site Recovery가 HTTPS 인증을 위한 인증서를 자동으로 구성합니다. 수동으로 수행할 작업은 없습니다. 기본적으로 Hyper-V 호스트 서버의 Windows 방화벽에서 포트 8083과 8084(인증서용)가 열립니다. **Kerberos**를 선택하면 호스트 서버의 상호 인증에 Kerberos 티켓이 사용됩니다. 이 설정은 Windows Server 2012 R2에서 실행 중인 Hyper-V 호스트 서버와만 관련이 있습니다.
5. **복사 빈도**에서 초기 복제 후 델타 데이터를 복제할 빈도(30초마다, 5분마다 또는 15분마다)를 지정합니다.
6. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다.
7. **앱 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(1~12시간)를 지정합니다. Hyper-V는 두 가지 유형의 스냅숏, 즉 전체 가상 컴퓨터의 증분 스냅숏을 제공하는 표준 스냅숏과 가상 컴퓨터 내 응용 프로그램 데이터의 지정 시간 스냅숏을 만드는 응용 프로그램 일치 스냅숏을 사용합니다. 응용 프로그램 일치 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏을 만들 때 응용 프로그램이 일관된 상태가 되도록 합니다. 응용 프로그램 일치 스냅숏을 사용하도록 설정하면 원본 가상 컴퓨터에서 실행되는 응용 프로그램의 성능에 영향을 줍니다. 구성하는 추가 복구 지점 수보다 작은 값을 설정해야 합니다.
8. **데이터 전송 압축**에서 전송되는 복제된 데이터를 압축할지 여부를 지정합니다.
9. **복제본 VM 삭제**를 선택하여 소스 VM에 대해 보호를 사용하지 않도록 설정하는 경우 복제본 가상 컴퓨터가 삭제되도록 지정합니다. 이 설정을 사용하도록 지정하는 경우 보호가 설정되지 않은 원본 VM이 Site Recovery 콘솔에서 제거되고, VMM에 대한 Site Recovery 설정이 VMM 콘솔에서 제거되고, 복제본이 삭제됩니다.
10. 네트워크를 통해 복제하는 경우 **초기 복제 방법**에서 초기 복제를 시작할지 또는 예약할지를 지정합니다. 네트워크 대역폭을 절약하려면 사용량이 많지 않은 시간에 예약하는 것이 좋습니다. 그런 후 **OK**를 클릭합니다.

     ![복제 정책](./media/vmm-to-vmm-walkthrough-replication/gs-replication2.png)
11. 새 정책을 만들면 새 정책이 자동으로 VMM 클라우드에 연결됩니다. **복제 정책**에서 **확인**을 클릭합니다. **복제** > 정책 이름 > **VMM 클라우드 연결**에서 추가 VMM 클라우드(및 그 안에 포함된 VM)를 이 복제 정책과 연결할 수 있습니다.

     ![복제 정책](./media/vmm-to-vmm-walkthrough-replication/policy-associate.png)



## <a name="prepare-for-offline-initial-replication"></a>오프라인 초기 복제 준비

초기 데이터 복사본에 대해 오프라인 복제를 수행할 수 있습니다. 이 작업은 다음과 같이 준비할 수 있습니다.

* 원본 서버에서 데이터 내보내기가 수행되는 경로 위치를 지정합니다. NTFS에 대해 모든 권한을 할당하고 내보내기 경로의 VMM 서비스에 공유 권한을 할당합니다. 대상 서버에서 데이터 가져오기가 수행되는 경로 위치를 지정합니다. 이 가져오기 경로에 동일한 사용 권한을 할당합니다.
* 가져오기 또는 내보내기 경로가 공유되는 경우 공유가 있는 원격 컴퓨터의 VMM 서비스 계정에 대해 Administrator, Power User, Print Operator 또는 Server Operator 그룹 구성원 자격을 할당합니다.
* 실행 계정을 사용하여 호스트를 추가하는 경우 가져오기 및 내보내기 경로에서 VMM의 실행 계정에 읽기 및 쓰기 권한을 할당합니다.
* Hyper-V는 루프백 구성을 지원하지 않으므로 Hyper-V 호스트 서버로 사용되는 컴퓨터에는 가져오기 및 내보내기 공유가 있으면 안 됩니다.
* 보호할 가상 컴퓨터를 포함하는 Active Directory의 각 Hyper-V 호스트 서버에서 다음과 같이 제한 위임을 사용하도록 설정하고 가져오기 및 내보내기 경로가 있는 원격 컴퓨터를 신뢰하도록 구성합니다.
  1. 도메인 컨트롤러에서 **Active Directory 사용자 및 컴퓨터**를 엽니다.
  2. 콘솔 트리에서 **DomainName** > **컴퓨터**를 클릭합니다.
  3. Hyper-V 호스트 서버 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
  4. **위임** 탭에서 **지정한 서비스에 대한 위임용으로만 이 컴퓨터 트러스트**를 클릭합니다.
  5. **모든 인증 프로토콜 사용**을 클릭합니다.
  6. **추가** > **사용자 및 컴퓨터**에 문의하세요.
  7. 내보내기 경로를 호스트하는 컴퓨터 이름을 입력하고 > **확인**을 클릭합니다. 사용 가능한 서비스 목록에서 Ctrl 키를 누른 채 **cifs** > **확인**을 클릭합니다. 가져오기 경로를 호스트하는 컴퓨터 이름에 대해 반복합니다. 필요에 따라 추가 Hyper-V 호스트 서버에 대해 반복합니다.



## <a name="next-steps"></a>다음 단계

[9단계: 복제 활성화](vmm-to-vmm-walkthrough-enable-replication.md)로 이동합니다.
