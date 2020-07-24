---
title: Azure에서 사용할 수 있는 Red Hat Enterprise Linux 이미지
description: Microsoft Azure의 Red Hat Enterprise Linux 이미지에 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 01c9c4389e2c950fe68c8be171b4c50d3d6271c1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052032"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure에서 사용할 수 있는 Red Hat Enterprise Linux (RHEL) 이미지
Azure는 다양 한 사용 사례에 대 한 다양 한 RHEL 이미지를 제공 합니다.

> [!NOTE]
> 모든 RHEL 이미지는 Azure 공용 및 Azure Government 클라우드에서 사용할 수 있습니다. Azure 중국 클라우드에서 사용할 수 없습니다.

## <a name="list-of-rhel-images"></a>RHEL 이미지 목록
Azure에서 사용할 수 있는 RHEL 이미지 목록입니다. 달리 명시 되지 않은 경우 모든 이미지는 LVM으로 분할 되 고 일반 RHEL 리포지토리에 연결 됩니다 (E4S가 아닌 EUS는 아님). 다음 이미지는 현재 일반적으로 사용할 수 있습니다.

> [!NOTE]
> 원시 이미지는 LVM 분할 이미지를 위해 더 이상 생성 되지 않습니다. LVM은 훨씬 더 유연한 파티션 크기 조정 옵션을 포함 하 여 오래 된 원시 (LVM) 파티션 구성표에 비해 여러 가지 이점을 제공 합니다.

