---
title: Data Science Virtual Machine를 Ubuntu 18.04로 업그레이드 하는 방법
titleSuffix: Azure Data Science Virtual Machine
description: CentOS 및 Ubuntu 16.04에서 최신 Ubuntu 18.04 Data Science Virtual Machine로 업그레이드 하는 방법을 알아봅니다.
keywords: 심층 학습, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 팀 데이터 과학 프로세스
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b98384d4d735f4c124c6af40d6edbff896900ce
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320990"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Data Science Virtual Machine을 Ubuntu 18.04로 업그레이드

Ubuntu 16.04 또는 CentOS와 같은 이전 릴리스를 실행 하는 Data Science Virtual Machine 있는 경우 DSVM을 Ubuntu 18.04로 마이그레이션해야 합니다. 마이그레이션을 통해 최신 운영 체제 패치, 드라이버, 사전 설치 된 소프트웨어 및 라이브러리 버전을 가져올 수 있습니다. 이 문서에서는 이전 버전의 Ubuntu 또는 CentOS에서 마이그레이션하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

- SSH 및 Linux 명령줄 사용에 대 한 지식

## <a name="overview"></a>개요

마이그레이션할 수 있는 두 가지 방법은 다음과 같습니다.

- "동일 서버" 마이그레이션이 라고도 하는 전체 마이그레이션. 이 마이그레이션은 새 가상 컴퓨터를 만들지 않고 기존 VM을 업그레이드 합니다. 현재 위치의 마이그레이션은 Ubuntu 16.04에서 Ubuntu 18.04로 마이그레이션하는 쉬운 방법입니다.
- "서버 간" 마이그레이션이 라고도 하는 병렬 마이그레이션. 이 마이그레이션은 기존 가상 머신에서 새로 만든 VM으로 데이터를 전송 합니다. 병렬 마이그레이션은 Centos에서 Ubuntu 18.04로 마이그레이션하는 방법입니다. 이전 설치가 불필요 하 게 복잡해 지는 경우 Ubuntu 버전 간의 업그레이드에 대 한 단계별 마이그레이션을 선호 하는 경우가 있습니다.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>롤백해야 할 경우 VM 스냅숏

Azure Portal에서 검색 표시줄을 사용 하 여 **스냅숏** 기능을 찾을 수 있습니다. 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="* * 스냅숏이 강조 표시 된 Azure Portal 및 검색 창을 보여 주는 스크린샷 * * 강조 표시":::

1. **추가** 를 선택 합니다. 그러면 **스냅숏 만들기** 페이지로 이동 합니다. 가상 컴퓨터의 구독 및 리소스 그룹을 선택 합니다. **지역** 에서 대상 저장소가 있는 동일한 지역을 선택 합니다. DSVM 저장소 디스크 및 추가 백업 옵션을 선택 합니다. **표준 HDD** 은이 백업 시나리오에 적합 한 저장소 유형입니다.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="' 스냅숏 만들기 ' 옵션을 보여 주는 스크린샷":::

2. 모든 세부 정보가 채워지고 유효성 검사에 통과 하면 **검토 + 만들기** 를 선택 하 여 스냅숏 유효성을 검사 하 고 만듭니다. 스냅숏이 성공적으로 완료 되 면 배포가 완료 되었다는 메시지가 표시 됩니다.

## <a name="in-place-migration"></a>현재 위치의 마이그레이션

