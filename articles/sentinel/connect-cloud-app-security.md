---
title: Azure 센티널에 Cloud App Security 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Cloud App Security 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240116"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security에서 데이터 연결 



한 번의 클릭으로 [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 에서 Azure 센티널로 로그를 스트리밍할 수 있습니다. 이 연결을 통해 Cloud App Security에서 Azure 센티널로 경고를 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자

## <a name="connect-to-cloud-app-security"></a>Cloud App Security에 연결

Cloud App Security 이미 있는 경우 [네트워크에서 사용 하도록 설정](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)되었는지 확인 합니다.
Cloud App Security 배포 하 고 데이터를 수집 경고 데이터를 Azure 센티널로 쉽게 스트리밍할 수 있습니다.


1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Cloud App Security** 타일을 클릭 합니다.

1. Azure 센티널로 스트리밍할 로그를 선택 하 고 **경고**를 선택할 수 있습니다. 

1. Microsoft Cloud App Security 경고가 자동으로 Azure 센티널에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기** 에서 **사용** 을 선택 하 여 연결 된 보안 서비스에 생성 된 경고에서 인시던트를 자동으로 생성 하는 기본 분석 규칙을 사용 하도록 설정 합니다. 그런 다음 **분석** 및 **활성 규칙**에서이 규칙을 편집할 수 있습니다.

1. **연결**을 클릭합니다.

1. Cloud App Security 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Cloud App Security를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats-built-in.md)시작 합니다.
