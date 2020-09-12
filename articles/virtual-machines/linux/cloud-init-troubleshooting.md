---
title: 클라우드 초기화를 사용 하 여 문제 해결
description: 클라우드 초기화를 사용 하 여 Azure VM 프로 비전 문제를 해결 합니다.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6412036e3f16e2efb3bbf6669f6a31e9dc6e3584
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434642"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>클라우드 초기화를 사용 하 여 VM 프로 비전 문제 해결

클라우드 init를 사용 하 여 프로 비전을 수행 하는 일반화 된 사용자 지정 이미지를 만든 경우 VM이 올바르게 생성 되지 않은 것을 발견 한 경우 사용자 지정 이미지의 문제를 해결 해야 합니다.

몇 가지 예, 프로 비전 관련 문제:
- VM은 40 분 동안 ' 만들기 '에서 중단 되 고 VM 만들기는 실패로 표시 됩니다.
- `CustomData` 처리 되지 않음
- 임시 디스크를 탑재 하지 못했습니다.
- 사용자가 만들어지지 않았거나 사용자 액세스 문제가 있는 경우
- 네트워킹이 올바르게 설정 되지 않았습니다.
- 스왑 파일 또는 파티션 오류

이 문서에서는 클라우드 초기화 문제를 해결 하는 방법을 단계별로 설명 합니다. 자세한 내용은 [클라우드-초기화 심층](./cloud-init-deep-dive.md)분석을 참조 하세요.

## <a name="step-1-test-the-deployment-without-customdata"></a>1 단계:를 사용 하지 않고 배포 테스트 `customData`

VM을 만들 때 클라우드 초기화는이를 수락 하 고,이를 `customData` 전달 합니다. 먼저 배포에 문제가 발생 하지 않는지 확인 해야 합니다. 구성을 전달 하지 않고 VM을 프로 비전 해 보세요. VM을 프로 비전 하지 못할 경우 다음 단계를 계속 진행 합니다. 전달 중인 구성이 적용 되지 않는 경우 [4 단계로]()이동 합니다. 

## <a name="step-2-review-image-requirements"></a>2 단계: 이미지 요구 사항 검토
VM 프로 비전 실패의 주요 원인은 OS 이미지가 Azure에서 실행 하기 위한 필수 조건을 충족 하지 않는다는 것입니다. Azure에서 프로 비전을 시도 하기 전에 이미지가 제대로 준비 되었는지 확인 합니다. 


다음 문서에서는 Azure에서 지원 되는 다양 한 linux 배포를 준비 하는 단계를 보여 줍니다.

