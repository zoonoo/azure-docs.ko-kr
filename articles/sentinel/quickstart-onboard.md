---
title: Azure 센티널 Preview에서 온보드 | Microsoft Docs
description: Azure 센티널에서 데이터를 수집 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: rkarlin
ms.openlocfilehash: b609dc70c45941ec1132c7cdf614cf9bec8119ff
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019014"
---
# <a name="on-board-azure-sentinel-preview"></a>온-보드 Azure 센티널 미리 보기

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 빠른 시작에서는 Azure 센티널을 온보드 하는 방법에 대해 알아봅니다. 

온-보드 Azure 센티널을 사용 하려면 먼저 Azure 센티널을 사용 하도록 설정한 다음 데이터 원본을 연결 해야 합니다. Azure 센티널은 Microsoft 솔루션에 대 한 다양 한 커넥터와 함께 제공 되며, Microsoft 위협 방지 솔루션, Office 365, Azure AD, Azure ATP 등을 비롯 한 Microsoft 365 소스를 포함 하 여 실시간 통합을 제공 합니다. Microsoft Cloud App Security 등 또한 타사 솔루션에 대한 광범위한 보안 에코시스템에 기본 제공 커넥터도 제공됩니다. 일반 이벤트 형식 Syslog 또는 REST API를 사용 하 여 데이터 소스를 Azure 센티널에 연결할 수도 있습니다.  

데이터 원본에 연결한 후 데이터를 기반으로 하는 정보를 노출 하는 expertly 만든 대시보드의 갤러리에서 선택 합니다. 이러한 대시보드는 요구 사항에 맞게 쉽게 사용자 지정할 수 있습니다.


## <a name="global-prerequisites"></a>전역 전제 조건

- 활성 Azure 구독이 없는 경우 시작 하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

- Log Analytics 작업 영역입니다. [Log Analytics 작업 영역을 만드는](../log-analytics/log-analytics-quick-create-workspace.md) 방법 알아보기

-  Azure 센티널을 사용 하도록 설정 하려면 Azure 센티널 작업 영역이 상주 하는 구독에 대 한 참가자 권한이 필요 합니다. 
- Azure 센티널을 사용 하려면 작업 영역이 속한 리소스 그룹에 대 한 참가자 또는 판독기 권한이 있어야 합니다.
- 특정 데이터 원본을 연결 하려면 추가 권한이 필요할 수 있습니다.
 
## Azure 센티널 사용<a name="enable"></a>

1. Azure Portal로 이동 합니다.
2. Azure 센티널이 생성 된 구독이 선택 되었는지 확인 합니다. 
3. Azure 센티널을 검색 합니다. 
   ![search](./media/quickstart-onboard/search-product.png)

1. **+추가**를 클릭합니다.
1. 사용 하려는 작업 영역을 선택 하거나 새 작업 영역을 만듭니다. Azure 센티널은 둘 이상의 작업 영역에서 실행할 수 있지만 데이터는 단일 작업 영역으로 격리 됩니다.

   ![검색](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure Security Center에서 만든 기본 작업 영역은 목록에 표시 되지 않습니다. Azure에는 Azure 센티널을 설치할 수 없습니다.
   > - Azure 센티널은 다음 지역 중 하나에 배포 된 작업 영역에서 실행할 수 있습니다. 오스트레일리아 동부, 오스트레일리아 남동쪽, 캐나다 중부, 인도 중부, 미국 동부, 미국 동부 2 EUAP (카나리아), 일본 동부, 동남 아시아, 영국 남부, 유럽 서부, 미국 서 부 2, 미국 서 부 중부, 프랑스 중부, 대한민국 중부, 북부 북부, 미국 동부 2, 동아시아, 미국 서 부, 미국 중부, 미국 미국 중 남부.


6. **Azure 센티널 추가**를 클릭 합니다.
  

## <a name="connect-data-sources"></a>데이터 원본 연결

Azure 센티널은 서비스에 연결 하 고 이벤트 및 로그를 Azure 센티널에 전달 하 여 서비스 및 앱에 대 한 연결을 만듭니다. 컴퓨터 및 가상 컴퓨터의 경우 로그를 수집 하 고 Azure 센티널에 전달 하는 Azure 센티널 에이전트를 설치할 수 있습니다. 방화벽과 프록시의 경우 Azure 센티널은 Linux Syslog 서버를 활용 합니다. 에이전트가 설치 되 고 에이전트가 로그 파일을 수집 하 여 Azure 센티널에 전달 합니다. 
 
1. **데이터 컬렉션**을 클릭 합니다.
2. 연결할 수 있는 각 데이터 원본에 대 한 타일이 있습니다.<br>
예를 들어 **Azure Active Directory**를 클릭 합니다. 이 데이터 소스를 연결 하는 경우 Azure AD의 모든 로그를 Azure 센티널로 스트리밍합니다. Wan에서 로그인 로그 및/또는 감사 로그를 가져올 로그 유형을 선택할 수 있습니다. <br>
아래에서 Azure 센티널은 각 커넥터에 대해 설치 해야 하는 대시보드에 대 한 권장 사항을 제공 하므로 데이터를 통해 흥미로운 정보를 즉시 얻을 수 있습니다. <br> 자세한 내용은 설치 지침을 따르거나 [관련 연결 가이드를 참조](connect-data-sources.md) 하세요. 데이터 커넥터에 대 한 자세한 내용은 [Microsoft 서비스 연결](connect-data-sources.md)을 참조 하세요.

데이터 원본이 연결 된 후 데이터는 Azure 센티널로의 스트리밍을 시작 하 고 작업을 시작할 준비가 되었습니다. [기본 제공 대시보드의](quickstart-get-visibility.md) 로그를 확인 하 고 Log Analytics에서 쿼리 작성을 시작 하 여 데이터를 [조사할](tutorial-investigate-cases.md)수 있습니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널에 데이터 원본을 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats.md)시작 합니다.
- [일반 오류 형식 어플라이언스](connect-common-event-format.md) 의 데이터를 Azure 센티널로 스트림 합니다.
