---
title: Azure 서비스 계층 위협 검색-Azure Security Center
description: 이 항목에서는 Azure Security Center에서 사용할 수 있는 Azure 서비스 계층 경고를 제공 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665702"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Azure Security Center에서 Azure 서비스 계층에 대 한 위협 감지

이 항목에서는 다음 Azure 서비스 계층을 모니터링할 때 사용할 수 있는 Azure Security Center 경고를 제공 합니다.

* [Azure 네트워크 계층](#network-layer)
* [Azure 관리 계층 (Azure Resource Manager) (미리 보기)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

## Azure 네트워크 계층<a name="network-layer"></a>

Security Center 네트워크 계층 분석은 Azure core 라우터가 수집 하는 패킷 헤더 인 샘플 [Ipfix 데이터](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)를 기반으로 합니다. 이 데이터 피드에 따라 Security Center는 machine learning 모델을 사용 하 여 악성 트래픽 활동을 식별 하 고 플래그를 지정 합니다. 또한 Security Center는 Microsoft 위협 인텔리전스 데이터베이스를 사용 하 여 IP 주소를 보강 합니다.

일부 네트워크 구성에서 의심 스러운 네트워크 활동에 대 한 경고를 생성 하는 Security Center 제한할 수 있습니다. 네트워크 경고를 생성 Security Center를 확인 하려면 다음을 확인 하십시오.

- 가상 컴퓨터에 공용 IP 주소가 있거나, 공용 ip 주소를 사용 하는 부하 분산 장치에 있습니다.

- 가상 머신의 네트워크 송신 트래픽이 외부 ID 솔루션에 의해 차단 되지 않습니다.

- 가상 머신이 의심 스러운 통신이 발생 한 전체 시간에 대해 동일한 IP 주소를 할당 받았습니다. 이는 관리 되는 서비스의 일부로 생성 된 Vm (예: AKS, Databricks)에도 적용 됩니다.

Azure 네트워크 계층 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azurenetlayer)을 참조 하세요.

Security Center 네트워크 관련 신호를 사용 하 여 위협 방지를 적용 하는 방법에 대 한 자세한 내용은 [Security Center에서 추론 DNS](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)검색을 참조 하세요.


## Azure 관리 계층 (Azure Resource Manager) (미리 보기)<a name ="management-layer"></a>

Azure Resource Manager 기반 Security Center의 보호 계층은 현재 미리 보기로 제공 됩니다.

Security Center는 Azure Resource Manager 이벤트를 사용 하 여 추가 보호 계층을 제공 하며,이는 Azure의 제어 평면으로 간주 됩니다. Security Center Azure Resource Manager 레코드를 분석 하 여 Azure 구독 환경에서 비정상 이거나 잠재적으로 유해한 작업을 검색 합니다.

Azure Resource Manager (미리 보기) 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azureresourceman)을 참조 하세요.



>[!NOTE]
> 이전 분석의 일부는 Microsoft Cloud App Security에 의해 제공 됩니다. 이러한 분석을 활용 하려면 Cloud App Security 라이선스를 활성화 해야 합니다. Cloud App Security 라이선스가 있는 경우 이러한 경고는 기본적으로 사용 하도록 설정 됩니다. 사용 하지 않도록 설정 하려면:
>
> 1. **Security Center** 블레이드에서 **보안 정책**을 선택 합니다. 변경 하려는 구독에 대해 **설정 편집**을 선택 합니다.
> 2. **위협 검색**을 선택 합니다.
> 3. **통합 사용**에서 Microsoft Cloud App Security에서 **내 데이터에 액세스 하도록 허용**을 선택 취소 하 고 **저장**을 선택 합니다.

>[!NOTE]
>Security Center는 보안과 관련 된 고객 데이터를 해당 리소스와 동일한 지역에 저장 합니다. Microsoft에서 아직 리소스의 지역에 Security Center 배포 하지 않은 경우에는 데이터를 미국에 저장 합니다. Cloud App Security 사용 하도록 설정 된 경우이 정보는 Cloud App Security의 지리적 위치 규칙에 따라 저장 됩니다. 자세한 내용은 [비 지역 서비스에 대 한 데이터 저장소](https://azuredatacentermap.azurewebsites.net/)를 참조 하세요.

## Azure Key Vault (미리 보기)<a name="azure-keyvault"></a>

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

Azure Security Center에는 Azure Key Vault에 대 한 Azure 네이티브 및 고급 위협 방지 기능이 포함 되어 있으며, 추가 보안 인텔리전스 계층을 제공 합니다. Security Center은 Key Vault 계정을 액세스 하거나 악용 하려는 비정상적인 잠재적으로 유해한 시도를 검색 합니다. 이 보호 계층을 사용 하면 보안 전문가가 아니더라도 타사 보안 모니터링 시스템을 관리할 필요 없이 위협을 해결할 수 있습니다.  

비정상적인 활동이 발생 하면 Security Center는 경고를 표시 하 고 필요에 따라 구독 관리자에 게 전자 메일을 통해 보냅니다. 이러한 경고는 의심 스러운 활동의 세부 정보와 위협을 조사 하 고 수정 하는 방법에 대 한 권장 사항을 포함 합니다. 

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

Azure Key Vault 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azurekv)을 참조 하세요.
