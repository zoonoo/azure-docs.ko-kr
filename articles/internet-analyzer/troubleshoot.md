---
title: Azure 인터넷 분석기 문제 해결 | 마이크로 소프트 문서
description: Azure 인터넷 분석기의 문제 해결 참조입니다.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069220"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure 인터넷 분석기 문제 해결

이 문서에는 일반적인 인터넷 분석기 문제에 대한 문제 해결 단계가 포함되어 있습니다.

## <a name="things-to-keep-in-mind"></a>주의할 사항
- 클라이언트 스크립트는 **HTTPS** 웹 사이트에 포함되어야 합니다. 스크립트가 일반**텍스트(http://)** 또는**로컬(file://)** 웹 사이트에서 실행되는 경우 측정값이 수집되지 않습니다.
- 측정 데이터는 인터넷 분석기 프로필의 클라이언트 스크립트가 실제 사용자 트래픽을 수신하는 응용 프로그램에 포함된 경우에만 수집됩니다. 가상 트래픽(예: Azure WebApp 성능 테스트)은 일반적으로 포함된 Javascript 코드를 실행하지 않으므로 해당 유형의 트래픽에 의해 측정이 생성되지 않습니다.

## <a name="azure-portal"></a>Azure portal
**성과 기록표 섹션에서 "선택한 필터 조합에 대해 성과 기록표가 생성되지 않았습니다."**
- 스코어카드는 매일 생성됩니다(매일 종료시, UTC 시간).
- 성과 기록표는 선택한 필터 조합(테스트, 기간, 국가 등)에 대해 100개 이상의 측정값을 수집한 경우에만 생성됩니다.

**테스트에서 하나 또는 둘 다 끝점에 대해 "총 측정 횟수"가 0입니다.**
- 시계열 및 측정 횟수는 한 시간에 한 번 계산되므로 새 측정 데이터가 표시될 때까지 최소한 그 시간을 기다려야 합니다.
- 인터넷 분석기는 분석을 위해 성공적인 측정(예: HTTP 200 응답)만 계산합니다. 테스트의 하나 또는 두 끝점에 연결할 수 없거나 200이 아닌 HTTP 코드를 반환하는 경우 총 측정값이 0으로 표시됩니다.

## <a name="next-steps"></a>다음 단계
인터넷 [분석기 FAQ](internet-analyzer-faq.md) 읽기
