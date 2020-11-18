---
title: Azure에서 Red Hat Enterprise Linux 이미지의 전체 업그레이드
description: Red Hat Enterprise 4.x 이미지에서 최신 8gb 버전으로 전체 업그레이드를 수행 하는 단계 찾기
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 1160bc43db0dc9ec1714b1766c8cadf09660e291
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844570"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>현재 위치의 업그레이드 Red Hat Enterprise Linux

이 문서에서는 Azure의 ' Leapp ' 유틸리티를 사용 하 여 Red Hat Enterprise Linux 7에서 Red Hat Enterprise Linux 8로 전체 업그레이드를 수행 하는 방법에 대 한 단계별 지침을 제공 합니다. 현재 위치의 업그레이드 중에는 기존 RHEL 7 운영 체제가 RHEL 8 버전으로 대체 됩니다.

>[!Note] 
> Red Hat Enterprise Linux 제품의 SQL Server는 Azure에서 전체 업그레이드를 지원 하지 않습니다.

## <a name="what-to-expect-during-the-upgrade"></a>업그레이드 하는 동안 발생할 수 있는 작업
업그레이드 하는 동안 시스템이 몇 번 다시 부팅 되며이는 정상입니다. 마지막 재부팅은 VM을 RHEL 8 최신 부 릴리스로 업그레이드 합니다. 

업그레이드 프로세스는 20 분에서 몇 시간까지 걸릴 수 있습니다 .이는 VM 크기 및 시스템에 설치 된 패키지의 수와 같은 여러 요소에 따라 달라 집니다.

## <a name="preparations-for-the-upgrade"></a>업그레이드 준비
현재 위치의 업그레이드는 Red Hat 및 Azure에서 고객이 시스템을 다음 주 버전으로 업그레이드할 수 있도록 하는 공식적으로 권장 되는 방법입니다. 업그레이드를 수행 하기 전에 다음 사항에 주의 해야 합니다. 

>[!Important] 
> 업그레이드를 수행 하기 전에 이미지의 스냅숏을 만드십시오.

>[!NOTE]
> 이 문서의 명령은 루트 계정을 사용 하 여 실행 해야 합니다.

* 현재 RHEL 7.9 인 최신 RHEL 7 버전을 사용 하 고 있는지 확인 합니다. 잠긴 버전을 사용 하 고 RHEL 7.9로 업그레이드할 수 없는 경우 [여기서 설명 하는 단계를 사용 하 여 EUS 리포지토리가 아닌 리포지토리로 전환할](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)수 있습니다.

* 아래 명령을 실행 하 여 업그레이드가 진행 되 고 있는지 여부를 확인 합니다. 이 명령은 프로세스를 설명 하는 '/var/log/leapp/leapp-report.txt ' 아래 파일을 생성 하 고, 업그레이드를 수행할 수 있는지 여부를 확인 합니다.
    ```bash
    leapp preupgrade --no-rhsm
    ```
* 업그레이드 프로세스 중에 모니터링을 허용 하므로 직렬 콘솔이 작동 하는지 확인 합니다.

* 에서 SSH 루트 액세스를 사용 하도록 설정 `/etc/ssh/sshd_config`
    1. `/etc/ssh/sshd_config` 파일을 엽니다.
    1. ' #PermitRootLogin 예 '를 검색 합니다.
    1. 주석 처리를 제거 하려면 ' # '을 제거 하십시오.

## <a name="steps-for-performing-the-upgrade"></a>업그레이드를 수행 하는 단계

이러한 단계를 신중 하 게 수행 합니다. 프로덕션 인스턴스 전에 테스트 컴퓨터에서 업그레이드를 시도 하는 것이 좋습니다.

1. Yum 업데이트를 수행 하 여 최신 클라이언트 패키지를 가져옵니다.
    ```bash
    yum update -y
    ```

1. Leapp-client 패키지를 설치 합니다.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. repomap.csv에서 release.tar.gz 파일을 사용 하 고, [Redhat 포털](https://access.redhat.com/articles/3664871)에 있는를 pes-events.js하 고, 압축을 풉니다. 
    1. 파일을 다운로드 합니다.
    1. 다음 명령을 사용 하 여 콘텐츠를 추출 하 고 파일을 제거 합니다.
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. ' Leapp '의 ' 응답 ' 파일을 추가 합니다.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. ' Leapp ' 업그레이드를 수행 합니다.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade`명령이 성공적으로 완료 되 면 시스템을 수동으로 다시 부팅 하 여 프로세스를 완료 합니다. 시스템은 사용할 수 없게 되는 몇 번의 시간 동안 다시 부팅 됩니다. 직렬 콘솔을 사용 하 여 프로세스를 모니터링 합니다.

1.  업그레이드가 성공적으로 완료 되었는지 확인 합니다.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. 업그레이드가 완료 된 후 루트 ssh 액세스를 제거 합니다.
    1. `/etc/ssh/sshd_config` 파일을 엽니다.
    1. ' #PermitRootLogin 예 '를 검색 합니다.
    1. 주석에 ' # ' 추가

1. 변경 내용을 적용 하려면 sshd 서비스를 다시 시작 하십시오.
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>일반적인 문제
`leapp preupgrade`또는 프로세스가 실패할 수 있는 몇 가지 일반적인 인스턴스는 다음과 같습니다 `leapp upgrade` .

**오류: 다음 비활성화 된 플러그 인 패턴과 일치 하는 항목이 없습니다.**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**해결 방법**\
파일을 편집 하 고 사용을로 변경 하 여 구독 관리자 플러그 인 `/etc/yum/pluginconf.d/subscription-manager.conf` 을 비활성화 `enabled=0` 합니다.

이는 PAYG Vm에 사용 되지 않는 구독 관리자 yum 플러그 인을 사용 하도록 설정 하는 경우에 발생 합니다.

**오류: root를 사용 하 여 원격 로그인에 문제가 있을 수 있습니다.** 는 `leapp preupgrade` 다음 오류와 함께 실패할 수 있습니다.
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
**해결 방법**\
에서 루트 액세스를 사용 하도록 설정 `/etc/sshd_config` 합니다.
이는 `/etc/sshd_config` "[업그레이드 준비](#preparations-for-the-upgrade)" 섹션에 따라에서 루트 ssh 액세스를 사용 하도록 설정 하지 않은 경우에 발생 합니다. 

## <a name="next-steps"></a>다음 단계
* [Azure의 Red Hat 이미지](./redhat-images.md)에 대해 자세히 알아보세요.
* [Red Hat 업데이트 인프라](./redhat-rhui.md)에 대해 자세히 알아보세요.
* [RHEL BYOS 제품](./byos.md)에 대해 자세히 알아보세요.
* Red hat 전체 업그레이드 프로세스에 대 한 정보는 Red Hat 설명서에서 [RHEL 7에서 RHEL 8로 업그레이드](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) 를 참조 하세요.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
