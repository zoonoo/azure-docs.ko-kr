---
title: SAP HANA (많은 인스턴스)에서 Kdump를 사용 하도록 설정 하는 스크립트 | Microsoft Docs
description: SAP HANA (Large Instances) HLI Type I, HLI Type II에서 Kdump를 사용 하도록 설정 하는 스크립트
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d723e95212e457a81eedf7726bf3c5bd2499643
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84488888"
---
# <a name="enable-kdump-service"></a>Kdump 서비스 사용

이 문서에서는 Azure HANA Large Instance에서 Kdump 서비스를 사용 하도록 설정 하는 방법에 대 한 자세한 내용 (**유형 I 및 유형 II**)을 설명 합니다.

## <a name="supported-skus"></a>지원되는 SKU

|  Hana Large Instance 유형   |  OS 공급 업체   |  OS 패키지 버전   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   입력 I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   입력 I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   입력 I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   입력 I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   입력 I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   입력 I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   입력 I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   입력 I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   입력 I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   입력 I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   입력 I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   입력 I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   입력 I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   입력 I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   입력 I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   입력 I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   입력 I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   형식 II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   형식 II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   형식 II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   형식 II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   형식 II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   형식 II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   형식 II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   형식 II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   형식 II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>사전 요구 사항

- Kdump 서비스는 디렉터리를 사용 하 여 `/var/crash` 덤프를 쓰고,이 디렉터리에 해당 하는 파티션에 덤프를 수용할 수 있는 충분 한 공간이 있는지 확인 합니다.

## <a name="setup-details"></a>설정 정보

- Kdump를 사용 하도록 설정 하는 스크립트는 [여기](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh) 에서 찾을 수 있습니다.

- 아래의 명령을 사용 하 여 HANA Large Instance에서이 스크립트를 실행 합니다.

    > [!NOTE]
    > 이 명령을 실행 하는 데 필요한 sudo 권한입니다.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Kdump 출력이 성공적으로 사용 하도록 설정 된 경우 시스템을 다시 부팅 하 여 변경 내용을 적용 하 고 Kdump를 사용 하도록 설정 합니다. 시스템을 다시 부팅 하 여 변경 내용을 적용 합니다.

- 명령 출력에서 특정 작업을 수행 하지 못한 경우!!!!,를 끝낸 후 Kdump 서비스를 사용할 수 없습니다. [지원 문제](#support-issue)섹션을 참조 하세요.

## <a name="test-kdump"></a>Kdump 테스트

> [!NOTE]
>  아래 작업을 수행 하면 커널 충돌 및 시스템 다시 부팅이 트리거됩니다.

- 커널 충돌 트리거

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- 시스템이 다시 부팅 되 면 `/var/crash` 디렉터리에서 커널 크래시 로그를 확인 합니다.

- 에 `/var/crash` 현재 날짜의 디렉터리가 있으면 Kdump를 사용 하도록 설정 합니다.

## <a name="support-issue"></a>지원 문제

오류가 발생 하 여 스크립트가 실패 하거나 Kdump를 사용 하지 않는 경우 다음 세부 정보를 사용 하 여 Microsoft 지원 팀에 서비스 요청을 발생 시킵니다.

* HLI 구독 ID

* 서버 이름

* OS 공급 업체

* OS 버전

* 커널 버전
