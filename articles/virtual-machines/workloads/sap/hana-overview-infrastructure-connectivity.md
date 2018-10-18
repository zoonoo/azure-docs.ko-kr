---
title: Azure(큰 인스턴스)의 SAP HANA에 대한 인프라 및 연결 | Microsoft Docs
description: Azure(큰 인스턴스)의 SAP HANA를 사용하도록 필수 연결 인프라를 구성합니다.
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
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161359"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA(대규모 인스턴스) 배포 

사용자와 Microsoft 엔터프라이즈 계정 팀 간에 Azure(큰 인스턴스)의 SAP HANA 구매를 최종적으로 마무리한 후에 Microsoft에서 HANA 큰 인스턴스 단위를 배포하려면 다음과 같은 정보가 필요합니다.

- 고객 이름
- 비즈니스 연락처 정보(전자 메일 주소 및 전화 번호 포함)
- 기술 담당자 정보(전자 메일 주소 및 전화 번호 포함)
- 기술 네트워킹 담당자 정보(전자 메일 주소 및 전화 번호 포함)
- Azure 배포 지역(2017년 7월 현재 미국 서부, 미국 동부, 오스트레일리아 동부, 오스트레일리아 남동부, 유럽 서부 및 북유럽)
- Azure(큰 인스턴스) SKU(구성)에서 SAP HANA 확인
- HANA 큰 인스턴스의 개요 및 아키텍처 문서에서 이미 자세히 설명했듯이 다음에 배포되는 모든 Azure 지역의 경우:
    - Azure VNet을 HANA 큰 인스턴스에 연결하는 ER-P2P 연결에 대한 /29 IP 주소 범위
    - /24 CIDR 블록(HANA 큰 인스턴스 서버 IP 풀에 사용)
- HANA 큰 인스턴스에 연결하는 모든 Azure VNet의 VNet 주소 공간 특성에 사용된 IP 주소 범위 값
- HANA 큰 인스턴스 시스템 각각에 대한 데이터:
  - 원하는 호스트 이름 - 정규화된 도메인 이름이 이상적.
  - 서버 IP 풀 주소 범위 밖의 HANA 큰 인스턴스 단위의 원하는 IP 주소 - 서버 IP 풀 주소 범위의 처음 30개 IP 주소는 HANA 큰 인스턴스 내에서 내부용으로 예약되어 있습니다.
  - SAP HANA 인스턴스의 SAP HANA SID 이름(필요한 SAP HANA 관련 디스크 볼륨을 만드는 데 필수). HANA SID는 NFS 볼륨에서 sidadm에 대한 허락을 생성하기 위해 필요하며, HANA 큰 인스턴스 단위에 연결됩니다. 또한 탑재된 디스크 볼륨의 이름 구성 요소 중 하나로 사용됩니다. 단위에 둘 이상의 HANA 인스턴스를 실행하려는 경우 여러 HANA SID를 나열해야 합니다. 각각에 별도의 볼륨 집합이 할당됩니다.
  - sidadm 사용자가 Linux OS에서 가지고 있는 groupid는 필요한 SAP HANA 관련 디스크 볼륨을 만드는 데 필요합니다. 일반적으로 SAP HANA 설치에서는 그룹 ID가 1001인 sapsys 그룹을 생성합니다. sidadm 사용자가 그룹에 포함됩니다.
  - sidadm 사용자가 Linux OS에서 가지고 있는 userid는 필요한 SAP HANA 관련 디스크 볼륨을 만드는 데 필요합니다. 단위에서 여러 HANA 인스턴스를 실행하는 경우 모든 <sid>adm 사용자를 나열해야 합니다 
- Azure HANA 큰 인스턴스의 SAP HANA를 직접 연결하는 Azure 구독의 Azure 구독 ID. 이 구독 ID는 HANA 큰 인스턴스 단위로 청구될 Azure 구독을 참조합니다.

정보를 제공한 후 Microsoft는 Azure(큰 인스턴스)에 SAP HANA를 프로비전하고 Azure VNet을 HANA 큰 인스턴스에 연결하고 HANA 큰 인스턴스 단위에 액세스하는 데 필요한 정보를 반환합니다.

이 문서를 읽기 전에 [HANA 대규모 인스턴스 공통 용어](hana-know-terms.md) 및 [HANA 대규모 인스턴스 SKU](hana-available-skus.md)를 숙지하세요.

Microsoft에서 HANA 대규모 인스턴스를 배포한 후에 다음 순서에 따라 HANA 대규모 인스턴스에 연결할 수 있습니다.

1. [Azure VM을 HANA 큰 인스턴스에 연결](hana-connect-azure-vm-large-instances.md)
2. [VNet을 HANA 대규모 인스턴스 ExpressRoute에 연결](hana-connect-vnet-express-route.md)
3. [추가 네트워크 요구 사항(옵션)](hana-additional-network-requirements.md)

**다음 단계**

- [Azure VM을 HANA 대규모 인스턴스에 연결](hana-connect-azure-vm-large-instances.md)을 참조하세요.
- [VNet을 HANA 대규모 인스턴스 ExpressRoute에 연결](hana-connect-vnet-express-route.md)을 참조하세요.