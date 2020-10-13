---
title: Windows 가상 데스크톱 재해 복구 계획 설정-Azure
description: Windows 가상 데스크톱 배포에 대 한 비즈니스 연속성 및 재해 복구 계획을 설정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935706"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>비즈니스 연속성 및 재해 복구 계획 설정

조직의 데이터를 안전 하 게 유지 하려면 BCDR (비즈니스 연속성 및 재해 복구) 전략을 채택 해야 할 수 있습니다. Sound BCDR 전략은 계획 되거나 계획 되지 않은 서비스 또는 Azure 중단 중에 앱과 워크 로드를 가동 및 실행 상태로 유지 합니다.

Windows 가상 데스크톱은 중단 중에 고객 메타 데이터를 보존 하기 위해 Windows 가상 데스크톱 서비스에 BCDR을 제공 합니다. 지역에서 중단이 발생 하면 서비스 인프라 구성 요소가 보조 위치로 장애 조치 (failover) 되 고 정상적으로 계속 작동 합니다. 서비스 관련 메타 데이터에 계속 액세스할 수 있으며, 사용자는 여전히 사용 가능한 호스트에 연결할 수 있습니다. 최종 사용자 연결은 테 넌 트 환경 또는 호스트에 계속 액세스할 수 있는 한 온라인 상태를 유지 합니다.

사용자가 지역 가동 중단 중에 계속 연결할 수 있도록 하려면 다른 위치에 Vm (가상 머신)을 복제 해야 합니다. 작동 중단 중에 기본 사이트는 보조 위치의 복제 된 Vm으로 장애 조치 (failover) 됩니다. 사용자는 중단 없이 보조 위치에서 앱에 계속 액세스할 수 있습니다. VM을 복제 하는 동안에는 보조 위치에서 액세스할 수 있는 사용자 id를 유지 해야 합니다. 프로필 컨테이너를 사용 하는 경우에는 해당 컨테이너도 복제 해야 합니다. 마지막으로, 기본 위치의 데이터를 사용 하는 비즈니스 앱이 나머지 데이터로 장애 조치 (failover) 할 수 있는지 확인 합니다.

요약 하자면, 중단 중에 사용자를 연결 된 상태로 유지 하려면 다음 작업을 순서 대로 수행 해야 합니다.

- 보조 위치에서 Vm을 복제 합니다.
- 프로필 컨테이너를 사용 하는 경우 보조 위치에서 데이터 복제를 설정 합니다.
- 기본 위치에 설정한 사용자 id를 보조 위치에서 사용할 수 있는지 확인 합니다.
- 기본 위치의 데이터에 의존 하는 lob (기간 업무) 응용 프로그램이 보조 위치로 장애 조치 (failover) 되었는지 확인 합니다.

## <a name="vm-replication"></a>VM 복제

먼저 Vm을 보조 위치에 복제 해야 합니다. 이러한 작업을 수행 하기 위한 옵션은 Vm을 구성 하는 방법에 따라 달라 집니다.

- Azure Site Recovery를 사용 하 여 풀링된 및 개인 호스트 풀 모두에 대 한 모든 Vm을 구성할 수 있습니다. 이 방법을 사용 하면 하나의 호스트 풀과 관련 앱 그룹 및 작업 영역을 설정 하기만 하면 됩니다.
- 장애 조치 (failover) 위치의 모든 리소스를 해제 하면서 장애 조치 (failover) 지역에 새 호스트 풀을 만들 수 있습니다. 이 방법의 경우 장애 조치 (failover) 지역에서 새 앱 그룹 및 작업 영역을 설정 해야 합니다. 그런 다음 Azure Site Recovery 계획을 사용 하 여 호스트 풀을 켤 수 있습니다.
- 장애 조치 (failover) 지역의 Vm이 꺼진 상태를 유지 하면서 기본 및 장애 조치 (failover) 지역 모두에서 빌드된 Vm에 의해 채워지는 호스트 풀을 만들 수 있습니다. 이 경우 호스트 풀과 관련 앱 그룹 및 작업 영역을 하나만 설정 하면 됩니다. Azure Site Recovery 요금제를 사용 하 여이 방법으로 호스트 풀의 전원을 켤 수 있습니다.

