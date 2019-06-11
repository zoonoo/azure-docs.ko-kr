---
title: Site Recovery를 통해 온-프레미스 Hyper-V VM(VMM 없음)을 Azure로 재해 복구하도록 설정 | Microsoft Docs
description: Site Recovery를 사용하여 온-프레미스 Hyper-V VM(VMM 없음)을 Azure로 재해 복구하도록 설정하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9d45e0f0c759685f9d35285ee7718585d5961333
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399405"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>온-프레미스 Hyper-V VM의 Azure로의 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 머신과 Azure VM(가상 머신)의 복제, 장애 조치, 장애 복구를 관리하고 오케스트레이션하여 재해 복구 전략에 기여합니다.

이 문서는 시리즈의 세 번째 자습서입니다. 여기서는 온-프레미스 Hyper-V VM을 Azure로 재해 복구하도록 설정하는 방법을 보여 줍니다. 이 자습서는 Microsoft System Center VMM(Virtual Machine Manager)에서 관리하지 않는 Hyper-V VM에 적용됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 복제 원본 및 대상을 선택합니다.
> * 온-프레미스 Site Recovery 구성 요소 및 대상 복제 환경을 포함한 원본 복제 환경을 설정합니다.
> * 복제 정책을 만듭니다.
> * VM의 복제를 사용하도록 설정합니다.

