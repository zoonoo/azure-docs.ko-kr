---
title: 'Azure 상태 모니터 v2 API 참조: 계측 엔진을 사용 하도록 설정 | Microsoft Docs'
description: 상태 모니터 v2-InstrumentationEngine API 참조 사용 하도록 설정 합니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 7d337c9b6b22f3abfcb4aea1c47127706ed9e9d7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870509"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>상태 모니터 v2 API: 사용-InstrumentationEngine (v0.2.1 알파)

이 문서에서는의 구성원으로 제공 되는 cmdlet을 설명 합니다 [Az.ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)합니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 참조 하세요. [추가 사용 약관에 대 한 Microsoft Azure 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>설명

이 cmdlet을 일부 레지스트리 키를 설정 하 여 계측 엔진을 사용 합니다.
이러한 변경 내용을 적용 하려면 IIS를 다시 시작 합니다.

계측 엔진은.NET Sdk를 통해 수집 된 데이터를 보완할 수 있습니다.
이벤트 및 메시지는 수집 관리 되는 프로세스의 실행을 설명 하는. 종속성 결과 코드, HTTP 동사 및 SQL 명령 텍스트에 제한 되지 않고 포함 됩니다. 

계측 엔진을 사용 하도록 설정 하는 경우:
- 모니터링 사용 cmdlet을 사용 하 여 이미 활성화 해도 InstrumentationEngine를 사용 하지 않도록 합니다.
- 수동으로.NET Sdk를 사용 하 여 응용 프로그램 계측을 추가 원격 분석을 수집 하려면.

> [!IMPORTANT] 
> 이 cmdlet은 관리자 권한으로 PowerShell 세션에 필요합니다.

> [!NOTE] 
> 이 cmdlet를 검토 하 고이 라이선스 및 개인정보취급방침 적용 해야 합니다.

> [!NOTE] 
> 계측 엔진 추가 오버 헤드를 추가 및 기본적으로 해제 되어 있습니다.

## <a name="examples"></a>예

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>매개 변수 

### <a name="-acceptlicense"></a>-AcceptLicense
**선택** 헤드리스 설치에서 라이선스 및 개인정보취급방침 적용할이 스위치를 사용 합니다.

### <a name="-verbose"></a>-Verbose
**일반적인 매개 변수입니다.** 자세한 로그를 출력 하려면이 스위치를 사용 합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>예제 출력에서 성공적으로 계측 엔진을 사용 하도록 설정

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```
