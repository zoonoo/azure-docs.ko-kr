---
title: Azure Stack 통합 시스템에 대 한 연결 네트워크 통합 고려 사항 테두리 | Microsoft Docs
description: 다중 노드 Azure Stack을 사용 하 여 데이터 센터 테두리 네트워크 연결에 대 한 계획을 수행할 수 있는 작업에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 12219e2df875d317aece73cabebdfb55115f7b41
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021087"
---
# <a name="border-connectivity"></a>테두리 연결 
네트워크 통합 계획은 성공적으로 Azure Stack 통합 시스템 배포, 작업 및 관리는 중요 한 필수 구성 요소입니다. BGP (경계 게이트웨이 프로토콜)를 사용 하 여 동적 라우팅을 사용 여부를 선택 하 여 테두리 연결 계획을 시작 합니다. (공용 또는 개인)는 16 비트 BGP 익명 시스템 번호를 할당 합니다. 그러려면 하거나 정적 라우팅을 사용 하면 여기서 정적 기본 경로에 지정 된 테두리 장치입니다.

> [!IMPORTANT]
> Tor () 스위치의 맨 위에 필요한 지점 간 Ip를 사용 하 여 3 계층 업링크 (30 / 네트워크) 물리적 인터페이스에 구성 합니다. Azure Stack 작업을 지 원하는 TOR 스위치를 사용 하 여 계층 2 업링크를 사용 하는 지원 되지 않습니다. 

## <a name="bgp-routing"></a>BGP 라우팅
BGP와 같은 동적 라우팅 프로토콜을 사용 하 여 시스템은 항상 네트워크 변경 내용을 인식 하 고 관리를 용이 하 게 보장 합니다. Enhaced 보안에는 암호 TOR 테두리 사이의 피어 링 BGP에 설정할 수 있습니다. 

다음 다이어그램에 표시 된 것과 같이 개인 ip 보급 TOR 스위치에는 공간 차단 됩니다 접두사 목록을 사용 하 여. 개인 네트워크의 보급을 거부 하는 접두사 목록 및 맵으로 경로-TOR 및 테두리 사이의 연결에 적용 됩니다.

소프트웨어 부하 분산 장치 (SLB)를 Azure Stack 솔루션 내에서 실행 되는 TOR 장치에 피어 링 된 VIP 주소를 보급할 동적으로 수 있도록 합니다.

오류에서 복구 즉시 하 고 투명 하 게 사용자 트래픽을 위해이 VPC 또는 MLAG TOR 장치 사이 구성 된 다중 섀시 링크를 사용할 수 있습니다. 호스트 및 HSRP 또는 VRRP 제공 하는 집계 IP 네트워크에 대 한 중복을 네트워크입니다.

![BGP 라우팅](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>정적 라우팅
정적 라우팅 테두리 장치에 추가 구성이 필요합니다. 더 많은 수동 작업이 필요 하며 구성 오류로 인해 하 모든 문제와 변경 하기 전에 철저 한 분석 뿐만 아니라 관리 롤백 변경에 따라 더 많은 시간이 걸릴 수 있습니다. 권장 되는 라우팅 메서드를이 하 이지만 지원 됩니다.

정적 라우팅을 사용 하 여 네트워킹 환경에 Azure Stack 통합, 테두리 및 TOR 장치 간의 모든 4 개의 물리적 링크에 연결 되어 있어야 하 고 라우팅 작동 방법을 정적으로 인해 고가용성을 보장할 수 없습니다.

테두리 장치에 도착 하는 트래픽은 TOR 장치 P2P 가리키는 고정 경로 사용 하 여 구성 해야 합니다 *외부* 네트워크나 공용 Vip 및 *인프라* 네트워크입니다. 고정 경로 필요 합니다 *BMC* 하며 *외부* 배포에 대 한 네트워크. 연산자에 상주 하는 관리 리소스에 액세스 하려면 테두리에서 고정 경로 유지 하도록 선택 합니다 *BMC* 네트워크입니다. 고정 경로를 추가 *스위치 인프라* 하 고 *전환 관리* 네트워크는 선택 사항입니다.

TOR 장치 제공 테두리 장치에 모든 트래픽을 전송 하는 정적 기본 경로 사용 하 여 구성 합니다. 기본 규칙은 트래픽을 예외 테두리 연결 TOR에 적용할 액세스 제어 목록을 사용 하 여 차단 된 개인 공간입니다.

정적 라우팅 업링크를 테두리 및 TOR 스위치 사이만 적용 됩니다. 이 SLB 및 기타 구성 요소에 대 한 필수 도구 및 사용 하지 않도록 설정 하거나 제거할 수 있으므로 랙 내 BGP 동적 라우팅 사용 됩니다.

![정적 라우팅](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> 배포 후 BMC 네트워크 선택 사항입니다.

<sup>\*\*</sup> 스위치 인프라 네트워크는 선택적 이며 전체 네트워크 스위치 관리 네트워크에 포함 될 수 있습니다.

<sup>\*\*\*</sup> 스위치 관리 네트워크는 필수 항목이 며 스위치 인프라 네트워크에서 개별적으로 추가할 수 있습니다.

## <a name="transparent-proxy"></a>투명 프록시
모든 트래픽이 프록시를 사용 하도록 해야 하는 데이터 센터를 구성 해야 합니다는 *투명 프록시* 정책, 네트워크의 영역 간에 트래픽을 분리에 따라 처리 랙에서 모든 트래픽을 처리 하기.

> [!IMPORTANT]
> Azure Stack 솔루션 일반 웹 프록시를 지원 하지 않습니다.  

투명 프록시 (라고도 가로채기, 인라인 또는 강제 프록시) 특수 한 클라이언트 구성 없이 네트워크 계층에서 일반적인 통신을 차단 합니다. 클라이언트는 프록시는 주의 하지 해야 합니다.

![투명 프록시](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>다음 단계
[DNS 통합](azure-stack-integrate-dns.md)
