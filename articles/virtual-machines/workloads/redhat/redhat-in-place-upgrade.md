---
title: Azure에서 Red Hat Enterprise Linux 이미지의 전체 업그레이드
description: Red Hat Enterprise 4.x 이미지에서 최신 8gb 버전으로 전체 업그레이드를 수행 하는 방법을 알아봅니다.
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 04a83687161c390d86e1a9b40d33f10cdd6a47d5
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916681"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>현재 위치의 업그레이드 Red Hat Enterprise Linux

이 문서에서는 Red Hat Enterprise Linux (RHEL) 7에서 Red Hat Enterprise Linux 8로 전체 업그레이드를 수행 하는 방법에 대 한 지침을 제공 합니다. 이 지침에서는 `leapp` Azure의 도구를 사용 합니다. 현재 위치의 업그레이드 중에는 기존 RHEL 7 운영 체제가 RHEL 8 버전으로 대체 됩니다.

>[!Note] 
> Red Hat Enterprise Linux에 대 한 SQL Server 제품은 Azure에서 전체 업그레이드를 지원 하지 않습니다.

## <a name="what-to-expect-during-the-upgrade"></a>업그레이드 하는 동안 발생할 수 있는 작업
업그레이드 하는 동안 시스템이 몇 번 다시 시작 됩니다. 마지막 다시 시작은 VM을 RHEL 8 최신 부 릴리스로 업그레이드 합니다. 

업그레이드 프로세스는 20 분에서 2 시간까지 걸릴 수 있습니다. 총 시간은 VM 크기 및 시스템에 설치 된 패키지 수와 같은 여러 요소에 따라 달라 집니다.

## <a name="preparations"></a>준비
Red Hat 및 Azure는 전체 업그레이드를 사용 하 여 시스템을 다음 주 버전으로 전환 하는 것이 좋습니다. 

업그레이드를 시작 하기 전에 다음 사항을 고려해 야 합니다. 

>[!Important] 
> 업그레이드를 시작 하기 전에 이미지의 스냅숏을 만듭니다.

* 최신 RHEL 7 버전을 사용 하 고 있는지 확인 합니다. 현재 최신 버전은 RHEL 7.9입니다. 잠긴 버전을 사용 하 고 RHEL 7.9로 업그레이드할 수 없는 경우 다음 단계에 따라 [EUS (확장 업데이트 지원) 리포지토리로 전환](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)합니다.

* 다음 명령을 실행 하 여 업그레이드를 확인 하 고 성공적으로 완료 되는지 확인 합니다. 명령은 */var/log/leapp/leapp-report.txt* 파일을 생성 해야 합니다. 이 파일은 프로세스, 진행 상황 및 업그레이드가 가능한 지 여부를 설명 합니다.

    >[!NOTE]
    > 이 문서의 명령을 실행 하려면 루트 계정을 사용 합니다. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* 직렬 콘솔이 작동 하는지 확인 합니다. 업그레이드 프로세스 중에이 콘솔을 사용 하 여 모니터링 합니다.

* */Etc/ssh/sshd_config* 에서 SSH 루트 액세스를 사용 하도록 설정 합니다.
    1. */Etc/ssh/sshd_config* 파일을 엽니다.
    1. `#PermitRootLogin yes`를 검색합니다.
    1. `#`문자열의 주석 처리를 제거 하려면 숫자 기호 ()를 제거 합니다.

## <a name="upgrade-steps"></a>업그레이드 단계

이러한 단계를 신중 하 게 수행 합니다. 프로덕션 인스턴스에서 시도 하기 전에 테스트 컴퓨터에서 업그레이드를 시도 하는 것이 좋습니다.

1. 업데이트를 수행 `yum` 하 여 최신 클라이언트 패키지를 인출 합니다.
    ```bash
    yum update -y
    ```

1. `leapp-client-package`를 설치합니다.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. [Red Hat 포털](https://access.redhat.com/articles/3664871)에서 *repomap.csv* 를 포함 하는 *leapp-data release.tar.gz* 파일을 가져오고 *pes-events.js* 합니다. *Leapp-data release.tar.gz* 파일의 압축을 풉니다.
    1. *Leapp-data release.tar.gz* 파일을 다운로드 합니다.
    1. 콘텐츠를 추출 하 고 파일을 제거 합니다. 다음 명령을 사용합니다.
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. `answers`에 대 한 파일을 추가 `leapp` 합니다.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. 업그레이드를 시작 합니다.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade`명령이 성공적으로 완료 되 면 시스템을 수동으로 다시 시작 하 여 프로세스를 완료 합니다. 시스템은 두 번 다시 시작 될 때 사용할 수 없습니다. 직렬 콘솔을 사용 하 여 프로세스를 모니터링 합니다.

1.  업그레이드가 성공적으로 완료 되었는지 확인 합니다.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. 업그레이드가 완료 되 면 루트 SSH 액세스를 제거 합니다.
    1. */Etc/ssh/sshd_config* 파일을 엽니다.
    1. `#PermitRootLogin yes`를 검색합니다.
    1. 숫자 기호 ()를 추가 `#` 하 여 문자열을 주석으로 처리 합니다.

1. SSHD 서비스를 다시 시작 하 여 변경 내용을 적용 합니다.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>일반적인 문제

다음 오류는 `leapp preupgrade` 프로세스가 실패 하거나 프로세스가 실패할 때 일반적으로 발생 합니다 `leapp upgrade` .

* **오류**: 비활성화 된 다음 플러그 인 패턴과 일치 하는 항목이 없습니다.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **해결 방법**: 구독 관리자 플러그 인을 사용 하지 않도록 설정 합니다. */Etc/yum/pluginconf.d/subscription-manager.conf* 파일을 편집 하 고을로 변경 하 여 사용 하지 않도록 설정 `enabled` `enabled=0` 합니다.

    이 오류는 `yum` 사용 하도록 설정 된 구독 관리자 플러그 인이 vm에 사용 되지 않는 경우에 발생 `PAYG` 합니다.

* **오류**: root를 사용 하 여 원격 로그인에 문제가 있을 수 있습니다.

    가 실패 하면 다음과 같은 오류가 표시 될 수 있습니다 `leapp preupgrade` .

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **해결 방법**: */etc/sshd_config* 에서 루트 액세스를 사용 하도록 설정 합니다.

    이 오류는 */etc/sshd_config* 에서 루트 SSH 액세스를 사용 하도록 설정 하지 않은 경우에 발생 합니다. 자세한 내용은이 문서의 [준비](#preparations) 섹션을 참조 하세요. 


## <a name="next-steps"></a>다음 단계
* [Azure의 Red Hat 이미지](./redhat-images.md)에 대해 자세히 알아보세요.
* [Red Hat 업데이트 인프라](./redhat-rhui.md)에 대해 자세히 알아보세요.
* [RHEL BYOS 제품](./byos.md)에 대해 자세히 알아보세요.
* Red Hat 현재 위치의 업그레이드 프로세스에 대 한 자세한 내용은 Red Hat 설명서의 [RHEL 7에서 RHEL 8로 업그레이드](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) 를 참조 하세요.
* 모든 버전의 RHEL에 대 한 Red Hat 지원 정책에 대 한 자세한 내용은 Red Hat 설명서의 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 를 참조 하세요.