[Azure-azure 재해 복구 아키텍처](../site-recovery/azure-to-azure-architecture.md)에 설명 된 대로 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 를 사용 하 여 다른 Azure 위치에 있는 vm 복제를 관리 하는 것이 좋습니다. Azure Site Recovery는 [서버 기반 sku와 클라이언트 기반 sku](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems)를 모두 지원 하기 때문에 특히 개인 호스트 풀에 Azure Site Recovery를 사용 하는 것이 좋습니다.

Azure Site Recovery 사용 하는 경우 이러한 Vm을 수동으로 등록 하지 않아도 됩니다. 보조 VM의 Windows 가상 데스크톱 에이전트는 가장 가까운 서비스 인스턴스에 연결 하는 데 최신 보안 토큰을 자동으로 사용 합니다. 보조 위치의 VM (세션 호스트)이 자동으로 호스트 풀의 일부가 됩니다. 최종 사용자는 프로세스 중에 다시 연결 해야 하지만 그 외에는 다른 수동 작업이 없습니다.

가동 중단 중에 기존 사용자 연결이 있는 경우 관리자가 보조 지역으로 장애 조치 (failover)를 시작 하기 전에 현재 지역에서 사용자 연결을 종료 해야 합니다.

Windows 가상 데스크톱 (클래식)에서 사용자의 연결을 끊으려면 다음 cmdlet을 실행 합니다.

```powershell
Invoke-RdsUserSessionLogoff
```

Azure 통합 버전의 Windows 가상 데스크톱에서 사용자의 연결을 끊으려면 다음 cmdlet을 실행 합니다.

```powershell
Remove-AzWvdUserSession
```

주 지역의 모든 사용자를 로그 아웃 하면 주 지역에서 Vm을 장애 조치 (failover) 하 고 사용자가 보조 지역의 Vm에 연결할 수 있습니다. 이 프로세스의 작동 방식에 대 한 자세한 내용은 [다른 azure 지역에 Azure Vm 복제](../site-recovery/azure-to-azure-how-to-enable-replication.md)를 참조 하세요.

## <a name="virtual-network"></a>가상 네트워크

그런 다음 작동 중단 중에 네트워크 연결을 고려 합니다. 보조 지역에서 VNET (가상 네트워크)을 설정 했는지 확인 해야 합니다. 사용자가 온-프레미스 리소스에 액세스 해야 하는 경우이 VNET에 액세스 하도록 구성 해야 합니다. VPN, Express 경로 또는 가상 WAN을 사용 하 여 온-프레미스 연결을 설정할 수 있습니다.

기본 네트워크 설정을 유지 하 고 피어 링이 필요 하지 않으므로 장애 조치 (failover) 지역에서 VNET을 설정 하는 Azure Site Recovery을 사용 하는 것이 좋습니다.

## <a name="user-identities"></a>사용자 id

그런 다음 보조 위치에서 도메인 컨트롤러를 사용할 수 있는지 확인 합니다. 

도메인 컨트롤러를 사용 가능 하 게 유지 하는 방법에는 다음 세 가지가 있습니다.

   - 보조 위치에 Active Directory 도메인 컨트롤러 포함
   - 온-프레미스 Active Directory 도메인 컨트롤러 사용
   - [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md) 를 사용 하 여 Active Directory 도메인 컨트롤러 복제

## <a name="user-and-app-data"></a>사용자 및 앱 데이터

