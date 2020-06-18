---
title: Azure 인터넷 분석기 문제 해결 | Microsoft Docs
description: Azure 인터넷 분석기에 대한 문제 해결 참조서입니다.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 0c9e203d7e8be7b24c711f73e2152a7745a57dac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745479"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet Analyzer 문제 해결

이 문서에는 일반적인 인터넷 분석기 이슈에 대한 문제 해결 단계가 포함되어 있습니다.

## <a name="things-to-keep-in-mind"></a>주의할 사항
- 클라이언트 스크립트는 **HTTPS** 웹 사이트에 포함되어야 합니다. 스크립트가 일반 텍스트(**http://** ) 또는 로컬(**file://** ) 웹 사이트에서 실행되는 경우에는 측정이 수집되지 않습니다.
- 인터넷 분석기 프로필의 클라이언트 스크립트가 실제 사용자 트래픽을 수신하는 애플리케이션에 포함된 경우에만 측정 데이터가 수집됩니다. 가상 트래픽(예: Azure WebApp 성능 테스트)은 일반적으로 포함된 Javascript 코드를 실행하지 않으므로 해당 트래픽 유형에 따라 측정값이 생성되지 않습니다.

## <a name="azure-portal"></a>Azure portal
**성과 기록표 섹션에서 "선택한 필터 조합에 대해 성과 기록표가 생성되지 않았습니다."**
- 성과 기록표는 매일(UTC 시간으로 하루가 끝날 때) 생성됩니다.
- 성과 기록표는 선택한 필터 조합(테스트, 기간, 국가/지역 등)에 대해 100개가 넘는 측정값이 수집된 경우에만 생성됩니다.

**테스트의 엔드포인트 하나 또는 둘 다에 대해 "총 측정 수"가 0입니다.**
- 시계열 및 측정 수는 1시간에 한 번 계산되므로, 새 측정 데이터가 표시되려면 적어도 해당 시간 동안 기다려야 합니다.
- 인터넷 분석기는 분석을 위해 성공적인 측정(즉, HTTP 200 응답)만 계산합니다. 테스트의 엔드포인트 하나 또는 둘 다에 연결할 수 없거나 200이 아닌 HTTP 코드를 반환하는 경우 총 측정 수가 0으로 표시됩니다.

## <a name="next-steps"></a>다음 단계
[Internet Analyzer FAQ](internet-analyzer-faq.md) 읽기
