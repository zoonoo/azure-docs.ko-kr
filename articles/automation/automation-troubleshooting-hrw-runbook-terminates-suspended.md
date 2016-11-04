---
title: 'Hybrid Runbook Worker: Runbook 작업이 일시 중단됨 상태로 종료됨 | Microsoft Docs'
description: Hybrid Runbook Worker 작업 종류 오류에 대한 증상, 원인 및 해결 방법.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/17/2016
ms.author: magoedte

---
# Hybrid Runbook Worker: Runbook 작업이 일시 중단됨 상태로 종료됨
## 요약
Runbook이 3회 실행 시도 직후 일시 중단됩니다. Runbook이 성공적으로 완료되는 것을 막는 조건이 있으며 관련된 오류 메시지에 이유를 알리는 정보가 포함되어 있지 않습니다. 이 문서는 Hybrid Runbook Worker Runbook 실행 실패와 관련된 문제를 해결하는 단계를 제공합니다.

Azure 문제를 이 문서에서 다루지 않은 경우 [MSDN 및 Stack Overflow](https://azure.microsoft.com/support/forums/)에서 Azure 포럼을 방문하세요. 이러한 포럼이나 [Twitter의 @AzureSupport](https://twitter.com/AzureSupport)에 문제를 게시할 수 있습니다. 또한 [Azure 지원](https://azure.microsoft.com/support/options/) 사이트에서 **지원 받기**를 선택하여 Azure 지원 요청을 제출할 수 있습니다.

## 증상
Runbook 실행에 실패하고 “프로세스가 예기치 않게 중지되어서 '활성화' 작업을 실행할 수 없습니다. 작업을 3번 시도했습니다.”라는 오류가 반환됩니다.

## 원인
오류의 잠재적 원인에는 몇 가지가 있습니다.

1. Hybrid Worker가 프록시 또는 방화벽 뒤에 있습니다.
2. Hybrid Worker가 실행되는 컴퓨터가 최소 하드웨어 [요구 사항](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements)에 미치지 못합니다.
3. 로컬 리소스를 사용하여 Runbook을 인증할 수 없습니다.

## 원인 1: Hybrid Runbook Worker가 프록시 또는 방화벽 뒤에 있습니다.
Hybrid Runbook Worker가 실행되는 컴퓨터가 프록시 또는 방화벽 뒤에 있고, 아웃바운드 네트워크 액세스가 허용되지 않았거나 제대로 구성되지 않았습니다.

### 해결 방법
컴퓨터가 포트 443, 9354, 및 30000-30199의 *.cloudapp.net에 대한 아웃바운드 액세스가 있는지 확인합니다.

## 원인 2: 컴퓨터가 최소 하드웨어 요구 사항을 충족하지 못합니다.
Hybrid Runbook Worker가 실행되는 컴퓨터는 이 기능을 호스트하도록 지정하기 전에, 최소 하드웨어 요구 사항을 충족해야 합니다. 그렇지 않으면, 실행 중 Runbook에 의해 유발되는 다른 백그라운드 프로세스 및 경합의 리소스 사용률에 따라, 컴퓨터가 과도하게 사용되거나 Runbook 작업이 지연되거나 시간이 초과되는 원인이 될 수 있습니다.

### 해결 방법
먼저, Hybrid Runbook Worker 기능을 실행하도록 지정된 컴퓨터가 최소 하드웨어 요구 사항을 충족하는지 확인합니다. 그렇다면, CPU 및 메모리 사용률을 모니터링하여 Hybrid Runbook Worker 프로세스와 Windows 사이에 어떠한 상관 관계가 있는지 확인합니다. 메모리 또는 CPU가 과도하게 사용된다면, 리소스 디스크 병목 현상에 대처하고 오류를 해결하기 위해 메모리를 증가시키거나 프로세서를 업그레이드 또는 추가해야 한다는 것을 나타냅니다. 아니면, 최소 요구 사항을 지원할 수 있는 다른 계산 리소스를 선택하고 필요한 워크로드에 증가가 필요하다는 것이 나타나면 규모를 확장합니다.

## 원인 3: 로컬 리소스를 사용하여 Runbook을 인증할 수 없습니다.
### 해결 방법
**Microsoft-SMA** 이벤트 로그에 *Win32 프로세스가[4294967295] 코드와 함께 종료되었습니다.*라고 설명하는 이벤트가 있는지 확인합니다. 이 오류의 원인은 Runbook에 인증을 구성하지 않았거나 Hybrid Worker 그룹에 대해 실행 자격 증명을 지정하지 않았기 때문일 수 있습니다. [Runbook 권한](automation-hybrid-runbook-worker.md#runbook-permissions)을 검토하여 Runbook에 대한 인증을 올바르게 구성했는지 확인합니다.

<!---HONumber=AcomDC_0817_2016-->