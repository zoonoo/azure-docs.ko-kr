---
title: Azure(큰 인스턴스)의 SAP HANA에서 HANA 백업 및 복원 | Microsoft Docs
description: Azure(큰 인스턴스)의 SAP HANA에서 HANA 백업 및 복원을 수행하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987908"
---
# <a name="backup-and-restore"></a>Backup 및 복원

>[!IMPORTANT]
>이 문서에서는 SAP HANA 관리 설명서 또는 SAP 노트를 대체 하지 않습니다. SAP HANA 관리 및 작업, 특히 백업, 복원, 고가용성 및 재해 복구에 대 한 전문을 확실 하 게 이해 해야 하는 것이 예정입니다. 이 문서에서는 SAP HANA Studio의 스크린샷이 나와 있습니다. SAP 관리 도구 스크린의 콘텐츠, 구조 및 특성과 도구 자체는 릴리스할 SAP HANA 릴리스마다 변경될 수 있습니다.

사용자 환경에서 수행되는 단계와 프로세스를 해당 HANA 버전 및 릴리스로 연습하는 것이 중요합니다. 이 문서에 설명 된 일부 프로세스 이해에 대 한 간소화 됩니다. 최종 운영 핸드북의 자세한 단계로 사용 될 하려는 되지 않습니다. 구성에 대 한 작업 핸드북을 만들려는 경우 테스트 및 프로세스를 실행 하 고 특정 구성과 관련 된 프로세스를 문서화 합니다. 

운영 데이터베이스의 가장 중요 한 측면 중 하나 치명적인 이벤트 로부터 보호 하는 것입니다. 이러한 이벤트의 원인은 자연 재해에서 단순한 사용자 오류에 이르기까지 다양합니다.

중단 전의 복원 시점에 시간 내에 삭제 된 중요 한 데이터를 가장 하는 방식과 최대한 가까운 상태로 복원할 수 있도록 사람이 하기 전에 같은 기능을 사용 하 여 데이터베이스를 백업 합니다.

두 가지 백업 복원 하는 기능을 달성 하기 위해 수행 해야 합니다.

- 데이터베이스 백업: 전체, 증분 또는 차등 백업
- 트랜잭션 로그 백업

애플리케이션 수준에서 수행되는 전체 데이터베이스 백업 외에도 스토리지 스냅샷으로 백업을 수행할 수 있습니다. 저장소 스냅숏은 트랜잭션 로그 백업을 대체 하지 않습니다. 트랜잭션 로그 백업은 데이터베이스를 특정 시점으로 복원하거나 이미 커밋된 트랜잭션에서 로그를 비우는 데 중요합니다. 저장소 스냅숏은 데이터베이스의 롤포워드 이미지를 신속 하 게 제공 하 여 복구를 가속화할 수 있습니다. 

Azure(큰 인스턴스)의 SAP HANA는 두 가지 백업 및 복원 옵션을 제공합니다.

- **그렇게 직접 (DIY).** 디스크 공간이 있는지를 변경한 후 다음 디스크 백업 메서드 중 하나를 사용 하 여 전체 데이터베이스 및 로그 백업을 수행 합니다. Azure 가상 머신 (VM)에 설정 된 NFS 공유 또는 HANA 큰 인스턴스 단위에 연결 하는 볼륨에 직접 컴퓨터를 백업할 수 있습니다. 후자의 경우 고객은 Azure에서 Linux VM을 설정하고, VM에 Azure Storage를 연결하고, 해당 VM에 구성된 NFS 서버를 통해 스토리지를 공유합니다. HANA 큰 인스턴스 단위에 직접 연결 된 볼륨에 대 한 backup을 수행 하는 경우 Azure storage 계정에 백업을 복사 합니다. Azure Storage를 기반으로 하는 NFS 공유를 내보내는 Azure VM을 설정한 후이 작업을 수행 합니다. 또한 Azure Backup vault 또는 Azure 콜드 저장소를 사용할 수 있습니다. 

   Azure storage 계정에 복사 될 후 백업을 저장할 타사 데이터 보호 도구를 사용 하는 방법도 있습니다. DIY 백업 옵션은 또한 해야 할 수도 있습니다 오랜 규정 준수 및 감사 목적에 대 한 시간에 대 한 저장 해야 하는 데이터에 대 한 합니다. 모든 경우에 백업은 VM 및 Azure Storage를 통해 표시되는 NFS 공유로 복사됩니다.

- **인프라 백업 및 복원 기능입니다.** 또한 백업을 사용 하 여를 SAP HANA on Azure (큰 인스턴스)의 기본 인프라를 제공 하는 기능을 복원 합니다. 이 옵션은 백업 및 빠른 복원에 대한 필요를 충족합니다. 이 섹션의 나머지 부분에서는 HANA 큰 인스턴스와 함께 제공되는 백업 및 복원 기능을 설명합니다. 이 섹션에서는 관계 백업 및 복원 하는 재해 복구 기능은 HANA 큰 인스턴스에서 제공 합니다.

