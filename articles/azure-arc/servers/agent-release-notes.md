---
title: Azure Arc 사용 서버 (미리 보기) 에이전트의 새로운 기능
description: 이 문서에는 Azure Arc 사용 서버 (미리 보기) 에이전트에 대 한 릴리스 정보가 포함 되어 있습니다. 요약 된 많은 문제에 대 한 자세한 내용은 추가 정보를 참조 하세요.
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 3b739401603f6dc18b9f48fb3cb6e28023a051ab
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532174"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Azure Arc 사용 서버 (미리 보기) 에이전트의 새로운 기능

Azure Arc 사용 서버 (미리 보기)에 연결 된 컴퓨터 에이전트는 지속적으로 향상 된 기능을 받습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정

## <a name="september-2020"></a>2020년 9월

버전: 1.0 (일반 공급)

### <a name="plan-for-change"></a>변경 계획

- 미리 보기 에이전트 (1.0 이전의 모든 버전)에 대 한 지원은 향후 서비스 업데이트에서 제거 될 예정입니다.
- 대체 끝점에 대 한 지원이 제거 `.azure-automation.net` 되었습니다. 프록시가 있는 경우 끝점을 허용 해야 `*.his.arc.azure.com` 합니다.
- 연결 된 컴퓨터 에이전트가 Azure에서 호스트 되는 가상 컴퓨터에 설치 된 경우 Arc 사용 서버 리소스에서 VM 확장을 설치 하거나 수정할 수 없습니다. 이는 가상 머신의 **HybridCompute 및 microsoft** 리소스에서 수행 되는 충돌 하는 확장 작업을 방지 하기 위한 것 **입니다.** 모든 확장 작업에 컴퓨터에 대 한 **Microsoft Compute** 리소스를 사용 합니다.
- 게스트 구성 프로세스의 이름이 *gcd* 에서 Linux의 *gcad* 로 변경 되 고 *gcservice* 가 Windows의 *gcarcservice* 로 변경 되었습니다.

### <a name="new-feature"></a>새로운 기능

- `azcmagent logs`지원 정보를 수집 하는 옵션이 추가 되었습니다.
- `azcmagent license`EULA를 표시 하는 옵션이 추가 되었습니다.
- `azcmagent show --json`간편한 구문 분석할 형식으로 에이전트 상태를 출력 하는 옵션이 추가 되었습니다.
- `azcmagent show`서버가 Azure에 호스트 된 가상 컴퓨터에 있는지 여부를 나타내는 플래그를 출력에 추가 했습니다.
- `azcmagent disconnect --force-local-only`Azure 서비스에 연결할 수 없는 경우 로컬 에이전트 상태를 다시 설정 하도록 허용 하는 옵션이 추가 되었습니다.
- `azcmagent connect --cloud`추가 클라우드를 지원 하기 위한 옵션이 추가 되었습니다. 이 릴리스에서는 Azure만 에이전트 릴리스 시 서비스에서 지원 됩니다.
- 에이전트가 Azure 지원 언어로 지역화 되었습니다.

### <a name="fixed"></a>고정

- 연결 확인 향상.
- Linux에서 에이전트를 업그레이드할 때 손상 된 프록시 서버 설정 문제를 수정 했습니다.
- Windows Server 2012 r 2를 실행 하는 서버에 에이전트를 설치 하는 동안 문제가 해결 되었습니다.
- 확장 설치 안정성 향상

## <a name="august-2020"></a>2020년 8월

버전: 0.11

- 이 릴리스에는 이전에 Ubuntu 20.04에 대 한 지원이 발표 되었습니다. 일부 Azure VM 확장은 Ubuntu 20.04을 지원 하지 않으므로이 Ubuntu 버전에 대 한 지원이 제거 됩니다.

- 확장 배포에 대 한 안정성 향상.

### <a name="known-issues"></a>알려진 문제

이전 버전의 Linux 에이전트를 사용 하 고 프록시 서버를 사용 하도록 구성한 경우 업그레이드 후 프록시 서버 설정을 다시 구성 해야 합니다. 이 위해 실행 `sudo azcmagent_proxy add http://proxyserver.local:83`합니다.

## <a name="next-steps"></a>다음 단계

여러 하이브리드 머신에서 Arc 지원 서버(미리 보기)를 평가하거나 활성화하기 전에 [연결된 머신 에이전트 개요](agent-overview.md)를 검토하여 요구 사항, 에이전트에 대한 기술 세부 정보 및 배포 방법을 이해하세요.