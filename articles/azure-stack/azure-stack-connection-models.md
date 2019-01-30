---
title: Azure Stack 통합 시스템 연결 모델 | Microsoft Docs
description: 다중 노드 Azure Stack에 대 한 결정을 계획 하는 배포를 확인 합니다.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: d55cebf380c4ca5183e8ff15fd193e254b66c30b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246588"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure Stack 통합 시스템 연결 모델
이해 해야 하는 Azure Stack 통합 시스템에 관심이 [여러 데이터 센터 통합 고려 사항](azure-stack-datacenter-integration.md) 시스템 데이터 센터에 들어가는지는 방법을 확인 하려면 Azure Stack 배포에 대 한 합니다. 또한 정확 하 게 하는 방법을 하는 Azure Stack 통합 하이브리드 클라우드 환경에 결정 해야 합니다. 이 문서에서는 이러한 주요 의사 결정 및 Azure 연결을 id 저장소를 포함 하 여 청구 모델 의사 결정의 개요를 제공 합니다.

통합된 시스템 구매 하려는 경우 원래 장비 제조업체 (OEM) 하드웨어 공급 업체는 데 도움이 됩니다 계획 프로세스를 자세히 많은 과정을 안내 합니다. 실제 배포도 수행 합니다.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Azure Stack 배포 연결 모델을 선택 합니다.
인터넷 (및 Azure) 연결 또는 연결 끊김 Azure Stack 배포를 선택할 수 있습니다. Azure Stack 및 Azure 간에 하이브리드 시나리오를 포함 하 여 Azure Stack에서 가장 유용 하 게 하는 배포 하려는 Azure에 연결 합니다. 이 옵션 정의 id 저장소 (Azure Active Directory 또는 Active Directory Federation Services) 및 청구 모델에 대해 사용할 수 있는 옵션 (사용 하 여 기준으로 요금을 지불 대금 청구 또는 용량 기반 청구) 다음 다이어그램과 테이블에 요약 된 것 처럼: 

![Azure Stack 배포 및 청구 시나리오](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> 이 주요 의사 결정 지점! 배포 시에 적용 해야 하는 한 번만 결정은 Active Directory Federation Services (AD FS) 또는 Azure Active Directory (Azure AD)를 선택 합니다. 전체 시스템을 다시 배포 하지 않고이 작업을 나중에 변경할 수 없습니다.  


|옵션|Azure에 연결|Azure에서 연결 끊김|
|-----|-----|-----|
|Azure AD|![지원됨](media/azure-stack-connection-models/check.png)| |
|AD FS|![지원됨](media/azure-stack-connection-models/check.png)|![지원됨](media/azure-stack-connection-models/check.png)|
|사용량 기반 요금 청구|![지원됨](media/azure-stack-connection-models/check.png)| |
|용량 기반 청구|![지원됨](media/azure-stack-connection-models/check.png)|![지원됨](media/azure-stack-connection-models/check.png)|
|Azure Stack에 직접 업데이트 패키지 다운로드|![지원됨](media/azure-stack-connection-models/check.png)|  |

Azure Stack 배포에 사용할 Azure 연결 모델에서 결정 한, 다음 청구 방법과 id 저장소에 대 한 추가, 연결 종속 결정을 수행 합니다. 

## <a name="next-steps"></a>다음 단계

[Azure 연결 된 Azure Stack 배포 결정](azure-stack-connected-deployment.md)

[Azure 연결 되지 않은 Azure Stack 배포 결정](azure-stack-disconnected-deployment.md)