> [!NOTE]
>   HANA 큰 인스턴스의 기본 인프라에서 사용 되는 스냅숏 기술은 SAP HANA 스냅숏에 대 한 종속성을 갖습니다. 이 시점에서 SAP HANA 스냅숏은 SAP HANA 다중 테 넌 트 데이터베이스 컨테이너의 다중 테 넌 트와 함께에서 작동 하지 않습니다. 하나의 테 넌 트가 배포 된 경우 SAP HANA 스냅숏이 작동 하 고이 메서드를 사용할 수 있습니다.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (큰 인스턴스)의 저장소 스냅숏을 사용 하 여

Azure(큰 인스턴스)의 SAP HANA에 기반한 저장소 인프라는 볼륨의 저장소 스냅샷을 지원합니다. 볼륨의 백업 및 복원은 다음 사항을 고려하여 지원됩니다.

- 전체 데이터베이스 백업 대신 저장소 볼륨 스냅샷을 자주 사용합니다.
- /Hana/data 통해 스냅숏으로 트리거되고 /hana/shared /usr/sap, 볼륨, 스냅숏 기술은 포함 하는 시작 하기 전에 스냅숏 SAP HANA 저장소 스냅숏을 실행 됩니다. 이 SAP HANA 스냅샷은 저장소 스냅샷을 복구한 후에 최종 로그 복원에 대한 설치 지점입니다. 성공 하는 HANA 스냅숏의 경우 HANA 인스턴스를 활성화 해야 합니다. HSR 시나리오에서는 저장소 스냅숏에 HANA 스냅숏을 수행할 수 없습니다는 현재 보조 노드에서 지원 되지 않습니다.
- 저장소 스냅숏이 성공적으로 실행 된 후에 SAP HANA 스냅숏이 삭제 됩니다.
- 트랜잭션 로그 백업은 자주 사용되며 /hana/logbackups 볼륨 또는 Azure에 저장됩니다. 트랜잭션 로그 백업을 포함하는 /hana/logbackups 볼륨을 트리거하여 별도의 스냅샷을 생성할 수 있습니다. 이런 경우는 HANA 스냅숏을 실행할 필요가 없습니다.
- 프로덕션 중단에 대 한 시간 내에 특정 시점으로 데이터베이스를 복원 해야 하는 경우 요청 Azure 복원에는 Microsoft Azure 지원 또는 SAP HANA를 특정 저장소 스냅숏으로 합니다. 예를 들어 샌드박스 시스템을 원래 상태로 복원하는 계획이 있습니다.
- SAP HANA 스냅숏은 저장소 스냅숏이 포함 된는 실행 하는 저장소 스냅숏을 만든 후 저장 된 트랜잭션 로그 백업 적용에 대 한 오프셋된 지점입니다.
- 이러한 트랜잭션 로그 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원합니다.

세 가지 볼륨 클래스를 대상으로 하는 저장소 스냅숏을 수행할 수 있습니다.

- /Usr/sap을 포함 하는 hana/data 및 공유에 대해 결합 된 스냅숏. 이 스냅샷을 만들려면 저장소 스냅샷에 대한 준비로 SAP HANA 스냅샷을 만들어야 합니다. SAP HANA 스냅숏은 데이터베이스가 저장소의 관점에서 일관 된 상태로 하는지 확인 합니다. 복원 프로세스를 설정 하기 위해 지점입니다.
- /hana/logbackups에 대한 별도 스냅샷.
- 운영 체제 파티션

최신 스냅숏 스크립트 및 설명서를 참조 하세요 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)합니다. 스냅숏 스크립트 패키지를 다운로드 하는 경우 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), 세 개의 파일을 가져옵니다. 파일 중 하나는 PDF에서 제공 하는 기능에 설명 되어 있습니다. 도구 집합을 다운로드 한 후 지침에서 "스냅숏 도구 얻기."

## <a name="storage-snapshot-considerations"></a>저장소 스냅샷 고려 사항

>[!NOTE]
>저장소 스냅숏은 HANA 큰 인스턴스 단위에 할당 된 저장소 공간을 사용 합니다. 저장소 스냅숏을 유지할 저장소 스냅숏의 수를 예약 하는 다음과 같은 측면을 고려해 야 합니다. 

Azure(큰 인스턴스)의 SAP HANA에 대한 저장소 스냅샷의 특정 역학에는 다음이 포함됩니다.

- 만들어진 시점에서 특정 저장소 스냅숏을 작은 저장소를 사용 합니다.
- 데이터 콘텐츠 변경 내용 및 SAP HANA 데이터 저장소 볼륨에서 변경 내용이 파일의에서 콘텐츠는 스냅숏은 원래 블록 콘텐츠 및 데이터 변경 내용을 저장 해야 합니다.
- 결과적으로 저장소 스냅샷의 크기가 증가합니다. 스냅샷이 오래 존재할수록 저장소 스냅샷은 커집니다.
- 저장소 스냅샷의 수명 동안 SAP HANA 데이터베이스 볼륨에 변경 내용이 많을수록 저장소 스냅샷이 소진하는 공간이 커집니다.

Azure(큰 인스턴스)의 SAP HANA는 SAP HANA 데이터 및 로그 볼륨에 대해 고정된 볼륨 크기로 제공됩니다. 이러한 볼륨의 스냅샷을 수행하면 볼륨 공간이 줄어듭니다. 그러려면 다음 작업을 수행해야 합니다.

- 저장소 스냅숏을 예약할 시기를 결정 합니다.
- 저장소 볼륨의 공간 사용량을 모니터링 합니다. 
- 저장 되는 스냅숏의 수를 관리 합니다. 

