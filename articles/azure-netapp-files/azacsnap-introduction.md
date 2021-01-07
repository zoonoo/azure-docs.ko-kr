---
title: Azure NetApp Files에 대 한 Azure 애플리케이션 일치 스냅숏 도구는 무엇입니까? Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅숏 도구에 대해 소개 합니다.
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
ms.openlocfilehash: b168167ce4f44d87c396746cca3f271f95f83163
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632822"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Azure 애플리케이션 일치 스냅숏 도구 (미리 보기) 란?

AzAcSnap (Azure 애플리케이션 일치 스냅숏 도구)는 Linux 환경 (예: SUSE 및 RHEL)에서 타사 데이터베이스 (SAP HANA)에 대 한 데이터 보호를 간소화 하는 데 사용할 수 있는 명령줄 도구입니다.  

## <a name="benefits-of-using-azacsnap"></a>AzAcSnap 사용의 이점

AzAcSnap는 Azure NetApp Files 및 Azure 대규모 인스턴스의 볼륨 스냅숏 및 복제 기능을 활용 합니다.  다음과 같은 이점을 제공합니다.

- **응용 프로그램 일치 데이터 보호** AzAcSnap은 중요 한 데이터베이스 파일을 백업 하기 위한 중앙화 된 솔루션입니다. 저장소 볼륨 스냅숏을 수행 하기 전에 데이터베이스 일관성을 보장 합니다. 따라서 저장소 볼륨 스냅숏을 데이터베이스 복구에 사용할 수 있습니다.
- **데이터베이스 카탈로그 관리** 기본 제공 백업 카탈로그가 있는 데이터베이스에서 AzAcSnap를 사용 하는 경우 카탈로그 내의 레코드는 저장소 스냅숏으로 최신 상태로 유지 됩니다.  이 기능을 사용 하면 데이터베이스 관리자가 백업 작업을 볼 수 있습니다.
- **임시 볼륨 보호** 이 기능은 저장소 스냅숏을 만들기 전에 응용 프로그램이 정지 하지 않아도 되는 비 데이터베이스 볼륨에 유용 합니다.  예를 들면 SAP HANA 로그 백업 볼륨 또는 SAPTRANS 볼륨이 포함 됩니다.
- **저장소 볼륨 복제** 이 기능은 개발 및 테스트를 위해 공간 효율성이 뛰어난 저장소 볼륨 클론을 제공 합니다.
- **재해 복구 지원** AzAcSnap는 저장소 볼륨 복제를 활용 하 여 원격 사이트에서 복제 된 응용 프로그램 일치 스냅숏을 복구 하는 옵션을 제공 합니다.

AzAcSnap는 단일 이진 파일입니다.  데이터베이스 또는 저장소 (Azure Resource Manager를 통해 Azure NetApp Files, SSH를 통해 Azure Large Instance)와 상호 작용 하는 추가 에이전트 또는 플러그 인은 필요 하지 않습니다.  AzAcSnap는 데이터베이스와 저장소에 연결 된 시스템에 설치 해야 합니다.  그러나 설치 및 구성의 유연성을 통해 단일 중앙 집중식 설치 또는 각 데이터베이스 설치 시 복사본이 설치 된 완전 분산 설치를 수행할 수 있습니다.

## <a name="architecture-overview"></a>아키텍처 개요

AzAcSnap는 데이터베이스와 동일한 호스트 (SAP HANA)에 설치 하거나 중앙 시스템에 설치할 수 있습니다.  그러나 데이터베이스 서버와 저장소 백 엔드 (Azure의 Azure Resource Manager에 대 한 Azure NetApp Files 또는 SSH의 경우)에 대 한 네트워크 연결이 있어야 합니다.

AzAcSnap는 일반적으로 외부 스케줄러에서 실행 되는 간단한 응용 프로그램입니다.  대부분의 Linux 시스템에서이 작업은 `cron` 설명서에서 중점적으로 살펴볼 수 있는입니다.  하지만 스케줄러는 `azacsnap` 사용자의 셸 프로필을 가져올 수 있는 다른 도구가 될 수 있습니다.  사용자 환경 설정을 가져오면 파일 경로 및 사용 권한이 올바르게 초기화 됩니다.

## <a name="command-synopsis"></a>명령 개요

