---
title: "Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성 및 재해 복구 | Microsoft Docs"
description: "Azure(큰 인스턴스)의 SAP HANA에 대한 고가용성을 달성하고 및 재해 복구를 계획합니다."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f95e944fc3ec3a831d97386443eb644420ae54dc
ms.lasthandoff: 04/03/2017


---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure(큰 인스턴스)의 SAP HANA 고가용성 및 재해 복구 

Azure(큰 인스턴스) 서버에서 업무상 중요한 SAP HANA를 실행할 때 고가용성 및 재해 복구는 중요한 측면입니다. SAP, 시스템 통합업체 또는 Microsoft와 협의하여 적합한 고가용성/재해복구 전략을 설계하고 구현하는 것이 중요합니다. 사용자 환경에 지정된 복구 지점 목표와 복구 시간 목표를 고려하는 것도 중요합니다.

## <a name="high-availability"></a>고가용성

Microsoft에서는 SAP HANA 고가용성 메서드를 "바로" 지원하며 여기에는 다음이 포함됩니다.

- **저장소 복제:** 동일한 데이터 센터 또는 별도의 위치 내에서 다른 위치에 모든 데이터를 복제하는 저장소 시스템의 기능입니다. SAP HANA는 이 메서드와 독립적으로 작동합니다.
- **HANA 시스템 복제:** 별도 SAP HANA 시스템으로 SAP HANA에 있는 모든 데이터를 복제합니다. 복구 시간 목표는 정기적으로 데이터 복제를 통해 최소화됩니다. SAP HANA는 비동기 모드, 메모리 내 동기 모드 및 동기 모드를 지원합니다(동일한 데이터 센터 내에 있거나 100KM 이내에 있는 SAP HANA 시스템에만 권장됨). HANA 큰 인스턴스 스탬프의 현재 디자인에서 고가용성을 위해 HANA 시스템 복제만을 사용할 수 있습니다.
- **자동 장애 조치 호스트:** 시스템 복제하는 대신 사용할 로컬 오류 복구 솔루션입니다. 하나 이상의 대기 SAP HANA 노드를 확장 모드로 구성하고 마스터 노드를 사용할 수 없을 때 SAP HANA를 자동으로 다른 노드에 장애 조치합니다.

SAP HANA 고가용성에 대한 자세한 내용은 다음 SAP 정보를 참조하세요.