대량의 데이터를 가져오거나 HANA 데이터베이스에 대해 다른 중요한 변경을 수행할 때 저장소 스냅샷을 사용하지 않도록 설정할 수 있습니다. 


다음 섹션에서는 이러한 스냅숏을 수행 하는 것에 대 한 정보를 제공 및 일반 권장 사항을 포함 합니다.

- 하드웨어가 볼륨당 255 개의 스냅숏을 유지할 수 있습니다, 있지만이 숫자 보다 훨씬 유지 하려고 합니다. 250 이하의 하는 것이 좋습니다.
- 스냅샷 저장소를 수행하기 전에 여유 공간을 모니터링하고 추적합니다.
- 사용 가능한 공간에 따라 저장소 스냅샷의 수를 줄입니다. 보관할 스냅샷의 수를 줄이거나 볼륨을 확장할 수 있습니다. 추가 저장소는 1테라바이트 단위로 주문할 수 있습니다.
- SAP 플랫폼 마이그레이션 도구(R3load)를 사용하여 데이터를 SAP HANA로 이동하거나 백업에서 SAP HANA에 데이터를 복원하는 것과 같은 작업 중에는 /hana/data 볼륨에 대해 저장소 스냅샷을 사용하지 않도록 설정합니다. 
- SAP HANA 테이블을 크게 재구성 하는 동안 저장소 스냅숏을 가능 하면 방지 합니다.
- 저장소 스냅샷은 Azure(대규모 인스턴스)에서 SAP HANA의 재해 복구 기능을 활용하기 위한 필수 구성 요소입니다.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>셀프 서비스 저장소 스냅샷을 사용하기 위한 필수 구성 요소

스냅숏 스크립트가 성공적으로 실행 되도록 하려면 Perl이 HANA 큰 인스턴스 서버의 Linux 운영 체제에 설치 되어 있는지를 확인 합니다. Perl은 HANA 큰 인스턴스 단위에 미리 설치 됩니다. Perl 버전을 확인하려면 다음 명령을 사용합니다.

`perl -v`

![이 명령을 실행하면 공개 키가 복사됩니다.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>저장소 스냅샷 설정

HANA 큰 인스턴스를 사용 하 여 저장소 스냅숏을 설정 하려면 다음이 단계를 수행 합니다.
1. Perl이 HANA 큰 인스턴스 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다.
1. /etc/ssh/ssh\_config를 수정하여 _MACs hmac-sha1_ 줄을 추가합니다.
1. 해당 하는 경우를 실행 하면 SAP HANA 인스턴스마다 마스터 노드에서 SAP HANA 백업 사용자 계정을 만듭니다.
1. 모든 SAP HANA 큰 인스턴스 서버에 SAP HANA HDB 클라이언트를 설치합니다.
1. 각 지역의 첫 번째 SAP HANA 큰 인스턴스 서버에서 스냅샷 생성을 제어하는 기본 저장소 인프라에 액세스하기 위한 공개 키를 만듭니다.
1. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)의 스크립트 및 구성 파일을 SAP HANA 설치의 **hdbsql** 위치에 복사합니다.
1. 적절한 고객 사양에 필요한 대로 *HANABackupDetails.txt* 파일을 수정합니다.

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)에서 최신 스냅숏 스크립트와 설명서를 다운로드하세요. 이전에 나열 된 단계를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

### <a name="consideration-for-mcod-scenarios"></a>MCOD 시나리오에 대한 고려 사항
실행 하는 경우는 [MCOD 시나리오](https://launchpad.support.sap.com/#/notes/1681092) 하나의 HANA 큰 인스턴스 단위에 여러 SAP HANA 인스턴스를 사용 하 여 별도 저장소 볼륨이 각 SAP HANA 인스턴스에 대 한 프로 비전 해야 합니다. MDC 및 기타 고려 사항에 대 한 자세한 내용은 "기억해 야 할 중요 한"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1단계: SAP HANA HDB 클라이언트 설치

SAP HANA on Azure (큰 인스턴스)에 설치 된 Linux 운영 체제 폴더와 복구를 위해 백업 및 재해에 대 한 SAP HANA 저장소 스냅숏을 실행 하는 데 필요한 스크립트를 포함 합니다. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)에서 최신 릴리스를 확인하세요. 스크립트의 최신 릴리스 버전 4.0입니다. 각 스크립트마다 동일한 주 릴리스 내의 다른 부 릴리스를 가질 수 있습니다.

SAP HANA를 설치 하는 동안 HANA 큰 인스턴스 단위에 SAP HANA HDB 클라이언트를 설치 해야 하는 것입니다.

### <a name="step-2-change-the-etcsshsshconfig"></a>2단계: /etc/ssh/ssh\_config 변경

이 단계에서 "저장소와의 통신 사용"에 설명 되어 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.


### <a name="step-3-create-a-public-key"></a>3단계: 공개 키 만들기

HANA 큰 인스턴스 테 넌 트의 저장소 스냅숏 인터페이스에 대 한 액세스를 사용 하도록 설정 하려면 공개 키를 통한 로그인 프로시저를 설정 합니다. 

