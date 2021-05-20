---
title: Azure(큰 인스턴스)의 SAP HANA에서 HANA 백업 및 복원 | Microsoft Docs
description: Azure(큰 인스턴스)의 SAP HANA에서 HANA 백업 및 복원을 수행하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b4b7ac968564c6c2e734fe48cca27c617eb82533
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109737279"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>HANA 대규모 인스턴스에서 SAP HANA 백업 및 복원

>[!IMPORTANT]
>이 문서는 SAP HANA 관리 설명서 또는 SAP 노트를 대체하지 않습니다. 이 문서에서는 독자가 SAP HANA 관리 및 작업 중에서도 특히 백업, 복원, 고가용성 및 재해 복구에 대해 확실히 이해하고 있고 전문 지식을 가졌다고 가정합니다. 이 문서에서는 SAP HANA Studio의 스크린샷이 표시됩니다. SAP 관리 도구 스크린의 콘텐츠, 구조 및 특성과 도구 자체는 릴리스할 SAP HANA 릴리스마다 변경될 수 있습니다.

사용자 환경에서 수행되는 단계와 프로세스를 해당 HANA 버전 및 릴리스로 연습하는 것이 중요합니다. 이 문서에서 설명하는 일부 프로세스는 일반적인 이해를 돕기 위해 간소화되었습니다. 따라서 최종 작업 핸드북의 구체적인 단계로 사용하면 안 됩니다. 구성에 대한 작업 핸드북을 만들려는 경우 프로세스를 테스트 및 실행하고 특정 구성과 관련된 프로세스를 문서화해야 합니다. 

운영 데이터베이스에서 가장 중요한 측면 중 하나는 치명적인 이벤트로부터 해당 데이터베이스를 보호하는 것입니다. 이러한 이벤트의 원인은 자연 재해에서 단순한 사용자 오류에 이르기까지 다양합니다.

특정 시점(예: 누군가가 중요한 데이터를 삭제하기 전에)으로 복원하는 기능을 사용하여 데이터베이스를 백업하면 중단이 발생하기 전의 상태에 가능한 한 가깝게 복원할 수 있습니다.

이러한 복원 기능을 구현하려면 다음과 같은 두 가지 유형의 백업을 수행해야 합니다.

- 데이터베이스 백업: 전체, 증분 또는 차등 백업
- 트랜잭션 로그 백업

애플리케이션 수준에서 수행되는 전체 데이터베이스 백업 외에도 스토리지 스냅샷으로 백업을 수행할 수 있습니다. 스토리지 스냅샷은 트랜잭션 로그 백업을 대체하지 않습니다. 트랜잭션 로그 백업은 데이터베이스를 특정 시점으로 복원하거나 이미 커밋된 트랜잭션에서 로그를 비우는 데 중요합니다. 스토리지 스냅샷은 데이터베이스의 롤포워드 이미지를 신속하게 제공하여 복구를 가속화할 수 있습니다. 

Azure(큰 인스턴스)의 SAP HANA는 두 가지 백업 및 복원 옵션을 제공합니다.

- **DIY(Do It Yourself)** 디스크 공간이 충분한지 확인한 후에는 다음과 같은 디스크 백업 방법 중 하나를 사용하여 전체 데이터베이스 및 로그 백업을 수행합니다. HANA 대규모 인스턴스 단위에 연결된 볼륨이나 Azure VM(가상 머신)에 설정된 NFS 공유에 직접 백업할 수 있습니다. 후자의 경우 고객은 Azure에서 Linux VM을 설정하고, VM에 Azure Storage를 연결하고, 해당 VM에 구성된 NFS 서버를 통해 스토리지를 공유합니다. HANA 대규모 인스턴스 단위에 직접 연결하는 볼륨에 백업을 수행하는 경우 Azure 스토리지 계정에 백업을 복사합니다. 이 작업은 Azure Storage 기반의 NFS 공유를 내보내는 Azure VM을 설정한 후에 수행합니다. Azure Backup 자격 증명 모음 또는 Azure 콜드 스토리지를 사용할 수도 있습니다. 

   또 다른 옵션은 백업을 Azure 스토리지 계정에 복사한 후 타사 데이터 보호 도구를 사용하여 백업을 저장하는 것입니다. DIY 백업 옵션은 준수 및 감사 목적으로 인해 장기간 저장되어야 하는 데이터에 필요할 수 있습니다. 모든 경우에 백업은 VM 및 Azure Storage를 통해 표시되는 NFS 공유로 복사됩니다.

- **인프라 백업 및 복원 기능** Azure(대규모 인스턴스)의 SAP HANA 기본 인프라가 제공하는 백업 및 복원 기능을 사용할 수도 있습니다. 이 옵션은 백업 및 빠른 복원에 대한 필요를 충족합니다. 이 섹션의 나머지 부분에서는 HANA 큰 인스턴스와 함께 제공되는 백업 및 복원 기능을 설명합니다. 이 섹션에서는 HANA 대규모 인스턴스가 제공하는 재해 복구 기능에 대한 백업 및 복원의 관계에 대해서도 설명합니다.

> [!NOTE]
>   HANA 대규모 인스턴스의 기본 인프라에서 사용하는 스냅샷 기술은 SAP HANA 스냅샷에 대한 종속성을 갖습니다. 현재 SAP HANA 스냅샷은 SAP HANA 다중 테넌트 데이터베이스 컨테이너의 다중 테넌트와 함께 작동하지 않습니다. 테넌트가 하나만 배포되면 SAP HANA 스냅샷이 작동하며 이 방법을 사용할 수 있습니다.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure(대규모 인스턴스)의 SAP HANA 스토리지 스냅샷 사용

Azure(큰 인스턴스)의 SAP HANA에 기반한 스토리지 인프라는 볼륨의 스토리지 스냅샷을 지원합니다. 볼륨의 백업 및 복원은 다음 사항을 고려하여 지원됩니다.

- 전체 데이터베이스 백업 대신 스토리지 볼륨 스냅샷을 자주 사용합니다.
- /hana/data 및 /hana/shared(/usr/sap 포함) 볼륨을 통해 스냅샷이 트리거되는 경우 스냅샷 기술은 SAP HANA 스냅샷을 시작한 후 스토리지 스냅샷을 실행합니다. 이 SAP HANA 스냅샷은 스토리지 스냅샷을 복구한 후에 최종 로그 복원에 대한 설치 지점입니다. HANA 스냅샷이 성공하려면 활성 HANA 인스턴스가 필요합니다. HSR 시나리오에서 스토리지 스냅샷은 HANA 스냅샷을 수행할 수 없는 현재 보조 노드에서 지원되지 않습니다.
- 스토리지 스냅샷이 성공적으로 실행된 후에는 SAP HANA 스냅샷이 삭제됩니다.
- 트랜잭션 로그 백업은 자주 사용되며 /hana/logbackups 볼륨 또는 Azure에 저장됩니다. 트랜잭션 로그 백업을 포함하는 /hana/logbackups 볼륨을 트리거하여 별도의 스냅샷을 생성할 수 있습니다. 이 경우 HANA 스냅샷을 실행할 필요가 없습니다.
- 프로덕션 중단으로 인해 데이터베이스를 특정 시점으로 복원해야 하는 경우 Azure에서 Microsoft Azure 지원 또는 SAP HANA를 요청하여 특정 스토리지 스냅샷으로 복원합니다. 예를 들어 샌드박스 시스템을 원래 상태로 복원하는 계획이 있습니다.
- 스토리지 스냅샷에 포함된 SAP HANA 스냅샷은 스토리지 스냅샷을 만든 후에 실행되고 저장된 트랜잭션 로그 백업을 적용하는 오프셋 지점입니다.
- 이러한 트랜잭션 로그 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원합니다.

다음과 같은 세 가지 볼륨 클래스를 대상으로 하는 스토리지 스냅샷을 수행할 수 있습니다.

