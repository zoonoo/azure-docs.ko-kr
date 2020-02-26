---
title: Azure 센티널에 Cloud App Security 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Cloud App Security 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588368"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security에서 데이터 연결 



한 번의 클릭으로 [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 에서 Azure 센티널로 로그를 스트리밍할 수 있습니다. 이 연결을 통해 Cloud App Security에서 Azure 센티널로 경고를 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자
- Cloud Discovery 로그를 Azure 센티널로 스트리밍하려면 [Microsoft Cloud App Security에서 SIEM으로 Azure 센티널을 사용 하도록 설정](https://aka.ms/AzureSentinelMCAS)합니다.

> [!IMPORTANT]
> Cloud Discovery 로그 수집은 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
## <a name="connect-to-cloud-app-security"></a>Cloud App Security에 연결

Cloud App Security 이미 있는 경우 [네트워크에서 사용 하도록 설정](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)되었는지 확인 합니다.
Cloud App Security 배포 하 고 데이터를 수집 경고 데이터를 Azure 센티널로 쉽게 스트리밍할 수 있습니다.


1. Azure 센티널에서 **데이터 커넥터**를 선택 하 고 **Cloud App Security** 타일을 클릭 한 다음 **커넥터 열기 페이지**를 선택 합니다.

1. Azure 센티널로 스트리밍할 로그를 선택 합니다. **경고** 를 선택 하 고 로그 (미리 보기)를 **Cloud Discovery** 수 있습니다. 

1. **연결**을 클릭합니다.

1. Cloud App Security 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Cloud App Security를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