테 넌 트의 Azure (큰 인스턴스) 서버에서 첫 번째 SAP hana, 저장소 인프라에 액세스 하기 위한 공개 키를 만듭니다. 공개 키를 사용 하 여 암호 저장소 스냅숏 인터페이스에 로그인 할 필요는 없습니다. 또한 공개 키를 사용 하 여 암호 자격 증명을 유지 관리할 필요가 없습니다. 

공개 키를 생성 하려면에서 "저장소와의 통신 사용"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.


### <a name="step-4-create-an-sap-hana-user-account"></a>4단계: SAP HANA 사용자 계정 만들기

SAP HANA 스냅숏 만들기를 시작 하려면 SAP hana 저장소 스냅숏 스크립트에서 사용할 수 있는 사용자 계정을 만듭니다. 이를 위해 SAP HANA Studio 내에 SAP HANA 사용자 계정을 만듭니다. 아닌 SYSTEMDB 아래에 사용자를 만들어야 합니다 하 고 *되지* MDC에 대 한 SID 데이터베이스입니다. 단일 컨테이너 환경에서 사용자는 테 넌 트 데이터베이스에 생성 됩니다. 이 계정에 있어야 **백업 관리** 하 고 **카탈로그 읽기** 권한. 

설정 하 고 사용자 계정을 사용 하 여, "SAP HANA와의 통신 사용"의 참조 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)합니다.


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5단계: SAP HANA 사용자 계정 권한 부여

이 단계에서는 스크립트 런타임 시 암호를 제출 하지 않아도 되도록 사용자가 만든 SAP HANA 사용자 계정에 권한을 부여 합니다. SAP HANA 명령 `hdbuserstore` 는 SAP HANA 사용자 키를 만들 수 있습니다. 키를 하나 이상의 SAP HANA 노드에 저장 됩니다. 사용자 키를 사용하면 스크립팅 프로세스 내에서 암호를 관리하지 않고도 SAP HANA에 액세스할 수 있습니다. 스크립팅 프로세스는 이 아티클의 뒤에서 설명하겠습니다.

>[!IMPORTANT]
>스냅숏 명령을에서 실행 되는 동일한 사용자 컨텍스트를 사용 하 여 이러한 구성 명령을 실행 합니다. 그렇지 않은 경우 스냅숏 명령을 제대로 작동 하지 않습니다.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6단계: 스냅샷 스크립트 가져오기, 스냅샷 구성, 구성 및 연결 테스트

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)에서 최신 버전의 스크립트를 다운로드하십시오. 스크립트의 4.0 릴리스를 사용 하 여 스크립트 설치 방법을 변경 합니다. 자세한 내용은 "SAP HANA와의 통신 사용"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

명령의 정확한 순서에 대 한에서 "스냅숏 도구 (기본값)를 쉽게 설치"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 기본 설치를 사용 하 여를 사용 하는 것이 좋습니다. 

버전에서 업그레이드 하려면 3.x를 4.0으로, "기존 설치 업그레이드"의 참조 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 4.0 도구 집합을 제거 하려면에서 "스냅숏 도구의 제거"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

"스냅숏 도구의 전체 setup"에 설명 된 단계를 실행 하려면 반드시 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

"이러한 스냅숏 도구 이란?"에 설명가 설치 될 때와 다른 스크립트 및 파일의 용도 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

스냅숏 도구를 구성 하기 전에 구성 되었는지 확인 하면 또한 HANA 백업 위치 및 설정을 올바르게 합니다. 자세한 내용은 "SAP HANA 구성"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

구성 스냅숏 도구 집합에 있는 "구성 파일-HANABackupCustomerDetails.txt"에 설명 되어 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

#### <a name="test-connectivity-with-sap-hana"></a>SAP HANA를 사용 하 여 연결 테스트

모든 구성 데이터를 *HANABackupCustomerDetails.txt* 파일에 추가한 후에 HANA 인스턴스 데이터에 대한 구성이 올바른지 확인합니다. `testHANAConnection` 스크립트를 사용합니다. 이것은 SAP HANA 강화 또는 스케일 아웃 구성과 별개입니다.

자세한 내용은 "testHANAConnection-SAP HANA와의 연결 확인"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

#### <a name="test-storage-connectivity"></a>저장소 연결 테스트

다음 테스트 단계에 추가한 데이터를 기반으로 저장소에 대 한 연결을 확인 하는 것은 *HANABackupCustomerDetails.txt* 구성 파일입니다. 다음 테스트 스냅숏으로 실행 합니다. 실행 하기 전에 `azure_hana_backup` 명령,이 테스트를 실행 해야 합니다. 이 테스트에 대 한 명령의 시퀀스 "저장소-testStorageSnapshotConnection와의 연결 확인"을 참조 하세요. "에서 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

저장소 가상 머신 인터페이스에 로그인이 성공하면 스크립트는 2단계로 진행되고 테스트 스냅샷을 만듭니다. SAP HANA의 3 노드 스케일 아웃 구성에 대 한 출력은 다음과 같습니다.

스크립트를 사용 하 여 테스트 스냅숏을 성공적으로 실행 하는 경우 실제 저장소 스냅숏을 예약할 수 있습니다. 성공적인 없으면 앞으로 이동 하기 전에 문제를 조사 합니다. 테스트 스냅샷은 첫 번째 실제 스냅샷이 완료될 때까지 유지되어야 합니다.