- [SAP HANA 고가용성 백서](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 관리 가이드](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA 시스템 복제에 대한 SAP Academy 비디오](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 지원 참고 사항 #1999880 – SAP HANA 시스템 복제에 대한 FAQ](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP 지원 참고 사항 #2165547 – SAP HANA 시스템 복제 환경 내의 SAP HANA 백업 및 복원](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 지원 참고 사항 #1984882 – 가동 중지 시간이 최소/없는 하드웨어 Exchange에 대한 SAP HANA 시스템 복제 사용](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="disaster-recovery"></a>재해 복구

Azure(큰 인스턴스)의 SAP HANA가 지역 정책 지역에 있는 여러 Azure 지역에 제공됩니다. 재해 복구 중 두 개의 서로 다른 지역에 있는 두 개의 큰 인스턴스 스탬프 간에 데이터를 복제하는 네트워크에 직접 연결합니다. 데이터의 복제는 저장소 인프라를 기반으로 합니다. 기본적으로 복제가 수행되지 않습니다. 고객 구성에서 재해 복구를 주문한 경우에 수행됩니다. 현재 디자인에서 재해 복구를 위해 HANA 시스템 복제를 사용할 수 없습니다.

그러나 재해 복구를 활용하기 위해 두 개의 서로 다른 Azure 지역에 대한 네트워크 연결을 디자인하기 시작해야 합니다. 이를 위해 사용자의 기본 Azure 지역에 있는 온-프레미스에서 Azure ExpressRoute 회로 연결 및 온-프레미스에서 사용자의 재해 복구 지역에 다른 회로 연결이 필요합니다. 이러한 측정값은 MSEE(Microsoft Enterprise Edge 라우터) 위치를 포함하는 전체 Azure 지역에 문제가 발생한 상황에 적용됩니다.

두 번째 측정값으로 해당 ExpressRoute 회로 모두에 대한 지역 중 하나에 있는 Azure(큰 인스턴스)의 SAP HANA에 연결된 모든 Azure 가상 네트워크를 연결할 수 있습니다. 이 측정값은 Azure와 온-프레미스 위치를 연결하는 MSEE 위치 중 하나가 작동하지 않는 경우에 문제를 해결합니다.

다음 그림에서는 재해 복구에 대한 최적의 구성을 보여 줍니다.

![재해 복구에 대한 최적의 구성](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

네트워크 재해 복구 구성에 대한 최적의 사례는 온-프레미스에서 두 개의 다른 Azure 지역까지 두 개의 ExpressRoute 회로를 배치하는 것입니다. 하나의 회로는 #1 지역으로 연결되어 프로덕션 인스턴스를 실행합니다. 두 번째 ExpressRoute 회로는 #2 지역으로 이동하여 일부 비프로덕션 HANA 인스턴스를 실행합니다. (이것은 MSEE 및 큰 인스턴스의 스탬프를 비롯한 전체 Azure 지역이 그리드를 중단하는 경우에 중요합니다.)

두 번째 측정값으로 다양한 가상 네트워크는 Azure(큰 인스턴스)의 SAP HANA에 연결되어 있는 다양한 ExpressRoute 회로에 연결됩니다. 나중에 설명된 대로 MSEE가 실패한 위치를 무시하거나 재해 복구에 대한 복구 지점 목표를 낮출 수 있습니다.

재해 복구 설정에 대한 이후 요구 사항은 다음과 같습니다.

- 사용자의 프로덕션 SKU와 동일한 크기인 Azure(큰 인스턴스)의 SAP HANA를 주문하고 재해 복구 지역에서 배포해야 합니다. 이러한 인스턴스를 사용하여 테스트, 샌드박스 또는 QA HANA 인스턴스를 실행할 수 있습니다.
- 필요한 경우 재해 복구 사이트에서 복구하려는 사용자의 Azure(큰 인스턴스)의 SAP HANA SKU 각각에 대한 재해 복구 프로필을 주문해야 합니다. 이 작업으로 인해 프로덕션 지역에서 재해 복구 지역까지 저장소 복제의 대상인 저장소 볼륨을 할당하게 됩니다.

앞의 요구 사항을 충족한 후에 사용자는 저장소 복제를 시작해야 합니다. Azure(큰 인스턴스)의 SAP HANA에 사용되는 저장소 인프라에서 저장소 복제의 기본은 저장소 스냅숏입니다. 재해 복구 복제를 시작하려면 다음을 수행해야 합니다.

- 앞서 설명한 대로 부팅 LUN의 스냅숏
- 앞서 설명한 대로 HANA 관련 볼륨의 스냅숏

이러한 스냅숏을 실행한 후에 재해 복구 지역에 있는 재해 복구 프로필과 연결된 볼륨에 볼륨의 초기 복제본이 시드됩니다.

이후에 최신 저장소 스냅숏은 1시간마다 저장소 볼륨을 개발하는 델타를 복제하는 데 사용됩니다.

이 구성을 사용하여 달성된 복구 지점 목표는 60에서 90분입니다. 재해 복구 사례에서 복구 지점 목표를 향상시키려면 Azure(큰 인스턴스)의 SAP HANA에 있는 HANA 트랜잭션 로그 백업을 다른 Azure 지역에 복사합니다. 이 복구 지점 목표를 달성하려면 다음을 수행합니다.

1. HANA 트랜잭션 로그를 가능한 한 자주 /hana/log/backup에 백업합니다.
2. Azure VM(가상 컴퓨터)에 완료되는 경우 트랜잭션 로그 백업을 복사합니다. 해당 항목은 Azure(큰 인스턴스)의 SAP HANA 서버에 연결된 가상 네트워크에 있습니다.
3. 해당 VM에서 재해 복구 지역에 있는 가상 네트워크의 VM에 백업을 복사합니다.
4. VM의 해당 지역에서 트랜잭션 로그 백업을 유지합니다.

재해 복구 프로필이 실제 서버에 배포된 후에 재해 발생 시 VM에서 Azure(큰 인스턴스)의 SAP HANA로 트랜잭션 로그 백업을 복사합니다. 해당 위치는 이제 재해 복구 지역의 주 서버로 해당 백업을 복원합니다. 이 복구는 재해 복구 디스크에 대한 HANA의 상태는 HANA 스냅숏의 상태이기 때문에 가능합니다. 트랜잭션 로그 백업의 추가 복원에 대한 오프셋 지점입니다.

## <a name="backup-and-restore"></a>백업 및 복원

운영 데이터베이스에 대한 가장 중요한 측면 중 하나는 다양한 치명적인 이벤트에서 데이터베이스를 보호할 수 있다는 점입니다. 자연 재해부터 간단한 사용자 오류까지 다양한 원인으로 인해 이러한 이벤트가 발생할 수 있습니다.

지정 시점(예: 누군가가 중요한 데이터를 삭제하기 전에)으로 복원하는 기능을 사용하여 데이터베이스를 백업하면 중단이 발생하기 전의 방식에 가장 가까운 상태로 복원할 수 있습니다.

최상의 결과를 위해 두 가지 백업을 수행해야 합니다.

- 데이터베이스 백업
- 트랜잭션 로그 백업

응용 프로그램 수준에서 수행되는 전체 데이터베이스 백업 외에도 저장소 스냅숏에 백업을 수행하여 더욱 철저해질 수 있습니다. 로그 백업을 수행하는 작업은 데이터베이스 복원에 중요하고 커밋된 트랜잭션에서 로그를 비우게 됩니다.

Azure(큰 인스턴스)의 SAP HANA는 다음과 같은 두 개의 백업과 옵션을 제공합니다.

- DIY(Do It Yourself) 디스크 공간이 충분한지 계산한 후에 해당 디스크에 대한 디스크 백업 메서드를 사용하여 전체 데이터베이스 및 로그 백업을 수행합니다. 사실상 무제한인 저장소로 Azure 기반 파일 서버를 설정한 후에 시간이 지남에 따라 백업은 Azure Storage 계정에 복사되거나 Azure Backup Vault 또는 Azure 콜드 저장소를 사용합니다. 또 다른 옵션은 백업을 저장소 계정에 복사한 후에 저장하기 위해 Commvault와 같은 타사 데이터 보호 도구를 사용하는 것입니다. DIY 백업 옵션은 준수 및 감사 목적으로 인해 장기간 저장되어야 하는 데이터에 필요할 수도 있습니다.
- Azure(큰 인스턴스)의 SAP HANA 기본 인프라가 제공하는 백업 및 복원 기능을 사용합니다. 이 옵션은 백업에 대한 필요성을 충족하고 데이터 백업이 규정 준수를 위해 필요한 경우를 제외하고 수동 백업을 거의 사용하지 않습니다. 이 섹션의 나머지 부분에서는 HANA 큰 인스턴스와 함께 제공되는 백업 및 복원 기능을 설명합니다.

> [!NOTE]
> HANA(큰 인스턴스)의 기본 인프라에서 사용하는 스냅숏 기술은 SAP HANA 스냅숏에 대한 종속성을 갖습니다. SAP HANA 스냅숏은 SAP HANA 다중 테넌트 데이터베이스 컨테이너와 함께 작동하지 않습니다. 결과적으로, SAP HANA 다중 테넌트 데이터베이스 컨테이너를 배포하는 데 백업의 메서드를 사용할 수 없습니다.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA 저장소 스냅숏 사용

Azure(큰 인스턴스)의 SAP HANA에 기반한 저장소 인프라는 볼륨 저장소 스냅숏의 개념을 지원합니다. 특정 볼륨의 백업 및 복원은 모두 다음 고려 사항으로 지원됩니다.

- 데이터베이스 백업 대신 저장소 볼륨 스냅숏을 자주 사용합니다.
- 저장소 스냅숏은 저장소 스냅숏을 실행하기 전에 SAP HANA 스냅숏을 시작합니다. 이 SAP HANA 스냅숏은 저장소 스냅숏을 복구한 후에 최종 로그 복원에 대한 설치 지점입니다.
- 저장소 스냅숏이 성공적으로 실행되는 시점에서 SAP HANA 스냅숏이 삭제됩니다.
- 로그 백업은 자주 사용되며 로그 백업 볼륨 또는 Azure에 저장됩니다.
- 데이터베이스를 특정 시점으로 복원해야 하는 경우 특정 저장소 스냅숏을 복원(예: 샌드박스 시스템을 원래 상태로의 계획된 복원)하도록 Microsoft Azure 지원(프로덕션 작동 중단) 또는 Azure의 SAP HANA Service Management에 대한 요청이 이루어집니다.
- 저장소 스냅숏에 포함된 SAP HANA 스냅숏은 저장소 스냅숏을 만든 후에 실행되고 저장된 로그 백업에 적용하는 오프셋 지점입니다.
- 이러한 로그 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원합니다.

backup\_name을 지정하면 다음 볼륨의 스냅숏을 만듭니다.

- hana/data
- hana/log
- hana/log\_backup(hana/log에 백업으로 탑재됨)
- hana/shared

### <a name="storage-snapshot-considerations"></a>저장소 스냅숏 고려 사항

>[!NOTE]
>추가 저장소 공간이 할당되어야 하기 때문에 저장소 스냅숏은 무료로 제공되지 _않습니다_.

Azure(큰 인스턴스)의 SAP HANA에 대한 저장소 스냅숏의 특정 역학에는 다음이 포함됩니다.

- 만들어진 시점에서 특정 저장소 스냅숏은 아주 작은 저장소를 사용합니다.
- SAP HANA 데이터 파일의 데이터 콘텐츠 변경 내용 및 콘텐츠는 저장소 볼륨에서 변경되고 스냅숏은 원래 블록 콘텐츠를 저장해야 합니다.
- 저장소 스냅숏의 크기가 증가합니다. 스냅숏이 오래 존재할수록 저장소 스냅숏은 커집니다.
- 스토리지 스냅샷의 수명 동안 SAP HANA 데이터베이스 볼륨에 변경 내용이 많을수록 스토리지 스냅샷이 소진하는 공간이 커집니다.

Azure(큰 인스턴스)의 SAP HANA는 SAP HANA 데이터 및 로그 볼륨에 대한 고정된 볼륨 크기로 제공됩니다. 해당 볼륨의 스냅숏을 수행하면 사용자의 볼륨 공간을 사용하게 되므로 Azure의 SAP HANA [큰 인스턴스] 프로세스 내에서 저장소 스냅숏을 예약해야 합니다.

다음 섹션에서는 일반적인 권장 사항을 비롯한 이러한 스냅숏을 수행하는 방법에 대한 정보를 제공합니다.

- 하드웨어가 볼륨당 255개의 스냅숏을 유지할 수 있지만 이 숫자보다 훨씬 낮게 유지하는 것이 좋습니다.
- 스냅숏 저장소를 수행하기 전에 여유 공간을 모니터링하고 추적합니다.
- 사용 가능한 공간에 따라 저장소 스냅숏의 수를 줄입니다. 유지할 수 있는 스냅숏 수를 줄이거나 볼륨을 확장해야 할 수 있습니다. (추가 저장소를 1TB 단위로 주문할 수 있습니다.)
- 시스템 마이그레이션 도구(R3load 또는 백업에서 SAP HANA 데이터베이스 복원)를 사용하여 SAP HANA에 데이터를 이동하는 것과 같은 작업 중에 저장소 스냅숏을 수행하지 않는 것이 좋습니다. (시스템 마이그레이션이 새 SAP HANA 시스템에서 수행되는 경우 저장소 스냅숏을 수행할 필요가 없습니다.)
- SAP HANA 테이블을 크게 재구성하는 동안 저장소 스냅숏을 가능하면 사용하지 않아야 합니다.
- 저장소 스냅숏은 Azure(큰 인스턴스)의 SAP HANA 중 재해 복구 기능을 수행하는 필수 구성 요소입니다.

### <a name="setting-up-storage-snapshots"></a>저장소 스냅숏 설정

1. Perl이 HANA(큰 인스턴스) 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다.
2. /etc/ssh/ssh\_config를 수정하여 _MACs hmac-sha1_ 줄을 추가합니다.
3. 적용할 수 있는 경우 실행하는 SAP HANA 인스턴스마다 마스터 노드에서 SAP HANA 백업 사용자 계정을 만듭니다.
4. SAP HANA HDB 클라이언트는 모든 SAP HANA(큰 인스턴스) 서버에 설치되어야 합니다.
5. 각 지역의 첫 번째 SAP HANA(큰 인스턴스) 서버에서 스냅숏 생성을 제어하는 기본 저장소 인프라에 액세스하기 위해 공개 키를 만들어야 합니다.
6. azure\_hana\_backup.pl 스크립트를 /scripts에서 SAP HANA 설치의 **hdbsql** 위치에 복사합니다.
7. HANABackupDetails.txt 파일을 /scripts에서 Perl 스크립트와 동일한 위치에 복사합니다.
8. 적절한 고객 사양에 대한 필요에 따라 HANABackupDetails.txt 파일을 수정합니다.

### <a name="step-1-install-sap-hana-hdbclient"></a>1단계: SAP HANA HDBClient 설치

Azure(큰 인스턴스)의 SAP HANA에 설치된 Linux는 백업 및 재해 복구를 위해 SAP HANA 저장소 스냅숏을 실행하는 데 필요한 폴더와 스크립트를 포함합니다. 그러나 SAP HANA를 설치하는 동안 SAP HANA HDBclient를 설치해야 합니다. (Microsoft에서는 HDBclient나 SAP HANA를 모두 설치하지 않습니다.)

### <a name="step-2-change-etcsshsshconfig"></a>2단계: /etc/ssh/ssh\_config 변경

여기에 표시된 _MACs hmac-sha1_ 줄을 추가하여 /etc/ssh/ssh\_config를 변경합니다.
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>3단계: 공개 키 만들기

각 Azure 지역의 첫 번째 Azure(큰 인스턴스)의 SAP HANA 서버에서 스냅숏을 만들 수 있도록 저장소 인프라에 액세스하는 데 사용되는 공개 키를 만들어야 합니다. 공개 키를 통해 저장소에 로그인하는 데 암호를 요구하지 않도록 하면 해당 암호 자격 증명이 유지되지 않습니다. SAP HANA(큰 인스턴스) 서버의 Linux에서 공개 키를 생성하려면 다음 명령을 실행합니다.
```
  ssh-keygen –t dsa –b 1024
```
새 위치는 _/root/.ssh/id\_dsa.pub입니다. 실제 암호를 입력하지 마세요. 그렇지 않으면 로그인할 때마다 암호를 입력해야 합니다. 대신, **Enter**를 두 번 눌러서 로그인에 대한 입력 암호 요구 사항을 제거합니다.

폴더를 /root/.ssh/로 변경한 다음 **ls** 명령을 실행하여 예상 대로 공개 키가 수정되었는지 확인해야 합니다. 키가 있는 경우 다음 명령을 실행하여 복사할 수 있습니다.

![이 명령을 실행하여 공개 키를 복사합니다.](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

이 시점에서 Azure Service Management의 SAP HANA에 문의하고 키를 제공합니다. 서비스 담당자는 공개 키를 사용하여 기본 저장소 인프라에 등록합니다.

### <a name="step-4-create-an-sap-hana-user-account"></a>4단계: SAP HANA 사용자 계정 만들기

백업을 목적으로 SAP HANA Studio 내에서 SAP HANA 사용자 계정을 만듭니다. 이 계정에는 _백업 관리_ 및 _카탈로그 읽기_ 권한이 있어야 합니다. 이 예제에서는 사용자 이름 SCADMIN을 만듭니다.

![HANA Studio에서 사용자 만들기](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>5단계: SAP HANA 사용자 계정 권한 부여

스크립트를 실행할 때마다 권한 부여를 요구하지 않고 스크립트에 의해 사용되는 SAP HANA 사용자 계정에 권한을 부여합니다. SAP HANA 명령 `hdbuserstore`을 사용하면 하나 이상의 SAP HANA 노드에 저장되는 SAP HANA 사용자 키를 만들 수 있습니다. 또한 사용자 키를 사용하면 나중에 설명할 스크립팅 프로세스 내에서 암호를 관리하지 않고도 SAP HANA에 액세스할 수 있습니다.

>[!IMPORTANT]
>다음 명령을 `_root_`로 실행합니다. 그렇지 않으면 스크립트가 제대로 작동할 수 없습니다.

`hdbuserstore` 명령을 다음과 같이 입력합니다.

![hdbuserstore 명령을 입력합니다.](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

사용자가 SCADMIN01이고 호스트 이름이 lhanad01인 다음 예제에서 명령은 다음과 같습니다.
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
확장 HANA 인스턴스의 단일 서버에서 모든 스크립트를 관리합니다. 이 예제에서는 키에 관련된 호스트를 반영하는 방식으로 각 호스트에 대해 SAP HANA 키 SCADMIN01를 변경해야 합니다. 즉, SAP HANA 백업 계정은 **lhanad**라는 HANA DB의 인스턴스 번호를 사용하여 수정됩니다. 키는 할당된 호스트에 대한 관리자 권한이 있어야 하며, 확장에 대한 백업 사용자는 모든 SAP HANA 인스턴스에 대한 액세스 권한이 있어야 합니다.
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

### <a name="step-6-copy-items-from-the-scripts-folder"></a>6단계: /scripts 폴더에서 항목 복사

설치의 골드 이미지에 포함된 /scripts 폴더에서 **hdbsql**에 대한 작업 디렉터리까지 다음 항목을 복사합니다. 현재 HANA 설치의 경우 이 디렉터리는 /hana/shared/D01/exe/linuxx86\_64/hdb입니다.
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
확장 또는 OLAP를 실행 중인 경우 다음 항목을 복사합니다.
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
HANABackupCustomerDetails.txt 파일은 강화 배포의 경우 다음과 같이 수정할 수 있습니다. 저장소 스냅숏을 실행하는 스크립트의 컨트롤 및 구성 파일입니다. 사용자의 인스턴스를 배포한 Azure의 SAP HANA Service Management에서 _저장소 백업 이름_ 및 _저장소 IP 주소_를 수신했어야 합니다. 변수의 시퀀스, 순서 또는 간격을 수정할 수 없습니다. 그렇지 않으면 스크립트가 제대로 실행되지 않습니다.

강화 배포의 경우 구성 파일은 다음과 같습니다.
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Created by customer using hdbuserstore
HANA Backup Name: SCADMIND01
```
확장 구성의 경우 HANABackupCustomerDetailsBW.txt 파일은 다음과 같습니다.
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 10.254.15.21
Node 1 HANA instance number: 01
Node 1 HANA Backup Name: SCADMIN01
Node 2 IP Address: 10.254.15.22
Node 2 HANA instance number: 02
Node 2 HANA Backup Name: SCADMIN02
Node 3 IP Address: 10.254.15.23
Node 3 HANA instance number: 03
Node 3 HANA Backup Name: SCADMIN03
Node 4 IP Address: 10.254.15.24
Node 4 HANA instance number: 04
Node 4 HANA Backup Name: SCADMIN04
Node 5 IP Address: 10.254.15.25
Node 5 HANA instance number: 05
Node 5 HANA Backup Name: SCADMIN05
Node 6 IP Address: 10.254.15.26
Node 6 HANA instance number: 06
Node 6 HANA Backup Name: SCADMIN06
Node 7 IP Address: 10.254.15.27
Node 7 HANA instance number: 07
Node 7 HANA Backup Name: SCADMIN07
Node 8 IP Address: 10.254.15.28
Node 8 HANA instance number: 08
Node 8 HANA Backup Name: SCADMIN08
```
>[!NOTE]
>현재 노드 1 세부 정보만 실제 HANA 저장소 스냅숏 스크립트에 사용됩니다. 마스터 백업 노드가 변경되는 경우 노드 1의 세부 정보를 수정하여 다른 노드가 해당 위치를 사용할 수 있도록 모든 HANA 노드 간의 액세스를 테스트하는 것이 좋습니다.

구성 파일에서 올바른 구성 또는 HANA 인스턴스에 대한 적절한 연결을 확인하려면 다음 스크립트 중 하나를 실행합니다.
- 강화 구성의 경우(SAP 워크로드에 독립적임):

 ```
testHANAConnection.pl
```
- 확장 구성의 경우:

 ```
testHANAConnectionBW.pl
```

마스터 HANA 인스턴스에 모든 필수 HANA 서버에 대한 액세스 권한이 있는지 확인합니다. 스크립트에 대한 매개 변수가 없지만 스크립트를 적절하게 실행하려면 HANABackupCustomerDetails/ HANABackupCustomerDetailsBW 파일을 완료해야 합니다. 셸 명령 오류 코드만 반환되기 때문에 스크립트가 모든 인스턴스의 오류를 검사하는 것은 불가능합니다. 이런 경우에도 스크립트는 다시 확인하기 위한 몇 가지 유용한 설명을 제공하지 않습니다.

스크립트를 실행하려면
```
 ./testHANAConnection.pl
```
 스크립트가 HANA 인스턴스의 상태를 가져오는 데 성공한 경우 HANA 연결이 성공적이라는 메시지를 출력합니다.

또한 저장소에 로그인하는 마스터 HANA 인스턴스 서버의 기능을 확인하는 데 사용할 수 있는 스크립트의 두 번째 유형이 있습니다. azure\_hana\_backup(\_bw).pl 스크립트를 실행하기 전에 다음 스크립트를 실행해야 합니다. 볼륨에 스냅숏이 없는 경우 볼륨이 단순히 비어 있는지 아니면 스냅숏 정보를 포함한 ssh 오류가 있는지를 결정하는 것이 불가능합니다. 이러한 이유로 스크립트는 다음 두 단계를 실행합니다.

- 저장소 콘솔에 액세스할 수 있는지 확인합니다.
- HANA 인스턴스에서 각 볼륨의 테스트, 더미 또는 스냅숏을 만듭니다.

이러한 이유로 HANA 인스턴스를 인수로 포함합니다. 마찬가지로 저장소 연결에 대한 오류 검사를 제공할 수 없지만 실행이 실패하는 경우 스크립트는 유용한 힌트를 제공합니다.

스크립트는 다음의 경우 실행됩니다.
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
또는 다음의 경우 실행됩니다.
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
또한 스크립트는 배포된 저장소 테넌트에 적절하게 로그인할 수 있다는 메시지를 표시합니다. 해당 테넌트는 사용자가 소유한 서버 인스턴스에서 사용하는 LUN(논리적 단위 수)으로 구성됩니다.

첫 번째 저장소 스냅숏 기반 백업을 실행하기 전에 이러한 스크립트를 실행하여 구성이 올바른지 확인합니다.

이러한 스크립트를 실행한 후에 다음을 실행하여 스냅숏을 삭제할 수 있습니다.
```
./removeTestStorageSnapshot.pl <hana instance>
```
또는
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="step-7-perform-on-demand-snapshots"></a>7단계: 주문형 스냅숏 수행

여기에서 설명한 대로 주문형 스냅숏을 수행할 뿐만 아니라 cron를 사용하여 일반 스냅숏을 예약합니다.

강화 구성의 경우 다음 스크립트를 실행합니다.
```
./azure_hana_backup.pl lhanad01 customer 20
```
확장 구성의 경우 다음 스크립트를 실행합니다.
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
확장 스크립트는 모든 HANA 서버에 액세스할 수 있도록 추가 검사를 수행하고, 모든 HANA 인스턴스는 SAP HANA 또는 저장소 스냅숏을 계속 만들기 전에 적절한 인스턴스 상태를 반환합니다.

다음과 같은 인수가 필요합니다.

- HANA 인스턴스 요구 백업
- 저장소 스냅숏의 스냅숏 접두사
- 특정 접두사에 대해 유지되는 스냅숏 개수

```
./azure_hana_backup.pl lhanad01 customer 20
```

스크립트를 실행하면 이러한 세 가지 단계에서 저장소 스냅숏을 만듭니다.

- HANA 스냅숏을 실행합니다.
- 저장소 스냅숏을 실행합니다.
- HANA 스냅숏을 제거합니다.

스크립트가 복사되었던 HDB 실행 파일 폴더에서 호출하여 실행합니다. 최소한 다음 볼륨을 백업하지만 볼륨 이름에 명시적 SAP HANA 인스턴스 이름을 가진 모든 볼륨도 백업합니다.
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
보존 기간은 스크립트를 실행할 경우 매개 변수로 제출되는 스냅숏 개수와 함께 엄격하게 관리됩니다(예: 20처럼 이전에 표시됨). 따라서 시간의 양은 스크립트의 호출에서 실행 기간 및 스냅숏 개수의 함수입니다. 유지되는 스냅숏 개수가 스냅숏 스크립트의 호출에서 매개 변수로 이름이 지정된 수를 초과하면 새 스냅숏을 실행하기 전에 이 레이블의 가장 오래된 저장소가 삭제됩니다(앞의 경우 _사용자 지정_). 즉, 호출의 마지막 매개 변수로 지정한 숫자는 스냅숏 개수를 제어하는 데 사용할 수 있는 숫자입니다.

>[!NOTE]
>레이블을 변경하면 즉시 계수가 다시 시작됩니다.

다중 노드 환경에서 스냅숏을 만드는 경우 Azure의 SAP HANA Service Management에서 인수로 제공하는 HANA 인스턴스 이름을 포함해야 합니다. 단일 노드 환경에서 Azure(큰 인스턴스)의 SAP HANA 단위의 이름도 충분하지만 HANA 인스턴스 이름을 사용하는 것이 좋습니다.

또한 동일한 스크립트를 사용하여 부팅 volumes\LUNs를 백업할 수 있습니다. 처음 HANA를 실행하는 경우 한 번 이상 부팅 볼륨을 백업해야 합니다. 하지만 cron의 부팅에 대해 주간 또는 야간 백업 일정을 사용하는 것이 좋습니다. SAP HANA 인스턴스 이름을 추가하는 대신 다음과 같이 스크립트에 _부팅_을 인수로 삽입합니다.
```
./azure_hana_backup boot customer 20
```
부팅 볼륨에도 동일한 보존 정책을 제공합니다. 앞에 설명한 대로 특별한 경우에만 주문형 스냅숏을 사용합니다(예: SAP EHP(Enhancement Package) 업그레이드 중 또는 고유한 저장소 스냅숏을 만들어야 하는 경우).

cron를 사용하여 예약된 저장소 스냅숏을 수행하는 것이 좋으며, 모든 백업 및 재해 복구 요구 사항에 동일한 스크립트를 사용하는 것이 좋습니다(요청된 다른 백업 시간과 일치하도록 스크립트 입력 수정). 해당 실행 시간(매시간, 12시간, 매일 또는 매주)에 따라 cron에서 모두 다르게 예약됩니다. 장기 오프사이트 백업의 경우 cron 일정은 앞에 설명한 보존 레이블과 일치하는 저장소 스냅숏을 만들도록 설계되었습니다. 스크립트는 요청된 해당 빈도에 따라 모든 프로덕션 볼륨을 백업하는 명령을 포함합니다. 데이터 및 로그 파일이 매시간 백업되는 반면 부팅 볼륨은 매일 백업됩니다.

다음 cron 스크립트에 있는 항목은 매시간 10분 단위, 12시간마다 10분 단위 및 매일 10분 단위로 실행됩니다. cron 작업은 특정 시간 동안 하나의 SAP HANA 저장소 스냅숏을 만드는 방식으로 만들어집니다. 따라서 매시간 및 매일 백업은 동시에(오전 12시 10분) 발생하지 않습니다. Azure의 SAP HANA Service Management에서는 스냅숏 생성 및 복제를 최적화하기 위해 백업을 실행하는 사용자에게 권장된 시간을 제공합니다.

/etc/crontab에서 예약된 기본 cron은 다음과 같습니다.
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
앞의 cron 지침에서 부팅 볼륨 없는 HANA 볼륨은 레이블의 스냅숏을 매시간 가져옵니다. 이러한 66개의 스냅숏이 유지됩니다. 또한 12시간 레이블을 포함한 14 스냅숏이 유지됩니다. 잠재적으로 3일 간의 매시간 스냅숏에 추가로 4일 간의 12시간 스냅숏을 얻게 되어 한 주 간의 스냅숏을 제공합니다.

스크립트가 몇 분 사이에 엇갈리게 배치되지 않는 한 특정 시간에 하나의 스크립트만을 실행해야 하기 때문에 cron 내에서 예약 작업은 복잡할 수 있습니다. 매일 백업을 장기 보존하려는 경우 각각 보존 개수가 7인 매일 스냅숏을 12시간 스냅숏과 함께 유지하거나 매시간 스냅숏을 10분 후에 만들도록 지연합니다. 프로덕션 볼륨에는 하나의 매일 스냅숏만이 유지됩니다.
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
여기에 나열된 빈도는 예제일 뿐입니다. 스냅숏의 최적수를 알아내려면 다음 조건을 사용합니다.

- 지정 시간 복구에 대한 복구 시간 목표의 요구 사항
- 공간 사용
- 잠재적 재해 복구를 위한 복구 지점 목표 및 복구 시간 목표의 요구 사항
- 디스크에 대한 HANA 전체 데이터베이스 백업의 최종 실행 디스크 또는 _backint_ 인터페이스에 대한 전체 데이터베이스 백업이 실행되는 경우 저장소 스냅숏의 실행에 실패합니다. 저장소 스냅숏을 기반으로 전체 데이터베이스 백업을 실행하려는 경우 이 시간 동안 저장소 스냅숏이 실행되지 않는지 확인합니다.

>[!IMPORTANT]
> SAP HANA 백업에 대한 저장소 스냅숏 사용은 스냅숏이 SAP HANA 로그 백업과 함께 수행된 경우에만 유효합니다. 이러한 로그 백업은 저장소 스냅숏 간에 기간에 적용되어야 합니다. 30일이라는 지정 시간 복구의 사용자에 대한 커밋을 설정한 경우 다음 항목이 필요합니다.

- 30일이 지난 저장소 스냅숏에 액세스하는 기능
- 지난 30일 동안의 연속 로그 백업

로그 백업의 범위에서 백업 로그 볼륨의 스냅숏도 만듭니다. 그러나 다음을 수행할 수 있도록 정기적인 로그 백업을 수행해야 합니다.

- 지정 시간 복구를 수행하는 데 필요한 연속 로그 백업을 보유합니다.
- SAP HANA 로그 볼륨의 공간이 부족하지 않도록 방지합니다.

마지막 단계 중 하나는 SAP HANA Studio에서 SAP HANA 백업 로그를 예약하는 것입니다. SAP HANA 백업 로그 대상 위치는 탑재 지점이 /hana/log/backups인 특별히 만든 hana/log\_backups 백업 볼륨입니다.

![SAP HANA Studio에서 SAP HANA 백업 로그 예약](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15분보다 더 자주 수행된 백업을 선택할 수 있습니다. 일부 사용자도 1분마다 로그 백업을 수행할 수 있지만 15분 _이상_은 권장하지 않습니다.

최종 단계는 SAP HANA의 초기 설치 후에 파일 기반 백업을 수행하여 백업 카탈로그 내에 있어야 하는 단일 백업 항목을 만드는 것입니다. 그렇지 않으면 SAP HANA는 지정된 로그 백업을 시작할 수 없습니다.

![단일 백업 항목을 만드는 파일 기반 백업 수행](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>디스크 볼륨에서 스냅숏의 개수 및 크기 모니터링

특정 저장소 볼륨에서 스냅숏의 개수 및 스냅숏의 저장소 사용을 모니터링할 수 있습니다. `ls` 명령은 스냅숏 디렉터리 또는 파일을 표시하지 않습니다. 그러나 Linux OS 명령인 `du`는 다음 명령을 사용하여 해당 항목을 표시합니다.

- `du –sh .snapshot`는 스냅숏 디렉터리 내에서 모든 스냅숏을 제공합니다.
- `du –sh --max-depth=1`는 .snapshot 폴더에 저장된 모든 스냅숏 및 각 스냅숏의 크기를 나열합니다.
- `du –hc`는 모든 스냅숏에서 사용하는 전체 크기를 제공합니다.

이러한 명령을 사용하여 만들고 저장한 스냅숏이 볼륨에서 모든 저장소를 사용하지 않도록 합니다.

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>서버에서 스냅숏 개수 감소

앞에서 설명한 대로 사용자가 저장한 스냅숏의 특정 레이블 수를 줄일 수 있습니다. 스냅숏을 시작하는 명령의 마지막 두 매개 변수는 레이블과 보존할 스냅숏 개수입니다.
```
./azure_hana_backup.pl lhanad01 customer 20
```
앞의 예제에서 스냅숏 레이블은 _고객_이고, 보존할 이 레이블의 스냅숏 개수는 _20_입니다. 디스크 공간 사용량에 응답하는 대로 저장된 스냅숏 수를 줄이려고 할 수 있습니다. 스냅숏 수를 줄이는 가장 좋은 방법은 마지막 매개 변수를 5로 설정한 스크립트를 실행하는 것입니다.
```
./azure_hana_backup.pl lhanad01 customer 5
```
이 설정으로 스크립트를 실행한 결과로 새 저장소 스냅숏을 포함한 스냅숏 개수는 _5_입니다.

 >[!NOTE]
 > 이 스크립트는 가장 최근의 이전 스냅숏이 한 시간 이전인 경우에만 스냅숏 수를 줄입니다. 스크립트는 한 시간이 되지 않은 스냅숏을 삭제하지 않습니다.

이러한 제한 사항은 제공되는 선택적 재해 복구 기능과 관련되어 있습니다.

해당 접두사를 사용하는 스냅숏 집합을 더 이상 유지하지 않으려면 _0_을 포함하는 스크립트를 보존 숫자로 실행하여 해당 접두사와 일치하는 모든 스냅숏을 제거할 수 있습니다. 그러나 모든 스냅숏을 제거하면 재해 복구의 기능에 영향을 미칠 수 있습니다.

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>가장 최근 HANA 스냅숏으로 복구

프로덕션 중단 시나리오를 경험한 이벤트에서 Azure의 SAP HANA Service Management를 사용한 고객 인시던트처럼 저장소 스냅숏에서 복구하는 프로세스를 시작할 수 있습니다. 예기치 않은 시나리오는 데이터가 프로덕션 시스템에서 삭제되고 데이터를 다시 가져올 유일한 방법이 프로덕션 데이터베이스를 복원하는 것인 경우 긴급한 문제가 될 수 있습니다.

반면에 지정 시간 복구는 낮은 긴급도로 몇 일 앞서 계획될 수도 있습니다. 우선 순위 문제를 발생시키는 대신 Azure의 SAP HANA Service Management를 사용하여 이 복구를 계획할 수 있습니다. 예를 들어, 새로운 Enhancement Package를 적용하여 SAP 소프트웨어의 업그레이드를 시도하려는 경우 EHP 업그레이드 전에 상태를 나타내는 스냅숏으로 다시 전환해야 합니다.

요청을 실행하기 전에 몇 가지 준비를 수행해야 합니다. Azure의 SAP HANA Service Management 팀은 요청을 처리하고 복원된 볼륨을 제공할 수 있습니다. 나중에 사용자는 스냅숏을 기반으로 HANA 데이터베이스를 복원할 수 있습니다. 요청에 대해 준비하는 방법은 다음과 같습니다.

>[!NOTE]
>사용자 인터페이스는 사용하는 SAP HANA 릴리스에 따라 다음 스크린샷에서 달라질 수 있습니다.

1. 복원할 스냅숏을 결정합니다. 달리 지시하지 않으면 hana/data 볼륨은 복원됩니다.

2. HANA 인스턴스를 종료합니다.

 ![HANA 인스턴스를 종료합니다.](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 각 HANA 데이터베이스 노드에서 데이터 볼륨을 분리합니다. 데이터 볼륨이 탑재되지 않은 경우 스냅숏 복원에 실패합니다.

 ![각 HANA 데이터베이스 노드에서 데이터 볼륨 분리](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Azure 지원 요청을 열어서 특정 스냅숏을 복원하도록 지시합니다.

 - 복원 중 Azure의 SAP HANA Service Management는 데이터를 손실하지 않도록 전화 회의에 참석하도록 요청할 수도 있습니다.

 - 복원한 후에 Azure의 SAP HANA Service Management에서는 저장소 스냅숏이 복원된 시기를 알립니다.

5. 복원 프로세스가 완료된 후에 모든 데이터 볼륨을 다시 탑재합니다.

 ![모든 데이터 볼륨 다시 탑재](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. SAP HANA Studio를 통해 HANA DB에 다시 연결하는 경우 자동으로 표시되지 않으면 SAP HANA Studio 내에서 복구 옵션을 선택합니다. 다음 예제에서는 최신 HANA 스냅숏에 대한 복원을 보여 줍니다. 저장소 스냅숏에는 하나의 HANA 스냅숏이 포함되고, 가장 최근의 저장소 스냅숏으로 복원하는 경우 가장 최근의 HANA 스냅숏이어야 합니다. (이전 저장소 스냅숏으로 복원하는 경우 저장소 스냅숏이 만들어진 시간에 따라 HANA 스냅숏을 배치해야 합니다.)

 ![SAP HANA Studio 내에서 복구 옵션 선택](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. **특정 데이터 백업이나 저장소 스냅숏으로 데이터베이스 복구**를 선택합니다.

 !["복구 유형 지정" 창](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. **카탈로그 없이 백업 지정**을 선택합니다.

 !["백업 위치 지정" 창](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. **대상 형식** 목록에서 **스냅숏**을 선택합니다.

 !["복구할 백업 지정" 창](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. **마침**을 클릭하여 복구 프로세스를 시작합니다.

 !["마침"을 클릭하여 복구 프로세스를 시작합니다.](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA 데이터베이스는 저장소 스냅숏이 포함된 HANA 스냅숏으로 복원되고 복구됩니다.

 ![HANA 데이터베이스는 HANA 스냅숏으로 복원되고 복구됩니다.](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>가장 최근 상태로 복구

다음 프로세스는 저장소 스냅숏에 포함되는 HANA 스냅숏을 복원합니다. 그런 다음 저장소 스냅숏을 복원하기 전에 트랜잭션 로그 백업을 데이터베이스의 가장 최근 상태로 복원합니다.

>[!IMPORTANT]
>계속 진행하기 전에 트랜잭션 로그 백업의 완전한 연속 체인이 있는지 확인합니다. 이러한 백업 없이 데이터베이스의 현재 상태를 복원할 수 없습니다.

1. "가장 최근 HANA 스냅숏으로 복구"에서 이전 절차의 1~6단계를 완료합니다.

2. **가장 최근 상태로 데이터베이스 복구**를 선택합니다.

 !["가장 최근 상태로 데이터베이스 복구"를 선택합니다.](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 가장 최근인 HANA 로그 백업의 위치를 지정합니다. 위치는 HANA 스냅숏에서 가장 최근 상태까지 모든 HANA 트랜잭션 로그 백업을 포함해야 합니다.

 ![가장 최근인 HANA 로그 백업의 위치를 지정합니다.](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. 데이터베이스를 복구하는 기본으로 백업을 선택합니다. 이 예제에서 다음은 저장소 스냅숏이 포함되었던 HANA 스냅숏입니다. (하나의 스냅숏만 다음 스크린샷에 나열됩니다.)

 ![데이터베이스를 복구하는 기본으로 백업을 선택합니다.](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. 델타가 HANA 스냅숏 시간과 가장 최근의 상태 간에 존재하지 않는 경우 **델타 백업 사용** 확인란의 선택을 취소합니다.

 ![델타가 존재하지 않는 경우 "델타 백업 사용" 확인란의 선택을 취소합니다](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 요약 화면에서 **마침**을 클릭하여 복원 절차를 시작합니다.

 ![요약 페이지에서 "마침"을 클릭합니다.](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>다른 지정 시점으로 복구
저장소 스냅숏에 포함된 HANA 스냅숏과 HANA 스냅숏 지정 시점 복구보다 이후인 스냅숏 간의 지정 시점으로 복구하려면 다음을 수행합니다.

1. HANA 스냅숏에서 복구하려는 시간까지 모든 트랜잭션 로그 백업이 있는지 확인합니다.
2. "가장 최근 상태로 복구"에서 절차를 시작합니다.
3. 프로시저의 2단계에 있는 **복구 유형 선택** 창에서 **데이터베이스를 다음 지정 시점으로 복구**를 선택한 다음 3~6단계를 완료합니다.

## <a name="monitoring-the-execution-of-snapshots"></a>스냅숏의 실행 모니터링

저장소 스냅숏의 실행을 모니터링해야 합니다. 저장소 스냅숏을 실행하는 스크립트는 파일에 출력을 작성한 다음 Perl 스크립트와 동일한 위치에 저장합니다. 각 스냅숏에 대해 별도 파일을 기록합니다. 각 파일의 출력은 스냅숏 스크립트가 실행되는 다양한 단계를 명확하게 표시합니다.

- 스냅숏을 만드는 데 필요한 볼륨 찾기
- 이러한 볼륨에서 만든 스냅숏 찾기
- 최종 기존 스냅숏을 삭제하여 지정한 스냅숏 개수와 일치
- HANA 스냅숏 만들기
- 볼륨을 통해 저장소 스냅숏 만들기
- HANA 스냅숏 삭제
- 가장 최근의 스냅숏의 이름을 **.0**으로 지정

스크립트의 가장 중요한 부분은 다음과 같습니다.
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
스크립트가 HANA 스냅숏의 생성을 기록하는 방법을 이 샘플에서 볼 수 있습니다. 확장 사례의 경우 이 프로세스는 마스터 노드에서 시작됩니다. 마스터 노드는 각 작업자 노드에서 스냅숏을 동기적으로 생성하기 시작합니다. 그런 다음 저장소 스냅숏이 만들어집니다. 저장소 스냅숏을 성공적으로 실행한 후에 HANA 스냅숏이 삭제됩니다.

