---
title: Azure 보안 센터의 경고 유효성 검사(EICAR 테스트 파일) | 마이크로 소프트 문서
description: 이 문서에서는 Azure Security Center에서 보안 경고를 유효성 검사하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140000"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure Security Center의 경고 유효성 검사(EICAR 테스트 파일)
이 문서에서는 시스템이 Azure Security Center 경고에 대해 제대로 구성되었는지 확인하는 방법을 알아봅니다.

## <a name="what-are-security-alerts"></a>보안 경고란?
경고는 리소스에 대한 위협을 탐지할 때 Security Center에서 생성하는 알림입니다. 문제를 신속하게 조사하는 데 필요한 정보와 함께 경고의 우선 순위를 지정하고 나열합니다. Security Center는 공격을 해결하는 방법에 대한 권장 사항도 제공합니다.
자세한 내용은 [보안 센터의 보안 경고](security-center-alerts-overview.md) 및 보안 경고 관리 및 응답을 [참조하십시오.](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>경고 유효성 검사

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Windows VM에서 경고 유효성 검사<a name="validate-windows"></a>

컴퓨터에 보안 센터 에이전트를 설치한 후 경고의 공격받은 리소스가 될 컴퓨터에서 다음 단계를 따릅니다.

1. 실행 파일을 복사 (예 : **calc.exe)** 컴퓨터의 바탕 화면에, 또는 편의의 다른 디렉토리, **ASC_AlertTest_662jfi039N.exe로**이름을 변경합니다 .
1. 명령 프롬프트를 열고 다음과 같은 인수(가짜 인수 이름만)로 이 파일을 실행합니다.```ASC_AlertTest_662jfi039N.exe -foo```
1. 5~10분 정도 기다렸다가 Security Center 경고를 엽니다. 아래 [예제와](#alert-validate) 유사한 경고가 표시되어야 합니다.

> [!NOTE]
> Windows에 대한 이 테스트 경고를 검토할 때 **인수 감사 사용 이** 가능한 필드가 **true인지**확인합니다. **false이면**명령줄 인수 감사를 사용하도록 설정해야 합니다. 활성화하려면 다음 명령을 사용합니다.
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Linux VM에서 경고 유효성 검사<a name="validate-linux"></a>

컴퓨터에 보안 센터 에이전트를 설치한 후 경고의 공격받은 리소스가 될 컴퓨터에서 다음 단계를 따릅니다.
1. 실행 을 편리한 위치로 복사하고 **이름을 ./asc_alerttest_662jfi039n.**

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 명령 프롬프트를 열고 이 파일을 실행합니다.

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5~10분 정도 기다렸다가 Security Center 경고를 엽니다. 아래 [예제와](#alert-validate) 유사한 경고가 표시되어야 합니다.

### <a name="alert-example"></a>경고 예제<a name="alert-validate"></a>

![경고 유효성 검사 예제](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Kubernetes에서 경고 유효성 검사<a name="validate-kubernetes"></a>

Azure Kubernetes 서비스를 통합하는 보안 센터 미리 보기 기능을 사용하는 경우 다음 kubectl 명령을 실행하여 경고가 작동하는지 테스트합니다.

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Azure Kubernetes 서비스 및 Azure 보안 센터의 통합에 대한 자세한 내용은 [이 문서를](azure-kubernetes-service-integration.md)참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 경고 유효성 검사 프로세스에 대해 소개했습니다. 이제 유효성 검사에 익숙해졌으므로 다음 문서를 시도해 보세요.

* [Azure 보안 센터에서 Azure 키 볼트 위협 탐지 유효성 검사](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Azure 보안 센터에서 보안 경고 관리 및 응답](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) - 경고를 관리하고 보안 센터의 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) - 다양한 유형의 보안 경고에 대해 알아봅니다.