### <a name="step-7-perform-snapshots"></a>7단계: 스냅샷 수행

준비 단계가 완료 되 면 구성 하 고 실제 저장소 스냅숏을 예약할 시작할 수 있습니다. 예약할 스크립트는 SAP HANA 강화 및 규모 확장 구성에 작동합니다. 백업 스크립트의 주기적인 일반 실행의 경우 cron 유틸리티를 사용하여 스크립트를 예약합니다. 

정확한 명령 구문 및 기능을에 "수행 스냅숏 백업-azure_hana_backup"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 

때 스크립트 `azure_hana_backup` 실행 세 단계는 다음과 같은 스냅숏 저장소를 만듭니다.

1. SAP HANA 스냅숏을 실행합니다.
1. 저장소 스냅숏을 실행합니다.
1. 저장소 스냅숏을 실행 하기 전에 만든 SAP HANA 스냅숏을 제거 합니다.

스크립트를 실행 하려면 복사 된 HDB 실행 파일 폴더에서 호출 합니다. 

보존 기간은 스크립트를 실행할 때 매개 변수로 제출 되는 스냅숏 수를 사용 하 여 관리 됩니다. 저장소 스냅숏을 보증 하는 시간에는 스크립트를 실행할 때 매개 변수로 제출 된 스냅숏 개수 및 실행 기간이 함수. 

유지 되는 스냅숏 개수는 스크립트의 호출에서 매개 변수로 이름이 지정 된 수를 초과 하면 새 스냅숏을 실행 하기 전에 동일한 레이블의 가장 오래 된 저장소 스냅숏이 삭제 됩니다. 호출의 마지막 매개 변수로 지정한 숫자는 유지되는 스냅샷 개수를 제어하는 데 사용할 수 있는 숫자입니다. 이 번호를 사용 하 여 수도 제어할 수 있습니다 하지 직접 스냅숏에 대해 사용 되는 디스크 공간입니다. 


## <a name="snapshot-strategies"></a>스냅샷 전략
다른 유형에 대한 스냅샷 빈도는 HANA 대규모 인스턴스 재해 복구 기능을 사용하는지 여부에 따라 달라집니다. 이 기능은 저장소 스냅샷을 사용합니다. 그러려면 저장소 스냅샷의 빈도 및 실행 기간에 대한 특별한 권장 사항이 필요할 수 있습니다. 

다음 고려 사항 및 권장 사항에서는 HANA 대규모 인스턴스가 제공하는 재해 복구 기능을 사용하지 *않는다*고 가정합니다. 대신, 저장소 스냅샷을 사용하여 백업을 보유하고 지난 30일 동안의 지정 시간 복구를 제공할 수 있습니다. 스냅숏과 공간 개수 제한인 지정 되 면 다음 요구 사항을 것이 좋습니다.

- 지정 시간 복구에 대한 복구 시간.
- 사용된 공간.
- 재해로부터 잠재적 복구를 위한 복구 지점 및 복구 시간 목표.
- 디스크에 대한 HANA 전체 데이터베이스 백업의 최종 실행. 디스크 또는 **backint** 인터페이스에 대한 전체 데이터베이스 백업이 수행될 때마다 저장소 스냅샷의 실행에 실패합니다. 저장소 스냅숏 기반으로 전체 데이터베이스 백업을 실행 하려면 확인 하려는 경우 저장소 스냅숏의 실행이이 시간 동안 비활성화 됩니다.
- 250으로 제한 되는 볼륨당 스냅숏 수입니다.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

HANA 큰 인스턴스의 재해 복구 기능을 사용 하지 않는 경우 스냅숏 기간은 덜 빈번 합니다. 이러한 경우 /hana/data, /hana/shared /usr/sap 12 시간제 또는 24 시간 기간에 포함 하는 결합 된 스냅숏을 수행 합니다. 한 달 동안 해당 스냅숏을 유지 합니다. 로그 백업 볼륨의 스냅숏에 대해서도 마찬가지입니다. 로그 백업 볼륨에 대 한 SAP HANA 트랜잭션 로그 백업 실행 15 분 기간으로 5 분 내에 발생합니다.

예약된 저장소 스냅샷은 cron를 사용하여 수행하는 것이 가장 좋습니다. 모든 백업 및 재해 복구 요구 사항에 대 한 동일한 스크립트를 사용 합니다. 요청된 다양한 백업 시간에 맞게 스크립트 입력을 수정합니다. 이러한 스냅숏은 모두 다르게 예약 됩니다 해당 실행 시간에 따라 cron에서. 매일 12 시간 마다 또는 매주 매시간 수 있습니다. 

다음 예제에서는 /etc/crontab의 cron 일정을 보여 줍니다.
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
이전 예제에서는 시간별로 결합된 스냅숏이 /hana/data 및 /usr/sap에 위치를 포함 하는 /hana/shared/SID 포함 하는 볼륨을 처리 합니다. 지난 2일 내에 더 빠른 지정 시간 복구에 이 형식의 스냅샷을 사용합니다. 이러한 볼륨의 스냅숏을 매일 이기도 합니다. 따라서 경우 2 일 간의 시간별 스냅숏과 4 주 간의 일별 스냅숏을 트랜잭션 로그 백업 볼륨도은 매일 백업 합니다. 이러한 백업은 4 주 동안 유지 됩니다. 