- /hana/data 및 /hana/shared(/usr/sap 포함)에 대해 결합된 스냅샷. 이 스냅샷을 만들려면 스토리지 스냅샷에 대한 준비로 SAP HANA 스냅샷을 만들어야 합니다. SAP HANA 스냅샷은 데이터베이스가 스토리지 관점에서 일관적인 상태를 유지하도록 합니다. 복원 프로세스의 경우 설치 지점입니다.
- /hana/logbackups에 대한 별도 스냅샷.
- 운영 체제 파티션

최신 스냅샷 스크립트와 설명서를 받으려면 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)를 참조하세요. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)에서 스냅샷 스크립트 패키지를 다운로드하면 3개 파일이 들어 있습니다. 그 중 하나는 제공되는 기능을 PDF로 설명하는 파일입니다. 도구 세트를 다운로드한 후에는 "스냅샷 도구 가져오기"의 지침을 따릅니다.

## <a name="storage-snapshot-considerations"></a>스토리지 스냅샷 고려 사항

>[!NOTE]
>스토리지 스냅샷은 HANA 대규모 인스턴스 단위에 할당된 스토리지 공간을 사용합니다. 스토리지 스냅샷을 예약하는 경우 다음과 같은 측면과 유지할 스토리지 스냅샷의 수를 고려해야 합니다. 

Azure(큰 인스턴스)의 SAP HANA에 대한 스토리지 스냅샷의 특정 역학에는 다음이 포함됩니다.

- 만들어진 시점에서 특정 스토리지 스냅샷은 스토리지를 적게 사용합니다.
- 데이터 콘텐츠가 변경되고 스토리지 볼륨에서 SAP HANA 데이터 파일의 콘텐츠가 변경되면 스냅샷은 원래 블록 콘텐츠와 데이터 변경 내용을 저장해야 합니다.
- 결과적으로 스토리지 스냅샷의 크기가 증가합니다. 스냅샷이 오래 존재할수록 스토리지 스냅샷은 커집니다.
- 스토리지 스냅샷의 수명 동안 SAP HANA 데이터베이스 볼륨에 변경 내용이 많을수록 스토리지 스냅샷이 소진하는 공간이 커집니다.

Azure(큰 인스턴스)의 SAP HANA는 SAP HANA 데이터 및 로그 볼륨에 대해 고정된 볼륨 크기로 제공됩니다. 이러한 볼륨의 스냅샷을 수행하면 볼륨 공간이 줄어듭니다. 그러려면 다음 작업을 수행해야 합니다.

- 스토리지 스냅샷을 예약할 시기를 결정합니다.
- 스토리지 볼륨의 공간 사용을 모니터링합니다. 
- 저장하는 스냅샷 수를 관리합니다. 

대량의 데이터를 가져오거나 HANA 데이터베이스에 대해 다른 중요한 변경을 수행할 때 스토리지 스냅샷을 사용하지 않도록 설정할 수 있습니다. 


다음 섹션에서는 일반적인 권장 사항을 포함하여 이러한 스냅샷을 수행하는 방법에 대한 정보를 제공합니다.

- 하드웨어가 볼륨당 255개의 스냅샷을 유지할 수 있지만 이 숫자보다 낮게 유지하는 것이 좋습니다. 권장 구성은 250개 이하입니다.
- 스냅샷 스토리지를 수행하기 전에 여유 공간을 모니터링하고 추적합니다.
- 사용 가능한 공간에 따라 스토리지 스냅샷의 수를 줄입니다. 보관할 스냅샷의 수를 줄이거나 볼륨을 확장할 수 있습니다. 추가 스토리지는 1테라바이트 단위로 주문할 수 있습니다.
- SAP 플랫폼 마이그레이션 도구(R3load)를 사용하여 데이터를 SAP HANA로 이동하거나 백업에서 SAP HANA에 데이터를 복원하는 것과 같은 작업 중에는 /hana/data 볼륨에 대해 스토리지 스냅샷을 사용하지 않도록 설정합니다. 
- SAP HANA 테이블을 더 크게 재구성하는 동안 되도록이면 스토리지 스냅샷을 사용하지 말아야 합니다.
- 스토리지 스냅샷은 Azure(대규모 인스턴스)에서 SAP HANA의 재해 복구 기능을 활용하기 위한 필수 구성 요소입니다.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>셀프 서비스 스토리지 스냅샷을 사용하기 위한 필수 구성 요소

스냅샷 스크립트가 성공적으로 실행되도록 하려면 Perl이 HANA 대규모 인스턴스 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다. Perl은 HANA 대규모 인스턴스 단위에 미리 설치되어 제공됩니다. Perl 버전을 확인하려면 다음 명령을 사용합니다.

`perl -v`

![이 명령을 실행하면 공개 키가 복사됩니다.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>스토리지 스냅샷 설정

HANA 대규모 인스턴스를 사용하여 스토리지 스냅샷을 설정하려면 다음 단계를 수행합니다.
1. Perl이 HANA 큰 인스턴스 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다.
1. /etc/ssh/ssh\_config를 수정하여 _MACs hmac-sha1_ 줄을 추가합니다.
1. 실행하는 SAP HANA 인스턴스마다 마스터 노드에서 SAP HANA 백업 사용자 계정을 만듭니다(해당하는 경우).
1. 모든 SAP HANA 큰 인스턴스 서버에 SAP HANA HDB 클라이언트를 설치합니다.
1. 각 지역의 첫 번째 SAP HANA 큰 인스턴스 서버에서 스냅샷 생성을 제어하는 기본 스토리지 인프라에 액세스하기 위한 공개 키를 만듭니다.
1. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)의 스크립트 및 구성 파일을 SAP HANA 설치의 **hdbsql** 위치에 복사합니다.
1. 적절한 고객 사양에 필요한 대로 *HANABackupDetails.txt* 파일을 수정합니다.

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)에서 최신 스냅샷 스크립트와 설명서를 다운로드하세요. 앞에서 나열한 단계는 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)를 참조하세요.

### <a name="consideration-for-mcod-scenarios"></a>MCOD 시나리오에 대한 고려 사항
한 HANA 대규모 인스턴스 단위에 여러 SAP HANA 인스턴스가 있는 [MCOD 시나리오](https://launchpad.support.sap.com/#/notes/1681092)를 실행하는 경우 각 SAP HANA 인스턴스에 별도의 스토리지 볼륨을 프로비저닝했습니다. MDC 및 기타 고려 사항에 대한 자세한 내용은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)의 "기억해야 할 중요 사항"을 참조하세요.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1단계: SAP HANA HDB 클라이언트 설치

Azure 대규모 인스턴스의 SAP HANA에 설치된 Linux 운영 체제는 백업 및 재해 복구를 위해 SAP HANA 스토리지 스냅샷을 실행하는 데 필요한 폴더와 스크립트를 포함하고 있습니다. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)에서 최신 릴리스를 확인하세요. 

SAP HANA를 설치하는 동안 HANA 대규모 인스턴스 단위에 SAP HANA HDB 클라이언트를 설치하는 것은 사용자의 책임입니다.

### <a name="step-2-change-the-etcsshssh_config"></a>2단계: /etc/ssh/ssh\_config 변경

이 단계는 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "스토리지와 통신 사용"에 설명되어 있습니다.


### <a name="step-3-create-a-public-key"></a>3단계: 공개 키 만들기

HANA 대규모 인스턴스 테넌트의 스토리지 스냅샷 인터페이스에 액세스할 수 있도록 공개 키를 통해 로그인 프로시저를 설정합니다. 

테넌트에 있는 첫 번째 Azure의 SAP HANA(대규모 인스턴스) 서버에서 스토리지 인프라에 액세스하기 위한 공개 키를 만듭니다. 공개 키를 사용하면 스토리지 스냅샷 인터페이스에 로그인할 때 암호가 필요 없습니다. 또한 공개 키를 사용하면 암호 자격 증명을 유지 관리할 필요가 없습니다. 

공개 키를 생성하려면 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "스토리지와 통신 사용"을 참조하세요.


### <a name="step-4-create-an-sap-hana-user-account"></a>4단계: SAP HANA 사용자 계정 만들기

