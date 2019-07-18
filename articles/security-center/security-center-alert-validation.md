---
title: Azure Security Center에서 유효성 검사 (EICAR 테스트 파일) 경고 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 보안 경고를 유효성 검사하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626312"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure Security Center에서 경고 유효성 검사 (EICAR 테스트 파일)
이 문서에서는 시스템이 Azure Security Center 경고에 대해 제대로 구성되었는지 확인하는 방법을 알아봅니다.

## <a name="what-are-security-alerts"></a>보안 경고란?
경고는 리소스에 위협 요소를 검색 하는 경우 Security Center에서 생성 하는 알림. 우선 순위를 지정 하 고 문제를 빠르게 조사 하는 데 필요한 정보와 함께 경고를 나열 합니다. Security Center에는 공격을 재구성할 수 있습니다 하는 방법에 대 한 권장 사항도 제공 합니다.
자세한 내용은 [Azure Security Center에서 보안 경고](security-center-alerts-overview.md) 고 [관리 및 Azure Security Center에서 보안 경고에 대응](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>경고 유효성 검사

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Windows VM에 대 한 경고를 확인 합니다. <a name="validate-windows"></a>

Security Center 에이전트가 컴퓨터에 설치 된 후 컴퓨터에서 경고의 공격 받은 리소스를 저장할 다음과 같이

1. 실행 파일을 복사 (예를 들어 **calc.exe**) 컴퓨터의 바탕 화면 또는 사용자 편의 위해 다른 디렉터리를 및로 이름 바꾸기 **ASC_AlertTest_662jfi039N.exe**합니다.
1. 명령 프롬프트를 열고는 인수 (가짜 인수 이름만)를 사용 하 여이 파일을 같은 실행 합니다. ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5~10분 정도 기다렸다가 Security Center 경고를 엽니다. 경고 비슷합니다는 [예제에서는](#alert-validate) 아래 표시:

> [!NOTE]
> Windows에 대 한이 테스트 경고를 검토할 때는 있는지 필드 **인수 감사 사용 하도록 설정** 됩니다 **true**합니다. 있으면 **false**를 감사 하는 명령줄 인수를 사용 하도록 설정 해야 합니다. 기능을 사용 하려면 다음 명령줄을 사용 합니다.
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Linux VM에 대 한 경고를 확인 합니다. <a name="validate-linux"></a>

Security Center 에이전트가 컴퓨터에 설치 된 후 컴퓨터에서 경고의 공격 받은 리소스를 저장할 다음과 같이
1. 실행 파일을 편리한 위치에 복사 하 고 이름을 **. / asc_alerttest_662jfi039n**예를 들어:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 명령 프롬프트를 열고이 파일을 실행 합니다.

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5~10분 정도 기다렸다가 Security Center 경고를 엽니다. 경고 비슷합니다는 [예제에서는](#alert-validate) 아래 표시:

### 경고 예제 <a name="alert-validate"></a>

![경고 유효성 검사 예](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>참고자료
이 문서에서는 경고 유효성 검사 프로세스에 대해 소개했습니다. 이제 유효성 검사에 익숙해졌으므로 다음 문서를 시도해 보세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
