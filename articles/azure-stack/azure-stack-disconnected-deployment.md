---
title: Azure Stack에 대 한 azure 연결 되지 않은 배포 결정 통합 시스템 | Microsoft Docs
description: 다중 노드 Azure Stack에 연결 된 배포에 대 한 결정을 계획 하는 배포를 확인 합니다.
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
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 5447bcb0dc37cb3c923c4e6bbff4d69d987b6df6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244371"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure Stack에 대 한 결정을 계획 하는 azure 연결이 끊긴된 배포 통합 시스템
결정 한 다음 [하이브리드 클라우드 환경에 Azure Stack에서는 통합 하는 방법을](azure-stack-connection-models.md), Azure Stack 배포 관련 결정 사항을 완료 후 수 있습니다.

배포 하 고 인터넷에 연결 하지 않고 Azure Stack를 사용할 수 있습니다. 그러나 연결이 끊긴된 배포는 AD FS id 저장소 및 용량 기반 청구 모델에 제한 됩니다. Azure AD 사용 해야 하는 다중 테 넌 트, 때문에 연결이 끊어진된 배포에 대 한 다중 테 넌 트 지원 되지 않습니다. 

이 옵션을 선택 하면:
- 보안 또는 인터넷에 연결 되지 않은 환경에서 Azure Stack을 배포 해야 할 다른 제한이 있어야 합니다.
- 차단 하려면 데이터 사용 현황 데이터 등 Azure로 전송 되지 않도록 합니다.
- 회사 인트라넷에 배포 되 고 하이브리드 시나리오에 관심이 있는 사설 클라우드 솔루션을 전적으로 Azure Stack을 사용 하려고 합니다.

> [!TIP]
> 경우에 따라 이러한 유형의 환경도 라고 "해저 시나리오"입니다.

연결이 끊긴된 배포를 엄격 하 게 아닙니다는 연결할 수 없습니다 나중에 Azure Stack 인스턴스 Azure 하이브리드 테 넌 트 VM 시나리오에 대 한 합니다. 배포 하는 동안 Azure에 연결할 필요가 있고 프로그램 id 저장소로 Azure Active Directory를 사용 하지 않으려는 있음을 의미 합니다.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>장애가 있는 사용자 또는에서 사용할 수 없는 연결 되지 않은 배포를 수 있는 기능 
Azure Stack 기능 중 일부를 손상 또는 연결이 끊어진된 모드에서 완전히 사용할 수 있는지 확인 해야 하므로 Azure에 연결 하는 경우 가장 잘 작동 하도록 설계 되었습니다. 

|기능|연결이 끊긴된 모드의 영향|
|-----|-----|
|VM 배포 후 구성 하기 위해 DSC 확장을 사용 하 여 VM 배포|장애가 있는-DSC 확장은 인터넷에 최신 WMF에 대 한 합니다.|
|Docker 명령을 실행할 Docker 확장을 사용 하 여 VM 배포|장애가 – Docker는 인터넷에서 최신 버전을 확인 및이 확인이 실패 합니다.|
|Azure Stack 포털에 대 한 설명서 링크|사용할 수 없음 – 링크 같은 피드백 제공, 도움말, 빠른 시작에서는 등 사용 하는 인터넷 URL이 작동 하지 않습니다.|
|온라인 업데이트 관리 가이드를 참조 하는 경고 수정/완화|사용할 수 없음 – 모든 경고 문제 해결 링크 사용 하는 인터넷 URL이 작동 하지 않습니다.|
|Marketplace 선택 하 고 Azure Marketplace에서 직접 갤러리 패키지를 추가 하는 기능|장애가 있는 – Azure Stack 포털을 사용 하 여 마켓플레이스 항목을 다운로드할 수 없습니다 (모든 인터넷 연결) 없이 연결이 끊어진된 모드에서 Azure Stack을 배포 하는 경우. 사용할 수 있습니다 합니다 [마켓플레이스 배포 도구](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) 를 인터넷에 연결 되어 있는 컴퓨터에 마켓플레이스 항목을 다운로드 하 여 Azure Stack 환경에 전송 합니다.|
|Azure Active Directory 페더레이션 계정을 사용 하 여 Azure Stack 배포를 관리 합니다.|사용할 수 없음 –이 기능은 Azure에 연결 되어 있어야 합니다. 로컬 Active Directory 인스턴스를 사용 하 여 AD FS는 대신 사용 되어야 합니다.|
|App Services|장애가 있는-WebApps 업데이트 된 콘텐츠에 대 한 인터넷 액세스가 필요할 수 있습니다.|
|CLI(Command Line Interface)|장애가 있는-CLI의 인증 및 서비스 주체를 프로 비전 기능이 감소 되었습니다.|
|Visual Studio – Cloud discovery|장애가 있는 – Cloud Discovery에서 다른 클라우드를 검색 하거나 또는 전혀 작동 하지 않습니다.|
|Visual Studio – AD FS|장애가 있는 – Visual Studio Enterprise에는 AD FS 지원 전용
원격 분석|사용할 수 없음 – 원격 분석 데이터에 의존 하는 타사 갤러리 패키지 뿐 아니라 Azure Stack에 대 한 원격 분석 데이터.|
|인증서|사용할 수 없음 – 인터넷 연결이 HTTPS의 컨텍스트에서 인증서 해지 목록 (CRL) 및 온라인 인증서 상태 프로토콜 (OSCP) 서비스에 대 한 필요합니다.|
|Key-Vault|Key Vault에 대 한 일반적인 사용 사례는 응용 프로그램 런타임 시 암호를 읽기 방법은 손상 –입니다. 이 응용 프로그램 디렉터리에 서비스 주체가 필요합니다. Azure Active Directory에서 서비스 주체를 추가할 수는 기본적으로 일반 사용자 (비관리자) 됩니다. (ADFS를 사용 하 여) ad에서 되지 않습니다. 이 모든 응용 프로그램을 추가 하는 디렉터리 관리자를 항상 통과 해야 하나 때문에 종단 간 환경에 장애물이 배치 합니다.| 

## <a name="learn-more"></a>자세한 정보
- 사용 사례, 구매, 파트너 및 OEM 하드웨어 공급 업체에 대 한 내용은 참조는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 제품 페이지입니다.
- 통합된 시스템, Azure Stack에 대 한 로드맵 및 지역 가용성에 대 한 자세한 백서를 참조 합니다. [Azure Stack: Azure의 확장](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)합니다. 
- Microsoft Azure Stack 패키징 및 가격 책정에 대해 자세히 알아보려면 [는.pdf 다운로드](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)합니다. 

## <a name="next-steps"></a>다음 단계
[데이터 센터 네트워크 통합](azure-stack-network.md)