SAP HANA 스냅샷 만들기를 시작하려면 SAP HANA에서 스토리지 스냅샷 스크립트에 사용할 수 있는 사용자 계정을 만듭니다. 이를 위해 SAP HANA Studio 내에 SAP HANA 사용자 계정을 만듭니다. 사용자는 MDC의 SID 데이터베이스가 *아닌* SYSTEMDB 아래에 만들어야 합니다. 단일 컨테이너 환경에서는 사용자가 테넌트 데이터베이스 아래에 생성됩니다. 이 계정에는 **백업 관리자** 및 **카탈로그 읽기** 권한이 있어야 합니다. 

사용자 계정을 설정하고 사용하려면 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "SAP HANA와 통신 사용"을 참조하세요.


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5단계: SAP HANA 사용자 계정 권한 부여

이 단계에서는 스크립트가 런타임에 암호를 제출하지 않아도 되도록 앞에서 만든 SAP HANA 사용자 계정에 권한을 부여합니다. SAP HANA 명령 `hdbuserstore`는 SAP HANA 사용자 키를 만들 수 있습니다. 키는 하나 이상의 SAP HANA 노드에 저장됩니다. 사용자 키를 사용하면 스크립팅 프로세스 내에서 암호를 관리하지 않고도 SAP HANA에 액세스할 수 있습니다. 스크립팅 프로세스는 이 아티클의 뒤에서 설명하겠습니다.

>[!IMPORTANT]
>스냅샷 명령이 실행되는 동일한 사용자 컨텍스트에서 이러한 구성 명령을 실행합니다. 그렇지 않으면 스냅샷 명령이 제대로 작동하지 않습니다.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6단계: 스냅샷 스크립트 가져오기, 스냅샷 구성, 구성 및 연결 테스트

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)에서 최신 버전의 스크립트를 다운로드하십시오. 스크립트 설치 방법이 스크립트 릴리스 4.1에서 변경되었습니다. 자세한 내용은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "SAP HANA와 통신 사용"을 참조하세요.

명령의 정확한 순서는 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "간편한 스냅샷 도구 설치(기본값)"를 참조하세요. 기본 설치를 사용하는 것이 좋습니다. 

버전 3.x에서 4.1로 업그레이드하려면 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "기존 설치 업그레이드"를 참조하세요. 4\.1 도구 세트를 제거하려면 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "스냅샷 도구 제거"를 참조하세요.

[Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)의 "스냅샷 도구 설치 완료"에 설명된 단계를 잊지 말고 실행해야 합니다.

설치되는 여러 스크립트와 파일의 용도는 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)의 "이러한 스냅샷 도구는 무엇입니까?"에 설명되어 있습니다.

스냅샷 도구를 구성하기 전에 HANA 백업 위치 및 설정을 올바르게 구성했는지 확인합니다. 자세한 내용은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "SAP HANA 구성"을 참조하세요.

스냅샷 도구 세트의 구성은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)의 "구성 파일 - HANABackupCustomerDetails.txt"에 설명되어 있습니다.

#### <a name="test-connectivity-with-sap-hana"></a>SAP HANA 연결 테스트

모든 구성 데이터를 *HANABackupCustomerDetails.txt* 파일에 추가한 후에 HANA 인스턴스 데이터에 대한 구성이 올바른지 확인합니다. `testHANAConnection` 스크립트를 사용합니다. 이것은 SAP HANA 강화 또는 스케일 아웃 구성과 별개입니다.

자세한 내용은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "SAP HANA 연결 확인 - testHANAConnection"을 참조하세요.

#### <a name="test-storage-connectivity"></a>스토리지 연결 테스트

그 다음 테스트 단계는 *HANABackupCustomerDetails.txt* 구성 파일에 입력한 데이터를 기반으로 스토리지 연결을 확인하는 것입니다. 그 후 테스트 스냅샷을 실행합니다. `azure_hana_backup` 명령을 실행하기 전에 이 테스트를 실행해야 합니다. 이 테스트의 명령 순서는 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "스토리지 연결 확인 - testStorageSnapshotConnection"을 참조하세요.

스토리지 가상 머신 인터페이스에 로그인이 성공하면 스크립트는 2단계로 진행되고 테스트 스냅샷을 만듭니다. SAP HANA의 3노드 스케일 아웃 구성에 대한 출력은 다음과 같습니다.

이 스크립트를 사용하여 테스트 스냅샷이 성공적으로 실행되면 실제 스토리지 스냅샷을 예약할 수 있습니다. 성공하지 못했다면 문제를 조사한 후 계속 진행합니다. 테스트 스냅샷은 첫 번째 실제 스냅샷이 완료될 때까지 유지되어야 합니다.


### <a name="step-7-perform-snapshots"></a>7단계: 스냅샷 수행

준비 단계가 완료되면 실제 스토리지 스냅샷을 구성하고 예약할 수 있습니다. 예약할 스크립트는 SAP HANA 강화 및 규모 확장 구성에 작동합니다. 백업 스크립트의 주기적인 일반 실행의 경우 cron 유틸리티를 사용하여 스크립트를 예약합니다. 

정확한 명령 구문 및 기능은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "스냅샷 백업 수행 - azure_hana_backup"을 참조하세요. 

`azure_hana_backup` 스크립트를 실행하면 다음과 같은 세 가지 단계로 스토리지 스냅샷이 만들어집니다.

1. SAP HANA 스냅샷을 실행합니다.
1. 스토리지 스냅샷을 실행합니다.
1. 스토리지 스냅샷을 실행하기 전에 만든 SAP HANA 스냅샷을 제거합니다.

스크립트를 실행하려면 스크립트가 복사된 HDB 실행 파일 폴더에서 스크립트를 호출합니다. 

보존 기간은 스크립트를 실행할 경우 매개 변수로 제출되는 스냅샷 개수와 함께 관리됩니다. 스토리지 스냅샷이 적용되는 기간은 실행 기간 및 스크립트가 실행될 때 매개 변수로 제출되는 스냅샷 개수입니다. 

유지되는 스냅샷 개수가 스냅샷 스크립트의 호출에서 매개 변수로 이름이 지정된 수를 초과하면 새 스냅샷을 실행하기 전에 동일한 레이블의 가장 오래된 스토리지가 삭제됩니다. 호출의 마지막 매개 변수로 지정한 숫자는 유지되는 스냅샷 개수를 제어하는 데 사용할 수 있는 숫자입니다. 이 숫자를 사용하여 스냅샷에 사용되는 디스크 공간을 간접적으로 제어할 수도 있습니다. 


## <a name="snapshot-strategies"></a>스냅샷 전략
다른 유형에 대한 스냅샷 빈도는 HANA 대규모 인스턴스 재해 복구 기능을 사용하는지 여부에 따라 달라집니다. 이 기능은 스토리지 스냅샷을 사용합니다. 그러려면 스토리지 스냅샷의 빈도 및 실행 기간에 대한 특별한 권장 사항이 필요할 수 있습니다. 

다음 고려 사항 및 권장 사항에서는 HANA 대규모 인스턴스가 제공하는 재해 복구 기능을 사용하지 *않는다* 고 가정합니다. 대신, 스토리지 스냅샷을 사용하여 백업을 보유하고 지난 30일 동안의 지정 시간 복구를 제공할 수 있습니다. 스냅샷 수와 공간의 제한을 감안하여 다음과 같은 요구 사항을 고려해야 합니다.

- 지정 시간 복구에 대한 복구 시간.
- 사용된 공간.
- 재해로부터 잠재적 복구를 위한 복구 지점 및 복구 시간 목표.
- 디스크에 대한 HANA 전체 데이터베이스 백업의 최종 실행. 디스크 또는 **backint** 인터페이스에 대한 전체 데이터베이스 백업이 수행될 때마다 스토리지 스냅샷의 실행에 실패합니다. 스토리지 스냅샷을 기반으로 전체 데이터베이스 백업을 실행하려는 경우 이 시간 동안 스토리지 스냅샷이 실행되지 않도록 설정해야 합니다.
- 볼륨당 스냅샷 수. 250개로 제한됩니다.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

