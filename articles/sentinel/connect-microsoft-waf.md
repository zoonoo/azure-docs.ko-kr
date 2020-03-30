---
title: Azure Sentinel에 웹 응용 프로그램 방화벽 데이터를 연결
description: Microsoft 웹 응용 프로그램 방화벽 데이터를 Azure Sentinel에 연결하는 방법에 대해 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588181"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Microsoft 웹 응용 프로그램 방화벽의 데이터 연결



Azure 응용 프로그램 게이트웨이의 Microsoft 웹 응용 프로그램 방화벽(WAF)에서 로그를 스트리밍할 수 있습니다. 이 WAF는 SQL 주입 및 사이트 간 스크립팅과 같은 일반적인 웹 취약점으로부터 응용 프로그램을 보호하고 규칙을 사용자 지정하여 거짓 긍정을 줄일 수 있도록 합니다. 다음 지침에 따라 Microsoft 웹 응용 프로그램 방화벽 로그를 Azure Sentinel로 스트리밍합니다.


## <a name="prerequisites"></a>사전 요구 사항

- 기존 응용 프로그램 게이트웨이 리소스

## <a name="connect-to-microsoft-web-application-firewall"></a>Microsoft 웹 응용 프로그램 방화벽에 연결

이미 Microsoft 웹 응용 프로그램 방화벽이 있는 경우 기존 게이트웨이 리소스가 있는지 확인합니다.
Microsoft 웹 응용 프로그램 방화벽이 배포되고 데이터를 받으면 경고 데이터를 Azure Sentinel으로 쉽게 스트리밍할 수 있습니다.
    
1. Azure Sentinel 포털에서 **데이터 커넥터를 선택합니다.**
1. 데이터 커넥터 페이지에서 **WAF** 타일을 선택합니다.
1. 응용 [프로그램 게이트웨이 리소스로](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) 이동하여 WAF를 선택합니다.
    1. **진단 설정**을 선택합니다.
    1. + 테이블 아래에 **진단 설정 추가를** 선택합니다.
    1. 진단 **설정** 페이지에서 **이름을** 입력하고 **로그 분석으로 보내기를**선택합니다.
    1. **로그 분석 작업 영역에서** Azure Sentinel 작업 영역을 선택합니다.
    1. 분석할 로그 유형을 선택합니다. 우리는 권장 : 응용 프로그램 게이트웨이 액세스 로그 및 응용 프로그램 게이트웨이 방화벽 로그.
1. Microsoft 웹 응용 프로그램 방화벽 경고에 대한 로그 분석에서 관련 스키마를 사용하려면 **AzureDiagnostics 를 검색합니다.**

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft 웹 응용 프로그램 방화벽을 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
