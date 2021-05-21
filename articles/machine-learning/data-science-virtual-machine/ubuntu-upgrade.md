---
title: Data Science Virtual Machine을 Ubuntu 18.04로 업그레이드하는 방법
titleSuffix: Azure Data Science Virtual Machine
description: CentOS 및 Ubuntu 16.04에서 최신 Ubuntu 18.04 Data Science Virtual Machine으로 업그레이드하는 방법을 알아봅니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 팀 데이터 과학 프로세스
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b897ff7527d2d60234162ccbdeb08a00260bb1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101659463"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Data Science Virtual Machine을 Ubuntu 18.04로 업그레이드

Ubuntu 16.04 또는 CentOS와 같은 이전 릴리스를 실행하는 Data Science Virtual Machine이 있는 경우 DSVM을 Ubuntu 18.04로 마이그레이션해야 합니다. 마이그레이션하면 최신 운영체제 패치, 드라이버, 사전 설치된 소프트웨어, 라이브러리 버전을 사용할 수 있습니다. 이 문서에서는 이전 버전의 Ubuntu 또는 CentOS에서 마이그레이션하는 방법을 알려줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

- SSH 및 Linux 명령줄에 대한 기본 지식

## <a name="overview"></a>개요

가능한 마이그레이션 방법은 두 가지입니다.

- “동일 서버” 마이그레이션이라고도 부르는 현재 위치 마이그레이션. 이 마이그레이션은 새 가상 머신을 만들지 않고 기존 VM을 업그레이드합니다. 현재 위치 마이그레이션은 Ubuntu 16.04에서 Ubuntu 18.04로 더 쉽게 마이그레이션할 수 있는 방법입니다.
- “서버 간” 마이그레이션이라고도 부르는 병렬 마이그레이션. 이 마이그레이션은 기존 가상 머신에서 새로 생성된 VM으로 데이터를 전송합니다. 병렬 마이그레이션은 Centos에서 Ubuntu 18.04로 마이그레이션하는 방법입니다. 이전 설치가 불필요하게 복잡해진다고 여겨질 경우 Ubuntu 버전 간 업그레이드를 위해 병렬 마이그레이션을 선호할 수 있습니다.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>롤백이 필요한 경우를 위해 VM 스냅샷 작성

Azure Portal에서 검색 창을 사용하여 **스냅샷** 기능을 찾습니다. 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="**스냅샷**이 강조 표시된 Azure Portal 및 검색 창을 보여주는 스크린샷":::

1. **추가** 를 선택하면 **스냅샷 만들기** 페이지가 표시됩니다. 가상 머신의 구독 및 리소스 그룹을 선택합니다. **지역** 에서 대상 스토리지가 있는 동일한 지역을 선택합니다. DSVM 스토리지 디스크 및 추가 백업 옵션을 선택합니다. **표준 HDD** 는 이 백업 시나리오에 적합한 스토리지 유형입니다.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="‘스냅샷 만들기’ 옵션을 보여주는 스크린샷":::

2. 모든 세부 정보가 채워지고 유효성 검사를 통과한 후 **검토 + 만들기** 를 선택하여 스냅샷을 유효성 검사하고 만듭니다. 스냅샷이 성공적으로 완료되면 배포가 완료되었다는 메시지가 표시됩니다.

## <a name="in-place-migration"></a>현재 위치 마이그레이션

