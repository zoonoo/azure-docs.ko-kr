---
title: Azure Stack에서 Azure App Service 서버 역할의 용량 계획 | Microsoft Docs
description: Azure Stack에서 Azure App Service 서버 역할의 용량 계획
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 06bafbcf3e668ba17b1245b9352e942e02569997
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852375"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Azure Stack에서 Azure App Service 서버 역할의 용량 계획

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure App Service on Azure Stack의 프로덕션 준비 배포를 설정 하려면 지원 하도록 시스템을 예상 용량 계획 해야 합니다.  

이 문서는 계산 인스턴스 및 모든 프로덕션 배포에 사용 해야 하는 Sku 계산의 최소 수에 대 한 지침을 제공 합니다.

이러한 지침을 사용 하 여 App Service 용량 전략을 계획할 수 있습니다.

| 앱 서비스 서버 역할 | 최소 인스턴스 수를 권장 합니다. | 권장 되는 계산 SKU|
| --- | --- | --- |
| Controller | 2 | A1 |
| 프런트 엔드 | 2 | A1 |
| 관리 | 2 | A3 |
| 게시자 | 2 | A1 |
| 웹 작업자-공유 | 2 | A1 |
| 웹 작업자-전용 | 계층 당 2 개 | A1 |

## <a name="controller-role"></a>컨트롤러 역할

**권장 최소**: A1 표준의 두 인스턴스

Azure App Service 컨트롤러는 일반적으로 CPU, 메모리 및 네트워크 리소스 사용이 적습니다 경험입니다. 그러나 고가용성을 위해 두 명의 컨트롤러가 있어야 합니다. 두 명의 컨트롤러도 허용 하는 컨트롤러의 최대 수 있습니다. 직접 만들 수 있습니다 두 번째 웹 사이트 컨트롤러 설치 관리자에서 배포 중입니다.

## <a name="front-end-role"></a>프런트 엔드 역할

**권장 최소**: A1 표준의 두 인스턴스

웹 작업자 가용성에 따라 웹 작업자로 프런트 엔드 경로 요청입니다. 고가용성을 위해 둘 이상의 프런트 엔드를 해야 하 고 두 개 이상의 할 수 있습니다. 용량 계획 시 각 코어에 초당 약 100 개 요청을 처리할 수 있도록 하는 것이 좋습니다.

## <a name="management-role"></a>관리 역할

**권장 최소**: 두 인스턴스의 표준 A3

Azure App Service 관리 역할은 Azure App Service 리소스 관리자 및 API 끝점, 포털 확장 (관리자, 테 넌 트, Functions 포털)와 데이터 서비스를 담당 합니다. 관리 서버 역할은 일반적으로 프로덕션 환경에서 4GB RAM에 대 한 정보만 필요합니다. 그러나 다양 한 관리 작업 (예: 웹 사이트 만들기)을 수행할 때 높은 CPU 수준이 발생할 수 있습니다 것입니다. 고가용성을 위해 둘 이상의 서버에서이 역할에 할당 해야 하 고 서버당 둘 이상의 코어가 있습니다.

## <a name="publisher-role"></a>게시자 역할

**권장 최소**: A1 표준의 두 인스턴스

많은 사용자가 동시에 게시 하는 경우 게시자 역할의 CPU 사용량이 발생할 수 있습니다. 고가용성을 위해 둘 이상의 게시자 역할을 사용할 수 있는지를 확인 합니다. 게시자는 FTP/FTPS 트래픽도 처리합니다.

## <a name="web-worker-role"></a>웹 작업자 역할

**권장 최소**: A1 표준의 두 인스턴스

고가용성을 위해 4 개 이상의 웹 작업자 역할, 공유 웹 사이트 모드에 대 한 두 및 제공 하려는 각 전용된 작업자 계층에 대해 두 개 있어야 합니다. 공유 및 전용 계산 모드 테 넌 트에 서로 다른 수준의 서비스를 제공 합니다. 많은 고객에 게 경우 추가 웹 작업자를 할 수 있습니다.

- 전용된 계산 모드 작업자 계층 (리소스를 많이 사용)를 사용 합니다.
- 공유 계산 모드에서 실행 합니다.

사용자가 컴퓨팅 전용된 모드로 SKU에 대 한 App Service 계획을 만든 후 해당 App Service 계획에 지정 된 웹 작업자 수가 더 이상 사용자가 사용할 수입니다.

Azure Functions 소비 계획 모델에서 사용자에 게를 제공 하려면 공유 웹 작업자를 배포 해야 합니다.

사용 하려면 공유 웹 작업자 역할 수를 결정할 때 이러한 고려 사항을 검토 합니다.

