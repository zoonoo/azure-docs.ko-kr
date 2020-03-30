---
title: 윈도우 가상 데스크톱 관리 도구 - Azure
description: Windows 가상 데스크톱 관리 도구에서 문제를 해결하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127494"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Windows Virtual Desktop 관리 도구 문제 해결

이 문서에서는 Windows 가상 데스크톱 관리 도구를 배포하는 동안 발생할 수 있는 문제와 이를 해결하는 방법에 대해 설명합니다.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>오류: 구성되었지만 자동화된 설정이 실패한 관리 도구 서비스

관리 도구에 대한 서비스를 성공적으로 설정했지만 자동 설정에 실패하면 다음과 같은 오류 메시지가 표시됩니다.

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

이는 일반적으로 다음 두 가지 중 하나를 의미합니다.

- 사용자는 테넌트 수준에서 구독 및 전역 관리자에 대한 소유자 권한을 가지고 있지만 Azure에 로그인할 수 없습니다.
- 사용자의 계정 설정에는 다단계 인증이 활성화되어 있습니다.

이 문제를 해결하려면

1. Azure Active Directory 사용자 보안 주체 이름에 대해 만든 사용자에게 "기여자" 구독 수준이 있는지 확인합니다.
2. <portal.azure.com> 계정 설정을 확인하고 다단계 인증이 없는지 확인합니다. 켜져 있으면 끕니다.
3. Windows 가상 데스크톱 동의 페이지를 방문하여 서버 및 클라이언트 앱에 동의가 있는지 확인합니다.
4. 문제가 계속되는 경우 [관리 도구 배포](manage-resources-using-ui.md) 자습서를 검토하고 도구를 다시 배포합니다.

## <a name="error-job-with-specified-id-already-exists"></a>오류: 지정된 ID가 있는 작업이 이미 있습니다.

사용자에게 "지정된 ID가 있는 작업"이라는 오류 메시지가 표시되는 경우 템플릿을 배포할 때 "응용 프로그램 이름" 매개 변수에 고유한 이름을 제공하지 않았기 때문입니다.

이 문제를 해결하려면 관리 도구를 "응용 프로그램 이름" 매개 변수가 채워진 채 다시 배포합니다.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>관리 도구를 열 때 지연된 동의 프롬프트

관리 도구를 배포할 때 동의 프롬프트가 바로 열리지 않을 수 있습니다. 즉, Azure 웹 앱 서비스를 로드하는 데 평소보다 더 오래 걸립니다. Azure Web 로드가 완료된 후 프롬프트가 나타납니다.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>사용자는 미국 동부 지역에 관리 도구를 배포할 수 없습니다.

고객이 지역을 미국 동부로 설정하면 관리 도구를 배포할 수 없습니다.

이 문제를 해결하려면 관리 도구를 다른 지역에 배포합니다. 다른 지역에서 도구를 다시 배포하는 것은 사용자 경험에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문제 해결 개요, 피드백 및 지원에서](troubleshoot-set-up-overview.md)에스컬레이션 트랙에 대해 자세히 알아봅니다.
- [원격 데스크톱 서비스에 대한 ARM 템플릿에서](https://github.com/Azure/RDS-Templates/blob/master/README.md)Windows 가상 데스크톱 도구의 문제를 보고하는 방법에 대해 알아봅니다.
- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원을](troubleshoot-set-up-overview.md)참조하십시오.
- 관리 도구를 배포하는 방법에 대해 알아보려면 [관리 도구 배포를](manage-resources-using-ui.md)참조하십시오.