HANA 대규모 인스턴스의 재해 복구 기능을 사용하지 않는 경우 스냅샷 간격이 깁니다. 이 경우 12시간 또는 24시간 간격으로 /hana/data 및 /hana/shared(/usr/sap 포함)의 결합된 스냅샷을 수행합니다. 한 달 동안 스냅샷을 유지합니다. 로그 백업 볼륨의 스냅샷도 마찬가지입니다. 로그 백업 볼륨에 대한 SAP HANA 트랜잭션 로그 백업은 5-15분 간격으로 실행됩니다.

예약된 스토리지 스냅샷은 cron를 사용하여 수행하는 것이 가장 좋습니다. 모든 백업 및 재해 복구 요구 사항에 동일한 스크립트를 사용합니다. 요청된 다양한 백업 시간에 맞게 스크립트 입력을 수정합니다. 이러한 스냅샷은 해당 실행 시간에 따라 cron에서 모두 다르게 예약됩니다. 실행 시간은 매시간, 12시간, 매일 또는 매주입니다. 

다음 예제는 /etc/crontab의 cron 일정을 보여줍니다.
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
이전 예제에서는 매시간 결합 스냅샷이 /hana/data 및 /hana/shared/SID(/usr/sap 포함) 위치가 포함된 볼륨을 처리합니다. 지난 2일 내에 더 빠른 지정 시간 복구에 이 형식의 스냅샷을 사용합니다. 이러한 볼륨에 대한 일일 스냅샷도 있습니다. 따라서 2일 간의 시간별 스냅샷과 4주 간의 일별 스냅샷을 갖습니다. 트랜잭션 로그 백업 볼륨 역시 매일 백업됩니다. 이러한 백업은 4주 동안 유지됩니다. 

crontab의 세 번째 줄에 표시된 대로 HANA 트랜잭션 로그의 백업은 5분마다 실행되도록 예약됩니다. 스토리지 스냅샷을 실행하는 여러 cron 작업은 시차를 두고 시작됩니다. 따라서 스냅샷이 특정 시점에 한꺼번에 실행되지 않습니다. 

다음 예제에서는 /hana/data, /hana/shared/SID(/usr/sap 포함) 위치가 포함된 볼륨을 처리하는 결합된 스냅샷을 매시간 수행합니다. 이 스냅샷은 이틀 동안 보관합니다. 트랜잭션 로그 백업 볼륨의 스냅샷은 5분 단위로 실행되고 4시간 동안 유지됩니다. 이전과 마찬가지로, HANA 트랜잭션 로그 파일의 백업은 5분 간격으로 실행되도록 예약됩니다. 

트랜잭션 로그 백업 볼륨의 스냅샷은 트랜잭션 로그 백업이 시작된 후 2분 지연되어 수행됩니다. 정상적인 상황에서는 SAP HANA 트랜잭션 로그 백업이 2분 이내에 완료됩니다. 이전과 마찬가지로, 부팅 LUN이 포함된 볼륨은 스토리지 스냅샷에 의해 하루 1번 백업되고 4주 동안 보관됩니다.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

다음 그래픽은 이전 예제의 순서를 보여줍니다. 부팅 LUN은 제외되었습니다.

