---
title: 클라우드 이해-초기화
description: 클라우드 초기화를 사용 하 여 Azure VM을 프로 비전 하는 방법을 자세히 알아봅니다.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: f5028abadbe5600058c83a144d0095aee1278fe6
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042080"
---
# <a name="diving-deeper-into-cloud-init"></a>클라우드 초기화에 대해 자세히 알아보기
[클라우드 초기화](https://cloudinit.readthedocs.io/en/latest/index.html) 에 대 한 자세한 내용 또는 문제 해결에 대 한 자세한 내용은 작동 방식을 이해 해야 합니다. 이 문서에서는 중요 한 부분을 중점적으로 설명 하 고 Azure 세부 사항을 설명 합니다.

일반화 된 이미지에 클라우드 init가 포함 되어 있고 해당 이미지에서 VM을 만든 경우 초기 부팅 중에 구성을 처리 하 고 5 단계까지 실행 합니다. 이러한 단계는 클라우드 초기화가 구성을 적용 하는 지점에 대 한 정보를 표시 하므로 중요 합니다. 


## <a name="understand-cloud-init-configuration"></a>클라우드 초기화 구성 이해
VM을 구성 하는 것은 플랫폼에서 실행 되도록 VM을 구성 하는 것을 의미 합니다. 즉, 클라우드 init는 여러 구성을 적용 해야 함을 의미 하며, 상호 작용 하는 기본 구성은 `User data` (customData) 이며 여러 형식을 지원 합니다 .이에 대해서는 [여기](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats)에 설명 되어 있습니다. 추가 구성에 대해 스크립트 (/var/lib/cloud/scripts)를 추가 하 고 실행 하는 기능을 사용할 수도 있습니다 .이에 대해서는 아래에서 자세히 설명 합니다.

일부 구성은 이미 클라우드 init와 함께 제공 되는 Azure Marketplace 이미지로 구운 됩니다. 예를 들면 다음과 같습니다.

1. 클라우드 **데이터 원본** -클라우드-초기화는 클라우드 플랫폼과 상호 작용할 수 있는 코드를 포함 하며, 이러한 코드를 ' 데이터 원본 ' 이라고 합니다. [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)의 클라우드 init 이미지에서 vm을 만들 때 클라우드 초기화는 azure 메타 데이터를 로드 하며,이는 azure 메타 데이터 끝점과 상호 작용 하 여 vm 특정 구성을 가져옵니다.
2. **런타임 구성** (/run/cloud-init)
3. **이미지 구성** (/etc/cloud) (예:) `/etc/cloud/cloud.cfg` `/etc/cloud/cloud.cfg.d/*.cfg` . Azure에서이를 사용 하는 경우의 예입니다. 클라우드 init를 사용 하는 Linux OS 이미지에는 일반적으로 Azure datasource 지시어를 사용 하 여 클라우드 초기화에서 사용 해야 하는 데이터 원본을 지정 합니다. 이렇게 하면 클라우드 init 시간이 절감 됩니다.

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>클라우드-부팅 단계 초기화 (처리 구성)

클라우드 init를 사용 하 여 프로 비전 할 때 부팅의 5 단계 (구성 처리 및 로그에 표시 됨)가 있습니다.

1. [생성기 단계](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): 클라우드 초기화 systemd 생성기가 시작 되 고, 클라우드 init가 부팅 목표에 포함 되어야 하는지 확인 하 고, 클라우드 초기화를 사용 하도록 설정 합니다. 

2. [클라우드 초기화 로컬 단계](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): 여기서 클라우드 초기화는 로컬 "azure" 데이터 원본을 검색 합니다 .이를 통해 클라우드 Init가 Azure와 상호 작용 하 고 대체 (fallback)를 비롯 한 네트워킹 구성을 적용할 수 있습니다.

3. [클라우드-초기화 초기화 단계 (네트워크)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): 네트워킹이 온라인 상태 여야 하 고 NIC 및 경로 테이블 정보가 생성 됩니다. 이 단계에서/etc/cloud/cloud.cfg의에 나열 된 모듈은 `cloud_init_modules` 실행 됩니다. Azure의 VM이 탑재 되 고, 임시 디스크 형식이 지정 되 고, 호스트 이름이 다른 작업과 함께 설정 됩니다.

   다음 중 일부입니다 `cloud_init_modules` .
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   이 단계 후에 클라우드 초기화는 VM이 성공적으로 프로 비전 되었음을 Azure 플랫폼에 알립니다. 일부 모듈에서 오류가 발생 했을 수 있습니다. 일부 모듈 오류는 프로 비전 실패를 초래 합니다.

4. [클라우드 초기화 구성 단계](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config):이 단계에서 `cloud_config_modules` 정의 되 고/etc/cloud/cloud.cfg에 나열 된 모듈이 실행 됩니다.


5. [클라우드 초기화 최종 단계](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final):이 마지막 단계에서는 `cloud_final_modules` /etc/cloud/cloud.cfg에 나열 된의 모듈이 실행 됩니다. 패키지 설치 및 스크립트 실행 등 부팅 프로세스를 실행 하는 동안 늦게 실행 해야 하는 모듈 

   -   이 단계를 수행 하는 동안 다음과 같은 디렉터리에 스크립트를 배치 하 여 스크립트를 실행할 수 있습니다 `/var/lib/cloud/scripts` .
   - `per-boot`-이 디렉터리 내의 스크립트는 다시 부팅할 때마다 실행 됩니다.
   - `per-instance`-이 디렉터리 내의 스크립트는 새 인스턴스가 처음 부팅 될 때 실행 됩니다.
   - `per-once`-이 디렉터리 내의 스크립트는 한 번만 실행 됩니다.

## <a name="next-steps"></a>다음 단계

[클라우드 초기화 문제를 해결](cloud-init-troubleshooting.md)합니다.
