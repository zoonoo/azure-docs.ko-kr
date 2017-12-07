---
title: "Azure Migrate에서 컴퓨터 종속성을 사용하여 컴퓨터 그룹화 | Microsoft Docs"
description: "Azure Migrate 서비스에서 컴퓨터 종속성을 사용하여 평가를 만드는 방법을 설명합니다."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 5ed49f3dc71af6a8c1c7b6c9e38fd84452505aec
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>컴퓨터 종속성 매핑을 사용하여 컴퓨터 그룹화

이 문서에서는 컴퓨터 종속성 매핑을 사용하여 [Azure Migrate](migrate-overview.md) 평가를 위한 컴퓨터 그룹을 만드는 방법에 대해 설명합니다. 일반적으로 평가를 실행하기 전에 컴퓨터 종속성을 교차 확인하여 더 높은 수준의 신뢰도로 VM 그룹을 평가하려는 경우에 이 방법을 사용합니다.



## <a name="prepare-machines-for-dependency-mapping"></a>종속성 매핑을 위한 컴퓨터 준비
종속성 매핑에 컴퓨터를 포함하려면 평가하려는 각 온-프레미스 컴퓨터에 에이전트를 다운로드하여 설치해야 합니다. 또한 인터넷에 연결되지 않은 컴퓨터가 있으면 [OMS 게이트웨이](../log-analytics/log-analytics-oms-gateway.md)를 다운로드하여 설치해야 합니다.

### <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치
1. **개요**에서 **관리** > **컴퓨터**를 차례로 클릭하고 필요한 컴퓨터를 선택합니다.
2. **종속성** 열에서 **에이전트 설치**를 클릭합니다. 
3. **종속성** 페이지에서 평가하려는 각 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 다운로드하여 설치합니다.
4. 작업 영역 ID와 키를 복사합니다. 이 옵션은 온-프레미스 컴퓨터에 MMA를 설치할 때 필요합니다.

### <a name="install-the-mma"></a>MMA 설치

Windows 컴퓨터에 에이전트를 설치하려면

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다. **사용 조건** 페이지에서 **동의함**을 클릭하여 라이선스에 동의합니다.
3. **대상 폴더**에서 기본 설치 폴더를 유지하거나 수정하고 **다음**을 클릭합니다. 
4. **에이전트 설치 옵션** 에서 **Azure Log Analytics(OMS)** > **다음**을 차례로 선택합니다. 
5. **추가**를 클릭하여 새 OMS 작업 영역을 추가합니다. 포털에서 복사한 작업 영역 ID와 키를 붙여넣습니다. **다음**을 누릅니다.


Linux 컴퓨터에 에이전트를 설치하려면

1. scp/sftp를 사용하여 Linux 컴퓨터로 해당 번들(x86 또는 x64)을 전송합니다.
2. --install 인수를 사용하여 번들을 설치합니다.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>종속성 에이전트 설치
1. Windows 컴퓨터에 종속성 에이전트를 설치하려면 설치 파일을 두 x 누르고 마법사를 따릅니다.
2. Linux 컴퓨터에 종속성 에이전트를 설치하려면 다음 명령을 사용하여 루트로 설치합니다.

    ```sh InstallDependencyAgent-Linux64.bin```

종속성 에이전트에서 지원하는 운영 체제에 대해 [자세히 알아보세요](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems). 

## <a name="create-a-group"></a>그룹 만들기

1. 에이전트를 설치한 후에 포털로 이동하여 **관리** > **컴퓨터**를 차례로 클릭합니다.
2. 이제 **종속성** 열이 **종속성 보기**로 표시됩니다. 해당 열을 클릭하여 종속성을 봅니다.
3. 각 컴퓨터에 대해 다음 항목을 확인할 수 있습니다.
    - MMA 및 종속성 에이전트가 설치되어 있는지 여부 및 컴퓨터가 검색되었는지 여부
    - 컴퓨터에서 실행되는 게스트 운영 체제
    - 인바운드/아웃바운드 IP 연결 및 포트
    - 컴퓨터에서 실행되는 프로세스
    - 컴퓨터 간 종속성

4. 더 세부적인 종속성을 보려면 시간 범위를 클릭하여 수정합니다. 범위는 기본적으로 1시간입니다. 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.
5. 함께 그룹화하려는 종속 컴퓨터를 식별한 후 맵에서 컴퓨터를 선택하고 **컴퓨터 그룹화**를 클릭합니다.
6. 그룹 이름을 지정합니다. Azure Migrate에서 컴퓨터가 검색되는지 확인합니다. 실행되지 않으면 온-프레미스에서 검색 프로세스가 다시 실행됩니다. 원하는 경우 평가를 바로 실행할 수 있습니다.
7. **확인**을 클릭하여 그룹을 저장합니다.

    ![컴퓨터 종속성을 사용하여 그룹 만들기](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>다음 단계

- 그룹 종속성을 확인하여 그룹을 구체화하는 방법을 [알아봅니다](how-to-create-group-dependencies.md).
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
