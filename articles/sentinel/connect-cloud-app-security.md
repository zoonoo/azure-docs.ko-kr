---
title: Azure 센티널 Preview에 Cloud App Security 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Cloud App Security 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 92de4120d73af70c5de013488344a15ba46513c7
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881077"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security에서 데이터 연결 

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

한 번의 클릭으로 [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 에서 Azure 센티널로 로그를 스트리밍할 수 있습니다. 이 연결을 통해 Cloud App Security에서 Azure 센티널로 경고를 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 전역 관리자 또는 보안 관리자 권한이 있는 사용자

## <a name="connect-to-cloud-app-security"></a>Cloud App Security에 연결

Cloud App Security 이미 있는 경우 [네트워크에서 사용 하도록 설정](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)되었는지 확인 합니다.
Cloud App Security 배포 하 고 데이터를 수집 경고 데이터를 Azure 센티널로 쉽게 스트리밍할 수 있습니다.


1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Cloud App Security** 타일을 클릭 합니다.

2. **연결**을 클릭합니다.

3. Cloud App Security 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Cloud App Security를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats.md)시작 합니다.