![백업과 스냅샷 간의 관계](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA는 /hana/log 볼륨에 대해 정기적인 쓰기를 수행하여 데이터베이스에 대한 커밋된 변경 내용을 문서화합니다. 정기적으로 SAP HANA는 /hana/data 볼륨에 저장점을 씁니다. crontab에 지정된 대로 SAP HANA 트랜잭션 로그 백업은 5분마다 실행됩니다. 

/hana/data 및 /hana/shared/SID 볼륨에 대해 결합된 스토리지 스냅샷을 트리거한 결과로, SAP HANA 스냅샷이 매시간 실행된다는 것을 알 수 있습니다. HANA 스냅샷이 성공한 후 결합된 스토리지 스냅샷이 실행됩니다. crontab에 설명된 대로 /hana/logbackup 볼륨의 스토리지 스냅샷은 HANA 트랜잭션 로그 백업이 있고 약 2분 후에 5분마다 실행됩니다.

> 

>[!IMPORTANT]
> SAP HANA 트랜잭션 백업에 대한 스토리지 스냅샷 사용은 스냅샷이 SAP HANA 로그 백업과 함께 수행된 경우에만 유용합니다. 이러한 트랜잭션 로그 백업은 스토리지 스냅샷 사이의 기간에 적용되어야 합니다. 

30일이라는 지정 시간 복구의 사용자에 대한 커밋을 설정한 경우 다음을 수행해야 합니다.

- 극단적인 경우 30일이 지난 /hana/data 및 /hana/shared/SID의 결합된 스토리지 스냅샷에 액세스합니다. 
- 결합된 스토리지 스냅샷 사이의 시간에 해당하는 인접한 트랜잭션 로그 백업이 있어야 합니다. 따라서 트랜잭션 로그 백업 볼륨의 가장 오래된 스냅샷은 30일이 되어야 합니다. 트랜잭션 로그 백업을 Azure Storage에 있는 다른 NFS 공유에 복사하는 경우에는 그렇지 않습니다. 이러한 경우에 해당 NFS 공유에서 오래된 트랜잭션 로그 백업을 끌어올 수 있습니다.

스토리지 스냅샷 및 트랜잭션 로그 백업의 최종 스토리지 복제를 활용하려면 SAP HANA가 트랜잭션 로그 백업을 쓰는 위치를 변경합니다. 이것은 HANA Studio에서 변경할 수 있습니다. 

SAP HANA가 전체 로그 세그먼트를 자동으로 백업하지만 확정된 로그 백업 간격을 지정합니다. 재해 복구 옵션을 사용하는 경우 일반적으로 확정된 기간으로 로그 백업을 실행하기 때문에 특히 그렇습니다. 다음과 같은 경우 로그 백업 간격으로 15분이 설정되었습니다.

![SAP HANA Studio에서 SAP HANA 백업 로그 예약](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15분보다 더 자주 수행되는 백업을 선택할 수도 있습니다. 더 잦은 설정은 HANA 대규모 인스턴스의 재해 복구 기능과 결합하여 자주 사용됩니다. 일부 고객은 5분마다 트랜잭션 로그 백업을 수행합니다.

이전에 데이터베이스를 백업한 적이 없는 경우 최종 단계는 파일 기반 데이터베이스 백업을 수행하여 백업 카탈로그 내에 있어야 하는 단일 백업 항목을 만드는 것입니다. 그렇지 않으면 SAP HANA가 지정된 로그 백업을 시작할 수 없습니다.

![단일 백업 항목을 만드는 파일 기반 백업 수행](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


첫 번째로 성공한 스토리지 스냅샷을 실행한 후 6단계에서 실행된 테스트 스냅샷을 삭제합니다. 자세한 내용은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "테스트 스냅샷 제거 - removeTestStorageSnapshot"를 참조하세요. 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>디스크 볼륨에서 스냅샷의 개수 및 크기 모니터링

특정 스토리지 볼륨에서 스냅샷의 개수 및 스냅샷의 스토리지 사용을 모니터링할 수 있습니다. `ls` 명령은 스냅샷 디렉터리 또는 파일을 표시하지 않습니다. Linux OS 명령 `du`는 스토리지 스냅샷이 동일한 볼륨에 저장되므로 이러한 스토리지 스냅샷에 대한 세부 정보를 보여줍니다. 이 명령의 옵션은 다음과 같습니다.

- `du –sh .snapshot`: 이 옵션은 스냅샷 디렉터리 내에서 모든 스냅샷을 제공합니다.
- `du –sh --max-depth=1`: 이 옵션은 **.snapshot** 폴더에 저장된 모든 스냅샷 및 각 스냅샷의 크기를 나열합니다.
- `du –hc`: 이 옵션은 모든 스냅샷에 사용되는 전체 크기를 제공합니다.

이러한 명령을 사용하여 만들고 저장한 스냅샷이 볼륨에서 모든 스토리지를 사용하지 않도록 합니다.

>[!NOTE]
>부팅 LUN의 스냅샷은 이전 명령으로 볼 수 없습니다.

### <a name="get-details-of-snapshots"></a>스냅샷 세부 정보 가져오기
스냅샷 세부 정보를 보려면 `azure_hana_snapshot_details` 스크립트를 사용합니다. 재해 복구 위치에 활성 서버가 있는 경우 어느 위치에서나 이 스크립트를 실행할 수 있습니다. 이 스크립트는 스냅샷이 포함된 각 볼륨별로 다음과 같이 세분화된 출력을 제공합니다. 
   * 볼륨의 총 스냅샷 크기
   * 해당 볼륨의 각 스냅샷에 다음 세부 정보가 포함됩니다. 
      - 스냅샷 이름 
      - 만든 시간 
      - 스냅샷 크기
      - 스냅샷 빈도
      - 스냅샷과 연결된 HANA 백업 ID(해당되는 경우)

명령 및 출력의 구문은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "스냅샷 나열 - azure_hana_snapshot_details"을 참조하세요. 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>서버의 스냅샷 수 줄이기

앞에서 설명한 대로 저장하는 스냅샷의 특정 레이블 수를 줄일 수 있습니다. 스냅샷을 시작하는 명령의 마지막 두 매개 변수는 보존하려는 스냅샷의 수와 레이블입니다.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

이전 예제의 스냅샷 레이블은 **dailyhana** 입니다. 이 레이블이 있는 보존할 스냅샷의 수는 **28** 개입니다. 디스크 공간 사용량에 응답하는 대로 저장된 스냅샷 수를 줄이려고 할 수 있습니다. 스냅샷 수를 줄이는(예를 들어 15개로) 가장 좋은 방법은 마지막 매개 변수를 **15** 로 설정하여 스크립트를 실행하는 것입니다.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

이 설정으로 스크립트를 실행하면 새 스토리지 스냅샷을 포함하여 스냅샷 개수가 15가 됩니다. 15개의 오래된 스냅샷은 삭제되고 15개의 가장 최근 스냅샷은 유지됩니다.

 >[!NOTE]
 > 이 스크립트는 1시간을 초과하는 스냅샷이 있는 경우에만 스냅샷 수를 줄입니다. 스크립트는 한 시간이 되지 않은 스냅샷을 삭제하지 않습니다. 이러한 제한 사항은 제공되는 선택적 재해 복구 기능과 관련되어 있습니다.

구문 예제에서 백업 접두사 **dailyhana** 가 붙은 스냅샷 세트를 더 이상 유지하지 않으려면 보존 기간이 **0** 인 스크립트를 실행합니다. 그러면 해당 레이블과 일치하는 모든 스냅샷이 제거됩니다. 모든 스냅샷을 제거하면 HANA 대규모 인스턴스 재해 복구 기능에 영향을 미칠 수 있습니다.

특정 스냅샷을 삭제하는 또 다른 옵션은 `azure_hana_snapshot_delete` 스크립트를 사용하는 것입니다. 이 스크립트는 HANA Studio에서 확인된 HANA 백업 ID를 사용하거나 스냅샷 이름 자체를 사용하여 스냅샷 또는 스냅샷 집합을 삭제하도록 디자인되었습니다. 현재, 백업 ID는 **hana** 스냅샷 유형에 대해 생성된 스냅샷에만 연결됩니다. **로그** 및 **부팅** 형식의 스냅샷 백업은 SAP HANA 스냅샷을 수행하지 않으므로 해당 스냅샷에는 백업 ID가 없습니다. 스냅샷 이름을 입력하는 경우 입력한 스냅샷 이름과 일치하는 다른 볼륨의 모든 스냅샷을 찾습니다. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

스크립트에 대한 자세한 내용은 [Azure의 SAP HANA용 Microsoft 스냅샷 도구](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)에서 "스냅샷 삭제 - azure_hana_snapshot_delete"를 참조하세요.

**root** 사용자로 스크립트를 실행합니다.

>[!IMPORTANT]
>데이터가 삭제하려는 스냅샷에만 있는 경우 스냅샷을 삭제하면 해당 데이터가 영구적으로 손실됩니다.


## <a name="file-level-restore-from-a-storage-snapshot"></a>스토리지 스냅샷에서 파일 수준 복원

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
스냅샷 형식 **hana** 및 **logs** 의 경우 **.snapshot** 디렉터리의 볼륨에서 직접 스냅샷에 액세스할 수 있습니다. 각 스냅샷의 하위 디렉터리가 있습니다. 해당 하위 디렉터리의 스냅샷 시점에 있는 상태인 각 파일을 실제 디렉터리 구조로 복사합니다. 

현재 버전의 스크립트에는 스냅샷 복원을 위해 셀프 서비스로 제공되는 복원 스크립트가 *없습니다*. 장애 조치(failover) 중에 재해 복구 사이트에서 셀프 서비스 재해 복구 스크립트의 일부로 스냅샷 복원을 수행할 수 있습니다. 사용 가능한 기존 스냅샷에서 원하는 스냅샷을 복원하려면 서비스 요청을 열어 Microsoft 운영 팀에 문의해야 합니다.

>[!NOTE]
>단일 파일 복원은 HANA 대규모 인스턴스 단위의 유형에 독립적인 부팅 LUN의 스냅샷에 대해 작동하지 않습니다. **.snapshot** 디렉터리는 부팅 LUN에서 노출되지 않습니다. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>가장 최근 HANA 스냅샷으로 복구

프로덕션 중단 시나리오에서 스토리지 스냅샷에서 복구하는 프로세스는 Microsoft Azure 지원에서 고객 인시던트로 시작될 수 있습니다. 프로덕션 시스템에서 데이터가 삭제되고 데이터를 검색하는 유일한 방법이 프로덕션 데이터베이스를 복원하는 것인 경우 긴급한 문제입니다.

다른 경우, 지정 시간 복구는 긴급성이 낮고 사전에 계획될 수 있습니다. 높은 우선 순위 플래그를 발생시키는 대신 Azure의 SAP HANA에서 이 복구를 계획할 수 있습니다. 예를 들어 새로운 개선 패키지를 적용하여 SAP 소프트웨어를 업그레이드하도록 계획할 수 있습니다. 이런 경우 개선 패키지를 업그레이드하기 전의 상태를 나타내는 스냅샷으로 되돌려야 합니다.

요청을 보내기 전에 준비해야 합니다. 그러면 Azure의 SAP HANA 팀에서 요청을 처리하고 복원된 볼륨을 제공할 수 있습니다. 나중에 사용자는 스냅샷을 기반으로 HANA 데이터베이스를 복원할 수 있습니다.

새 도구 세트로 스냅샷을 복원하는 방법은 [스토리지 스냅샷에서 Azure의 SAP HANA를 수동으로 복구하는 방법 가이드](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)의 "스냅샷 복원 방법"을 참조하세요.

요청을 준비하려면 다음 단계를 수행합니다.

1. 복원할 스냅샷을 결정합니다. 달리 지시가 없는 경우 hana/data 볼륨만 복원됩니다. 

1. HANA 인스턴스를 종료합니다.

   ![HANA 인스턴스를 종료합니다.](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 각 HANA 데이터베이스 노드에서 데이터 볼륨을 분리합니다. 데이터 볼륨이 운영 체제에 계속 탑재되는 경우 스냅샷 복원이 실패합니다.

   ![각 HANA 데이터베이스 노드에서 데이터 볼륨 분리](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Azure 지원 요청을 열어서 특정 스냅샷의 복원에 대한 지침을 포함합니다.

   - 복원 중: Azure의 SAP HANA 서비스 팀에서 전화 회의에 참여하여 올바른 스토리지 스냅샷이 복원될 수 있도록 조정, 검증 및 확인해 줄 것을 요청할 수 있습니다. 

   - 복원 후: 스토리지 스냅샷이 복원되면 Azure의 SAP HANA 서비스 팀에서 알려줍니다.

1. 복원 프로세스가 완료되면 모든 데이터 볼륨을 다시 탑재합니다.

   ![모든 데이터 볼륨 다시 탑재](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



또 다른 방법(예: 스토리지 스냅샷에서 복구된 SAP HANA 데이터 파일 가져오기)은 [스토리지 스냅샷에서 Azure의 SAP HANA를 수동으로 복구하는 방법 가이드](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)의 7단계에 설명되어 있습니다.

스냅샷 백업에서 복원하려면 [스토리지 스냅샷에서 Azure의 SAP HANA를 수동으로 복구하는 방법 가이드](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)를 참조하세요. 

>[!Note]
>Microsoft 작업을 통해 스냅샷을 복원한 경우에는 7단계를 수행할 필요가 없습니다.


### <a name="recover-to-another-point-in-time"></a>다른 지정 시점으로 복구
특정 시점으로 복원하려면 [스토리지 스냅샷에서 Azure의 SAP HANA를 수동으로 복구하는 방법 가이드](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)의 "데이터베이스를 다음 지정 시점으로 복구"를 참조하세요. 





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>SAP HANA 대량 인스턴스에 SnapCenter 통합

이 섹션에서는 고객이 NetApp SnapCenter 소프트웨어를 사용하여 Microsoft Azure HLI(HANA 대규모 인스턴스)에 호스트되는 SAP HANA 데이터베이스의 스냅샷을 만들고 백업하고 복원하는 방법을 설명합니다. 

SnapCenter는 백업/복구, 비동기 스토리지 복제를 사용하는 DR(재해 복구), 시스템 복제 및 시스템 클로닝을 비롯한 시나리오의 솔루션을 제공합니다. Azure의 SAP HANA 대규모 인스턴스와 통합되어 이제 고객은 백업 및 복구 작업에 SnapCenter를 사용할 수 있습니다.

추가 정보는 SnapCenter에서 NetApp TR-4614 및 TR-4646을 참조하세요.

- [SnapCenter를 사용하여 SAP HANA 백업/복구(TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)
- [스토리지 복제를 사용하여 SAP HANA 재해 복구(TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)
- [SnapCenter를 사용하여 SAP HANA HSR(TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)
- [SnapCenter에서 SAP 복제(TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)

### <a name="system-requirements-and-prerequisites"></a>시스템 요구 사항 및 필수 구성 요소

Azure HLI에서 SnapCenter를 실행하려면 다음과 같은 시스템 요구 사항을 충족해야 합니다.
* 4 vCPU, 16GB RAM, 650GB 이상의 관리형 프리미엄 SSD 스토리지가 있는 Azure Windows 2016 이상에서 실행되는 SnapCenter
* 1\.5TB~24TB RAM이 있는 SAP HANA(대규모 인스턴스) 시스템 복제 작업 및 테스트에는 두 개의 SAP HANA(대규모 인스턴스) 시스템을 사용하는 것이 좋습니다.

SAP HANA에 SnapCenter을 통합하는 단계는 다음과 같습니다. 

1. 사용자가 생성한 공개 키를 Microsoft 운영 팀에 전달해 달라는 지원 티켓 요청을 발생시킵니다. SnapCenter 사용자가 스토리지 시스템에 액세스하는 데 필요합니다.
1. HLI에 대한 액세스 권한이 있는 VNET에서 VM을 만듭니다. 이 VM은 SnapCenter에 사용됩니다. 
1. SnapCenter를 다운로드하여 설치합니다. 
1. 작업을 백업 및 복구합니다. 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>사용자 역할 스토리지 설정에 대한 지원 티켓 만들기

1. Azure Portal을 열고 **구독** 페이지로 이동합니다. "구독" 페이지에서 아래와 같이 빨간색으로 표시된 SAP HANA 구독을 선택합니다.

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="사용자 스토리지 설정을 위한 지원 사례 만들기":::

1. SAP HANA 구독 페이지에서 **리소스 그룹** 하위 페이지를 선택합니다.

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="솔루션 랩 구독 리소스 그룹" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. 지역에서 적절한 리소스 그룹을 선택합니다.

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="지역에서 적절한 리소스 그룹 선택" lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. Azure 스토리지의 SAP HANA에 해당하는 SKU 항목을 선택합니다.

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="SAP HANA에 해당하는 SKU 항목 선택" lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. 빨간색으로 표시된 **새 지원 티켓** 요청을 엽니다.

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="새 지원 티켓 요청 열기":::

1. **기본** 탭에서 다음과 같은 티켓 정보를 입력합니다.

   * **문제 유형:** 기술
   * **구독:** 해당 구독
   * **서비스:** SAP HANA 대규모 인스턴스
   * **리소스:** 해당 리소스 그룹
   * **요약:** 사용자가 생성한 공개 키 입력
   * **문제 유형:** 구성 및 설정
   * **문제 하위 유형:** HLI에 대한 SnapCenter 설정


1. 지원 티켓의 **설명** 에 있는 **세부 정보** 탭에 다음 정보를 입력합니다. 
   
   * HLI에 대한 SnapCenter 설정
   * SnapCenter 사용자의 공개 키(snapcenter.pem) - 아래의 공개 키 만들기 예제 참조

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="새 지원 요청 - 세부 정보 탭" lightbox="./media/snapcenter/new-support-request-details.png":::

1. **검토 + 만들기** 를 선택하고 지원 티켓을 검토합니다. 

1. HANA 대규모 인스턴스 또는 Linux 서버의 SnapCenter 사용자 이름에 대한 인증서를 생성합니다.

   SnapCenter에서 SVM(스토리지 가상 머신)에 액세스하고 HANA 데이터베이스의 스냅샷을 만들려면 사용자 이름과 암호가 필요합니다. Microsoft는 사용자(고객)가 스토리지 시스템에 액세스하기 위한 암호를 설정할 수 있도록 공개 키를 사용합니다.

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. snapcenter.pem 파일을 지원 티켓에 연결하고 **만들기** 를 선택합니다.

   공개 키 인증서를 제출하면 Microsoft에서 SVM IP 주소와 함께 테넌트의 SnapCenter 사용자 이름을 설정합니다.   

1. SVM IP를 받은 후에는 본인이 제어하는 SVM에 액세스하는 데 사용할 암호를 설정합니다.

   다음은 HANA 대규모 인스턴스 환경에 대한 액세스 권한을 갖고 있으며 암호를 설정하는 데 사용되는 HANA 대규모 인스턴스 또는 가상 네트워크의 VM에서 수행하는 REST CALL(설명서)의 예입니다.

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   HANA DB 시스템에 활성 상태의 프록시 변수가 없는지 확인합니다.

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>SnapCenter 다운로드 및 설치
SnapCenter에서 스토리지 시스템에 액세스하기 위한 사용자 이름을 설정했으므로, SnapCenter를 설치한 후 SnapCenter 사용자 이름을 사용하여 SnapCenter를 구성하겠습니다. 

SnapCenter를 설치하기 전에 [SnapCenter를 사용하여 SAP HANA 백업/복구](https://www.netapp.com/us/media/tr-4614.pdf)를 검토하여 백업 전략을 정의합니다. 

1. [NetApp](https://mysupport.netapp.com)에 로그인하여 최신 버전의 SnapCenter를 [다운로드](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0)합니다.

1. Windows Azure VM에 SnapCenter를 설치합니다.

   설치 관리자가 VM의 필수 구성 요소를 확인합니다. 

   >[!IMPORTANT]
   >VM 크기에 주의합니다. 특히 대규모 환경에서는 더욱 주의해야 합니다.

1. SnapCenter의 사용자 자격 증명을 구성합니다. 기본적으로 애플리케이션을 설치하는 데 사용되는 Windows 사용자 자격 증명으로 채워집니다. 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="설치 사용자 입력 대화 상자"::: 

1. 세션을 시작할 때 보안 예외를 저장하면 GUI가 시작됩니다.

1. VM(https://snapcenter-vm:8146) 에서 Windows 자격 증명으로 SnapCenter에 로그인하여 환경을 구성합니다.


### <a name="set-up-the-storage-system"></a>스토리지 시스템 설정

1. SnapCenter에서 **스토리지 시스템** 을 선택한 다음, **+새로 만들기** 를 선택합니다. 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="SnapCenter 스토리지 연결" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   기본값은 테넌트당 SVM 하나입니다. 고객의 테넌트가 여러 개 있거나 여러 지역에 HLI가 있는 경우 SnapCenter에서 모든 SVM을 구성하는 것이 좋습니다.

1. [스토리지 시스템 추가]에서 추가하려는 스토리지 시스템의 정보를 입력하고 SnapCenter 사용자 이름 및 암호를 입력한 다음, **제출** 을 선택합니다.

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="새 스토리지 연결":::

   >[!NOTE]
   >기본값은 테넌트당 SVM 하나입니다.  여러 테넌트가 있는 경우 SnapCenter에서 모든 SVM을 구성하는 것이 좋습니다. 

1. SnapCenter에서 **호스트** 를 선택하고, **+추가** 를 선택하여 HANA 플러그 인 및 hana DB 호스트를 설정합니다.  최신 버전의 SnapCenter는 호스트의 HANA 데이터베이스를 자동으로 검색합니다.

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="SnapCenter에서 [호스트]를 선택하고 [추가]를 선택합니다." lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. 새 호스트의 정보를 제공합니다.
   1. 호스트 유형의 운영 체제를 선택합니다.
   1. SnapCenter VM 호스트 이름을 입력합니다.
   1. 사용하려는 자격 증명을 입력합니다.
   1. **Microsoft Windows** 및 **SAP HANA** 옵션을 선택한 다음, **제출** 을 선택합니다.

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="새 호스트의 정보":::

   >[!IMPORTANT]
   >첫 번째 노드를 설치하기 전에, SnapCenter에서 루트가 아닌 사용자는 데이터베이스에 플러그 인을 설치할 수 있습니다.  루트가 아닌 사용자를 활성화하는 방법에 대한 자세한 내용은 [루트가 아닌 사용자를 추가하고 sudo 권한 구성](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html)을 참조하세요.

1. 호스트 세부 정보를 검토하고 **제출** 을 선택하여 SnapCenter 서버에 플러그 인을 설치합니다.

1. 플러그 인을 설치한 후, SnapCenter에서 **호스트** 를 선택하고 **+추가** 를 선택하여 HANA 노드를 추가합니다.

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="HANA 노드 추가" lightbox="media/snapcenter/add-hana-node.png":::

1. HANA 노드의 정보를 입력합니다.
   1. 호스트 유형의 운영 체제를 선택합니다.
   1. HANA DB 호스트 이름 또는 IP 주소를 입력합니다.
   1. **+** 기호를 선택하여 HANA DB 호스트 운영 체제에 구성된 자격 증명을 추가하고 **확인** 을 선택합니다.
   1. **SAP HANA** 를 선택하고 **제출** 을 선택합니다.

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="SAP HANA 노드 세부 정보":::

1. 지문을 확인하고 **확인 및 제출** 을 선택합니다.

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="지문 확인 및 제출":::

1. HANA 노드의 시스템 데이터베이스에서 **보안** > **사용자** > **SNAPCENTER** 를 선택하여 SnapCenter 사용자를 만듭니다.

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="HANA(system db)에서 SnapCenter 사용자 만들기":::



### <a name="auto-discovery"></a>자동 검색
SnapCenter 4.3은 기본적으로 자동 검색 기능을 사용합니다.  HSR(HANA System Replication)이 구성된 HANA 인스턴스는 자동 검색을 지원하지 않습니다. SnapCenter 서버에 인스턴스를 수동으로 추가해야 합니다.


### <a name="hana-setup-manual"></a>HANA 설정(수동)
HSR을 구성한 후에는 시스템을 수동으로 구성해야 합니다.  

1. SnapCenter에서 **리소스** 및 **SAN HANA**(맨 위에 있음)를 선택하고, 오른쪽에서 **+SAP HANA 데이터베이스 추가** 를 선택합니다.

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="HANA 수동 설정" lightbox="media/snapcenter/manual-hana-setup.png":::

1. Linux 호스트 또는 플러그 인이 설치된 호스트에서 구성된 HANA 관리자 사용자의 리소스 세부 정보를 지정합니다. 백업은 Linux 시스템의 플러그 인에서 관리됩니다.

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="Linux 호스트에 구성된 HANA 관리자 사용자의 리소스 세부 정보를 지정합니다.":::

1. 스냅샷을 만들어야 하는 데이터 볼륨을 선택하고 **저장** 을 선택한 다음, **마침** 을 선택합니다.

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="스냅샷을 만들어야 하는 데이터 볼륨을 선택하고 [저장]을 선택한 다음, [마침]을 선택합니다.":::

### <a name="create-a-snapshot-policy"></a>스냅샷 정책 만들기

SnapCenter를 사용하여 SAP HANA 데이터베이스 리소스를 백업하려면 먼저 백업하려는 리소스 또는 리소스 그룹에 대한 백업 정책을 만들어야 합니다. 스냅샷 정책을 만드는 과정에서 사전/사후 명령 및 특수 SSL 키를 구성하는 옵션이 제공됩니다. 스냅샷 정책을 만드는 방법에 대한 자세한 내용은 [SAP HANA 데이터베이스에 대한 백업 정책 만들기](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html)를 참조하세요.

1. SnapCenter에서 **리소스** 를 선택하고 데이터베이스를 선택합니다.

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="SnapCenter에서 [리소스]를 선택하고 데이터베이스를 선택합니다.":::

1. 구성 마법사의 워크플로를 따라 스냅샷 스케줄러를 구성합니다.

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="구성 마법사의 워크플로를 따라 스냅샷 스케줄러를 구성합니다." lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. 사전/사후 명령 및 특수 SSL 키를 구성하는 옵션을 제공합니다.  이 예제에서는 특별한 설정을 사용하지 않습니다.

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="사전/사후 명령 및 특수 SSL 키를 구성하는 옵션을 제공합니다." lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. **추가** 를 선택하여 스냅샷 정책을 만듭니다. 이 스냅샷 정책은 다른 HANA 데이터베이스에도 사용할 수 있습니다. 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="[추가]를 선택하여 스냅샷 정책을 만듭니다. 이 스냅샷 정책은 다른 HANA 데이터베이스에도 사용할 수 있습니다.":::

1. 정책 이름 및 설명을 입력합니다.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="정책 이름 및 설명을 입력합니다.":::


1. 백업 유형 및 빈도를 선택합니다.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="백업 유형 및 빈도를 선택합니다.":::

1. **주문형 백업 보존 설정** 을 구성합니다.  이 예제에서는 스냅샷 복사본 3개를 보관하도록 보존을 설정합니다.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="주문형 백업 보존 설정을 구성합니다.":::

1. **시간 단위 보존 설정** 을 구성합니다. 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="시간 단위 보존 설정을 구성합니다.":::

1. SnapMirror 설정을 구성한 경우 **로컬 스냅샷 복사본을 만든 후 SnapMirror 업데이트** 를 선택합니다.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="SnapMirror가 필요한 경우 [로컬 스냅샷 복사본을 만든 후 SnapMirror 업데이트]를 선택합니다.":::

1. **마침** 을 선택하여 새 백업 정책의 요약 정보를 검토합니다. 
1. **일정 구성** 에서 **추가** 를 선택합니다.

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="[일정 구성]에서 [추가]를 선택합니다.":::

1. **시작 날짜**, **만료 날짜** 및 빈도를 선택합니다.

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="[시작 날짜], [만료 날짜] 및 빈도를 선택합니다.":::

1. 알림을 받을 이메일 정보를 제공합니다.

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="알림을 받을 이메일 정보를 제공합니다.":::

1.  **마침** 을 선택하여 백업 정책을 만듭니다.

### <a name="disable-ems-message-to-netapp-autosupport"></a>NetApp Autosupport에 EMS 메시지를 사용하지 않도록 설정
기본적으로 EMS 데이터 수집이 사용되며 설치 날짜 이후 7일마다 실행됩니다.  PowerShell cmdlet `Disable-SmDataCollectionEms`를 사용하여 데이터 수집을 해제할 수 있습니다.

1. PowerShell에서 SnapCenter를 사용하여 세션을 설정합니다.

   ```powershell
   Open-SmConnection
   ```

1. 자격 증명을 사용하여 로그인합니다.
1. EMS 메시지 수집을 비활성화합니다.

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>크래시 후 데이터베이스 복원
SnapCenter를 사용하여 데이터베이스를 복원할 수 있습니다.  이 섹션에서는 개략적인 단계를 다룹니다. 자세한 내용은 [SnapCenter를 사용하여 SAP HANA 백업/복구](https://www.netapp.com/us/media/tr-4614.pdf)를 참조하세요.


1. 데이터베이스를 중지하고 모든 데이터베이스 파일을 삭제합니다.

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. 데이터베이스 볼륨을 분리합니다.

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. SnapCenter를 통해 데이터베이스 파일을 복원합니다.  데이터베이스를 선택하고 **복원** 을 선택합니다.  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="데이터베이스를 선택하고 [복원]을 선택합니다." lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. 복원 유형을 선택합니다.  이 예제에서는 전체 리소스를 복원합니다. 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="복원 유형을 선택합니다.":::

   >[!NOTE]
   >기본 설정을 사용하는 경우 디스크 상의 스냅샷에서 로컬 복원을 수행하는 명령을 지정할 필요가 없습니다. 

   >[!TIP]
   >볼륨 내의 특정 LUN을 복원하려면 **파일 수준** 을 선택합니다.

1. 구성 마법사의 워크플로에 따라 진행합니다.
   
   SnapCenter는 데이터를 원래 위치에 복원하므로 HANA에서 복원 프로세스를 시작할 수 있습니다. 또한 SnapCenter는 백업 카탈로그를 수정할 수 없기 때문에(데이터베이스가 다운됨) 경고가 표시됩니다.

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="SnapCenter는 백업 카탈로그를 수정할 수 없기 때문에 경고가 표시됩니다.":::

1. 모든 데이터베이스 파일이 복원되었으므로 HANA에서 복원 프로세스를 시작합니다. HANA Studio의 **시스템** 에서 시스템 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **백업 및 복구** > **시스템 데이터베이스 복구** 를 선택합니다.

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="HANA에서 복원 프로세스를 시작합니다.":::

1. 복구 유형을 선택합니다.

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="복구 유형을 선택합니다.":::

1. 백업 카탈로그의 위치를 선택합니다.

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="백업 카탈로그의 위치를 선택합니다.":::

1. SAP HANA 데이터베이스를 복구할 백업을 선택합니다.

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="SAP HANA 데이터베이스를 복구할 백업을 선택합니다.":::

   데이터베이스가 복구되면 **복구된 시간** 및 **복구된 로그 위치** 스탬프가 포함된 메시지가 표시됩니다.

1. **시스템** 에서 시스템 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **백업 및 복구** > **테넌트 데이터베이스 복구** 를 선택합니다.
1. 마법사의 워크플로를 따라 테넌트 데이터베이스 복구를 완료합니다. 

데이터베이스 복원에 대한 자세한 내용은 [SnapCenter를 사용하여 SAP HANA 백업/복구](https://www.netapp.com/us/media/tr-4614.pdf)를 참조하세요.


### <a name="non-database-backups"></a>비 데이터베이스 백업
네트워크 파일 공유(/hana/shared) 또는 운영 체제 백업 같은 비 데이터 볼륨을 복원할 수 있습니다.  비 데이터 볼륨의 복원에 대한 자세한 내용은 [SnapCenter를 사용하여 SAP HANA 백업/복구](https://www.netapp.com/us/media/tr-4614.pdf)를 참조하세요.

### <a name="sap-hana-system-cloning"></a>SAP HANA 시스템 복제

복제하려면 원본 데이터베이스와 동일한 HANA 버전이 설치되어 있어야 합니다. SID와 ID는 달라도 됩니다. 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="SAP HANA 시스템 복제" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. /usr/sap/H34/HDB40의 H34 데이터베이스에 대한 HANA 데이터베이스 사용자 저장소를 만듭니다.

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. 방화벽을 해제합니다.

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. Java SDK를 설치합니다.

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. SnapCenter에서 클론을 탑재할 대상 호스트를 추가합니다. 자세한 내용은 [원격 호스트에 호스트 추가 및 플러그 인 패키지 설치](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html)를 참조하세요.
   1. 추가하려는 실행 자격 증명의 정보를 제공합니다. 
   1. 호스트 운영 체제를 선택하고 호스트 정보를 입력합니다.
   1. **설치할 플러그 인** 에서 버전을 선택하고 설치 경로를 입력한 다음, **SAP HANA** 를 선택합니다.
   1. **유효성 검사** 를 선택하여 설치 전 검사를 실행합니다.

1. HANA를 중지하고 이전 데이터 볼륨을 분리합니다.  SnapCenter의 클론을 탑재할 것입니다.  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. 대상에 대한 구성 및 셸 스크립트 파일을 만듭니다.
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >[SnapCenter에서 SAP 복제](https://www.netapp.com/us/media/tr-4667.pdf)의 스크립트를 복사할 수 있습니다.

1. 구성 파일을 수정합니다. 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE="MDC_single_tenant"
   * KEY="H34KEY"
   * TIME_OUT_START=18
   * TIME_OUT_STOP=18
   * INSTANCENO="40"
   * STORAGE="10.250.101.33"

1. 셸 스크립트 파일을 수정합니다.

   ```bash
   vi sc-system-refresh.sh
   ```  

   * VERBOSE=NO
   * MY_NAME="`basename $0`"
   * BASE_SCRIPT_DIR="`dirname $0`"
   * MOUNT_OPTIONS="rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,nolock"

1. 백업 프로세스에서 클론을 시작합니다. 클론을 만들 호스트를 선택합니다. 

   >[!NOTE]
   >자세한 내용은 [백업에서 복제](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html)를 참조하세요.

1. **스크립트** 에서 다음을 입력합니다.

   * **탑재 명령:** /NetApp/sc-system-refresh.sh mount H34 %hana_data_h31_mnt00001_t250_vol_Clone
   * **복제 후 명령:** /NetApp/sc-system-refresh.sh recover H34

1. 사전 설치된 데이터베이스의 데이터 볼륨은 필요 없으므로 /etc/fstab에서 자동 탑재를 사용하지 않도록 설정(잠금)합니다. 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>클론 삭제

더 이상 필요 없는 클론을 삭제할 수 있습니다. 자세한 내용은 [클론 삭제](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html)를 참조하세요.

복제 전 삭제를 실행하는 데 사용되는 명령은 다음과 같습니다.
* **복제 전 삭제:** /NetApp/sc-system-refresh.sh shut down H34
* **분리:** /NetApp/sc-system-refresh.sh umount H34

이러한 명령을 사용하여 SnapCenter에서 데이터베이스를 쇼다운하고, 볼륨을 분리하고, fstab 항목을 삭제할 수 있습니다.  그 후 FlexClone이 삭제됩니다. 

### <a name="cloning-database-logfile"></a>데이터베이스 로그 파일 복제

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>Linux용 SnapCenter 플러그 인 패키지 제거

명령줄에서 Linux 플러그 인 패키지를 제거할 수 있습니다. 자동 배포에는 새 시스템이 필요하므로 플러그 인을 제거하는 것이 쉽습니다.  

>[!NOTE]
>이전 버전의 플러그 인을 수동으로 제거해야 할 수도 있습니다. 

플러그 인을 제거합니다.

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

이제 SnapCenter에서 **제출** 을 선택하여 새 노드에 최신 HANA 플러그 인을 설치할 수 있습니다. 




## <a name="next-steps"></a>다음 단계
- [재해 복구 원칙 및 준비](hana-concept-preparation.md)를 참조하세요.
