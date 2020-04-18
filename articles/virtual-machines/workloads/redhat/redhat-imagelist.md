---
title: Azure의 Red Hat Enterprise Linux 이미지 | Microsoft Docs
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
ms.openlocfilehash: 028c30fced14a60af9f5683e6c6e087b15591735
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605487"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure에서 사용할 수 있는 레드 햇 엔터프라이즈 리눅스 (RHEL) 이미지
Azure는 다양한 사용 사례에 대해 다양한 RHEL 이미지를 제공합니다.

> [!NOTE]
> 모든 RHEL 이미지는 Azure 공용 및 Azure 정부 클라우드에서 사용할 수 있습니다. Azure 중국 클라우드에서는 사용할 수 없습니다.

## <a name="list-of-rhel-images"></a>RHEL 이미지 목록
Azure에서 사용할 수 있는 RHEL 이미지 목록입니다. 달리 명시되지 않는 한, 모든 이미지는 LVM 분할되고 일반 RHEL 리포지토리(EUS가 아닌 E4S)에 첨부됩니다. 다음 이미지는 현재 일반적인 용도로 사용할 수 있습니다.

제안| SKU | 분할 | 프로비전 | 메모
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 에이전트 |
|             | 6.8      | RAW    | Linux 에이전트 |
|             | 6.9      | RAW    | Linux 에이전트 |
|             | 6.10     | RAW    | Linux 에이전트 |
|             | 7-RAW    | RAW    | Linux 에이전트 | RHEL 7.x 이미지 제품군입니다. <br> 기본적으로 일반 리포지토리에 연결됩니다(EUS 아님).
|             | 7-LVM    | LVM    | Linux 에이전트 | RHEL 7.x 이미지 제품군입니다. <br> 기본적으로 일반 리포지토리에 연결됩니다(EUS 아님). 배포할 표준 RHEL 이미지를 찾고 있는 경우 이 이미지 집합 및/또는 2세대 이미지를 사용합니다.
|             | 7lvm-gen2| LVM    | Linux 에이전트 | 2 세대, RHEL 7.x 이미지 제품군. <br> 기본적으로 일반 리포지토리에 연결됩니다(EUS 아님). 배포할 표준 RHEL 이미지를 찾고 있는 경우 이 이미지 집합 및/또는 1세대 이미지를 사용합니다.
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x 이미지 제품군입니다. <br> 기본적으로 일반 리포지토리에 연결됩니다(EUS 아님).
|             | 7.2      | RAW    | Linux 에이전트 |
|             | 7.3      | RAW    | Linux 에이전트 |
|             | 7.4      | RAW    | Linux 에이전트 | 기본적으로 2019년 4월 현재 EUS 리포지토리에 첨부됩니다.
|             | 74세대2  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결됩니다.
|             | 7.5      | RAW    | Linux 에이전트 | 기본적으로 2019년 6월 현재 EUS 리포지토리에 첨부됩니다.
|             | 75세대2세대  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결됩니다.
|             | 7.6      | RAW    | Linux 에이전트 | 기본적으로 2019년 5월 현재 EUS 리포지토리에 첨부됩니다.
|             | 76세대2세대  | RAW    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결됩니다.
|             | 7.7      | LVM    | Linux 에이전트 | 기본적으로 EUS 리포지토리에 연결됩니다.
|             | 8-LVM    | LVM    | Linux 에이전트 | RHEL 8.x 이미지 제품군입니다. 일반 리포지토리에 첨부됩니다.
|             | 8-lvm-gen2| LVM    | Linux 에이전트 | 하이퍼 V 세대 2 - RHEL 8.x 이미지 제품군. 일반 리포지토리에 첨부됩니다.
|             | 8        | LVM    | Linux 에이전트 | RHEL 8.0 이미지
|             | 8세대2   | LVM    | Linux 에이전트 | 하이퍼 V 세대 2 - RHEL 8.0 이미지.
|             | 8.1      | LVM    | Linux 에이전트 | RHEL 8.1 이미지. 현재 일반 리포지토리에 첨부되어 있습니다.
|             | 81gen2   | LVM    | Linux 에이전트 | 하이퍼 V 세대 2 - RHEL 8.1 이미지. 현재 일반 리포지토리에 첨부되어 있습니다.
RHEL-SAP      | 7.4      | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.4. E4S 리포지토리에 부착된 SAP 및 RHEL에 대한 프리미엄과 기본 계산 요금이 부과됩니다.
|             | 74삽 세대2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.4. 2세대 이미지. E4S 리포지토리에 부착된 SAP 및 RHEL에 대한 프리미엄과 기본 계산 요금이 부과됩니다.
|             | 7.5       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.5. E4S 리포지토리에 부착된 SAP 및 RHEL에 대한 프리미엄과 기본 계산 요금이 부과됩니다.
|             | 75삽 세대2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.5. 2세대 이미지. E4S 리포지토리에 부착된 SAP 및 RHEL에 대한 프리미엄과 기본 계산 요금이 부과됩니다.
|             | 7.6       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.6. E4S 리포지토리에 부착된 SAP 및 RHEL에 대한 프리미엄과 기본 계산 요금이 부과됩니다.
|             | 76삽 세대2| LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.6. 2세대 이미지. E4S 리포지토리에 부착된 SAP 및 RHEL에 대한 프리미엄과 기본 계산 요금이 부과됩니다.
|             | 7.7       | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.7. E4S 리포지토리에 부착된 SAP 및 RHEL에 대한 프리미엄과 기본 계산 요금이 부과됩니다.
RHEL-SAP-HANA | 6.7       | RAW    | Linux 에이전트 | SAP HANA의 경우 RHEL 6.7. RHEL-SAP 이미지에 찬성 구식.
|             | 7.2       | LVM    | Linux 에이전트 | SAP HANA의 경우 RHEL 7.2. RHEL-SAP 이미지에 찬성 구식.
|             | 7.3       | LVM    | Linux 에이전트 | SAP HANA의 경우 RHEL 7.3. RHEL-SAP 이미지에 찬성 구식.
RHEL-SAP-APPS | 6.8       | RAW    | Linux 에이전트 | SAP 비즈니스 애플리케이션의 경우 RHEL 6.8. RHEL-SAP 이미지에 찬성 구식.
|             | 7.3       | LVM    | Linux 에이전트 | SAP 비즈니스 애플리케이션의 경우 RHEL 7.3. RHEL-SAP 이미지에 찬성 구식.
RHEL-HA       | 7.4       | LVM    | Linux 에이전트 | RHEL 7.4 와 HA 애드온. 기본 계산 요금에 HA 및 RHEL에 대한 프리미엄이 부과됩니다.
|             | 7.5       | LVM    | Linux 에이전트 | RHEL 7.5 와 HA 애드온. 기본 계산 요금에 HA 및 RHEL에 대한 프리미엄이 부과됩니다.
|             | 7.6       | LVM    | Linux 에이전트 | RHEL 7.6 과 HA 애드온. 기본 계산 요금에 HA 및 RHEL에 대한 프리미엄이 부과됩니다.
RHEL-SAP-HA   | 7.4          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 제공하는 SAP의 경우 RHEL 7.4. E4S 리포지토리에 첨부되었습니다. 기본 계산 요금에 따라 SAP 및 HA 저장소및 RHEL에 대한 프리미엄이 부과됩니다.
|             | 74사파 세대2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 제공하는 SAP의 경우 RHEL 7.4. 2세대 이미지. E4S 리포지토리에 첨부되었습니다. 기본 계산 요금에 따라 SAP 및 HA 저장소및 RHEL에 대한 프리미엄이 부과됩니다.
|             | 7.5          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 제공하는 SAP의 경우 RHEL 7.5. E4S 리포지토리에 첨부되었습니다. 기본 계산 요금에 따라 SAP 및 HA 저장소및 RHEL에 대한 프리미엄이 부과됩니다.
|             | 7.6          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 있는 SAP의 경우 RHEL 7.6. E4S 리포지토리에 첨부되었습니다. 기본 계산 요금에 따라 SAP 및 HA 저장소및 RHEL에 대한 프리미엄이 부과됩니다.
|             | 76사파 세대2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스가 있는 SAP의 경우 RHEL 7.6. 2세대 이미지. E4S 리포지토리에 첨부되었습니다. 기본 계산 요금에 따라 SAP 및 HA 저장소및 RHEL에 대한 프리미엄이 부과됩니다.
|             | 7.7          | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 제공하는 SAP의 경우 RHEL 7.7. E4S 리포지토리에 첨부되었습니다. 기본 계산 요금에 따라 SAP 및 HA 저장소및 RHEL에 대한 프리미엄이 부과됩니다.
|             | 77사파 세대2 | LVM    | Linux 에이전트 | HA 및 업데이트 서비스를 제공하는 SAP의 경우 RHEL 7.7. 2세대 이미지. E4S 리포지토리에 첨부되었습니다. 기본 계산 요금에 따라 SAP 및 HA 저장소및 RHEL에 대한 프리미엄이 부과됩니다.
렐 바이오스     |rhel-lvm74| LVM    | Linux 에이전트 | RHEL 7.4 BYOS 이미지는 업데이트 소스에 첨부되지 않으며 RHEL 프리미엄은 청구되지 않습니다.
|             |rhel-lvm75| LVM    | Linux 에이전트 | RHEL 7.5 BYOS 이미지는 업데이트 소스에 첨부되지 않으며 RHEL 프리미엄은 청구되지 않습니다.
|             |rhel-lvm76| LVM    | Linux 에이전트 | RHEL 7.6 BYOS 이미지는 업데이트 소스에 첨부되지 않으며 RHEL 프리미엄은 청구되지 않습니다.
|             |rhel-lvm77| LVM    | Linux 에이전트 | RHEL 7.7 BYOS 이미지는 업데이트 소스에 첨부되지 않으며 RHEL 프리미엄은 청구되지 않습니다.
|             |rhel-lvm8 | LVM    | Linux 에이전트 | RHEL 8 BYOS 이미지(RHEL 마이너 버전은 이미지 버전 값에 표시됨)는 업데이트 소스에 첨부되지 않으며 RHEL 프리미엄이 청구되지 않습니다.

> [!NOTE]
> RHEL-SAP-HANA 제품은 Red Hat의 수명이 다한 것으로 간주됩니다. 기존 배포는 계속 정상적으로 작동하지만 Red Hat은 고객이 RHEL-SAP-HANA 이미지에서 SAP HANA 리포지토리와 HA 추가 기능을 포함하는 RHEL-SAP-HA 이미지로 마이그레이션할 것을 권장합니다. Red Hat의 SAP 클라우드 제품에 대한 자세한 내용은 [여기에서](https://access.redhat.com/articles/3751271)확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure의 Red Hat 이미지에](./redhat-images.md)대해 자세히 알아보십시오.
* Red Hat [업데이트 인프라에](./redhat-rhui.md)대해 자세히 알아보십시오.
* [RHEL BYOS 제공에](./byos.md)대해 자세히 알아보십시오.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
