---
title: Azure Stack Edge Pro 장치에서 자동 관리 로그 수집 이해
description: Azure Stack Edge Pro 장치에서 자동 관리 로그 수집을 수행 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466959"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Azure Stack Edge 장치의 자동 관리 로그 수집

장치 문제를 효율적으로 해결 하는 데 도움이 되도록 시스템 상태 표시기에 따라 Azure Stack Edge 장치에서 자동 관리 로그 수집을 사용 하도록 설정할 수 있습니다. 이 문서에서는 자동 관리 로그 수집 이란 무엇 이며,이를 사용 하도록 설정 하는 방법 및 자동 관리 로그 수집을 사용 하는 경우 데이터 처리 방법을 설명 합니다.
   
이 문서의 정보는 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 장치에 적용 됩니다.

## <a name="about-proactive-log-collection"></a>자동 관리 로그 수집 정보

Microsoft 고객 지원 및 엔지니어링 팀은 Azure Stack Edge 장치의 시스템 로그를 사용 하 여 작업 중에 발생할 수 있는 문제를 효율적으로 식별 하 고 해결 합니다. 자동 관리 로그 수집은 Microsoft에서 문제/이벤트 (추적 중인 이벤트에 대 한 자동 관리 로그 수집 지표 섹션 참조)가 고객의 Azure Stack Edge 어플라이언스에 의해 검색 되었음을 알리는 방법입니다. 문제와 관련 된 지원 로그는 Microsoft에서 관리 하 고 제어 하는 Azure Storage 계정에 자동으로 업로드 됩니다. Microsoft 지원 및 Microsoft 엔지니어가 이러한 지원 로그를 검토 하 여 고객과 관련 된 문제를 해결 하는 최선의 조치를 결정 합니다.    

> [!NOTE]
> 이러한 로그는 디버깅 목적 으로만 사용 되며 문제 발생 시 고객에 대 한 지원을 제공 합니다. 


## <a name="enabling-proactive-log-collection"></a>자동 관리 로그 수집 사용

로컬 UI를 통해 장치를 활성화 하려고 할 때 자동 관리 로그 수집을 사용 하도록 설정할 수 있습니다. 

1. 디바이스의 로컬 웹 UI에서 **시작** 페이지로 이동합니다.
2. **활성화** 타일에서 **활성화** 를 선택합니다. 

    ![로컬 웹 UI "클라우드 세부 정보" 페이지 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. **활성화** 창에서 다음을 수행 합니다.
    1. [Edge Pro r Azure Stack 활성화 키를 가져올](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key) **정품 인증 키** 를 입력 합니다.

    1. 자동 관리 로그 수집을 사용 하도록 설정 하 여 Microsoft에서 장치의 상태를 기준으로 로그를 수집 하도록 할 수 있습니다. 이러한 방식으로 수집 된 로그는 Azure Storage 계정에 업로드 됩니다.
    
    1. **적용** 을 선택합니다.

    ![로컬 웹 UI "클라우드 세부 정보" 페이지 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>자동 관리 로그 수집 표시기

자동 관리 로그 수집을 사용 하도록 설정 하면 장치에서 다음 이벤트 중 하나가 검색 될 때 로그가 자동으로 업로드 됩니다.  


|경고/오류/조건  |설명  |
|---------|---------|
|AcsUnhealthyCondition     |Azure 일관적인 서비스가 비정상 상태입니다.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge 에이전트가 실행 되 고 있지 않습니다.         |
|UpdateInstallFailedEvent | 업데이트를 설치할 수 없습니다.        |

 
Microsoft는 계속 해 서 이전 목록에 새 이벤트를 추가 합니다. 새 이벤트에 대 한 추가 동의가 필요 하지 않습니다. 최신 사전 로그 수집 지표에 대해 알아보려면이 페이지를 참조 하세요.    
 

## <a name="other-log-collection-methods"></a>기타 로그 수집 방법

검색 된 특정 문제에 대 한 특정 로그를 수집 하는 자동 관리 로그 수집 외에도 시스템 상태 및 동작에 대 한 보다 심도 있는 이해를 제공할 수 있는 다른 로그 컬렉션이 있습니다. 일반적으로 이러한 다른 로그 컬렉션은 지원 요청 중에 실행 하거나 장치가 제공 하는 원격 분석 데이터를 기반으로 Microsoft에서 트리거할 수 있습니다.  

## <a name="handling-data"></a>데이터 처리

고객이 자동 관리 로그 수집을 사용 하도록 설정 하는 경우 여기에 설명 된 대로 Azure Stack Edge 장치에서 로그를 수집 하는 Microsoft에 동의 하는 것입니다. 또한 고객은 Microsoft에서 관리 하 고 제어 하는 Azure Storage 계정에서 해당 로그의 업로드 및 보존을 승인 하 고 동의 합니다.

Microsoft는 시스템 상태 및 문제 해결을 위해 데이터를 사용 합니다. 데이터는 고객 동의가 없는 마케팅, 광고 또는 기타 상업적 용도로 사용 되지 않습니다. 

Microsoft에서 수집 하는 데이터는 표준 개인 정보 취급 방침에 따라 처리 됩니다. 고객이 자동 관리 로그 수집에 대 한 동의를 취소 하기로 결정 하는 경우, 해지 이전에 동의 하도록 수집 된 데이터는 영향을 받지 않습니다.

## <a name="next-steps"></a>다음 단계

[지원 패키지를 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)하는 방법에 대해 알아봅니다.