Crontab의 세 번째 줄에서 볼 수 있듯이 HANA 트랜잭션 로그의 백업은 5 분 마다 실행 되도록 예약 됩니다. 저장소 스냅숏을 실행 하는 여러 다른 cron 작업의 시작 시간이 지연 됩니다. 이러한 방식으로 스냅숏을 실행 되지 않습니다 한 번에 특정 시점으로. 

다음 예제에서는 /hana/data 및 /hana/shared/SID /usr/sap을 포함 하는 시간 단위로 위치를 포함 하는 볼륨을 포괄 하는 결합된 된 스냅숏을 수행 합니다. 이 스냅샷은 이틀 동안 보관합니다. 트랜잭션 로그 백업 볼륨의 스냅숏은 5 분 단위로 실행 하 고 4 시간 동안 유지 됩니다. 으로 5 분 마다 실행 되도록 하기 전에, HANA 트랜잭션 로그 파일의 백업을 예약 됩니다. 

트랜잭션 로그 백업 볼륨의 스냅샷은 트랜잭션 로그 백업이 시작된 후 2분 지연되어 수행됩니다. 정상적인 상황에서는 이러한 2 분 내 SAP HANA 트랜잭션 로그 백업이 완료 됩니다. 이전과 마찬가지로, 부팅 LUN이 포함된 볼륨은 저장소 스냅샷에 의해 하루 1번 백업되고 4주 동안 보관됩니다.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

다음 그림은 시퀀스를 앞의 예제를 보여 줍니다. 부팅 LUN 제외 됩니다.