> [!NOTE]
> 자습서는 시나리오의 가장 간단한 배포 경로를 보여줍니다. 가능한 경우 기본 옵션을 사용하고 가능한 모든 설정과 경로를 보여주지 않습니다. 자세한 지침은 [Site Recovery 설명서](https://docs.microsoft.com/azure/site-recovery)의 **방법 가이드** 섹션에 있는 문서를 검토하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서는 시리즈의 세 번째 자습서입니다. 여기서는 이전 자습서의 작업을 이미 완료했다고 가정합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
2. [온-프레미스 Hyper-V 준비](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>복제 목표 선택

1. Azure Portal에서 **Recovery Services 자격 증명 모음**으로 이동하여 자격 증명 모음을 선택합니다. 이전 자습서에서는 **ContosoVMVault** 자격 증명 모음을 준비했습니다.
2. **시작**에서 **Site Recovery**를 선택한 다음, **인프라 준비**를 선택합니다.
3. **보호 목표** > **머신이 어디에 있나요?** 에서 **온-프레미스**를 선택합니다.
4. **머신을 복제할 위치를 선택하세요.** 에서 **Azure**를 선택합니다.
5. **머신이 가상화되어 있습니까?** 에서 **예, Hyper-V 사용**을 선택합니다.
6. **System Center VMM을 사용하여 Hyper-V 호스트를 관리하나요?** 에서 **아니요**를 선택합니다.
7. **확인**을 선택합니다.

    ![복제 목표](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>배포 계획 확인

1. **배포 계획**에서 대규모 배포를 계획 중인 경우 페이지의 링크를 통해 Hyper-V용 Deployment Planner를 다운로드합니다. Hyper-V 배포 계획에 대해 [자세히 알아보세요](hyper-v-deployment-planner-overview.md).
2. 이 자습서에서는 Deployment Planner가 필요하지 않습니다. **배포 계획을 완료하셨나요**에서 **나중에 수행**을 선택한 다음, **확인**을 선택합니다.

    ![배포 계획](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>원본 환경 설정

원본 환경을 설정하려면 Hyper-V 사이트를 만들고, 복제하려는 VM이 포함된 Hyper-V 호스트를 해당 사이트에 추가합니다. 그런 다음, Azure Site Recovery Provider와 Azure Recovery Services 에이전트를 다운로드하여 각 호스트에 설치하고, 자격 증명 모음에 Hyper-V 사이트를 등록합니다.

1. **인프라 준비**에서 **원본**을 선택합니다.
2. **원본 준비**에서 **+Hyper-V 사이트**를 선택합니다.
3. **Hyper-V 사이트 만들기**에서 사이트 이름을 지정합니다. 여기서는 **ContosoHyperVSite**를 사용합니다.

    ![Hyper-V 사이트](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. 사이트가 생성되면 **원본 준비** > **1단계: Hyper-V 사이트 선택**에서 생성된 사이트를 선택합니다.
5. **+ Hyper-V**를 선택합니다.

    ![Hyper-V 서버](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Microsoft Azure Site Recovery Provider용 설치 관리자를 다운로드합니다.
7. 자격 증명 모음 등록 키를 다운로드합니다. 공급자를 설치하려면 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

    ![공급자 및 등록 키 다운로드](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Provider 설치

Hyper-V 사이트에 추가하려는 각 Hyper-V 호스트에 다운로드한 설치 파일(AzureSiteRecoveryProvider.exe)을 설치합니다. 설치 프로그램에서 Azure Site Recovery Provider와 Recovery Services 에이전트를 각 Hyper-V 호스트에 설치합니다.

1. 설치 파일을 실행합니다.
2. Azure Site Recovery Provider 설치 마법사 > **Microsoft 업데이트**에서 Provider 업데이트를 확인하려면 Microsoft 업데이트를 사용하는 옵션을 선택합니다.
3. **설치**에서 Provider와 에이전트에 대한 기본 설치 위치를 적용하고, **설치**를 선택합니다.
4. 설치 후에 Microsoft Azure Site Recovery 등록 마법사 > **자격 증명 모음 설정**에서 **찾아보기**를 선택하고, **키 파일**에서 다운로드한 자격 증명 모음 키 파일을 선택합니다.
5. Azure Site Recovery 구독, 자격 증명 모음 이름(**ContosoVMVault**) 및 Hyper-V Server가 속한 Hyper-V 사이트(**ContosoHyperVSite**)를 지정합니다.
6. **프록시 설정**에서 **프록시 서버 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
7. **등록**에서 자격 증명 모음에 서버를 등록한 후에 **마침**을 선택합니다.

Azure Site Recovery에서 Hyper-V 서버의 메타데이터가 검색되며 서버가 **Site Recovery 인프라** > **Hyper-V 호스트**에 표시됩니다. 이 프로세스는 최대 30분 정도 걸릴 수 있습니다.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Hyper-V 코어 서버에 Provider 설치

Hyper-V 코어 서버를 실행하는 경우 설치 파일을 다운로드하고 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 AzureSiteRecoveryProvider.exe에서 로컬 디렉터리로 파일을 추출합니다.

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. `.\setupdr.exe /i`을 실행합니다. 결과가 %Programdata%\ASRLogs\DRASetupWizard.log에 로깅됩니다.

3. 다음 명령을 실행하여 서버를 등록합니다.

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 선택합니다.
2. 장애 조치 후 Azure VM을 만들 구독과 **ContosoRG** 리소스 그룹을 선택합니다.
3. **Resource Manager"** 배포 모델을 선택합니다.

Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

## <a name="set-up-a-replication-policy"></a>복제 정책 설정

1. **인프라 준비** > **복제 설정** >  **+만들기 및 연결**을 차례로 선택합니다.
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다. 여기서는 **ContosoReplicationPolicy**를 사용합니다.
3. 이 자습서에서는 기본 설정을 그대로 적용합니다.
    - **복사 빈도**는 초기 복제 후 델타 데이터가 복제되는 빈도를 나타냅니다. 기본 빈도는 5분마다입니다.
    - **복구 지점 보존**은 복구 지점이 2시간 동안 보존됨을 나타냅니다.
    - **앱 일치 스냅샷 빈도**는 앱 일치 스냅샷을 포함하는 복구 지점이 1시간마다 만들어짐을 나타냅니다.
    - **초기 복제 시작 시간**은 초기 복제가 즉시 시작됨을 나타냅니다.
4. 정책이 만들어지면 **확인**을 선택합니다. 새 정책을 만들면 지정된 Hyper-V 사이트에 자동으로 연결됩니다. 이 자습서에서는 **ContosoHyperVSite**입니다.

    ![복제 정책](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>복제 사용

1. **애플리케이션 복제**에서 **원본**을 선택합니다.
2. **원본**에서 **ContosoHyperVSite** 사이트를 선택합니다. 그런 다음 **확인**을 선택합니다.
3. **대상**에서 대상(Azure), 자격 증명 모음 구독, **Resource Manager** 배포 모델을 확인합니다.
4. 자습서 설정을 사용하는 경우 복제된 데이터에 대해 이전 자습서에서 만든 **contosovmsacct1910171607** 스토리지 계정을 선택합니다. 또한 장애 조치 후에 Azure VM이 배치될 **ContosoASRnet** 네트워크도 선택합니다.
5. **가상 머신** > **선택**에서 복제하려는 VM을 선택합니다. 그런 다음 **확인**을 선택합니다.

   **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 완료되면 초기 복제가 완료되고 VM에서 장애 조치할 준비가 됩니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [재해 복구 드릴 실행](tutorial-dr-drill-azure.md)
