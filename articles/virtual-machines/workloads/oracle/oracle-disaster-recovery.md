---
title: "Azure 환경의 Oracle 재해 복구 시나리오 개요 | Microsoft Docs"
description: "Azure 환경의 Oracle 데이터베이스 12c 재해 복구 시나리오입니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4f6fc6ed79606f0510bc3d6258205d1e6105de25
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="disaster-recovery-dr-of-oracle-12c-database-on-an-azure-environment"></a>Azure 환경의 Oracle 12c 데이터베이스 DR(재해 복구)

## <a name="assumptions"></a>가정

- Oracle Data Guard 디자인 및 Azure 환경 이해


## <a name="goals"></a>목표
- DR 요구 사항을 충족하는 토폴로지 및 구성을 디자인합니다.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>시나리오 1(Azure에 기본 사이트 및 DR 사이트가 있음)

고객은 기본 사이트에 Oracle 데이터베이스가 설치되어 있습니다. DR 사이트는 다른 영역에 있습니다. Oracle Data Guard는 이러한 사이트 간의 빠른 복구에 사용됩니다. 기본 사이트에는 보고 및 기타 용도의 보조 데이터베이스도 있습니다. 

### <a name="topology"></a>토폴로지

다음은 Azure 설치 요약입니다.

- 두 개의 사이트(기본 사이트 및 DR 사이트)
- 두 개의 VNet(가상 네트워크)
- Data Guard가 있는 두 개의 Oracle 데이터베이스(주 및 대기)
- Golden Gate 또는 Data Guard가 있는 두 개의 Oracle 데이터베이스(기본 사이트만 해당)
- 기본 사이트의 응용 프로그램 서비스 두 개 및 DR 사이트의 응용 프로그램 서비스 한 개
- ‘가용성 집합’은 기본 사이트의 데이터베이스 및 응용 프로그램 서비스에 사용됨
- 각 사이트의 Jumpbox 한 개: 개인 네트워크에 대한 액세스를 제한하며 관리자의 로그인만 허용함
- Jumpbox, 응용 프로그램 서비스, 데이터베이스 및 VPN 게이트웨이는 별도의 서브넷에 있음
- NSG가 응용 프로그램 및 데이터베이스 서브넷에 적용됨

![DR 토폴로지 페이지의 스크린샷](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>시나리오 2(기본 사이트는 온-프레미스에 있고 DR 사이트는 Azure에 있음)

고객이 온-프레미스에 Oracle 데이터베이스를 설치했습니다(기본 사이트). DR 사이트는 Azure에 있습니다. Oracle Data Guard는 이러한 사이트 간의 빠른 복구에 사용됩니다. 기본 사이트에는 보고 및 기타 용도의 보조 데이터베이스도 있습니다. 

이 설치에 대한 접근 방법은 두 가지가 있습니다.

### <a name="1-direct-connections-between-on-premises-and-azure-required-open-tcp-ports-on-firewall-this-approach-is-not-recommended-as-it-expose-the-tcp-ports-to-outside-world"></a>1. 온-프레미스와 Azure 간의 직접 연결, 방화벽에 열린 TCP 포트가 필요함. 이 접근 방법은 외부 세계에 TCP 포트를 노출하므로 권장되지 않습니다.

### <a name="topology"></a>토폴로지

다음은 Azure 설치 요약입니다.

- 하나의 사이트(DR 사이트)
- 하나의 VNet(가상 네트워크)
- Data Guard(활성)가 있는 하나의 Oracle 데이터베이스
- DR 사이트의 응용 프로그램 서비스 한 개
- Jumpbox 한 개가 사용됨: 개인 네트워크에 대한 액세스를 제한하며 관리자의 로그인만 허용함
- Jumpbox, 응용 프로그램 서비스, 데이터베이스 및 VPN 게이트웨이는 별도의 서브넷에 있음
- NSG가 응용 프로그램 및 데이터베이스 서브넷에 적용됨
- 인바운드 TCP 포트 1521(또는 사용자 정의)을 허용하기 위한 NSG 정책/규칙 추가
- 온-프레미스(DB 또는 응용 프로그램)의 IP 주소(하나 이상)만 Vnet에 액세스하도록 제한하기 위한 NSG 정책/규칙 추가

![DR 토폴로지 페이지의 스크린샷](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="2-a-better-approach-is-using-site-to-site-vpn-more-information-about-setting-up-vpn-is-available-from-this-linkhttpsdocsmicrosoftcomen-usazurevpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-cli"></a>2. 더 나은 접근 방법은 사이트 간 VPN을 사용하는 것입니다. VPN을 설정하는 방법에 대한 자세한 내용은 이 [링크](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)를 참조하세요.

### <a name="topology"></a>토폴로지

다음은 Azure 설치 요약입니다.

- 하나의 사이트(DR 사이트)
- 하나의 VNet(가상 네트워크)
- Data Guard(활성)가 있는 하나의 Oracle 데이터베이스
- DR 사이트의 응용 프로그램 서비스 한 개
- Jumpbox 한 개가 사용됨: 개인 네트워크에 대한 액세스를 제한하며 관리자의 로그인만 허용함
- Jumpbox, 응용 프로그램 서비스, 데이터베이스 및 VPN 게이트웨이는 별도의 서브넷에 있음
- NSG가 응용 프로그램 및 데이터베이스 서브넷에 적용됨
- 온-프레미스와 Azure 간의 사이트 간 VPN 연결

![DR 토폴로지 페이지의 스크린샷](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-readings"></a>추가 정보:

- [Azure에서 Oracle 데이터베이스 디자인 및 구현](oracle-design.md)
- [Oracle Data Guard 구성](configure-oracle-dataguard.md)
- [Oracle Golden Gate 구성](configure-oracle-golden-gate.md)
- [Oracle 백업 및 복구](oracle-backup-recovery.md)


## <a name="next-steps"></a>다음 단계

[자습서: 고가용성 VM 만들기](../../linux/create-cli-complete.md)

[VM 배포 Azure CLI 샘플 탐색](../../linux/cli-samples.md)

