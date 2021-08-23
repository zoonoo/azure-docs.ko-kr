---
title: Cloud Service(클래식) 역할 재활용의 일반적인 원인 | Microsoft Docs
description: 재활용이 중요한 가동 중지 시간을 갑작스럽게 발생시킬 수 있는 클라우드 서비스 역할입니다. 역할이 재활용되도록 하는 일반적인 문제는 다음과 같으며 이는 가동 중지를 줄이는 데 도움이 될 수 있습니다.
ms.topic: troubleshooting
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a489a43b3c81c96a297013d6ca04b8be009dc661
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090046"
---
# <a name="common-issues-that-cause-azure-cloud-service-classic-roles-to-recycle"></a>Azure Cloud Service(클래식) 역할이 재활용되는 일반적인 문제

> [!IMPORTANT]
> [Azure Cloud Services(확장 지원)](../cloud-services-extended-support/overview.md)는 Azure Cloud Services 제품에 대한 새로운 Azure Resource Manager 기반 배포 모델입니다.해당 변경으로 Azure Service Manager 기반 배포 모델에서 실행되는 Azure Cloud Services는 Cloud Services(클래식)로 이름이 바뀌고 모든 새로운 배포는 [Cloud Services(확장된 지원)](../cloud-services-extended-support/overview.md)를 사용해야 합니다.

이 문서에서는 배포 문제의 일반적인 몇 가지 원인을 설명하고 이러한 문제를 해결하기 위한 문제 해결 팁을 제공합니다. 애플리케이션에 문제가 있다는 것을 역할 인스턴스가 시작에 실패하거나 초기화, 사용 중, 및 중지 상태를 반복할 경우에 알 수 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>런타임 종속성 누락
애플리케이션의 역할이 .NET Framework 또는 Azure 관리된 라이브러리의 일부가 아닌 어셈블리를 사용하는 경우 애플리케이션 패키지에 해당 어셈블리를 명시적으로 포함해야 합니다. 기타 Microsoft 프레임워크가 기본적으로 Azure에서 사용할 수 없다는 사실을 염두에 두십시오. 역할이 이러한 프레임워크를 사용하는 경우 애플리케이션 패키지에 해당 어셈블리를 추가해야 합니다.

애플리케이션을 작성하고 포장하기 전에 다음을 확인합니다.

* Visual studio를 사용하는 경우 **로컬 복사** 속성이 Azure SDK 또는.NET Framework의 일부가 아닌 프로젝트에서 참조된 각 어셈블리에 대해 **True** 로 설정해야 합니다.
* web.config 파일이 컴파일 요소에서 사용되지 않은 어셈블리를 참조하지 않도록 합니다.
* 모든 .cshtml 파일의 **빌드 작업** 은 **콘텐츠** 로 설정합니다. 이렇게 하면 패키지에서 파일이 올바르게 표시되고 기타 참조된 파일이 표시되도록 합니다.

## <a name="assembly-targets-wrong-platform"></a>어셈블리가 잘못된 플랫폼을 목표로 합니다.
Azure는 64비트 환경입니다. 따라서 32비트 대상에 컴파일된 .NET 어셈블리는 Azure에서 작동하지 않습니다.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>초기화하거나 중지하는 동안 처리되지 않은 예외를 throw하는 역할
[OnStart], [OnStop] 및 [실행] 메서드를 포함하는 [RoleEntryPoint] 클래스의 메서드에서 throw되는 예외는 처리되지 않은 예외입니다. 이러한 메서드에서 처리되지 않은 예외가 발생하면 역할이 재활용됩니다. 역할이 반복적으로 재활용되면 시작하려 할 때마다 처리되지 않은 예외가 throw될 수 있습니다.

## <a name="role-returns-from-run-method"></a>역할은 실행 메서드에서 반환합니다.
[실행] 메서드는 무기한 실행되도록 합니다. 코드가 [실행] 메서드를 재정의하는 경우 무기한 대기해야 합니다. [실행] 메서드가 반환되는 경우 역할은 재활용됩니다.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>잘못된 DiagnosticsConnectionString 설정
애플리케이션이 Azure Diagnostics를 사용하는 경우 서비스 구성 파일은 `DiagnosticsConnectionString` 구성 설정을 지정해야 합니다. 이 설정은 Azure에서 스토리지 계정에 HTTPS 연결을 지정해야 합니다.

Azure에 애플리케이션 패키지를 배포하기 전에 `DiagnosticsConnectionString` 설정이 올바른지 확인하려면 다음을 확인합니다.  

* `DiagnosticsConnectionString` 설정이 Azure의 유효한 스토리지 계정을 가리킵니다.  
  기본적으로 이 설정은 에뮬레이트된 스토리지 계정을 가리키므로 애플리케이션 패키지를 배포하기 전에 이 설정을 명시적으로 변경해야 합니다. 이 설정을 변경하지 않는 경우 역할 인스턴스가 진단 모니터링을 시작하려고 할 때 예외가 throw됩니다. 역할 인스턴스가 무기한 재활용되게 할 수 있습니다.
* 연결 문자열은 다음과 같은 [형식](../storage/common/storage-configure-connection-string.md)으로 지정해야 합니다. (프로토콜은 HTTPS로 지정해야 합니다.) *MyAccountName* 을 스토리지 계정의 이름으로 바꾸고 *MyAccountKey* 를 액세스 키로 바꿉니다.    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Microsoft Visual Studio용 Azure Tools를 사용하여 애플리케이션을 개발하는 경우 속성 페이지를 사용하여 이 값을 설정할 수 있습니다.

## <a name="exported-certificate-does-not-include-private-key"></a>내보낸 인증서는 프라이빗 키를 포함하지 않습니다.
TLS에서 웹 역할을 실행하려면 내보낸 관리 인증서가 프라이빗 키를 포함해야 합니다. *Windows 인증서 관리자* 를 사용하여 인증서를 내보내는 경우 **프라이빗 키 내보내기** 옵션에 대해 **예** 를 선택해야 합니다. 인증서는 현재 지원되는 유일한 형식인 PFX 형식으로 내보내야 합니다.

## <a name="next-steps"></a>다음 단계
클라우드 서비스에 대한 [문제해결 문서](../index.yml?product=cloud-services&tag=top-support-issue) 를 더 봅니다.

[Kevin Williamson의 블로그 시리즈](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)에서 더 많은 역할 재활용 시나리오를 보세요.

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[OnStart]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Run]: /previous-versions/azure/reference/ee772746(v=azure.100)