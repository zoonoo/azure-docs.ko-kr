---
title: App Service Environment 개요
description: App Service Environment 개요
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fbc498fcd654d16936c2548528e2600be68a2ad9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663841"
---
# <a name="app-service-environment-overview"></a>App Service Environment 개요 

> [!NOTE]
> 이 문서는 App Service Environment v3 (미리 보기)에 대 한 것입니다.
> 

Azure App Service Environment는 Azure App Service 앱을 매우 높은 확장성으로 안전하게 실행하기 위해 완전히 격리된 전용 환경을 제공하는 Azure App Service 기능입니다. 이 기능은 다음을 호스트할 수 있습니다.

- Windows 웹앱
- Linux 웹앱
- Docker 컨테이너
- Functions

ASE(App Service Environment)는 다음을 필요로 하는 애플리케이션 워크로드에 적합합니다.

- 높은 규모.
- 격리 및 보안된 네트워크 액세스
- 높은 메모리 사용률
- 초당 높은 요청 수 (RPS). 단일 Azure 지역 또는 여러 Azure 지역에서 여러 Ase를 만들 수 있습니다. 이러한 유연성으로 인해 Ase이 높은 상태 비저장 응용 프로그램을 사용 하는 것이 좋습니다.

ASE의 호스트 응용 프로그램은 한 명의 고객 에서만 응용 프로그램을 Vnet 합니다. 고객은 인바운드 및 아웃바운드 애플리케이션 네트워크 트래픽을 세부적으로 제어할 수 있습니다. 애플리케이션은 온-프레미스 회사 리소스에 VPN을 통한 고속 보안 연결을 설정할 수도 있습니다.

ASEv3에는 고유한 가격 책정 계층 인 Isolated V2가 제공 됩니다.
App Service 환경 v3은 네트워크 서브넷에서 앱을 보호 하는 주변을 제공 하 고 Azure App Service의 고유한 개인 배포를 제공 합니다.
여러 ASE를 수평적 크기 조정에 사용할 수 있습니다. ASE에서 실행 중인 앱은 WAF(웹 애플리케이션 방화벽) 등의 업스트림 디바이스에서 제어된 액세스를 가질 수 있습니다. 자세한 내용은 WAF(웹 애플리케이션 방화벽)를 참조하세요.

## <a name="usage-scenarios"></a>사용 시나리오

App Service Environment에는 다음과 같은 여러 사용 사례가 있습니다.

- 내부 lob (기간 업무) 응용 프로그램
- 30 개 이상의 ASP 인스턴스를 필요로 하는 응용 프로그램
- 내부 규정 준수 또는 보안 요구 사항을 충족 하는 단일 테 넌 트 시스템
- 네트워크 격리 응용 프로그램 호스팅
- 다중 계층 응용 프로그램

다중 테 넌 트 App Service의 앱이 네트워크 격리 리소스에 도달 하거나 네트워크 격리 상태가 될 수 있도록 하는 다양 한 네트워킹 기능이 있습니다. 이러한 기능은 응용 프로그램 수준에서 사용 하도록 설정 됩니다.  ASE를 사용 하는 경우 VNet에 배치할 앱에 대 한 추가 구성이 없습니다. 앱은 이미 VNet에 있는 네트워크 격리 환경에 배포 됩니다. 네트워크 격리 앱을 호스트 하는 ASE는 단일 테 넌 트 시스템 이기도 합니다. ASE를 사용 하는 다른 고객은 없습니다. 완벽 한 격리 스토리가 필요한 경우에는 ASE를 전용 하드웨어에 배포할 수도 있습니다. 네트워크 격리 응용 프로그램 호스팅, 단일 테 넌 트 및 기능 간 

## <a name="dedicated-environment"></a>전용 환경
ASE는 단일 구독에 단독으로 사용 되며 200 App Service 계획 인스턴스를 호스트할 수 있습니다. 단일 App Service 계획의 100개 인스턴스부터 100개의 단일 인스턴스 App Service 계획까지 가능하며 모든 항목은 이 두 계획 사이에 있습니다.

ASE는 프런트 엔드 및 작업자로 구성됩니다. 프런트 엔드는 ASE 내의 앱 요청에 대한 자동 부하 분산 및 HTTP/HTTPS 종료를 담당합니다. 프런트 엔드는 ASE의 App Service 계획이 스케일 아웃됨에 따라 자동으로 추가됩니다.

작업자는 고객 앱을 호스트하는 역할입니다. 작업자는 3가지 고정된 크기로 사용할 수 있습니다.

- VCPU/8gb RAM 2 개
- 4 개 vCPU/16gb RAM
- 8 vCPU/32gb RAM