프로필 컨테이너를 사용 하는 경우 다음 단계는 보조 위치에서 데이터 복제를 설정 하는 것입니다. FSLogix 프로필을 저장 하는 5 가지 옵션이 있습니다.

   - 스토리지 공간 다이렉트(S2D)
   - 네트워크 드라이브 (추가 드라이브가 있는 VM)
   - Azure 파일
   - Azure NetApp Files
   - 복제용 클라우드 캐시

자세한 내용은 [Windows 가상 데스크톱의 FSLogix 프로필 컨테이너에 대 한 저장소 옵션](store-fslogix-profile.md)을 확인 하세요.

프로필에 대 한 재해 복구를 설정 하는 경우 다음과 같은 옵션을 사용할 수 있습니다.

   - 네이티브 Azure 복제를 설정 합니다 (예: Azure Files Standard storage 계정 복제, Azure NetApp Files 복제 또는 파일 서버에 대 한 Azure Files 동기화).
    
     >[!NOTE]
     >NetApp 복제는 처음 설정 된 후 자동으로 설정 됩니다. Azure Site Recovery 계획을 사용 하면 사전 스크립트 및 사후 스크립트를 추가 하 여 비 VM 리소스를 장애 조치 (failover) 하 Azure Storage 리소스를 복제할 수 있습니다.

   - 앱 및 사용자 데이터 모두에 대해 FSLogix Cloud Cache를 설정 합니다.
   - 응용 프로그램 데이터에 대 한 재해 복구를 설정 하 여 항상 업무상 중요 한 데이터에 액세스할 수 있도록 합니다. 이 방법을 사용 하면 중단이 끝난 후 사용자 데이터를 검색할 수 있습니다.

각 옵션에 대해 재해 복구를 설정 하도록 FSLogix를 구성 하는 방법을 살펴보겠습니다.

### <a name="fslogix-configuration"></a>FSLogix 구성

Fslogix에 대 한 레지스트리 항목을 구성 하는 경우 FSLogix 에이전트는 여러 프로필 위치를 지원할 수 있습니다.

레지스트리 항목을 구성 하려면:

1. **레지스트리 편집기**를 엽니다.
2. **컴퓨터**  >  **HKEY_LOCAL_MACHINE**  >  **소프트웨어**  >  **fslogix**  >  **프로필**로 이동 합니다.
   
     > [!div class="mx-imgBorder"]
     > ![레지스트리 편집기에서 프로필 창의 스크린샷 VHDLocation이 선택 됩니다.](media/regedit-profiles.png)

3. **VHDLocations** 를 마우스 오른쪽 단추로 클릭 하 고 **다중 문자열 편집**을 선택 합니다.

     > [!div class="mx-imgBorder"]
     > ![다중 문자열 편집 창의 스크린샷 값 데이터에는 미국 Centrual 및 미국 동부 위치가 나열 됩니다.](media/multi-string-edit.png)

4. **값 데이터** 필드에 사용 하려는 위치를 입력 합니다.
5. 완료되면 **확인**을 선택합니다.

첫 번째 위치를 사용할 수 없는 경우 FSLogix 에이전트가 자동으로 두 번째로 장애 조치 (failover) 됩니다.

주 지역의 보조 위치에 대 한 경로를 사용 하 여 FSLogix 에이전트를 구성 하는 것이 좋습니다. 기본 위치가 종료 되 면 FLogix 에이전트가 VM Azure Site Recovery 복제의 일부로 복제 됩니다. 복제 된 Vm이 준비 되 면 에이전트는 자동으로 보조 지역에 대 한 경로를 시도 합니다.

예를 들어 주 세션 호스트 Vm이 미국 중부 지역에 있지만 프로필 컨테이너가 성능상의 이유로 미국 중부 지역에 있다고 가정해 보겠습니다.

이 경우 미국 중부의 저장소 경로를 사용 하 여 FSLogix 에이전트를 구성 합니다. 미국 서 부에 복제할 세션 호스트 Vm을 구성 합니다. 미국 중부에 대 한 경로에 오류가 발생 하면 에이전트는 미국 서 부에 저장소에 대 한 새 경로를 대신 만들려고 시도 합니다.

