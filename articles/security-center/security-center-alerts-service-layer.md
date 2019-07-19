---
title: Azure Security Center에서 Azure 서비스 계층에 대 한 위협 감지 | Microsoft Docs
description: 이 항목에서는 Azure Security Center에서 사용할 수 있는 Azure 서비스 계층 경고를 제공 합니다.
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
ms.author: v-mohabe
ms.openlocfilehash: f795822d76def4a6695a4746fba7e8566041cb2b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295543"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Azure Security Center에서 Azure 서비스 계층에 대 한 위협 감지

이 항목에서는 다음 Azure 서비스 계층을 모니터링할 때 사용할 수 있는 Security Center 경고를 제공 합니다.

* [Azure 네트워크 계층](#network-layer)
* [Azure 관리 계층 (Azure Resource Manager) (미리 보기)](#management-layer)

>[!NOTE]
>Azure 내부 피드를 활용 하 여 Security Center 하는 원격 분석을 사용 하 여 아래에 제공 된 분석을 모든 리소스 유형에 적용할 수 있습니다.

## Azure 네트워크 계층<a name="network-layer"></a>

Security Center 네트워크 계층 분석은 Azure core 라우터가 수집 하는 패킷 헤더 인 샘플 [Ipfix 데이터](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)를 기반으로 합니다. 이 데이터 피드에 따라 machine learning 모델 Security Center 악성 트래픽 활동을 식별 하 고 플래그를 지정 합니다. IP 주소를 보강 하기 위해 Security Center Microsoft의 위협 인텔리전스 데이터베이스를 활용 합니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**의심 스러운 나가는 RDP 네트워크 활동**|샘플링 된 네트워크 트래픽 분석은 배포의 리소스에서 발생 하는 비정상적인 RDP (보내는 원격 데스크톱 프로토콜) 통신을 검색 했습니다. 이 작업은이 환경에서 비정상으로 간주 되며 리소스가 손상 되어 외부 RDP 끝점의 무작위 적용에 사용 되 고 있음을 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**여러 대상으로 의심 스러운 나가는 RDP 네트워크 활동**|샘플링 된 네트워크 트래픽 분석은 배포의 리소스에서 여러 대상으로 보내는 비정상적인 RDP (보내는 원격 데스크톱 프로토콜) 통신을 검색 했습니다. 이 작업은이 환경에서 비정상으로 간주 되며 리소스가 손상 되어 외부 RDP 끝점의 무작위 적용에 사용 되 고 있음을 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**의심 스러운 나가는 SSH 네트워크 활동**|샘플링 된 네트워크 트래픽 분석은 배포의 리소스에서 시작 된 비정상적인 SSH (보내는 Secure Shell) 통신을 검색 했습니다. 이 작업은이 환경에서 비정상으로 간주 되며 리소스가 손상 되어 외부 SSH 끝점의 무작위 적용에 사용 되 고 있음을 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**여러 대상으로 의심 스러운 나가는 SSH 네트워크 활동**|샘플링 된 네트워크 트래픽 분석은 배포의 리소스에서 여러 대상으로 보내는 비정상적인 SSH (보내는 Secure Shell) 통신을 검색 했습니다. 이 작업은이 환경에서 비정상으로 간주 되며 리소스가 손상 되어 외부 SSH 끝점의 무작위 적용에 사용 되 고 있음을 나타낼 수 있습니다. 이러한 유형의 활동으로 인해 외부 엔터티가 IP에 악성 플래그를 설정할 수 있습니다.|
|**여러 원본에서 의심 스러운 들어오는 SSH 네트워크 활동**|샘플링 된 네트워크 트래픽 분석을 통해 여러 원본에서 배포의 리소스로 비정상적인 들어오는 SSH 통신이 감지 되었습니다. 리소스에 연결 하는 다양 한 고유 Ip는이 환경에서 비정상으로 간주 됩니다. 이 활동은 여러 호스트 (봇 넷)에서 SSH 인터페이스를 무차별 암호 대입 하려고 시도 했음을 나타낼 수 있습니다.|
|**의심 스러운 들어오는 SSH 네트워크 활동**|샘플링 된 네트워크 트래픽 분석을 통해 배포에서 리소스에 대 한 비정상적인 들어오는 SSH 통신이 감지 되었습니다. 리소스에 대해 상대적으로 많은 수의 들어오는 연결이이 환경에서 비정상으로 간주 됩니다. 이 활동은 SSH 인터페이스의 무차별 암호 대입 시도를 나타낼 수 있습니다.
|**여러 원본에서 의심 스러운 들어오는 RDP 네트워크 활동**|샘플링 된 네트워크 트래픽 분석을 통해 여러 원본에서 배포의 리소스로 비정상적인 들어오는 RDP 통신이 감지 되었습니다. 리소스에 연결 하는 다양 한 고유 Ip는이 환경에서 비정상으로 간주 됩니다. 이 활동은 여러 호스트 (봇 넷)에서 RDP 인터페이스를 무차별 암호 대입 하려고 시도 했음을 나타낼 수 있습니다.|
|**의심 스러운 들어오는 RDP 네트워크 활동**|샘플링 된 네트워크 트래픽 분석을 통해 배포에서 리소스에 대 한 비정상적인 들어오는 RDP 통신이 감지 되었습니다. 리소스에 대해 상대적으로 많은 수의 들어오는 연결이이 환경에서 비정상으로 간주 됩니다. 이 활동은 SSH 인터페이스의 무차별 암호 대입 시도를 나타낼 수 있습니다.|

Security Center 네트워크 관련 신호를 사용 하 여 위협 방지를 적용 하는 방법을 이해 하려면 [Azure Security Center에서 추론 DNS](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)검색을 참조 하세요.
## Azure 관리 계층 (Azure Resource Manager) (미리 보기)<a name ="management-layer"></a>

>[!NOTE]
>Azure Resource Manager 기반 Security Center 보호 계층은 현재 미리 보기로 제공 됩니다.

Security Center는 Azure의 제어 평면으로 간주 되는 Azure Resource Manager 이벤트를 활용 하 여 추가 보호 계층을 제공 합니다. Security Center Azure Resource Manager 레코드를 분석 하 여 Azure 구독 환경에서 비정상 이거나 잠재적으로 유해한 작업을 검색 합니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**마이크로 버스트 도구 키트 실행**|사용자 환경에서 알려진 클라우드 환경 정찰 도구 키트를 실행 했습니다. 도구 "마이크로 버스트" ( https://github.com/NetSPI/MicroBurst) 공격자 또는 침투 테스터는를 사용 하 여 구독 리소스를 매핑하고, 안전 하지 않은 구성을 식별 하 고, 기밀 정보를 누출) 할 수 있습니다.|
|**Azurite toolkit 실행**|사용자 환경에서 알려진 클라우드 환경 정찰 도구 키트를 실행 했습니다. "Azurite" 도구 (공격자 또는 https://github.com/mwrlabs/Azurite) 침투 테스터는를 사용 하 여 구독 리소스를 매핑하고 안전 하지 않은 구성을 식별할 수 있음)를 참조 하세요.|
|**비활성 계정을 사용 하 여 의심 스러운 관리 세션**|구독 활동 로그 분석에서 의심 스러운 동작을 검색 했습니다. 오랜 시간 동안 사용 되지 않는 보안 주체는 이제 공격자의 지 속성을 보호할 수 있는 작업을 수행 합니다.|
|**PowerShell을 사용 하 여 의심 스러운 관리 세션**|구독 활동 로그 분석에서 의심 스러운 동작을 검색 했습니다. 정기적으로 PowerShell을 사용 하 여 구독 환경을 관리 하지 않는 보안 주체는 이제 PowerShell을 사용 하 고 공격자에 대 한 지 속성을 보호할 수 있는 작업을 수행 합니다.|
|**고급 Azure 지 속성 기술 사용**|구독 활동 로그 분석에서 의심 스러운 동작을 검색 했습니다. 사용자 지정 된 역할이 legitimized id 엔터티를 제공 했습니다. 이로 인해 공격자가 Azure 고객 환경에서 지 속성을 얻을 수 있습니다.|
|**자주 사용되지 않는 국가에서 발생한 활동**|조직의 모든 사용자가 최근에 방문 하지 않았거나 방문 하지 않은 위치에서 활동이 발생 했습니다.<br/>이 검색은 이전 활동 위치를 고려하여 새 위치 및 드문 위치를 확인합니다. 이상 문제 검색 엔진은 조직의 사용자가 사용하는 이전 위치에 대한 정보를 저장합니다. 
|**익명 IP 주소에서 발생한 활동**|익명 프록시 IP 주소로 식별 된 IP 주소에서의 사용자 활동이 검색 되었습니다. <br/>이 프록시는 해당 디바이스의 IP 주소를 숨기려는 사용자가 사용하며 악의적인 의도로 사용될 수 있습니다. 이 검색은 조직의 다른 사용자가 널리 사용하는 태그가 잘못 지정된 IP 주소와 같은 “가양성”을 줄이는 기계 학습 알고리즘을 이용합니다.|
|**불가능 한 이동 감지**|사용자가 첫 번째 위치에서 두 번째 위치로 이동 하는 데 걸리는 시간 보다 짧은 시간 내에 지리적으로 먼 위치에서 발생 하는 두 개의 사용자 활동 (단일 또는 다중 세션)이 발생 했습니다. 이는 다른 사용자가 동일한 자격 증명을 사용 하 고 있음을 나타냅니다. <br/>이 검색은 조직의 다른 사용자가 정기적으로 사용 하는 Vpn 및 위치와 같은 불가능 한 이동 조건에 영향을 주는 확실 한 "거짓 긍정"을 무시 하는 기계 학습 알고리즘을 활용 합니다. 검색에는 새 사용자 활동 패턴을 학습하는 7일의 초기 학습 기간이 있습니다.|

>[!NOTE]
> 위의 몇 가지 분석은 MCAS (Microsoft Cloud App Security에서 구동 됩니다. 이러한 분석을 활용 하려면 활성화 된 MCAS 라이선스가 필요 합니다. MCAS 라이선스가 있는 경우 이러한 경고는 기본적으로 사용 하도록 설정 됩니다. 사용 하지 않도록 설정 하려면:
>
> 1. Security Center 블레이드에서 **보안 정책**을 선택합니다. 변경하려는 구독의 경우 **설정 편집**을 클릭합니다.
> 2. **위협 검색**을 클릭합니다.
> 3. **통합 사용**에서 **Microsoft Cloud App Security에서 내 데이터에 액세스하도록 허용**의 선택을 취소하고 **저장**을 클릭합니다.

>[!NOTE]
>Azure Security Center는 보안과 관련 된 고객 데이터를 해당 리소스와 동일한 지역에 저장 합니다. Microsoft가 리소스의 지역에 Azure Security Center를 아직 배포 하지 않은 경우에는 미국의 데이터를 저장 합니다. MCAS (Microsoft Cloud App Security)를 사용 하는 경우이 정보는 MCAS의 지리적 위치 규칙에 따라 저장 됩니다. [자세한 내용은 비 지역별 서비스를 위한 데이터 저장소를](http://azuredatacentermap.azurewebsites.net/)참조 하세요.