고객은 프런트 엔드와 작업자를 관리할 필요가 없습니다. 모든 인프라는 자동으로입니다. ASE에서 App Service 계획을 생성하거나 규모를 변경함에 따라 필요한 인프라가 적절하게 추가되거나 제거됩니다.

Isolated V2 App Service 계획 인스턴스에 대 한 요금이 부과 됩니다. ASE에 App Service 계획이 없는 경우 두 코어 작업자의 인스턴스 하나를 사용 하 여 하나의 App Service 계획이 있는 것 처럼 요금이 청구 됩니다.

## <a name="virtual-network-support"></a>가상 네트워크 지원
ASE 기능은 고객의 Azure Resource Manager 가상 네트워크에 직접 Azure App Service를 배포하는 것입니다. ASE는 항상 가상 네트워크의 서브넷에 존재 합니다. 가상 네트워크의 보안 기능을 사용하여 앱에 대한 인바운드 및 아웃바운드 네트워크 통신을 제어할 수 있습니다.

네트워크 보안 그룹은 ASE가 있는 서브넷에 대한 인바운드 네트워크 통신을 제한합니다. NSG를 사용하여 WAF 및 네트워크 SaaS 공급자와 같은 업스트림 디바이스 및 서비스 뒤에서 앱을 실행할 수 있습니다.

또한 앱에서는 내부 데이터베이스 및 웹 서비스와 같은 회사 리소스에 자주 액세스해야 합니다. 온-프레미스 네트워크에 VPN이 연결되어 있는 가상 네트워크에 ASE를 배포하는 경우 ASE의 앱은 온-프레미스 리소스에 액세스할 수 있습니다. 이 기능은 VPN이 사이트 간이든 Azure ExpressRoute VPN이든 마찬가지입니다.

## <a name="preview"></a>미리 보기
App Service Environment v3은 공개 미리 보기로 제공 됩니다.  일부 기능은 미리 보기 진행 중에 추가 됩니다. ASEv3의 현재 제한 사항은 다음과 같습니다.

- 5 개 이상의 인스턴스를 초과 하는 App Service 계획의 크기를 조정할 수 없습니다.
- 개인 레지스트리에서 컨테이너를 가져올 수 없습니다.
- 현재 지원 되지 않는 App Service 기능이 고객 VNet을 통과 하지 못합니다.
- 인터넷에 액세스할 수 있는 끝점을 사용 하는 외부 배포 모델 없음
- 명령줄 지원 없음 (AZ CLI and PowerShell)
- Asev2)에서 ASEv3로 업그레이드 하는 기능이 없습니다.
- FTP 지원 안 함
- 고객 VNet을 통해 이동 하는 일부 App Service 기능에 대 한 지원은 없습니다. 앱 설정의 백업/복원, Key Vault 참조, 개인 컨테이너 레지스트리 사용 및 저장소에 대 한 진단 로깅은 서비스 끝점 또는 개인 끝점에서 작동 하지 않음
    
### <a name="asev3-preview-architecture"></a>ASEv3 미리 보기 아키텍처
ASEv3 preview에서 ASE는 개인 끝점을 사용 하 여 인바운드 트래픽을 지원 합니다. 개인 끝점은 GA로 부하 분산 장치로 바뀝니다. 미리 보기 중에는 ASE가 인터넷에 액세스할 수 있는 끝점을 기본적으로 지원 하지 않습니다. 이러한 목적을 위해 Application Gateway를 추가할 수 있습니다. ASE에는 두 서브넷의 리소스가 필요 합니다.  인바운드 트래픽은 개인 끝점을 통해 전달 됩니다. 개인 끝점에서 사용할 수 있는 사용 가능한 주소가 있는 모든 서브넷에 개인 끝점을 배치할 수 있습니다.  아웃 바운드 서브넷은 비어 있고 Microsoft. Web/hostingEnvironments에 위임 되어야 합니다. ASE에서 사용 하는 동안에는 아웃 바운드 서브넷을 다른 항목에 사용할 수 없습니다.

ASEv3를 사용 하는 경우 ASE 서브넷에는 인바운드 또는 아웃 바운드 네트워킹 요구 사항이 없습니다. 네트워크 보안 그룹 및 경로 테이블을 사용 하 여 트래픽을 제어할 수 있으며이는 응용 프로그램 트래픽에만 영향을 줍니다. 작업을 실행 취소할 수 없으므로 ASE와 연결 된 개인 끝점을 삭제 하지 마세요. Ase에 사용 되는 개인 끝점은 ASE의 모든 앱에 사용 됩니다. 