- [CentOS 기반 배포](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar 컨테이너 Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 및 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [기타: 보증되지 않는 배포](create-upload-generic.md)

[지원 되는 azure cloud init 이미지](./using-cloud-init.md)의 경우 Linux 배포판에는 azure에서 이미지를 올바르게 프로 비전 하는 데 필요한 모든 패키지 및 구성이 이미 있습니다. 사용자 고유의 큐 레이트 이미지에서 VM을 만들 수 없는 경우 선택적으로 클라우드 init에 대해 이미 구성 된 지원 되는 Azure Marketplace 이미지를 사용해 보세요 `customData` . 가 `customData` Azure Marketplace 이미지를 사용 하 여 제대로 작동 하는 경우 큐 레이트 이미지에 문제가 있을 수 있습니다.

## <a name="step-3-collect--review-vm-logs"></a>3 단계: VM 로그를 수집 & 검토

VM을 프로 비전 하지 못하면 Azure는 20 분 동안 ' 만들기 ' 상태를 표시 한 다음 VM을 다시 부팅 하 고, 마지막으로 VM 배포를 실패로 표시 하기 전에 20 분 동안 기다린 후 마지막으로 `OSProvisioningTimedOut` 오류로 표시 합니다.

VM이 실행 되는 동안 VM의 로그가 있어야 프로 비전이 실패 한 이유를 파악할 수 있습니다.  VM을 프로 비전 하지 못한 이유를 이해 하려면 VM을 중지 하지 마십시오. VM을 계속 실행 합니다. 로그를 수집 하기 위해 실패 한 VM을 실행 중 상태로 유지 해야 합니다. 로그를 수집 하려면 다음 방법 중 하나를 사용 합니다.

- [직렬 콘솔](../troubleshooting/serial-console-grub-single-user-mode.md)

- VM을 만들기 전에 [부팅 진단을 사용 하도록 설정](./tutorial-monitor.md#enable-boot-diagnostics) 하 고 부팅 하는 동안이를 [확인](./tutorial-monitor.md#view-boot-diagnostics) 합니다.

- [AZ VM Repair를 실행](../troubleshooting/repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 하 여 OS 디스크를 연결 하 고 탑재 합니다. 그러면 다음 로그를 수집할 수 있습니다.
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
초기 문제 해결을 시작 하려면 클라우드 초기화 로그를 시작 하 고 오류가 발생 한 위치를 파악 한 다음, 다른 로그를 사용 하 여 심층 분석을 수행 하 고 추가 정보를 제공 합니다. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* 직렬/부팅 로그

모든 로그에서 "실패", "경고", "경고", "오류", "오류", "오류" 검색을 시작 합니다. 대/소문자 구분 검색을 무시 하도록 구성을 설정 하는 것이 좋습니다. 

> [!TIP]
> 사용자 지정 이미지의 문제를 해결 하는 경우 이미지 중에 사용자를 추가 하는 것을 고려해 야 합니다. 프로 비전이 관리 사용자를 설정 하지 못한 경우에도 OS에 로그인 할 수 있습니다.

## <a name="analyzing-the-logs"></a>로그 분석

각 클라우드 초기화 로그에서 찾을 내용에 대 한 자세한 내용은 다음과 같습니다.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

기본적으로 우선 순위가 debug 이상인 모든 클라우드 초기화 이벤트는에 기록 됩니다 `/var/log/cloud-init.log` . 이는 클라우드 초기화를 초기화 하는 동안 발생 한 모든 이벤트에 대 한 자세한 로그를 제공 합니다. 

다음은 그 예입니다. 

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


오류 또는 경고를 발견 한 후에는 클라우드-init 로그에서 뒤로 읽어 오류 또는 경고를 발생 하기 전에 클라우드 초기화가 시도 된 작업을 파악 합니다. 대부분의 경우 클라우드-초기화는 오류가 발생 하기 전에 OS 명령을 실행 하거나 프로 비전 작업을 수행 하 여 로그에 오류가 나타난 이유에 대 한 정보를 제공할 수 있습니다. 다음 예제에서는 클라우드 초기화가 오류가 발생 하기 직전에 장치를 탑재 하려고 시도 함을 보여 줍니다.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

[직렬 콘솔](../troubleshooting/serial-console-grub-single-user-mode.md)에 대 한 액세스 권한이 있는 경우 클라우드의 실행을 시도 하는 명령을 다시 실행할 수 있습니다.

`/var/log/cloud-init.log`/Etc/cloud/cloud.cfg.d/05_logging. cfg 내에서에 대 한 로깅을 다시 구성할 수도 있습니다. 클라우드 초기화 로깅에 대 한 자세한 내용은 [클라우드 초기화 설명서](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)를 참조 하세요. 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

`stdout` `stderr` [클라우드 초기화 단계](cloud-init-deep-dive.md)중 및에서 정보를 가져올 수 있습니다. 여기에는 일반적으로 `stdout` `stderr` 각 단계의 타임 스탬프를 비롯 하 여 클라우드 초기화의 각 단계와 라우팅 테이블 정보, 네트워킹 정보, ssh 호스트 키 확인 정보 등이 포함 됩니다. 원하는 경우 `stderr` 및 `stdout` 로깅을에서 다시 구성할 수 있습니다 `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>직렬/부팅 로그 

클라우드 초기화에는 여러 종속성이 있습니다. 이러한 종속성은 네트워킹, 저장소, ISO 탑재 기능, 임시 디스크 탑재 및 포맷 등 Azure의 이미지에 대 한 필수 구성 요소에 설명 되어 있습니다. 이러한 방법 중 하나를 실행 하면 오류가 발생 하 여 클라우드 init가 실패할 수 있습니다. 예를 들어 VM이 DHCP 임대를 가져올 수 없는 경우 클라우드 초기화가 실패 합니다.

클라우드 초기화를 프로 비전 하지 못한 이유를 여전히 격리할 수 없는 경우 클라우드 초기화 단계를 이해 하 고 모듈이 실행 될 때를 이해 해야 합니다. 자세한 내용은 [클라우드 초기화에](cloud-init-deep-dive.md) 대해 자세히 알아보기를 참조 하세요.


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>4 단계: 구성이 적용 되지 않는 이유 조사
클라우드 초기화의 모든 오류로 인해 심각한 프로 비전 오류가 발생 하는 것은 아닙니다. 예를 들어 `runcmd` 클라우드 init 구성에서 모듈을 사용 하는 경우 실행 중인 명령에서 0이 아닌 종료 코드가 발생 하면 VM 프로 비전이 실패 합니다. 이는 클라우드 초기화의 처음 3 단계에서 발생 하는 코어 프로 비전 기능 후에 실행 되기 때문입니다. 구성이 적용 되지 않는 이유를 해결 하려면 3 단계에서 로그를 검토 하 고 클라우드 초기화 모듈을 수동으로 검토 합니다. 다음은 그 예입니다. 

- `runcmd` -스크립트가 오류 없이 실행 되나요? 터미널에서 구성을 수동으로 실행 하 여 예상 대로 실행 되는지 확인 합니다.
- 패키지를 설치 하는 중-VM이 패키지 리포지토리에 액세스할 수 있나요?
- `customData`VM에 제공 된 데이터 구성도 확인 해야 합니다 .이는에 있습니다 `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>다음 단계

클라우드 초기화가 구성을 실행 하지 않은 이유를 여전히 격리할 수 없는 경우 각 클라우드 초기화 단계에서 발생 하는 작업과 모듈이 실행 되는 시기를 자세히 확인 해야 합니다. 자세한 내용은 [클라우드 초기화 구성에 대해 자세히 알아보기](./cloud-init-deep-dive.md) 를 참조 하세요. 
