---
title: Windows Virtual Desktop(클래식) 관리 도구 - Azure
description: Windows Virtual Desktop(클래식) 관리 도구와 관련된 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 430ab72a3eb0cd41d707ff0eb5d10d8b97162d53
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444244"
---
# <a name="troubleshoot-the-windows-virtual-desktop-classic-management-tool"></a>Windows Virtual Desktop(클래식) 관리 도구 문제 해결

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다.

이 문서에서는 Windows Virtual Desktop 관리 도구를 배포하는 동안 발생할 수 있는 문제와 이러한 문제를 해결하는 방법을 설명합니다.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>오류: 관리 도구 서비스가 구성되었지만 자동 설치에 실패함

관리 도구에 대한 서비스를 성공적으로 설정했지만 자동 설치에 실패하면 다음 오류 메시지가 표시됩니다.

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

이는 일반적으로 다음 두 가지 중 하나를 의미합니다.

- 사용자는 테넌트 수준의 구독 및 전역 관리자에 대한 소유자 권한이 있지만 Azure에 로그인할 수 없습니다.
- 사용자의 계정 설정에는 다단계 인증이 활성화되어 있습니다.

이 문제를 해결하려면

1. Azure Active Directory 사용자 계정 이름에 대해 만든 사용자에게 "기여자" 구독 수준이 있는지 확인합니다.
2. UPN 계정으로 <portal.azure.com>에 로그인하여 계정 설정을 확인하고 다단계 인증이 설정되어 있지 않은지 확인합니다. 설정되어 있는 경우 해제합니다.
3. Windows Virtual Desktop 동의 페이지를 방문하여 서버 및 클라이언트 앱이 동의했는지 확인합니다.
4. 문제가 계속되면 [관리 도구 배포](manage-resources-using-ui.md) 자습서를 검토하고 도구를 다시 배포합니다.

## <a name="error-job-with-specified-id-already-exists"></a>오류: 지정한 ID를 가진 작업이 이미 있습니다.

사용자에게 "지정된 ID를 가진 작업이 이미 있습니다."라는 오류 메시지가 표시되는 경우는 템플릿을 배포할 때 "애플리케이션 이름" 매개 변수에 고유한 이름을 제공하지 않았기 때문입니다.

이 문제를 해결하려면 "애플리케이션 이름" 매개 변수를 입력한 관리 도구를 다시 배포합니다.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>관리 도구를 열 때 지연된 동의 확인 프롬프트

관리 도구를 배포할 때 동의 확인 프롬프트가 바로 열리지 않을 수 있습니다. 즉, Azure 웹앱 서비스를 로드하는 데 평소보다 오래 걸립니다. Azure 웹에서 로드를 완료한 후에 프롬프트가 표시됩니다.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>사용자는 미국 동부 지역에 관리 도구를 배포할 수 없습니다.

고객이 지역을 미국 동부로 설정하는 경우 관리 도구를 배포할 수 없습니다.

이 문제를 해결하려면 다른 지역에 관리 도구를 배포합니다. 다른 지역에 도구를 다시 배포해도 사용자 환경에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview-2019.md)에서 에스컬레이션 트랙에 대해 알아봅니다.
- [원격 데스크톱 서비스용 ARM 템플릿](https://github.com/Azure/RDS-Templates/blob/master/README.md)에서 Windows Virtual Desktop 도구와 관련된 문제를 보고하는 방법에 대해 알아봅니다.
- 관리 도구를 배포하는 방법에 대해 알아보려면 [관리 도구 배포](manage-resources-using-ui.md)를 참조하세요.