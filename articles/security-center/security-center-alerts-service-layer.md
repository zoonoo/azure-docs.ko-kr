---
title: Azure Security Center에서 Azure 서비스 계층에 대 한 검색 위협 | Microsoft Docs
description: 이 항목에서는 Azure Security Center에서 사용할 수 있는 Azure 서비스 계층 경고를 표시합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571726"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Azure Security Center에서 Azure 서비스 계층에 대 한 위협 검색

이 항목에서는 다음 Azure 서비스 계층을 모니터링 하는 경우 사용할 수 있는 Security Center 경고를 표시 합니다.

* [Azure 네트워크 계층](#network-layer)
* [Azure 관리 계층 (Azure Resource Manager) (미리 보기)](#management-layer)

>[!NOTE]
>아래에 제공 된 분석 Azure 내부 피드에 탭에서 Security Center를 활용 하는 원격 분석을 사용 하 여, 모든 리소스 유형에 적용할 수 있습니다.

## Azure 네트워크 계층<a name="network-layer"></a>

Security Center 네트워크 계층 분석 샘플에 기반한 [IPFIX 데이터](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), Azure core 라우터에 의해 수집 된 패킷 헤더는 합니다. 이 데이터 피드를 기준으로 Security Center machine learning 모델 식별 하 고 악성 트래픽을 활동 플래그 합니다. IP 주소를, Security Center는 Microsoft의 Threat Intelligence 데이터베이스를 활용 합니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**의심 스럽게 나가는 RDP 네트워크 활동**|샘플링 된 네트워크 트래픽 분석 리소스 배포에서 원래 비정상적으로 나가는 원격 데스크톱 프로토콜 (RDP) 통신이 검색 되었습니다. 이 작업은이 환경에 대해 비정상적으로 간주 및 리소스 이용 당하는 무차별 외부 RDP 끝점에 사용 되 고 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**의심 스럽게 나가는 RDP 네트워크 활동을 여러 대상**|샘플링 된 네트워크 트래픽 분석을 여러 대상 리소스 배포에서 원래 비정상적으로 나가는 원격 데스크톱 프로토콜 (RDP) 통신이 검색 되었습니다. 이 작업은이 환경에 대해 비정상적으로 간주 및 리소스 이용 당하는 무차별 외부 RDP 끝점에 사용 되 고 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**의심 스럽게 나가는 SSH 네트워크 활동**|샘플링 된 네트워크 트래픽 분석의 리소스 배포에서 시작 된 나가는 비정상적인 SSH (보안 셸) 통신이 검색 합니다. 이 작업은이 환경에 대해 비정상적으로 간주 및 리소스 이용 당하는 무차별 외부 SSH 끝점에 사용 되 고 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**의심 스럽게 나가는 SSH 네트워크 활동을 여러 대상**|샘플링 된 네트워크 트래픽 분석에는 여러 대상에 배포에서 리소스에서 시작 된 나가는 비정상적인 SSH (보안 셸) 통신이 검색. 이 작업은이 환경에 대해 비정상적으로 간주 및 리소스 이용 당하는 무차별 외부 SSH 끝점에 사용 되 고 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**여러 소스에서 의심 스럽게 들어오는 SSH 네트워크 활동**|배포에서 비정상적인 들어오는 SSH 통신 여러 원본에서 리소스를 검색 하는 샘플링 된 네트워크 트래픽 분석 합니다. 리소스에 연결 하는 다양 한 고유 Ip이이 환경에서 비정상적으로 간주 됩니다. 이 활동 여러 호스트 (봇 넷)의 SSH 인터페이스 무차별 대입 시도 나타낼 수 있습니다.|
|**의심 스럽게 들어오는 SSH 네트워크 활동**|배포에서 비정상적으로 들어오는 SSH 통신이 리소스를 검색 하는 샘플링 된 네트워크 트래픽 분석 합니다. 비교적 많은 수의 리소스에 대 한 들어오는 연결에이 환경에서 비정상적으로 간주 됩니다. 이 작업에 SSH 인터페이스 무차별 대입 시도 나타낼 수 있습니다.
|**여러 소스에서 의심 스럽게 들어오는 RDP 네트워크 활동**|배포에서 비정상적인 들어오는 RDP 통신 여러 원본에서 리소스를 검색 하는 샘플링 된 네트워크 트래픽 분석 합니다. 리소스에 연결 하는 다양 한 고유 Ip이이 환경에서 비정상적으로 간주 됩니다. 이 활동 여러 호스트 (봇 넷)의 RDP 인터페이스 무차별 대입 시도 나타낼 수 있습니다.|
|**의심 스럽게 들어오는 RDP 네트워크 활동**|샘플링 된 네트워크 트래픽 분석에는 배포에서 리소스에 들어오는 비정상적인 RDP 통신을 발견 했습니다. 비교적 많은 수의 리소스에 대 한 들어오는 연결에이 환경에서 비정상적으로 간주 됩니다. 이 작업에 SSH 인터페이스 무차별 대입 시도 나타낼 수 있습니다.|

Security Center에서 네트워크를 사용할 수는 방법을 이해 하려면 관련 내용은 위협 보호를 적용 하는 신호 [Azure Security Center에서 지능형 DNS 검색](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)합니다.
## Azure 관리 계층 (Azure Resource Manager) (미리 보기)<a name ="management-layer"></a>

>[!NOTE]
>Azure Resource Manager에 기반 하는 security Center 보호 계층은 현재 미리 보기 중입니다.

Security Center는 Azure에 대 한 제어 평면에 간주 되는 Azure Resource Manager 이벤트를 활용 하 여 보호 추가 계층을 제공 합니다. Azure Resource Manager 레코드를 분석 하 여 Security Center는 Azure 구독 환경에서 비정상적 이거나 잠재적으로 위험한 작업을 검색 합니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**실행 microBurst 도구 키트**|사용자 환경에서 실행 하는 알려진된 클라우드 환경 정찰 도구 키트 검색 되었습니다. 도구 "MicroBurst" (참조 https://github.com/NetSPI/MicroBurst) 는 공격자 (또는 침투 테스터) 구독 리소스에 안전 하지 않은 구성을 식별 매핑하고 기밀 정보를 누출을 사용할 수 있습니다.|
|**실행 azurite 도구 키트**|사용자 환경에서 실행 하는 알려진된 클라우드 환경 정찰 도구 키트 검색 되었습니다. 도구 "Azurite" (참조 https://github.com/mwrlabs/Azurite) 구독 리소스에 매핑하고 안전 하지 않은 구성을 식별 하는 공격자 (또는 침투 테스터)에서 사용할 수 있습니다.|
|**비활성 계정을 사용 하 여 의심 스러운 관리 세션**|구독 활동 로그 분석에서 의심 스러운 동작을 감지 합니다. 긴 시간 동안 사용에서 되지 않은 보안 주체의 수행 하 고 공격자에 대 한 지 속성을 보호할 수 있는 작업입니다.|
|**PowerShell을 사용 하 여 의심 스러운 관리 세션**|구독 활동 로그 분석에서 의심 스러운 동작을 감지 합니다. 구독 환경을 관리 하도록 정기적으로 PowerShell를 사용 하지 않는 보안 주체 되 PowerShell을 사용 하 고 공격자에 대 한 지 속성을 보호할 수 있는 작업을 수행 합니다.|
|**고급 Azure 지 속성 기술의 사용**|구독 활동 로그 분석에서 의심 스러운 동작을 감지 합니다. 사용자 지정된 역할 legitimized identity 엔터티 지정 되었습니다. 이 Azure 고객 환경에서 지 속성을 얻으려고 공격자가 발생할 수 있습니다.|
|**자주 사용되지 않는 국가에서 발생한 활동**|가 최근에 하지 않았거나 전혀 방문 하지 조직의 모든 사용자가 위치에서 활동이 발생 했습니다.<br/>이 검색은 이전 활동 위치를 고려하여 새 위치 및 드문 위치를 확인합니다. 이상 문제 검색 엔진은 조직의 사용자가 사용하는 이전 위치에 대한 정보를 저장합니다. 
|**익명 IP 주소에서 발생한 활동**|익명 프록시 IP 주소 감지으로 식별 된 IP 주소에서 사용자 작업입니다. <br/>이 프록시는 해당 디바이스의 IP 주소를 숨기려는 사용자가 사용하며 악의적인 의도로 사용될 수 있습니다. 이 검색은 조직의 다른 사용자가 널리 사용하는 태그가 잘못 지정된 IP 주소와 같은 “가양성”을 줄이는 기계 학습 알고리즘을 이용합니다.|
|**검색 불가능 한 이동**|두 개의 사용자 활동 (단일 또는 다중 세션은) 발생 한 지리적으로 먼 위치에서 발생 시간 보다 짧은 시간 안에 걸리는 사용자가 첫 번째 위치에서 두 번째 위치로 이동 합니다. 다른 사용자가 동일한 자격 증명을 사용 하 여 나타냅니다. <br/>이 검색은 기계 학습 명백한 "거짓 긍정", Vpn 및 조직의 다른 사용자가 정기적으로 사용 되는 위치와 같은 불가능 한 이동 조건에 영향을 주는 무시 하는 알고리즘을 활용 합니다. 검색에는 새 사용자 활동 패턴을 학습하는 7일의 초기 학습 기간이 있습니다.|

>[!NOTE]
> 위의 analytics의 여러 Microsoft 클라우드 앱 보안 (MCAS)에서 제공 됩니다. 이러한 분석을 활용 하려면 활성화는 MCAS 라이선스가 필요 합니다. MCAS 라이선스가 있는 경우 이러한 경고는 기본적으로 활성화 됩니다. 비활성화 합니다.
>
> 1. Security Center 블레이드에서 **보안 정책**을 선택합니다. 변경하려는 구독의 경우 **설정 편집**을 클릭합니다.
> 2. **위협 검색**을 클릭합니다.
> 3. **통합 사용**에서 **Microsoft Cloud App Security에서 내 데이터에 액세스하도록 허용**의 선택을 취소하고 **저장**을 클릭합니다.

>[!NOTE]
>Azure Security Center는 해당 리소스와 동일한 지역에서 고객 보안 관련 데이터를 저장합니다. Microsoft에 아직 배포 되지 않은 Azure Security Center는 리소스의 지역, 그런 다음 저장 데이터 미국의 됩니다. Microsoft Cloud App Security (MCAS)를 사용 하는 경우이 정보는 MCAS의 지리적 위치 규칙에 따라 저장 됩니다. 참조 [자세한 정보에 대 한 비 지역별 서비스에 대 한 데이터 저장소](http://azuredatacentermap.azurewebsites.net/)합니다.