명령의 일반적인 `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` 형식은 다음과 같습니다.

## <a name="command-options"></a>명령 옵션

명령 옵션은 기본 글머리 기호로 명령과 연결 된 하위 명령을 들여쓴 글머리 기호로 사용 하는 경우 다음과 같습니다.

- **`-h`** AzAcSnap 사용에 대 한 예제와 함께 확장 명령줄 도움말을 제공 합니다.
- **`-c configure`** 이 명령은 구성 파일을 만들거나 수정 하기 위한 스타일 인터페이스를 대화형 Q&제공 `azacsnap` 합니다 (기본값 = `azacsnap.json` ).
  - **`--configuration new`** 에서 새 구성 파일을 만듭니다.
  - **`--configuration edit`** 기존 구성 파일을 편집 합니다.
  - [명령 참조 구성](azacsnap-cmd-ref-configure.md)을 참조 하세요.
- **`-c test`** 구성 파일의 유효성을 검사 하 고 연결을 테스트 합니다.
  - **`--test hana`**  SAP HANA 인스턴스에 대 한 연결을 테스트 합니다.
  - **`--test storage`** 구성 된 모든 볼륨에서 임시 저장소 스냅숏을 만든 다음 제거 하 여 기본 저장소 인터페이스와의 통신 `data` 을 테스트 합니다.
  - **`--test all`** 는와 테스트를 차례로 수행 `hana` `storage` 합니다.
  - [테스트 명령 참조](azacsnap-cmd-ref-test.md)를 참조 하세요.
- **`-c backup`** 는 데이터 (SAP HANA 데이터 볼륨) & 기타 (예: 공유, 로그 백업 또는 부팅) 볼륨에 대해 데이터베이스 일치 저장소 스냅숏을 실행 하는 기본 명령입니다.
  - **`--volume data`** 구성 파일의 stanza에 있는 모든 볼륨을 스냅숏으로 설정 `dataVolume` 합니다.
  - **`--volume other`** 구성 파일의 stanza에 있는 모든 볼륨을 스냅숏으로 설정 `otherVolume` 합니다.
  - [백업 명령 참조](azacsnap-cmd-ref-backup.md)를 참조 하세요.
- **`-c details`** 스냅숏 또는 복제에 대 한 정보를 제공 합니다.
  - **`--details snapshots`** 구성 된 각 볼륨의 스냅숏에 대 한 기본 세부 정보 목록을 제공 합니다.
  - **`--details replication`** 프로덕션 사이트에서 재해 복구 사이트로의 복제 상태에 대 한 기본 세부 정보를 제공 합니다.
  - [자세한 명령 참조](azacsnap-cmd-ref-details.md)를 참조 하세요.
- **`-c delete`** 이 명령은 저장소 스냅숏 또는 스냅숏 집합을 삭제 합니다. HANA Studio에 있는 SAP HANA 백업 ID 또는 저장소 스냅숏 이름을 사용할 수 있습니다. 백업 ID는 `hana` 데이터 및 공유 볼륨에 대해 생성 된 스냅숏에만 연결 됩니다. 그렇지 않고 스냅숏 이름을 입력 하는 경우 입력 한 스냅숏 이름과 일치 하는 모든 스냅숏을 검색 합니다.
  - [삭제](azacsnap-cmd-ref-delete.md)를 참조 하세요.
- **`-c restore`** 에서는 스냅숏을 기반으로 새 볼륨을 만들거나 볼륨을 미리 보기 상태로 롤백하여 볼륨에 스냅숏을 복원 하는 두 가지 방법을 제공 합니다.
  - **`--restore snaptovol`** 대상 볼륨의 최신 스냅숏을 기반으로 새 볼륨을 만듭니다.
  - **`-c restore --restore revertvolume`** 가장 최근의 스냅숏을 기반으로 대상 볼륨을 이전 상태로 되돌립니다.
  - [복원 명령 참조](azacsnap-cmd-ref-restore.md)를 참조 하세요.
- **`[--configfile <configfilename>]`** 다른 JSON 구성 파일 이름을 제공 하는 선택적 명령줄 매개 변수입니다.  이는 SID 당 별도의 구성 파일을 만드는 데 특히 유용 합니다 (예: `--configfile H80.json` ).

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 일관성 있는 스냅숏 도구 시작](azacsnap-get-started.md)