![백업과 스냅샷 간의 관계](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA는 /hana/log 볼륨에 대해 정기적인 쓰기를 수행하여 데이터베이스에 대한 커밋된 변경 내용을 문서화합니다. 정기적으로 SAP HANA는 /hana/data 볼륨에 저장점을 씁니다. Crontab에 지정 된 대로 SAP HANA 트랜잭션 로그 백업은 5 분 마다 실행 됩니다. 

또한 SAP HANA 스냅숏을 매시간 조합된 된 저장소 /hana/data 및 /hana/shared/SID 볼륨 스냅숏을 트리거한 결과로 실행 되는 것이 표시 됩니다. HANA 스냅숏이 성공한 후 결합 된 저장소 스냅숏을 실행 합니다. Crontab에 설명 된 대로 /hana/logbackup 볼륨의 저장소 스냅숏은 HANA 트랜잭션 로그 백업 후 약 2 분에 5 분 마다 실행 됩니다.

> 

>[!IMPORTANT]
> SAP HANA 트랜잭션 백업에 대한 저장소 스냅샷 사용은 스냅샷이 SAP HANA 로그 백업과 함께 수행된 경우에만 유용합니다. 이러한 트랜잭션 로그 백업은 저장소 스냅샷 사이의 기간에 적용되어야 합니다. 

30일이라는 지정 시간 복구의 사용자에 대한 커밋을 설정한 경우 다음을 수행해야 합니다.

- 지난 /hana/data 및 30 일 이전 극단적인 경우에는 /hana/shared/SID 결합된 된 저장소 스냅숏을 액세스 합니다. 
- 결합된 저장소 스냅샷 사이의 시간에 해당하는 인접한 트랜잭션 로그 백업이 있어야 합니다. 따라서 트랜잭션 로그 백업 볼륨의 가장 오래된 스냅샷은 30일이 되어야 합니다. 트랜잭션 로그 백업을 Azure Storage에 있는 다른 NFS 공유로 복사 하는 경우에 해당 아닙니다. 이러한 경우에 해당 NFS 공유에서 오래된 트랜잭션 로그 백업을 끌어올 수 있습니다.

저장소 스냅숏 및 트랜잭션 로그 백업의 최종 저장소 복제를 활용 하려면 SAP HANA 트랜잭션 로그 백업을 쓰는 위치를 변경 합니다. 이것은 HANA Studio에서 변경할 수 있습니다. 

SAP HANA 전체 로그 세그먼트를 자동으로 백업 하지만 확정 된 로그 백업 간격을 지정 합니다. 일반적으로 결정적 마침표를 사용 하 여 로그 백업을 실행 하려고 하기 때문에 재해 복구 옵션을 사용 하는 경우 특히 그렇습니다. 다음의 경우 15 분 로그 백업 간격으로 설정 됩니다.

![SAP HANA Studio에서 SAP HANA 백업 로그 예약](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15 분 보다 더 자주 수행 된 백업을 선택할 수도 있습니다. 더 잦은 설정은 HANA 대규모 인스턴스의 재해 복구 기능과 결합하여 자주 사용됩니다. 일부 고객은 5분마다 트랜잭션 로그 백업을 수행합니다.

이전에 데이터베이스를 백업한 적이 없는 경우 최종 단계는 파일 기반 데이터베이스 백업을 수행하여 백업 카탈로그 내에 있어야 하는 단일 백업 항목을 만드는 것입니다. 그렇지 않으면 SAP HANA에 지정 된 로그 백업을 시작할 수 없습니다.

![단일 백업 항목을 만드는 파일 기반 백업 수행](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


첫 번째로 성공한 저장소 스냅숏을 실행 한 후 6 단계에서 실행 된 테스트 스냅숏을 삭제 합니다. 자세한 내용은 "제거 테스트 스냅숏을-removeTestStorageSnapshot"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>디스크 볼륨에서 스냅숏의 크기와 수를 모니터링 합니다.

특정 저장소 볼륨에서 스냅샷의 개수 및 스냅샷의 저장소 사용을 모니터링할 수 있습니다. `ls` 명령은 스냅숏 디렉터리 또는 파일을 표시하지 않습니다. Linux OS 명령인 `du` 동일한 볼륨에 저장 하 고 있기 때문에 이러한 저장소 스냅숏에 대 한 세부 정보를 보여 줍니다. 명령을 사용 하 여 다음 옵션을 사용 하 여:

- `du –sh .snapshot`: 이 옵션은 스냅샷 디렉터리 내에서 모든 스냅샷을 제공합니다.
- `du –sh --max-depth=1`: 이 옵션은 **.snapshot** 폴더에 저장된 모든 스냅샷 및 각 스냅샷의 크기를 나열합니다.
- `du –hc`: 이 옵션은 모든 스냅샷에 사용되는 전체 크기를 제공합니다.

스냅숏을 만들고 저장 하는 볼륨에서 모든 저장소를 사용 하지 않도록 할 이러한 명령을 사용 합니다.

>[!NOTE]
>부팅 LUN의 스냅숏은 이전 명령으로 보이지 않습니다.

### <a name="get-details-of-snapshots"></a>스냅숏 세부 정보 가져오기
스냅숏에 대 한 자세한 내용을, 스크립트를 사용 하 여 `azure_hana_snapshot_details`입니다. 재해 복구 위치에 활성 서버가 있으면 두 위치 중 하나에서이 스크립트를 실행할 수 있습니다. 이 스크립트는 스냅샷이 포함된 각 볼륨별로 다음과 같이 세분화된 출력을 제공합니다. 
   * 볼륨의 총 스냅샷 크기
   * 해당 볼륨의 각 스냅샷에 다음 세부 정보가 포함됩니다. 
      - 스냅샷 이름 
      - 만든 시간 
      - 스냅샷 크기
      - 스냅샷 빈도
      - 스냅샷과 연결된 HANA 백업 ID(해당되는 경우)

명령 및 출력의 구문에 대 한에서 "목록 스냅숏-azure_hana_snapshot_details"를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>서버에서 스냅숏 수를 줄일

이전에 설명한 대로 저장 하는 스냅숏의 특정 레이블 수를 줄일 수 있습니다. 스냅샷을 시작하는 명령의 마지막 두 매개 변수는 보존하려는 스냅샷의 수와 레이블입니다.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

이전 예제에서 스냅숏 레이블은 **dailyhana**합니다. 보관이 레이블의 스냅숏 개수는 **28**합니다. 디스크 공간 사용량에 응답하는 대로 저장된 스냅샷 수를 줄이려고 할 수 있습니다. 예를 들어 15 개로 스냅숏의 수를 줄이기 위해 쉬운으로 마지막 매개 변수를 사용 하 여 스크립트를 실행 하는 것 **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

이 설정을 사용 하 여 스크립트를 실행 하는 경우 새 저장소 스냅숏을 포함 하는 스냅숏 개수는 15입니다. 15개의 오래된 스냅샷은 삭제되고 15개의 가장 최근 스냅샷은 유지됩니다.

 >[!NOTE]
 > 이 스크립트는 스냅숏이 시간 넘은 경우에 스냅숏 수를 줄입니다. 스크립트는 보다 작거나 1 시간 이전 스냅숏을 삭제 하지 않습니다. 이러한 제한 사항은 제공되는 선택적 재해 복구 기능과 관련되어 있습니다.

더 이상 백업 접두사를 사용 하 여 스냅숏 집합을 유지 하려는 경우 **dailyhana** 구문 예제에서 사용 하 여 스크립트 실행 **0** 을 보존 숫자로 합니다. 그러면 해당 레이블과 일치 하는 모든 스냅숏이 제거 됩니다. 모든 스냅숏을 제거 하면 HANA 큰 인스턴스 재해 복구 기능에 영향을 줄 수 있습니다.

특정 스냅샷을 삭제하는 또 다른 옵션은 `azure_hana_snapshot_delete` 스크립트를 사용하는 것입니다. 이 스크립트는 HANA Studio에서 확인된 HANA 백업 ID를 사용하거나 스냅샷 이름 자체를 사용하여 스냅샷 또는 스냅샷 집합을 삭제하도록 디자인되었습니다. 현재, 백업 ID는 **hana** 스냅샷 유형에 대해 생성된 스냅샷에만 연결됩니다. 스냅숏 형식의 백업을 **로그** 하 고 **부팅** 이러한 스냅숏을 찾을 수 없는 백업 ID가 있으므로 SAP HANA 스냅숏을 수행 하지 않습니다. 스냅샷 이름을 입력하는 경우 입력한 스냅샷 이름과 일치하는 다른 볼륨의 모든 스냅샷을 찾습니다. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

스크립트에 대 한 자세한 내용은 "스냅숏으로-azure_hana_snapshot_delete 삭제"의 참조 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

사용자로 스크립트 실행 **루트**입니다.

>[!IMPORTANT]
>스냅숏을에 존재 하는 데이터가 있는 경우 삭제 된 스냅숏이 삭제 되 면 있는지, 데이터가 영구히 손실 됩니다 하려고 합니다.


## <a name="file-level-restore-from-a-storage-snapshot"></a>저장소 스냅샷에서 파일 수준 복원

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
스냅샷 형식 **hana** 및 **logs**의 경우 **.snapshot** 디렉터리의 볼륨에서 직접 스냅샷에 액세스할 수 있습니다. 각 스냅숏에 대 한 하위 디렉터리가 있습니다. 실제 디렉터리 구조로 해당 하위 디렉터리에서 스냅숏 시점 시점의 상태에서 각 파일을 복사 합니다. 

스크립트의 현재 버전에서 있기 *없습니다* 셀프 서비스 등록으로 스냅숏 복원에 대 한 제공 된 스크립트를 복원 합니다. 스냅숏 복원 장애 조치 중 재해 복구 사이트에서 셀프 서비스 재해 복구 스크립트의 일부로 수행할 수 있습니다. 기존 사용 가능한 스냅숏에서 원하는 스냅숏을 복원 하려면 서비스 요청을 열어 Microsoft 운영 팀에 문의 해야 합니다.

>[!NOTE]
>단일 파일 복원의 HANA 큰 인스턴스 단위 형식 독립적인 부팅 LUN의 스냅숏에 대해 작동 하지 않습니다. 합니다 **.snapshot** 디렉터리는 부팅 LUN에서에서 노출 되지 않습니다. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>가장 최근 HANA 스냅샷으로 복구

프로덕션 중단 시나리오에서 Microsoft Azure 기술 지원 서비스를 사용한 고객 인시던트 처럼 저장소 스냅숏에서 복구 하는 과정을 시작할 수 있습니다. 이 경우 긴급 한 문제를 프로덕션 시스템에서 데이터가 삭제 되 고 검색 하는 유일한 방법은 프로덕션 데이터베이스를 복원 하는 것입니다.

다른 경우, 지정 시간 복구는 긴급성이 낮고 사전에 계획될 수 있습니다. 우선 순위 플래그를 발생 시키는 대신 Azure에서 SAP HANA를 사용 하 여이 복구를 계획할 수 있습니다. 예를 들어 새로운 개선 패키지를 적용 하 여 SAP 소프트웨어를 업그레이드 하려는 수도 있습니다. 이런 경우 개선 패키지를 업그레이드하기 전의 상태를 나타내는 스냅샷으로 되돌려야 합니다.

요청을 보내기 전에 준비해야 합니다. 그런 다음 Azure 팀의 SAP HANA 요청을 처리 하 고 복원된 된 볼륨을 제공할 수 있습니다. 나중에 사용자는 스냅샷을 기반으로 HANA 데이터베이스를 복원할 수 있습니다.

"스냅숏으로 복원 하는 방법"을 참조 하는 새 도구 집합을 사용 하 여 복원 스냅숏을 가져오기 위한 가능성 [수동 복구 가이드 SAP HANA에 대 한 Azure에서 저장소 스냅숏에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)합니다.

요청을 준비 하려면 다음이 단계를 수행 합니다.

1. 복원할 스냅샷을 결정합니다. 달리 지시가 없는 경우 hana/data 볼륨만 복원됩니다. 

1. HANA 인스턴스를 종료합니다.

   ![HANA 인스턴스를 종료합니다.](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 각 HANA 데이터베이스 노드에서 데이터 볼륨을 분리합니다. 데이터 볼륨이 운영 체제에 계속 탑재되는 경우 스냅샷 복원이 실패합니다.

   ![각 HANA 데이터베이스 노드에서 데이터 볼륨 분리](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Azure 지원 요청을 열고 특정 스냅숏을 복원 하는 방법에 대 한 지침을 포함 합니다.

   - 복원 중: Azure 서비스에서 SAP HANA를 조정, 검증 및 올바른 저장소 스냅숏이 복원 된 확인 전화 회의 참석 하도록 요청할 수도 있습니다. 

   - 복원한 후에: Azure 서비스에서 SAP HANA 저장소 스냅숏을 복원 되 면이 알려 줍니다.

1. 복원 프로세스가 완료되면 모든 데이터 볼륨을 다시 탑재합니다.

   ![모든 데이터 볼륨 다시 탑재](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



7 단계에서 파일을 가져오고, 예를 들어 SAP HANA 데이터 저장소 스냅숏에서 복구 하는 또 다른 방법은 된다 [수동 복구 가이드 SAP HANA에 대 한 Azure에서 저장소 스냅숏에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)합니다.

스냅숏 백업에서 복원 하려면, 참조 [수동 복구 가이드 SAP HANA에 대 한 Azure에서 저장소 스냅숏에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)합니다. 

>[!Note]
>Microsoft operations에서 스냅숏을 복원 하는 경우에 7 단계 수행 필요가 없습니다.


### <a name="recover-to-another-point-in-time"></a>다른 지정 시점으로 복구
특정 시점으로에서 복원, "시간에서 다음 지점으로 데이터베이스 복구"를 참조 [수동 복구 가이드 SAP HANA에 대 한 Azure에서 저장소 스냅숏에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)합니다. 


## <a name="next-steps"></a>다음 단계
- 참조 [재해 복구 원칙 및 준비](hana-concept-preparation.md)합니다.
