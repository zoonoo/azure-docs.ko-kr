---
title: Azure NetApp Files에 대한 Azure 애플리케이션 일치 스냅샷 도구는 무엇인가요? | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구에 대해 소개합니다.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 4ba679459686340396e0e4d65344295c0fa9c4be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869959"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅샷 도구란(미리 보기)

AzAcSnap(Azure 애플리케이션 일치 스냅샷 도구)은 스토리지 스냅샷을 만들기 전에 애플리케이션을 일관된 상태에 배치하는 데 필요한 모든 오케스트레이션을 처리하여 타사 데이터베이스에 대해 데이터 보호를 사용하도록 설정하고 그 후에는 운영 상태로 되돌리는 명령줄 도구입니다.

## <a name="supported-platforms-and-os"></a>지원되는 플랫폼 및 OS

- **데이터베이스**
  - SAP HANA(자세한 내용은 [지원 매트릭스](azacsnap-get-started.md#snapshot-support-matrix-from-sap) 참조)

- **운영 체제**
  - SUSE Linux Enterprise Server 12+
  - Red Hat Enterprise Linux 7+

## <a name="benefits-of-using-azacsnap"></a>AzAcSnap 사용의 이점

AzAcSnap은 Azure NetApp Files 및 Azure 대규모 인스턴스의 볼륨 스냅샷 및 복제 기능을 활용합니다.  다음과 같은 이점을 제공합니다.

- **애플리케이션 일치 데이터 보호** AzAcSnap은 중요한 데이터베이스 파일을 백업하기 위한 중앙화된 솔루션입니다. 스토리지 볼륨 스냅샷을 수행하기 전에 데이터베이스 일관성을 보장합니다. 따라서 스토리지 볼륨 스냅샷을 데이터베이스 복구에 사용할 수 있게 합니다.
- **데이터베이스 카탈로그 관리** 기본 제공 백업 카탈로그가 있는 데이터베이스에서 AzAcSnap를 사용하는 경우, 카탈로그 내의 레코드는 스토리지 스냅샷으로 최신 상태로 유지됩니다.  이 기능을 사용하면 데이터베이스 관리자가 백업 작업을 볼 수 있습니다.
- **임시 볼륨 보호** 이 기능은 스토리지 스냅샷을 만들기 전에 애플리케이션이 정지하지 않아도 되는 비 데이터베이스 볼륨에 유용합니다.  예를 들면 SAP HANA 로그 백업 볼륨 또는 SAPTRANS 볼륨이 포함됩니다.
- **스토리지 볼륨 복제** 이 기능은 개발 및 테스트를 위해 공간 효율성이 뛰어난 스토리지 볼륨 복제본을 제공합니다.
- **재해 복구 지원** AzAcSnap은 스토리지 볼륨 복제를 활용하여 원격 사이트에서 복제된 애플리케이션 일치 스냅샷을 복구하는 옵션을 제공합니다.

AzAcSnap은 단일 이진 파일입니다.  데이터베이스 또는 스토리지(Azure Resource Manager를 통한 Azure NetApp Files, SSH를 통한 Azure Large Instance)와 상호 작용하는 추가 에이전트 또는 플러그 인은 필요하지 않습니다.  AzAcSnap은 데이터베이스와 스토리지에 연결된 시스템에 설치해야 합니다.  그러나 설치 및 구성의 유연성을 통해 단일 중앙 집중식 설치 또는 각 데이터베이스 설치 시 복사본이 설치된 완전 분산 설치를 수행할 수 있습니다.

## <a name="architecture-overview"></a>아키텍처 개요

AzAcSnap은 데이터베이스와 동일한 호스트(SAP HANA)에 설치하거나 중앙 집중식 시스템에 설치할 수 있습니다.  그러나 데이터베이스 서버와 스토리지 백 엔드(Azure NetApp Files에 대한 Azure Resource Manager 또는 Azure Large Instance에 대한 SSH)에 대한 네트워크 연결이 있어야합니다.

AzAcSnap은 일반적으로 외부 스케줄러에서 실행되는 간단한 애플리케이션입니다.  대부분의 Linux 시스템에서 이 작업은 설명서에서 중점적으로 살펴볼 수 있는 `cron`입니다.  하지만 스케줄러는 `azacsnap` 사용자의 셸 프로필을 가져올 수 있는 다른 도구가 될 수 있습니다.  사용자 환경 설정을 가져오면 파일 경로 및 사용 권한이 올바르게 초기화됩니다.

## <a name="command-synopsis"></a>명령 개요

일반 명령의 형식은 다음과 같습니다. `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]`

## <a name="command-options"></a>명령 옵션

명령 옵션은 명령을 기본 글머리 기호로 사용하는 경우와, 연결된 하위 명령을 들여쓴 글머리 기호로 사용하는 경우 다음과 같습니다.

- **`-h`** 은 AzAcSnap 사용에 대한 예제와 함께 확장 명령줄 도움말을 제공합니다.
- **`-c configure`** 이 명령은 `azacsnap` 구성 파일을 만들거나 수정하기 위한 대화형 Q&A 스타일의 인터페이스를 제공 합니다(기본값 = `azacsnap.json` ).
  - **`--configuration new`** 에서 새 구성 파일을 만듭니다.
  - **`--configuration edit`** 은 기존 구성 파일을 편집합니다.
  - [구성 명령 참조](azacsnap-cmd-ref-configure.md)을 참조하세요.
- **`-c test`** 은 구성 파일의 유효성을 검사하고 연결을 테스트합니다.
  - **`--test hana`** 은 SAP HANA 인스턴스에 대한 연결을 테스트합니다.
  - **`--test storage`** 은 구성된 모든 `data` 볼륨에서 임시 스토리지 스냅샷을 만든 다음 이를 제거하여 기본 스토리지 인터페이스와의 통신을 테스트합니다.
  - **`--test all`** 은 `hana` 및 `storage` 테스트 모두를 순차적으로 수행합니다.
  - [테스트 명령 참조](azacsnap-cmd-ref-test.md)를 참조하세요.
- **`-c backup`** 은 데이터(SAP HANA 데이터 볼륨) 및 기타(예: 공유, 로그 백업 또는 부팅) 볼륨에 대해 데이터베이스 일치 스토리지 스냅샷을 실행하는 주 명령입니다.
  - **`--volume data`** 은 구성 파일의 `dataVolume` stanza에 있는 모든 볼륨을 스냅샷으로 설정합니다.
  - **`--volume other`** 은 구성 파일의 `otherVolume` stanza에 있는 모든 볼륨을 스냅샷으로 설정합니다.
  - [백업 명령 참조](azacsnap-cmd-ref-backup.md)를 참조하세요.
- **`-c details`** 은 스냅샷 또는 복제에 대한 정보를 제공합니다.
  - **`--details snapshots`** 은 구성된 각 볼륨의 스냅샷에 대한 기본 세부 정보 목록을 제공합니다.
  - **`--details replication`** 은 프로덕션 사이트에서 재해 복구 사이트로의 복제 상태에 대한 기본 세부 정보를 제공합니다.
  - [자세한 명령 참조](azacsnap-cmd-ref-details.md)를 참조하세요.
- **`-c delete`** 은 스토리지 스냅샷 또는 스냅샷 세트를 삭제합니다. HANA Studio에 있는 SAP HANA 백업 ID 또는 스토리지 스냅샷 이름을 사용할 수 있습니다. 백업 ID는 데이터 및 공유 볼륨에 대해 생성된 `hana` 스냅샷에만 연결됩니다. 그렇지 않고 스냅샷 이름을 입력하는 경우 입력한 스냅샷 이름과 일치하는 모든 스냅샷을 검색합니다.
  - [삭제](azacsnap-cmd-ref-delete.md)를 참조하세요.
- **`-c restore`** 에서는 스냅샷을 기반으로 새 볼륨을 만들거나 볼륨을 미리 보기 상태로 롤백하여 볼륨에 스냅샷을 복원하는 두 가지 방법을 제공합니다.
  - **`--restore snaptovol`** 은 대상 볼륨의 최신 스냅샷을 기반으로 새 볼륨을 만듭니다.
  - **`-c restore --restore revertvolume`** 은 가장 최근의 스냅샷을 기반으로 대상 볼륨을 이전 상태로 되돌립니다.
  - [복원 명령 참조](azacsnap-cmd-ref-restore.md)를 참조하세요.
- **`[--configfile <configfilename>]`** 다른 JSON 구성 파일 이름을 제공하는 선택적 명령줄 매개 변수입니다.  이는 SID 당 별도의 구성 파일을 만드는 데 특히 유용합니다(예: `--configfile H80.json` ).

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일치 스냅샷 도구 시작](azacsnap-get-started.md)
