---
title: AI Vectra 데이터 검색 Azure 센티널에 연결 | Microsoft Docs
description: AI Vectra 검색 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038225"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Azure 센티널에 AI Vectra 검색 연결

> [!IMPORTANT]
> Azure 센티널의 AI Vectra 검색 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 [AI Vectra 검색](https://www.vectra.ai/product/cognito-detect) 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. AI Vectra 검색 데이터 커넥터를 사용 하 여 AI Vectra 검색 데이터를 Azure 센티널로 쉽게 가져올 수 있습니다. 그러면 통합 문서에서이 데이터를 보고,이를 사용 하 여 사용자 지정 경고를 만든 다음,이를 통합 하 여 조사를 개선할 수 있습니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>CEF 메시지를 보내도록 AI Vectra 검색 어플라이언스 구성  

AI Vectra 검색 구성 [1 단계: 로그 전달자 배포](connect-cef-agent.md)에서 설정한 Syslog 전달자를 통해 cef 형식 syslog 메시지를 Log Analytics 작업 영역으로 전달 합니다.

1. Vectra 인터페이스에서 설정 > 알림으로 이동 하 고 Syslog 구성 편집을 선택 합니다. 다음 지침에 따라 연결을 설정 합니다.

    - 새 대상 추가 ( [로그 전달자](connect-cef-agent.md)의 호스트 이름)
    - 포트를 **514** 로 설정 합니다.
    - 프로토콜을 **UDP** 로 설정
    - 형식을 **Cef** 로 설정 합니다.
    - 로그 유형 설정 (사용할 수 있는 모든 로그 유형 선택)
    - **저장** 을 클릭 합니다.

2. **테스트** 단추를 클릭 하 여 일부 테스트 이벤트를 로그 전달자에 게 강제로 보낼 수 있습니다.

3. AI Vectra 검색 이벤트에 대해 Log Analytics에서 관련 스키마를 사용 하려면 **CommonSecurityLog**를 검색 합니다.

4. [단계 3: 연결 유효성 검사](connect-cef-verify.md)로 계속 진행합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AI Vectra 검색 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. 이 데이터 커넥터에 기본 제공 되는 기능을 최대한 활용 하려면 데이터 커넥터 페이지에서 **다음 단계** 탭을 클릭 합니다. 유용한 정보를 찾을 수 있도록 몇 가지 미리 만들어진 샘플 쿼리를 찾을 수 있습니다.

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.