- **메모리**: 메모리는 웹 작업자 역할에 대 한 가장 중요 한 리소스입니다. 메모리가 부족 하 여 디스크에서 가상 메모리를 바꿀 때 웹 사이트 성능에 영향을 줍니다. 각 서버 운영 체제에 대해 약 1.2GB ram 필요합니다. 웹 사이트를 실행 하려면이 임계값 보다 RAM은 사용할 수 있습니다.
- **활성 웹 사이트 비율**: 일반적으로 Azure App service를 사용 하 여 Azure Stack 배포에서 응용 프로그램의 약 5% 활성화 됩니다. 그러나 특정된 시점에 활성화 된 응용 프로그램의 백분율 높거나 낮은 수 있습니다. 활성 응용 프로그램 속도 5%를 사용 하 여 Azure Stack 배포의 Azure App Service에 응용 프로그램의 최대 20 회 미만인 활성 웹 사이트 (5 x 20 = 100) 수 해야 합니다.
- **평균 메모리 사용량**: 프로덕션 환경에서 관찰 된 응용 프로그램에 대 한 평균 메모리 사용량은 약 70MB입니다. 이 공간을 사용 하 여 모든 웹 작업자 역할 컴퓨터 또는 Vm에서 할당 된 메모리를 계산할 수 있습니다 다음과 같습니다.

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   예를 들어 10 개의 웹 작업자 역할을 실행 하는 환경에서 5,000 응용 프로그램의 경우 각 웹 작업자 역할 VM 7060-RAM이 있어야 합니다.

   `5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= about 7 GB)`

   작업자 인스턴스를 더 추가 하는 방법에 대 한 내용은 [더 많은 작업자 역할 추가](azure-stack-app-service-add-worker-roles.md)합니다.

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>업그레이드 및 유지 관리 중 전용된 작업자에 대 한 추가 고려 사항

업그레이드 및 작업자의 유지 관리 하는 동안 Azure Stack에서 Azure App Service는 한 번에 각 작업자 계층의 20%에 유지 관리를 수행 합니다.  따라서 클라우드 관리자가 테 넌 트 업그레이드 및 유지 관리 하는 동안 서비스의 손실을 발생 하지 않도록 하려면 작업자 계층 당 할당 되지 않은 작업자의 20% 풀을 항상 유지 해야 합니다.  예를 들어 10 명의 작업 자가 작업자 계층에 있는 경우 2 업그레이드 수 있도록 할당 됩니다. 및 유지 관리 하면 전체 10 작업자 할당 될 경우 해야 작업자 계층으로 강화할 할당 되지 않은 작업자 풀을 유지 하는 확인 해야 합니다. 하지만 워크 로드는 워크 로드는 계속 작동 없으면 중 사용 가능한 할당 되지 않은 작업 자가 없습니다 업그레이드 후 있습니다 되도록 할당 되지 않은 작업자를 테 넌 트 워크 로드에 대 한 잠재적인 될 Azure App Service는 이동 하는 업그레이드 및 유지 관리 중 가동 중지 시간이 발생 합니다.  공유 작업자와 관련 하 여 고객에 게 않아도 서비스를 할당 테 넌 트 응용 프로그램 내의 사용 가능한 작업 자가 자동으로 하지만 고가용성을 위해 최소한 두 명의 작업 자가이 대 한 대로 추가 작업자를 프로 비전 계층입니다.

클라우드 관리자는 Azure Stack 관리 포털에서 앱 서비스 관리 영역에서 해당 작업자 계층 할당을 모니터링할 수 있습니다.  App Service로 이동 하 고 왼쪽 창에서 작업자 계층을 선택 합니다.  작업자 계층은 작업자 계층 이름, 크기, 사용 되는 이미지, 사용 가능한 작업자 수 (할당), 각 계층에서 작업자의 총 수 및 작업자 계층의 전반적인 상태를 보여 줍니다.

![App Service 관리-작업자 계층][1]

## <a name="file-server-role"></a>파일 서버 역할

파일 서버 역할에 대 한 개발 및 테스트에 대 한 독립 실행형 파일 서버를 사용할 수 있습니다. 예를 들어, Azure App Service에는 Azure Stack 개발 키트 ASDK ()를 배포할 때 사용할 수 있습니다 [템플릿](https://aka.ms/appsvconmasdkfstemplate)합니다.  프로덕션 환경에서는 미리 구성 된 Windows 파일 서버 또는 미리 구성 된 비 Windows 파일 서버를 사용 해야 합니다.

프로덕션 환경에서는 파일 서버 역할은 집중형 디스크 I/O을 경험 했습니다. 모든 사용자 웹 사이트에 대 한 콘텐츠 및 응용 프로그램 파일을 보관 하기 때문에 미리 구성 해야이 역할에 대 한 다음 리소스 중 하나:

- Windows 파일 서버
- Windows 파일 서버 클러스터
- 비 Windows 파일 서버
- 비 Windows 파일 서버 클러스터
- NAS (Network Attached Storage) 장치

자세한 내용은 다음 문서를 참조 하세요 [파일 서버를 프로 비전](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

[Azure Stack에서 App Service를 사용 하 여 시작 하기 전에](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png