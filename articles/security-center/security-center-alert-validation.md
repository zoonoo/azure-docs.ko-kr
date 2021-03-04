---
title: Azure Security Center의 경고 유효성 검사 | Microsoft Docs
description: 에서 보안 경고가 올바르게 구성 되었는지 확인 하는 방법에 대해 알아봅니다 Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 74323c63ab8985dee2391a546d82258dcb8d0114
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099525"
---
# <a name="alert-validation-in-azure-security-center"></a>Azure Security Center의 경고 유효성 검사
이 문서에서는 시스템이 Azure Security Center 경고에 대해 제대로 구성되었는지 확인하는 방법을 알아봅니다.

## <a name="what-are-security-alerts"></a>보안 경고란?
경고는 리소스에 대한 위협을 탐지할 때 Security Center에서 생성하는 알림입니다. 문제를 신속 하 게 조사 하는 데 필요한 정보와 함께 경고를 우선 순위를 정하여 나열 합니다. Security Center는 공격을 해결하는 방법에 대한 권장 사항도 제공합니다.
자세한 내용은 [Security Center의 보안 경고](security-center-alerts-overview.md) 및 [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) 을 참조 하세요.


## <a name="generate-sample-azure-defender-alerts"></a>샘플 Azure Defender 경고 생성

[Azure Security Center의 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)에 설명 된 대로 새로운 경고 미리 보기 환경을 사용 하는 경우 Azure Portal의 보안 경고 페이지에서 몇 번의 클릭으로 샘플 경고를 만들 수 있습니다.

샘플 경고를 사용 하 여 다음을 수행 합니다.

- Azure Defender의 가치 및 기능 평가
- 보안 경고에 대해 수행한 모든 구성 (예: SIEM 통합, 워크플로 자동화 및 전자 메일 알림)의 유효성을 검사 합니다.

샘플 경고를 만들려면 다음을 수행 합니다.

1. 경고 페이지의 도구 모음에서 **샘플 경고 만들기** 를 선택 합니다. 
1. 구독을 선택합니다.
1. 경고를 보려는 관련 Azure Defender 계획/s를 선택 합니다. 
1. **샘플 경고 만들기** 를 선택 합니다.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Azure Security Center에서 샘플 경고를 만드는 단계":::
    
    샘플 경고가 생성 되 고 있음을 알리는 알림이 표시 됩니다.

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="샘플 경고가 생성 되 고 있음을 알립니다.":::

    몇 분 후에 경고가 보안 경고 페이지에 표시 됩니다. 또한 Azure Security Center 보안 경고 (연결 된 SIEMs, 전자 메일 알림 등)를 수신 하도록 구성 된 다른 모든 위치에 표시 됩니다.

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="보안 경고 목록의 샘플 경고":::

    > [!TIP]
    > 시뮬레이션 된 리소스에 대 한 경고입니다.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Azure Vm에서 경고 시뮬레이션 (Windows) <a name="validate-windows"></a>

컴퓨터에 에이전트를 설치한 Security Center 후에는 경고의 공격을 받을 컴퓨터에서 다음 단계를 수행 합니다.

1. 실행 파일 (예: **calc.exe**)을 컴퓨터의 바탕 화면이 나 사용자 편의의 다른 디렉터리에 복사 하 고 **ASC_AlertTest_662jfi039N.exe** 로 이름을 바꿉니다.
1. 명령 프롬프트를 열고 인수 (예: 가짜 인수 이름)를 사용 하 여이 파일을 실행 합니다. ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5~10분 정도 기다렸다가 Security Center 경고를 엽니다. 경고가 표시 됩니다.

> [!NOTE]
> Windows에 대 한이 테스트 경고를 검토할 때 필드 **인수 감사를 사용 하도록 설정** 했는지 확인 **합니다.** **False** 인 경우에는 명령줄 인수 감사를 사용 하도록 설정 해야 합니다. 사용하도록 설정하려면 다음 명령을 사용합니다. 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Azure Vm에서 경고 시뮬레이트 (Linux) <a name="validate-linux"></a>

컴퓨터에 에이전트를 설치한 Security Center 후에는 경고의 공격을 받을 컴퓨터에서 다음 단계를 수행 합니다.
1. 실행 파일을 편리한 위치에 복사 하 고 이름을 **./asc_alerttest_662jfi039n** 로 바꿉니다. 예를 들면 다음과 같습니다.

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 명령 프롬프트를 열고 다음 파일을 실행 합니다.

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5~10분 정도 기다렸다가 Security Center 경고를 엽니다. 경고가 표시 됩니다.


## <a name="simulate-alerts-on-kubernetes"></a>Kubernetes에서 경고 시뮬레이트 <a name="validate-kubernetes"></a>

Azure Kubernetes Service를 Security Center와 통합 한 경우 다음 kubectl 명령을 사용 하 여 경고가 작동 하는지 테스트할 수 있습니다.

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Kubernetes 노드 및 클러스터를 방어 하는 방법에 대 한 자세한 내용은 [Kubernetes 용 Azure Defender 소개](defender-for-kubernetes-introduction.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 경고 유효성 검사 프로세스에 대해 소개했습니다. 이제 유효성 검사에 익숙해졌으므로 다음 문서를 시도해 보세요.

* [Azure Security Center에서 Azure Key Vault 위협 검색의 유효성 검사](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) -경고를 관리 하 고 Security Center의 보안 인시던트에 대응 하는 방법을 알아봅니다.
* [Azure Security Center의 보안 경고 이해](./security-center-alerts-overview.md) -다양 한 유형의 보안 경고에 대해 알아봅니다.