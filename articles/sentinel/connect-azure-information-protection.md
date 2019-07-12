---
title: Azure Information Protection 데이터 Azure Sentinel 미리 보기에 연결 | Microsoft Docs
description: Azure Sentinel에서 Azure Information Protection 데이터에 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 3edfdca16141726ea591c8473528fc23ee076c53
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620598"
---
# <a name="connect-data-from-azure-information-protection"></a>Azure Information Protection의 데이터에 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

로그를 스트리밍하려면 [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) 번의 클릭으로 Azure Sentinel에 있습니다. Azure Information Protection 클라우드 또는 온-프레미스 인프라 및 컨트롤과 도움말 보안 메일, 문서 및 중요 한 데이터를 회사 외부에서 공유에 저장 하는지 여부를 데이터를 보호할 수 있습니다. 포함 된 레이블 및 권한을 쉬운 분류부터 Azure Information Protection을 사용 하 여 항상 데이터 보호를 강화 합니다. 에 연결할 때 Azure Information Protection Azure Sentinel, 스트림 모든 경고를 Azure Information Protection의 Azure Sentinel에.


## <a name="prerequisites"></a>전제 조건

- 전역 관리자, 보안 관리자 또는 정보 보호 권한 있는 사용자


## <a name="connect-to-azure-information-protection"></a>Azure Information Protection에 연결

Azure Information Protection에 이미 있는 경우 인지 확인 [네트워크에서 사용 하도록 설정](https://docs.microsoft.com/azure/information-protection/activate-service)합니다.
Azure Information Protection을 배포 하 고 데이터 가져오기, 경고 데이터를 쉽게 수행할 수 있으면 Azure Sentinel로 스트림할 수 있습니다.


1. Azure Sentinel 선택 **데이터 커넥터** 클릭 하 고는 **Azure Information Protection** 바둑판식으로 배열 합니다.

2. 로 이동 합니다 [Azure Information Protection 포털](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. 아래 **연결**를 클릭 하 여 Azure Information Protection의 Azure Sentinel 로그의 스트리밍을 설정 [analytics 구성](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Azure Sentinel 배포한는 작업 영역을 선택 합니다. 

5. **확인**을 클릭합니다.

6. Log Analytics에서 관련 스키마를 사용 하 여 Azure Information Protection 경고에 대 한를 검색 **InformationProtectionLogs_CL**합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에 Azure Information Protection을 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.
