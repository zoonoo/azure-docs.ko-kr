---
title: Azure Stack 및 글로벌 Azure 비교 | Microsoft Docs
description: Microsoft Azure 및 Azure Stack 서비스 제품군에 하나의 Azure 에코 시스템에서 제공 하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 5e871d467fec476f1dac77fb879d180ea2322c80
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650106"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>전역 Azure, Azure Stack 및 Azure Stack HCI 간의 차이점

Microsoft은 Azure 및 Azure Stack 서비스 제품군에 하나의 Azure 에코 시스템에서 제공합니다. 사용 하 여 동일한 응용 프로그램 모델, 셀프 서비스 포털 및 Api Azure Resource Manager를 사용 하 여 하 든 비즈니스 전역 Azure를 사용 하 여 온-프레미스 리소스에 클라우드 기반 기능을 제공 합니다.

이 문서는 전역 Azure, Azure Stack 및 Azure Stack HCI 기능에 설명 하 고는 적합 한 조직에 대 한 Microsoft 클라우드 기반 서비스를 제공 하기 위해 일반적인 시나리오 권장 사항을 제공 합니다.

![Azure 에코 시스템 개요](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>글로벌 Azure

Microsoft Azure는 기업의 비즈니스를 위해 끊임없이 확장되는 클라우드 서비스입니다. 익숙한 도구 및 프레임워크를 사용하여 거대한 글로벌 네트워크에서 애플리케이션을 구축, 관리하고 사용할 수 있습니다.

전역 Azure는 전 세계 54 지역에서 사용할 수 있는 100 개 이상의 서비스를 제공합니다. 글로벌 Azure 서비스의 최신 목록에 대 한 참조를 [ *지역별 사용 가능한 제품*](https://azure.microsoft.com/regions/services)합니다. Azure에서 사용 가능한 서비스는 일반적으로 사용할 수 있는 여부 뿐만 범주별으로 나열 된 또는 미리 보기를 통해 사용할 수입니다.

글로벌 Azure 서비스에 대 한 자세한 내용은 참조 하세요. [Azure 시작](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1)합니다.

## <a name="azure-stack"></a>Azure Stack

Azure Stack에 민첩성을 제공 하는 Azure의 확장 이며 클라우드 컴퓨팅 온-프레미스 환경과 혁신을 가져옵니다. 배포 된 온-프레미스에서 Azure Stack (인터넷과 Azure) 또는 연결이 끊어진된 환경에서 인터넷에 연결 되지 않은 연결 하거나 일관 된 Azure 서비스를 제공 하기 사용할 수 있습니다. Azure Stack 인프라-as a Service (IaaS) 소프트웨어-as a Service (SaaS) 라는 핵심 구성 요소를 포함 하는 전역 Azure와 동일한 기본 기술을 사용 하 고 옵션 플랫폼-as a Service (PaaS) 기능을 포함 하 여:

- Windows 및 Linux 용 azure Vm
- Azure Web Apps 및 Functions
- Azure Key Vault
- Azure 리소스 관리자
- Azure Marketplace
- 컨테이너
- Azure IoT Hub 및 Event Hubs
- 관리 도구 (제품의 경우 RBAC 계획 등.)

Azure Stack은 Microsoft에서 운영 하지 때문에 Azure Stack의 PaaS 기능은 선택 사항-고객으로 작동 합니다. 즉, 추상 기본 인프라 및 최종 사용자 반대쪽으로 프로세스를 준비 해야 하는 경우 최종 사용자가 원하는 모든 PaaS 서비스를 제공할 수 있습니다. 그러나 Azure Stack은 App Service, SQL database 및 MySQL 데이터베이스를 포함 하 여 여러 선택적 PaaS 서비스 공급자를 포함 합니다. 다음은 다중 테 넌 트 준비 되 면 Azure Stack을 업데이트 하는 표준 시간이 지남에 따라 업데이트 되 고 Azure Stack 포털에 표시 되므로 리소스 공급자로 전달 된 Azure Stack을 사용 하 여 잘 통합입니다.

위에서 설명한 리소스 공급자 외에도 가지 추가 PaaS 서비스 사용 가능 하 고으로 테스트 [Azure Resource Manager 템플릿 기반 솔루션](https://github.com/Azure/AzureStack-QuickStart-Templates) IaaS에서 실행 되는 Azure Stack 연산자가 제공 하 하지만 포함 하 여 사용자에 게 PaaS 서비스:

- Service Fabric
- Kubernetes 컨테이너 서비스
- IoT Hub 및 Event Hub
- Etherium 블록 체인
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Azure Stack에 대 한 예제 사용 사례:

- 재무 모델링
- Clinical 및 청구 데이터
- IoT 장치 분석
- 소매 assortment 최적화
- 공급망 최적화
- 산업용 IoT
- 예측 유지 관리
- 스마트 시티
- 시민 참여

Azure Stack에 자세히 알아보려면 [Azure Stack 이란](azure-stack-overview.md)합니다.

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Azure Stack HCI 솔루션을 사용 하면 온-프레미스 가상 머신을 실행 하 여 하이퍼 수렴 형 인프라 (HCI) 솔루션을 사용 하 여 Azure에 쉽게 연결할 수 있습니다. 온-프레미스에서 일관된 Azure 서비스를 통해 클라우드 애플리케이션을 빌드하고 실행하여 규정 또는 기술 요구 사항을 충족하세요. 실행 중인 가상화 된 응용 프로그램 온-프레미스에서 하는 것 외에도 Azure Stack HCI를 사용 하면 대체 에이징 서버 인프라를 통합 하 고 Windows Admin Center 사용 하 여 클라우드 서비스에 대 한 Azure에 연결할 수 있습니다.

Azure Stack HCI Hyper-v 및 저장소 공간 다이렉트 사용 하 여 Windows Server 2019 소프트웨어 정의 데이터 센터 (SDDC)에서 제공 하는 유효성이 검사 된 HCI 솔루션을 제공 합니다. Windows Admin Center 관리에 사용 되 고와 같은 Azure 서비스에 대 한 액세스를 통합 합니다.

- Azure Backup
- Azure Site Recovery
- Azure 모니터링 및 업데이트

업데이트 된 목록은 Azure 서비스에 대 한 참조를 Azure Stack HCI를 연결할 수 있습니다 [Windows Server를 Azure 하이브리드 서비스를 연결](https://docs.microsoft.com/windows-server/azure-hybrid-services/index)합니다.

### <a name="example-use-cases-for-azure-stack-hci"></a>Azure Stack HCI에 대 한 예제 사용 사례
- 원격 또는 분기 office 시스템
- 데이터 센터 통합
- 가상 데스크톱 인프라
- 비즈니스에 중요 한 인프라
- 저렴 한 비용 저장소
- 클라우드에서 고가용성 및 재해 복구
- SQL Server와 같은 엔터프라이즈 앱

방문 합니다 [Azure Stack HCI 웹 사이트](https://azure.microsoft.com/overview/azure-stack/hci/) 70 + Azure Stack HCI 솔루션 보려면 Microsoft 파트너에서 현재 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack 관리 기본 사항](azure-stack-manage-basics.md)

[빠른 시작: Azure Stack 관리 포털 사용](azure-stack-manage-portals.md)