### <a name="s2d"></a>S2D

S2D는 내부적으로 지역 간 복제를 처리 하므로 보조 경로를 수동으로 설정 하지 않아도 됩니다.

### <a name="network-drives-vm-with-extra-drives"></a>네트워크 드라이브 (추가 드라이브가 있는 VM)

세션 호스트 Vm과 같은 Azure Site Recovery를 사용 하 여 네트워크 저장소 Vm을 복제 하는 경우 복구는 동일한 경로를 유지 합니다. 즉, FSlogix를 다시 구성할 필요가 없습니다.

### <a name="azure-files"></a>Azure 파일

Azure Files는 저장소 계정을 만들 때 지정할 수 있는 지역 간 비동기 복제를 지원 합니다. Azure Files의 비동기 특성이 이미 재해 복구 목표를 포함 하는 경우에는 추가 구성을 수행할 필요가 없습니다.

데이터 손실을 최소화 하기 위해 동기식 복제가 필요한 경우 FSLogix 클라우드 캐시를 대신 사용 하는 것이 좋습니다.

>[!NOTE]
>이 섹션에서는 Azure Files에 대 한 장애 조치 (failover) 인증 메커니즘을 다루지 않습니다.

### <a name="azure-netapp-files"></a>Azure NetApp Files

[Azure NetApp Files에 대 한 복제 피어 링 만들기](../azure-netapp-files/cross-region-replication-create-peering.md)에서 Azure NetApp Files에 대해 자세히 알아보세요.

## <a name="app-dependencies"></a>앱 종속성

마지막으로, 주 지역에 있는 데이터를 사용 하는 비즈니스 앱이 보조 위치로 장애 조치 (failover) 할 수 있는지 확인 합니다. 또한 앱이 새 위치에서 작업 해야 하는 설정을 구성 해야 합니다. 예를 들어 앱 중 하나가 SQL 백 엔드에 종속 되는 경우 보조 위치에서 SQL을 복제 해야 합니다. 보조 위치를 장애 조치 (failover) 프로세스의 일부 또는 기본 구성으로 사용 하도록 앱을 구성 해야 합니다. Azure Site Recovery 계획에 대 한 앱 종속성을 모델링할 수 있습니다. 자세히 알아보려면 [복구 계획 정보](../site-recovery/recovery-plan-overview.md)를 참조 하세요.

## <a name="disaster-recovery-testing"></a>재해 복구 테스트

재해 복구 설정을 완료 한 후 계획을 테스트 하 여 작동 하는지 확인 하는 것이 좋습니다.

다음은 계획을 테스트 하는 방법에 대 한 몇 가지 제안 사항입니다.

- 테스트 Vm에서 인터넷에 액세스할 수 있는 경우 새 연결에 대 한 기존 세션 호스트를 사용 하지만 원래 세션 호스트에 대 한 모든 기존 연결은 활성 상태로 유지 됩니다. 테스트를 실행 하는 관리자가 계획을 테스트 하기 전에 모든 활성 사용자를 로그 아웃 하는지 확인 합니다. 
- 사용자를 방해 하지 않는 유지 관리 기간 동안에만 전체 재해 복구 테스트를 수행 해야 합니다. 테스트에 대 한 유효성 검사 환경에서 호스트 풀을 사용할 수도 있습니다. 
- 테스트가 모든 업무상 중요 한 앱을 포함 하는지 확인 합니다.
- 한 번에 최대 100 개의 Vm만 장애 조치 (failover) 하는 것이 좋습니다. 보다 많은 Vm이 있는 경우 10 분 간격으로 일괄 처리로 장애 조치 (failover) 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

중단 계획 외에도 데이터를 안전 하 게 유지 하는 방법에 대 한 질문이 있는 경우 [보안 가이드](security-guide.md)를 확인 하세요.