제안| SKU | 분할 | 프로비저닝 | 참고
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 에이전트 |
|             | 6.8      | RAW    | Linux 에이전트 |
|             | 6.9      | RAW    | Linux 에이전트 |
|             | 6.10     | RAW    | Linux 에이전트 |
|             | 7-RAW    | RAW    | Linux 에이전트 | RHEL 7.x 이미지 제품군 <br> 기본적으로 일반 리포지토리에 연결 됩니다 (EUS 아님).
|             | 7-LVM    | LVM    | Linux 에이전트 | RHEL 7.x 이미지 제품군 <br> 기본적으로 일반 리포지토리에 연결 됩니다 (EUS 아님). 배포할 표준 RHEL 이미지를 찾고 있는 경우이 이미지 집합 및/또는 해당 하는 2 세대를 사용 합니다.
|             | 7lvm-gen2| LVM    | Linux 에이전트 | 2 세대, RHEL 7.x 이미지 제품군 <br> 기본적으로 일반 리포지토리에 연결 됩니다 (EUS 아님). 배포할 표준 RHEL 이미지를 찾고 있는 경우이 이미지 집합 및/또는 해당 하는 1 세대를 사용 합니다.
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x 이미지 제품군 <br> 기본적으로 일반 리포지토리에 연결 됩니다 (EUS 아님).
|             | 7.2      | RAW    | Linux 에이전트 |
|             | 7.3      | RAW    | Linux 에이전트 |
|             | 7.4      | RAW    | Linux 에이전트 | 2019 년 4 월 기준으로 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 74-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 7.5      | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다. 6 월 2019을 기준으로 합니다.
|             | 75-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 7.6      | RAW    | Linux 에이전트 | 2019 년 5 월에 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 76-gen2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 7.7      | LVM    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 77-gen2  | LVM    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결 됩니다.
|             | 7.8      | LVM    | Linux 에이전트 | 일반 리포지토리에 연결 됨 (RHEL 7.8에 대해 EUS를 사용할 수 없음)
|             | 78-gen2  | LVM    | Linux 에이전트 | 일반 리포지토리에 연결 됨 (RHEL 7.8에 대해 EUS를 사용할 수 없음)
|             | 8-LVM    | LVM    | Linux 에이전트 | RHEL .x 이미지 패밀리입니다. 일반 리포지토리에 연결 됩니다.
|             | 8lvm-gen2| LVM    | Linux 에이전트 | Hyper-v 세대 2-RHEL 8. x 이미지 제품군 일반 리포지토리에 연결 됩니다.
|             | 8        | LVM    | Linux 에이전트 | RHEL 8.0 이미지
|             | 8-gen2   | LVM    | Linux 에이전트 | Hyper-v 세대 2-RHEL 8.0 이미지
|             | 8.1      | LVM    | Linux 에이전트 | RHEL 8.2 이미지 현재 일반 리포지토리에 연결 되어 있습니다.
|             | 81gen2   | LVM    | Linux 에이전트 | Hyper-v 세대 2-RHEL 8.1 이미지 현재 일반 리포지토리에 연결 되어 있습니다.
|             | 8.1-ci   | LVM    | Linux 에이전트 | 프로 비전 에이전트로 클라우드 init를 사용 하 여 8.1 이미지를 RHEL 합니다. 현재 일반 리포지토리에 연결 되어 있습니다.
|             | 81-ci-gen2| LVM    | Linux 에이전트 | Hyper-v 세대 2-RHEL 8.1 이미지를 프로 비전 에이전트로 사용 하 여 클라우드 init를 사용 합니다. 현재 일반 리포지토리에 연결 되어 있습니다.
|             | 8.2      | LVM    | Linux 에이전트 | RHEL 8.2 이미지 현재 일반 리포지토리에 연결 되어 있습니다.
|             | 82gen2   | LVM    | Linux 에이전트 | Hyper-v 세대 2-RHEL 8.1 이미지 현재 일반 리포지토리에 연결 되어 있습니다.
RHEL-SAP      | 7.4      | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.4. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 74sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.4. 2 세대 이미지입니다. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 7.5       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.5. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 75sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.5. 2 세대 이미지입니다. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 7.6       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.6. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 76sap-gen2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.6. 2 세대 이미지입니다. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
|             | 7.7       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱에 대 한 RHEL 7.7. E4S 리포지토리에 연결 된는 SAP 및 RHEL에 대 한 프리미엄 및 기본 계산 요금을 부과 합니다.
RHEL-SAP-HANA | 6.7       | RAW    | Linux 에이전트 | SAP HANA RHEL 6.7입니다. 오래 된 RHEL 이미지를 선호 합니다.
|             | 7.2       | LVM    | Linux 에이전트 | SAP HANA RHEL 7.2입니다. 오래 된 RHEL 이미지를 선호 합니다.
|             | 7.3       | LVM    | Linux 에이전트 | SAP HANA RHEL 7.3입니다. 오래 된 RHEL 이미지를 선호 합니다.
RHEL-SAP-APPS | 6.8       | RAW    | Linux 에이전트 | RHEL 6.8 for SAP Business Applications. 오래 된 RHEL 이미지를 선호 합니다.
|             | 7.3       | LVM    | Linux 에이전트 | RHEL 7.3 for SAP Business Applications. 오래 된 RHEL 이미지를 선호 합니다.
RHEL-HA       | 7.4       | LVM    | Linux 에이전트 | HA 추가 기능을 사용 하는 RHEL 7.4. 는 기본 계산 요금을 RHEL HA 및에 대 한 프리미엄을 부과 합니다.
|             | 7.5       | LVM    | Linux 에이전트 | HA 추가 기능을 사용 하는 RHEL 7.5. 는 기본 계산 요금을 RHEL HA 및에 대 한 프리미엄을 부과 합니다.
|             | 7.6       | LVM    | Linux 에이전트 | HA 추가 기능을 사용 하는 RHEL 7.6. 는 기본 계산 요금을 RHEL HA 및에 대 한 프리미엄을 부과 합니다.
RHEL-HA   | 7.4          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.4. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 74sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.4. 2 세대 이미지입니다. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 7.5          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.5. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 7.6          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.6. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 76sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.6. 2 세대 이미지입니다. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 7.7          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.7. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
|             | 77sapha-gen2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 사용 하는 SAP 용 RHEL 7.7. 2 세대 이미지입니다. E4S 리포지토리에 연결 됩니다. 은 (는) 기본 계산 요금을 RHEL SAP 및 HA 리포지토리의 프리미엄 뿐만 아니라도 청구 합니다.
rhel-byos     |rhel-lvm74| LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 7.4 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm75| LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 7.5 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm76| LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 7.6 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm77| LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 7.7 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm8 | LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 8.0 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.
|             |rhel-lvm81 | LVM    | Linux 에이전트 | 업데이트 원본에 연결 되지 않은 RHEL 8.1 BYOS 이미지는 RHEL premium을 청구 하지 않습니다.

> [!NOTE]
> RHEL 제품 제공은 Red Hat의 수명 종료로 간주 됩니다. 기존 배포는 정상적으로 계속 작동 하지만 Red Hat는 고객이 RHEL 이미지에서 SAP HANA 리포지토리 뿐만 아니라 HA 추가 기능을 포함 하는 RHEL-HA 이미지로 마이그레이션할 것을 권장 합니다. Red Hat의 SAP 클라우드 제품에 대 한 자세한 내용은 [여기](https://access.redhat.com/articles/3751271)에서 제공 됩니다.

## <a name="next-steps"></a>다음 단계
* [Azure의 Red Hat 이미지](./redhat-images.md)에 대해 자세히 알아보세요.
* [Red Hat 업데이트 인프라](./redhat-rhui.md)에 대해 자세히 알아보세요.
* [RHEL BYOS 제품](./byos.md)에 대해 자세히 알아보세요.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
