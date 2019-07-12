---
title: Azure 마이그레이션 서버 마이그레이션을 사용 하 여 Azure로 마이그레이션에 대 한 Linux를 실행 하는 VMware Vm 준비 | Microsoft Docs
description: Azure 마이그레이션 서버 마이그레이션을 사용 하 여 Azure로의 마이그레이션에 대 한 Linux VM을 준비 하는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811792"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Linux VMware Vm을 Azure로 마이그레이션하기 위한 준비 

이 아티클에서 Azure로 마이그레이션할 때 Linux를 실행 하는 VMware Vm을 준비 하는 방법을 [Azure Migrate](migrate-overview.md)합니다. 

> [!NOTE]
> Azure 마이그레이션 서버 마이그레이션 현재 공개 미리 보기 중입니다. Azure Migrate의 기존 GA 버전을 사용 하 여 검색 하 고 마이그레이션에 대 한 Vm을 평가할 수 있지만 실제 마이그레이션을 기존 GA 버전에서 지원 되지 않습니다.

Linux 컴퓨터를 다음과 같이 준비 합니다.

1. Hyper-v Linux 통합 서비스를 설치 합니다. 최신 버전 Linux 배포판의 있을 기본적으로 설치).
2. 필요한 Hyper-v 드라이버가 포함 되도록 및 (일부 배포에 필요) 하는 Azure에서 VM 부팅 됩니다 있도록 Linux init 이미지를 다시 작성 합니다.
3. 문제 해결에 대 한 직렬 콘솔 로깅을 사용 하도록 설정 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console).
4. 영구 장치 식별자를 사용 하 여 볼륨 연결 장치 이름과 장치 맵 파일을 업데이트 합니다.
5. 영구적 볼륨 식별자를 사용 하도록 fstab 항목을 업데이트 합니다.
6. MAC 주소 등을 기준으로 하는 인터페이스 이름을 예약 하는 모든 udev 규칙을 제거 합니다.
7. 네트워크 인터페이스 업데이트 DHCP IP 주소를 수신 합니다.
8. 확인 ssh 사용 가능 합니다. Sshd 서비스를 다시 부팅할 때 자동으로 시작 되도록 설정 되어 있는지 확인 합니다.
9. 들어오는 ssh 연결 요청 되지 차단한 운영 체제 방화벽 또는 IP 테이블 규칙을 확인 합니다.

[자세한](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) 가장 인기 있는 Linux 배포판에서 이러한 변경에 대 한 합니다.

## <a name="sample-script"></a>샘플 스크립트

이 스크립트 (준비-에-azure.sh)는 Linux 컴퓨터를 준비 하는 것에 대 한 샘플을 제공 합니다. 모든 배포 및 환경에 대 한 스크립트가 작동 하지 않을 수 있습니다 이지만 쉽게 시작할 수 있습니다.

스크립트 표시 하는 방법. 

- 필요한 경우 필요한 드라이버를 사용 하 여 Linux init 이미지를 다시 생성 합니다.
- 영구적 볼륨 식별자를 사용 하도록 fstab 항목을 업데이트 합니다.
- 콘솔 로그를 직렬 포트로 리디렉션하십시오.
- 직렬 액세스 Azure 콘솔을 사용 하도록 설정
- Net udev 규칙을 제거
- VM이 부팅할 때 실행 되는 부팅 스크립트 실행을 삽입 합니다. 컴퓨터가 Azure에서 실행 되 고 있는지 확인 합니다. 인 경우 VM에서 네트워크 구성을 업데이트 하 고 DHCP IP 주소를 획득 하는 데 첫 번째 이더넷 인터페이스를 설정 합니다.

### <a name="before-you-start"></a>시작하기 전에

- 샘플 스크립트는 샘플 단계를 포함합니다. 프로덕션 시스템에서 실행 하면 안됩니다. 손상 또는 실행 되는 VM을 손상 시킬 수 있습니다.
- 테스트 VM에서 실행 하는 것이 좋습니다. 시작 하기 전에 수행할 VM 백업 또는 스냅숏이 필요한 경우 VM을 복원할 수 있도록 합니다. 
- 스크립트는 VM이 Linux 배포판 중 하나를 실행 하는 경우에 작동 합니다.
    - Red Hat Enterprise Linux 6.5+, 7.1+
    - Cent OS 6.5+, 7.1+
    - SUSE Linux Enterprise Server 12 SP1,SP2, SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>스크립트 실행

1. Sftp 또는 FileZilla 또는 WinScp와 같은 scp 클라이언트를 사용 하 여 VM을 테스트 하는 Linux 스크립트를 복사 합니다.
2. 관리자 계정을 사용 하 여 Linux 컴퓨터에 SSH 합니다.
3. 스크립트 디렉터리로 이동 합니다.
4. 스크립트를 실행 하도록 실행 **sudo chmod 777 준비-에-azure.sh**합니다.
5. 사용 하 여 스크립트 실행 **./prepare-for-azure.sh**합니다.

스크립트를 실행 하는 방법을 다음과 같습니다.

![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script1.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script2.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script3.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script5.png)
![Linux 스크립트 ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script7.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script8.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script10.png)
![Linux 스크립트 ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script12.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script13.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script15.png)
![Linux 스크립트 ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script17.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script18.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script20.png)
![Linux 스크립트 ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script22.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script23.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script24.png)
![Linux 스크립트](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>다음 단계

- [컴퓨터 종속성 매핑](how-to-create-group-machine-dependencies.md)을 사용하여 높은 신뢰도 그룹을 만드는 방법을 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
