---
title: Azure 센티널에 보안 데이터 연결 펄스 Microsoft Docs
description: 펄스 연결 보안 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531464"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Azure 센티널에 펄스 연결 보안 연결

이 문서에서는 Azure 센티널에 [펄스 연결 보안](https://www.pulsesecure.net/products/pulse-connect-secure/) 어플라이언스를 연결 하는 방법을 설명 합니다. 펄스 연결 보안 데이터 커넥터를 사용 하면 Azure 센티널로 펄스 연결 보안 로그를 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. 펄스 연결과 Azure 센티널 간의 통합은 Syslog를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>앞으로 펄스 연결 보안 로그를 Syslog 에이전트로 연결  

Syslog 에이전트를 통해 Azure 작업 영역에 Syslog 메시지를 전달 하도록 펄스 연결 보안을 구성 합니다.

1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **펄스 연결 보안** 커넥터를 선택 합니다.

1. **커넥터 페이지 열기**를 선택 합니다.

1. **펄스 연결 보안** 페이지의 지침을 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 된 후에는 해당 데이터가 Syslog 아래 Log Analytics에 표시 됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에 펄스 연결 보안을 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.