이전 Ubuntu 릴리스를 마이그레이션하는 경우 전체 마이그레이션을 수행 하도록 선택할 수 있습니다. 이 마이그레이션은 새 가상 컴퓨터를 만들지 않으며 side-by-side 마이그레이션 보다 단계가 훨씬 작습니다.  더 많은 제어가 필요 하거나 CentOS와 같은 다른 배포에서 마이그레이션하는 중이기 때문에 병렬 마이그레이션을 수행 하려는 경우 병렬 [마이그레이션](#side-by-side-migration) 섹션으로 건너뜁니다. 

1. Azure Portal에서 DSVM을 시작 하 고 SSH를 사용 하 여 로그인 합니다. 이렇게 하려면 **연결** 및 **SSH** 를 선택 하 고 연결 지침을 따릅니다. 

1. DSVM의 터미널 세션에 연결 되 면 다음 업그레이드 명령을 실행 합니다.

    ```bash
    sudo do-release-upgrade
    ```

업그레이드 프로세스를 완료 하는 데 시간이 오래 걸립니다. 이 기능을 사용 하는 경우 프로그램은 가상 컴퓨터를 다시 시작할 수 있는 권한을 요청 합니다. **예** 를 대답 합니다. 시스템이 다시 부팅 되 면 SSH 세션에서 연결을 끊습니다.

### <a name="if-necessary-regenerate-ssh-keys"></a>필요한 경우 SSH 키를 다시 생성 합니다.

> [!IMPORTANT] 
> 업그레이드 및 재부팅 후 SSH 키를 다시 생성 해야 할 수 있습니다.

VM을 업그레이드 하 고 다시 부팅 한 후 SSH를 통해 다시 액세스 하려고 시도 합니다. 다시 부팅 하는 동안 IP 주소가 변경 되었을 수 있으므로 연결을 시도 하기 전에 확인 합니다.

**원격 호스트 ID가 변경 되었습니다**. 라는 오류 메시지가 표시 되 면 SSH 자격 증명을 다시 생성 해야 합니다.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="원격 호스트 id 변경 경고를 보여 주는 PowerShell 스크린샷":::

이렇게 하려면 로컬 컴퓨터에서 명령을 실행 합니다.

```bash
ssh-keygen -R "your server hostname or ip"
```

이제 SSH를 사용 하 여 연결할 수 있습니다. 여전히 문제가 발생 하는 경우에는 **연결** 페이지에서 **SSH 연결 문제 해결** 에 대 한 링크를 따르세요.

## <a name="side-by-side-migration"></a>단계별 마이그레이션

CentOS에서 마이그레이션하는 경우 또는 운영 체제를 새로 설치 하려는 경우 병렬 마이그레이션을 수행할 수 있습니다. 이러한 유형의 마이그레이션에는 더 많은 단계가 있지만 어떤 파일이 전달 되었는지 정확 하 게 제어할 수 있습니다.

동일한 업스트림 원본 패키지 집합을 기반으로 하는 다른 시스템에서의 마이그레이션은 비교적 간단 합니다 (예: [FAQ/CentOS3](https://wiki.centos.org/FAQ/CentOS3)).

파일 시스템의 운영 체제 부분을 업그레이드 하 고 사용자 디렉터리를 그대로 둘 수 있습니다 `/home` . 이전 사용자 홈 디렉터리를 그대로 두면 GNOME/KDE 메뉴 및 기타 데스크톱 항목에서 몇 가지 문제가 발생할 수 있습니다. 마이그레이션 후 사용자 자료를 참조, 복사 또는 연결 하기 위해 새 사용자 계정을 만들고 파일 시스템의 다른 위치에 이전 디렉터리를 탑재 하는 것이 가장 쉽습니다.

### <a name="migration-at-a-glance"></a>한 눈에 마이그레이션

1.  앞에서 설명한 대로 기존 VM의 스냅숏 만들기

1.  해당 스냅숏에서 디스크 만들기

1.  새 Ubuntu Data Science Virtual Machine 만들기

1.  새 가상 머신에서 사용자 계정 다시 만들기

1.  새 Data Science Virtual Machine에서 스냅숏 VM의 디스크를 데이터 디스크로 탑재

1.  원하는 데이터를 수동으로 복사

### <a name="create-a-disk-from-your-vm-snapshot"></a>VM 스냅숏에서 디스크 만들기

앞서 설명한 대로 VM 스냅숏을 아직 만들지 않은 경우 그렇게 합니다. 

1. Azure Portal에서 **디스크** 를 검색 하 고 **추가** 를 선택 합니다. 그러면 **디스크** 페이지가 열립니다.

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="디스크 검색 페이지 및 추가 단추를 표시 하는 Azure Portal의 스크린샷":::

2. **구독** , **리소스 그룹** 및 **지역을** VM 스냅숏의 값으로 설정 합니다. 만들 디스크의 **이름을** 선택 합니다.

3. **원본 유형을** **스냅숏으로** 선택 하 고 **원본 스냅숏으로** VM 스냅숏을 선택 합니다. 디스크를 검토 하 고 만듭니다. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="옵션을 표시 하는 디스크 만들기 대화 상자의 스크린샷":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>새 Ubuntu Data Science Virtual Machine 만들기

[Azure Portal](https://portal.azure.com) 또는 [ARM 템플릿을](./dsvm-tutorial-resource-manager.md)사용 하 여 새 Ubuntu Data Science Virtual Machine를 만듭니다. 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>새 Data Science Virtual Machine에서 사용자 계정을 다시 만듭니다.

이전 컴퓨터의 데이터만 복사할 예정 이므로 새 컴퓨터에서 사용 하려는 모든 사용자 계정 및 소프트웨어 환경을 다시 만들어야 합니다.

Linux는 새 설치의 디렉터리와 경로를 사용자 지정 하 여 이전 컴퓨터를 따르도록 유연 하 게 사용할 수 있습니다. 그러나 일반적으로 최신 Ubuntu의 기본 레이아웃을 사용 하 고 사용자 환경 및 스크립트를 수정 하 여 조정할 수 있습니다.

자세한 내용은 [빠른 시작: Linux (Ubuntu)의 Data Science Virtual Machine 설정](./dsvm-ubuntu-intro.md)을 참조 하세요.

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>새 Data Science Virtual Machine에서 스냅숏 VM의 디스크를 데이터 디스크로 탑재

1. Azure Portal에서 Data Science Virtual Machine 실행 중인지 확인 합니다.

2. Azure Portal에서 Data Science Virtual Machine 페이지로 이동 합니다. 왼쪽 레일에서 **디스크** 블레이드를 선택 합니다. **기존 디스크 연결** 을 선택 합니다.

3. **디스크 이름** 드롭다운에서 이전 VM의 스냅숏에서 만든 디스크를 선택 합니다.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="디스크 첨부 파일 옵션을 보여 주는 DSVM 옵션 페이지의 스크린샷":::

4. **저장** 을 선택 하 여 가상 머신을 업데이트 합니다.

> [!Important]
> 데이터 디스크를 연결할 때 VM이 실행 중 이어야 합니다. VM이 실행 되 고 있지 않으면 디스크가 잘못 된 순서로 추가 되어 혼란 스 럽 고 부팅 불가능 한 시스템이 될 수 있습니다. VM을 사용 하 여 데이터 디스크를 추가 하는 경우 데이터 디스크 옆에 있는 **X** 를 선택 하 고 VM을 시작한 다음 다시 연결 합니다.

### <a name="manually-copy-the-wanted-data"></a>원하는 데이터를 수동으로 복사 

1. SSH를 사용 하 여 실행 중인 가상 머신에 로그온 합니다.

2. 다음 명령을 실행 하 여 이전 VM의 스냅숏에서 만든 디스크를 연결 했는지 확인 합니다.

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    결과는 다음 이미지와 유사 하 게 표시 됩니다. 이미지에서 디스크는 `sda1` 루트에 탑재 되 고 `sdb2` `/mnt` 스크래치 디스크입니다. 이전 VM의 스냅숏에서 생성 된 데이터 디스크는 탑재 위치가 부족 한 것으로 확인 `sdc1` 되었지만 아직 사용할 수 없는 것으로 식별 됩니다. 결과의 식별자는 다를 수 있지만 유사한 패턴이 표시 되어야 합니다.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="분리 된 데이터 드라이브를 표시 하는 lsblk 출력의 스크린샷":::
    
3. 데이터 드라이브에 액세스 하려면 해당 위치를 만들어 탑재 합니다. `/dev/sdc1`에서 반환 된 적절 한 값으로 대체 합니다 `lsblk` .

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. 이제에는 `/datadrive` 이전 Data Science Virtual Machine의 디렉터리와 파일이 포함 되어 있습니다. 원하는 대로 데이터 드라이브의 디렉터리 또는 파일을 새 VM으로 이동 하거나 복사 합니다.

자세한 내용은 [포털을 사용 하 여 LINUX VM에 데이터 디스크 연결을](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)참조 하세요.

## <a name="connect-and-confirm-version-upgrade"></a>버전 업그레이드 연결 및 확인

전체 마이그레이션 또는 side-by-side 마이그레이션 여부와 상관 없이 업그레이드 되었는지 확인 합니다. 터미널 세션에서 다음을 실행 합니다. 

```bash
cat /etc/os-release
```

Ubuntu 18.04를 실행 하는 것을 확인 해야 합니다.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="OS 버전 데이터를 표시 하는 Ubuntu 터미널의 스크린샷":::

버전의 변경 내용은 Azure Portal에도 표시 됩니다.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="OS 버전을 포함 하 여 DSVM 속성을 보여 주는 포털 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [Azure에서 Ubuntu 데이터 과학 컴퓨터를 사용 하는 데이터 과학](./linux-dsvm-walkthrough.md)
- [Azure Data Science Virtual Machine에 포함되는 도구는?](./tools-included.md)