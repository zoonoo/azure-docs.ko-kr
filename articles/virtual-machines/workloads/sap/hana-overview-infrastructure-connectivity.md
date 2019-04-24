---
title: Azure의 SAP HANA(대규모 인스턴스)에 대한 인프라 및 연결 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)를 사용하도록 필수 연결 인프라를 구성합니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d8bbe2fc218004116177c4c9d95777d9ec57503
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477300"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA(대규모 인스턴스) 배포 

이 문서에서는 사용자가 Microsoft에서 Azure의 SAP HANA(대규모 인스턴스) 구입을 완료했다고 가정합니다. 이 문서를 읽기 전에 기본 배경 지식을 위해 [HANA 대규모 인스턴스 공통 용어](hana-know-terms.md) 및 [HANA 대규모 인스턴스 SKU](hana-available-skus.md)를 숙지하세요.


Microsoft는 HANA 대규모 인스턴스 단위를 배포하기 위해 다음 정보를 요구합니다.

- 고객 이름
- 비즈니스 연락처 정보(이메일 주소 및 전화 번호 포함)
- 기술 담당자 정보(이메일 주소 및 전화 번호 포함)
- 기술 네트워킹 담당자 정보(이메일 주소 및 전화 번호 포함)
- Azure 배포 지역(예: 미국 서부, 오스트레일리아 동부 또는 유럽 북부)
- Azure의 SAP HANA(대규모 인스턴스) SKU(구성)
- 모든 Azure 배포 지역의 경우:
    - Azure 가상 네트워크를 HANA 대규모 인스턴스에 연결하는 ER-P2P 연결에 대한 /29 IP 주소 범위
    - HANA 대규모 인스턴스 서버 IP 풀에 사용되는 /24 CIDR 블록
- HANA 대규모 인스턴스에 연결하는 모든 Azure 가상 네트워크의 가상 네트워크 주소 공간 특성에 사용된 IP 주소 범위 값
- 각 HANA 대규모 인스턴스 시스템에 대한 데이터:
  - 원하는 호스트 이름 - 정규화된 도메인 이름이 이상적.
  - 서버 IP 풀 주소 범위를 벗어난 HANA 대규모 인스턴스 단위에 대한 원하는 IP 주소 (서버 IP 풀 주소 범위의 처음 30개 IP 주소는 HANA 대규모 인스턴스 내에서 내부 용도로 예약되어 있습니다.)
  - SAP HANA 인스턴스의 SAP HANA SID 이름(필요한 SAP HANA 관련 디스크 볼륨을 만드는 데 필수). Microsoft는 NFS 볼륨에서 sidadm에 대한 사용 권한을 생성하기 위해 HANA SID가 필요합니다. 이러한 볼륨은 HANA 대규모 인스턴스 단위에 연결됩니다. 또한 HANA SID는 탑재된 디스크 볼륨의 이름 구성 요소 중 하나로 사용됩니다. 단위에 둘 이상의 HANA 인스턴스를 실행하려는 경우 여러 HANA SID를 나열해야 합니다. 각각에 별도의 볼륨 집합이 할당됩니다.
  - Linux OS에서 sidadm 사용자에는 그룹 ID가 있습니다. 필수 SAP HANA 관련 디스크 볼륨을 만드는 데 이 ID가 필요합니다. 일반적으로 SAP HANA 설치에서 그룹 ID가 1001인 sapsys 그룹이 생성됩니다. sidadm 사용자가 해당 그룹에 포함됩니다.
  - Linux OS에서 sidadm 사용자에는 사용자 ID가 있습니다. 필수 SAP HANA 관련 디스크 볼륨을 만드는 데 이 ID가 필요합니다. 단위에서 여러 HANA 인스턴스를 실행하는 경우 모든 sidadm 사용자를 나열합니다. 
- Azure HANA 대규모 인스턴스의 SAP HANA를 직접 연결하는 Azure 구독의 Azure 구독 ID. 이 구독 ID는 HANA 대규모 인스턴스 단위로 청구될 Azure 구독을 참조합니다.

이전의 정보를 제공한 후에 Microsoft에서는 Azure의 SAP HANA(대규모 인스턴스)를 프로비전합니다. Microsoft는 Azure 가상 네트워크를 HANA 대규모 인스턴스에 연결하기 위해 정보를 보냅니다. 또한 HANA 대규모 인스턴스 단위에 액세스할 수 있습니다.

Microsoft에서 배포한 후에 다음 순서를 사용하여 HANA 대규모 인스턴스에 연결합니다.

1. [Azure VM을 HANA 대규모 인스턴스에 연결](hana-connect-azure-vm-large-instances.md)
2. [VNet을 HANA 대규모 인스턴스 ExpressRoute에 연결](hana-connect-vnet-express-route.md)
3. [추가 네트워크 요구 사항(옵션)](hana-additional-network-requirements.md)

