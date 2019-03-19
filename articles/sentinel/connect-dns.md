---
title: Azure Sentinel 미리 보기에서 DNS 데이터 수집 | Microsoft Docs
description: Azure Sentinel에서 DNS 데이터를 수집 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: a7f075b74876ec807d790f3ffbea5dad14163535
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530419"
---
# <a name="connect-your-domain-name-server"></a>도메인 이름 서버 연결

> [!IMPORTANT]
> Azure Sentinel 현재 공개 미리 보기 중입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

모든 서버 DNS (도메인 이름) Windows에서 실행 중 Azure Sentinel로 연결할 수 있습니다. DNS 컴퓨터의 에이전트를 설치 하면 됩니다. DNS를 사용 하 여 로그, 얻을 수 있습니다 보안, 성능 및 조직의 DNS 인프라에 대 한 작업 관련 정보를 수집 하 여 분석 및 DNS 서버에서 분석을 상호 연결 하 고 감사 로그 및 기타 관련 데이터

DNS 로그 수집을 사용 하도록 설정 하면 다음 작업을 수행할 수 있습니다.
- 악의적인 도메인 이름을 확인하려는 클라이언트 식별
- 부실 리소스 레코드 식별
- 자주 쿼리되는 도메인 이름 및 대화량이 많은 DNS 클라이언트 식별
- DNS 서버에서 요청 부하 보기
- 동적 DNS 등록 오류 보기

## <a name="how-it-works"></a>작동 방법

DNS 컬렉션 DNS 컴퓨터의 에이전트를 설치 하 여 수행 됩니다. 에이전트는 DNS에서 이벤트를 가져오는 및 Log Analytics에 전달 합니다.

## <a name="connect-your-dns-appliance"></a>DNS 어플라이언스를 연결 합니다.

1. Sentinel Azure portal에서 선택 **데이터 컬렉션** 선택 합니다 **DNS** 타일입니다.
1. DNS 컴퓨터를 Azure의 경우:
    1. 클릭 **Windows 가상 머신에 대 한 에이전트 다운로드 및 설치**합니다.
    1. 에 **가상 머신** 목록 Azure Sentinel를 스트리밍 하려는 DNS 컴퓨터를 선택 합니다. Windows VM이 있는지 확인 합니다.
    1. 해당 VM에 대해 열리는 창에서 클릭 **Connect**합니다.  
    1. 클릭 **을 사용 하도록 설정** 에 **DNS 커넥터** 창입니다. 

2. DNS 컴퓨터는 Azure VM 없는 경우:
    1. 클릭 **Windows 비 Azure 컴퓨터에 대 한 에이전트 다운로드 및 설치**합니다.
    1. 에 **직접 에이전트** 창 중 하나를 선택 **다운로드 Windows 에이전트 (64 비트)** 하거나 **다운로드 Windows 에이전트 (32 비트)** 합니다.
    1. DNS 컴퓨터에 에이전트를 설치 합니다. 복사 합니다 **작업 영역 ID**를 **기본 키**, 및 **보조 키** 및 설치 하는 동안 메시지가 표시 되 면 사용 합니다.

3. Log Analytics에서 관련 스키마를 사용 하 여 DNS 로그를 검색할 **DnsEvents**합니다.

## <a name="validate"></a>유효성 검사 

Log Analytics에서 스키마에 대 한 검색 **DnsEvents** 이벤트가 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel DNS 온-프레미스 어플라이언스 연결 하는 방법을 알아보았습니다. Azure에서 감시 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 합니다.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.