이전 Ubuntu 릴리스를 마이그레이션하는 경우 현재 위치 마이그레이션을 수행하도록 선택할 수 있습니다. 이 마이그레이션은 새 가상 머신을 만들지 않으며 병렬 마이그레이션보다 적은 수의 단계를 포함합니다.  더 많은 제어를 원하거나 CentOS와 같은 다른 배포로부터 마이그레이션하기 때문에 병렬 마이그레이션을 수행하려는 경우 [병렬 마이그레이션](#side-by-side-migration) 섹션으로 건너뜁니다. 

1. Azure Portal에서 DSVM을 시작하고 SSH를 사용하여 로그인합니다. 이렇게 하려면 **연결** 및 **SSH** 를 선택하고 연결 지침을 따릅니다. 

1. DSVM에서 터미널 세션에 연결된 후 다음 업그레이드 명령을 실행합니다.

    ```bash
    sudo do-release-upgrade
    ```

업그레이드 프로세스가 완료되려면 시간이 걸립니다. 완료되었으면 프로그램이 가상 머신을 다시 시작하기 위한 권한을 요청합니다. **예** 를 선택합니다. 시스템이 다시 부팅되면 SSH 세션에서 연결이 끊어집니다.

### <a name="if-necessary-regenerate-ssh-keys"></a>필요한 경우 SSH 키 다시 생성

> [!IMPORTANT] 
> 업그레이드 및 다시 부팅을 수행한 후 SSH 키를 다시 생성해야 할 수 있습니다.

VM을 업그레이드하고 다시 부팅한 후 SSH를 통해 다시 액세스하려고 시도합니다. 다시 부팅하는 동안 IP 주소가 변경되었을 수 있으므로 연결을 시도하기 전 확인합니다.

**원격 호스트 ID가 변경됨** 오류가 표시되면 SSH 자격 증명을 다시 생성해야 합니다.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="원격 호스트 ID가 변경됨 경고를 보여주는 PowerShell 스크린샷":::

이렇게 하려면 로컬 머신에서 다음 명령을 실행합니다.

```bash
ssh-keygen -R "your server hostname or ip"
```

이제 SSH를 사용하여 연결할 수 있습니다. 아직 문제가 있으면 **연결** 페이지에서 **SSH 연결 문제 해결** 링크를 따릅니다.

## <a name="side-by-side-migration"></a>단계별 마이그레이션

CentOS에서 마이그레이션하거나 OS를 새로 설치하려면 병렬 마이그레이션을 수행할 수 있습니다. 이 유형의 마이그레이션은 단계가 더 많지만 마이그레이션할 파일을 정확하게 제어할 수 있습니다.

[FAQ/CentOS3](https://wiki.centos.org/FAQ/CentOS3)과 같이 동일한 업스트림 소스 패키지 집합을 기반으로 하는 다른 시스템에서의 마이그레이션은 비교적 직관적입니다.

파일 시스템의 운영 체제 부분을 업그레이드하고 `/home`과 같은 사용자 디렉터리는 현재 위치에 남겨두도록 선택할 수 있습니다. 이전 사용자 홈 디렉터리를 현재 위치에 남겨둘 경우, GNOME/KDE 메뉴 및 기타 데스크톱 항목에 문제가 발생할 수 있습니다. 새 사용자 계정을 만들고 마이그레이션 후 사용자 자료를 참조, 복사 또는 연결할 수 있도록 이전 디렉터리를 파일 시스템의 다른 위치에 탑재하는 것이 가장 쉬울 수 있습니다.

### <a name="migration-at-a-glance"></a>마이그레이션 한 눈에 보기

1.  앞에서 설명한 대로 기존 VM의 스냅샷 만들기

1.  스냅샷으로부터 디스크 만들기

1.  새 Ubuntu Data Science Virtual Machine 만들기

1.  새 가상 머신에서 사용자 계정 다시 만들기

1.  Data Science Virtual Machine에 스냅샷으로 작성된 VM의 디스크를 데이터 디스크로 탑재

1.  원하는 데이터를 수동으로 복사

### <a name="create-a-disk-from-your-vm-snapshot"></a>VM 스냅샷에서 디스크 만들기

이전에 설명한 대로 VM 스냅샷을 이미 만든 경우, 그렇게 합니다. 

1. Azure Portal에서 **디스크** 를 검색하고 **추가** 를 선택하면 **디스크** 페이지가 열립니다.

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="디스크 검색 페이지 및 추가 단추를 보여주는 Azure Portal의 스크린샷":::

2. **구독**, **리소스 그룹** 및 **지역** 을 VM 스냅샷의 값으로 설정합니다. 만들 디스크의 **이름** 을 선택합니다.

3. **원본 유형** 으로 **스냅샷** 을 선택하고 VM 스냅샷으로 **소스 스냅샷** 을 선택합니다. 디스크를 검토하고 만듭니다. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="옵션을 표시하는 디스크 만들기 대화 상자의 스크린샷":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>새 Ubuntu Data Science Virtual Machine 만들기

[Azure Portal](https://portal.azure.com) 또는 [ARM 템플릿](./dsvm-tutorial-resource-manager.md)을 사용하여 새 Ubuntu Data Science Virtual Machine을 만듭니다. 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>새 Data Science Virtual Machine에서 사용자 계정 다시 만들기

이전 컴퓨터에서 데이터만 복사하므로 새 머신에서 사용하려는 사용자 계정 및 소프트웨어 환경을 다시 만들어야 합니다.

Linux는 이전 머신에 따라 새 설치에서 디렉터리 및 경로를 사용자 지정할 수 있을 정도로 충분히 유연합니다. 하지만 일반적으로 최신 Ubuntu의 선호 레이아웃을 사용하고 이에 맞게 사용자 환경 및 스크립트를 수정하는 것이 더 쉽습니다.

자세한 내용은 [빠른 시작: Linux(Ubuntu)용 Data Science Virtual Machine 설정](./dsvm-ubuntu-intro.md)을 참조하세요.

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Data Science Virtual Machine에 스냅샷으로 작성된 VM의 디스크를 데이터 디스크로 탑재

1. Azure Portal에서 Data Science Virtual Machine이 실행 중인지 확인합니다.

2. Azure Portal에서 Data Science Virtual Machine의 페이지로 이동합니다. 왼쪽 레일에서 **디스크** 블레이드를 선택합니다. **기존 디스크 연결** 을 선택합니다.

3. **디스크 이름** 드롭다운에서 이전 VM의 스냅샷으로부터 만든 디스크를 선택합니다.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="디스크 연결 옵션을 보여주는 DSVM 옵션의 스크린샷":::

4. **저장** 을 선택하여 가상 머신을 업데이트합니다.

> [!Important]
> 데이터 디스크를 연결할 때 VM이 실행 중이어야 합니다. VM이 실행 중이 아니면 디스크가 잘못된 순서로 추가되어 혼돈을 일으킬 수 있고 시스템이 부팅되지 않을 수 있습니다. VM이 꺼진 상태로 데이터 디스크를 추가하는 경우 데이터 디스크 옆의 **X** 를 선택하고, VM을 시작하고, 다시 연결합니다.

### <a name="manually-copy-the-wanted-data"></a>원하는 데이터를 수동으로 복사 

1. SSH를 사용하여 실행 중인 가상 머신에 로그온합니다.

2. 다음 명령을 실행하여 이전 VM 스냅샷에서 만든 디스크를 연결했는지 확인합니다.

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    결과는 다음 이미지와 비슷하게 표시됩니다. 이미지에서 `sda1` 디스크는 루트에 탑재되고 `sdb2`는 `/mnt` 스크래치 디스크입니다. 이전 VM의 스냅샷에서 생성된 데이터 디스크는 `sdc1`로 식별되지만, 탑재 위치 부족으로 입증된 대로 아직 사용할 수 없습니다. 결과에 다른 식별자가 있을 수 있지만, 비슷한 패턴이 표시됩니다.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="탑재되지 않은 데이터 드라이브를 보여주는 lsblk 출력의 스크린샷":::
    
3. 데이터 드라이브에 액세스하려면 그에 대한 위치를 만들고 탑재합니다. `/dev/sdc1`을 `lsblk`에서 반환되는 적절한 값으로 바꿉니다.

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. 이제 `/datadrive`에 이전 Data Science Virtual Machine의 디렉터리 및 파일이 포함됩니다. 데이터 드라이브에서 새 VM으로 원하는 디렉터리 또는 파일을 원하는 대로 이동하거나 복사합니다.

자세한 내용은 [포털을 사용하여 데이터 디스크를 Linux VM에 연결](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)을 참조하세요.

## <a name="connect-and-confirm-version-upgrade"></a>연결 및 버전 업그레이드 확인

현재 위치 또는 병렬 마이그레이션을 수행했는지 여부에 관계없이 업그레이드가 성공했는지 확인합니다. 터미널 세션에서 다음을 실행합니다. 

```bash
cat /etc/os-release
```

Ubuntu 18.04를 실행 중인 것으로 표시됩니다.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="OS 버전 데이터를 표시하는 Ubuntu 터미널의 스크린샷":::

버전 변경이 Azure Portal에도 표시됩니다.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="OS 버전을 포함하여 DSVM 속성을 보여주는 포털의 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [Azure에서 Ubuntu Data Science Machine을 사용하는 데이터 과학](./linux-dsvm-walkthrough.md)
- [Azure Data Science Virtual Machine에 포함되는 도구는?](./tools-included.md)