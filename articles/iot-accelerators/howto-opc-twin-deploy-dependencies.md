---
title: Azure에서 OPC Twin 클라우드 종속성을 배포하는 방법 | Microsoft Docs
description: 이 문서에서는 로컬 개발 및 디버깅을 수행하는 데 필요한 OPC Twin Azure 종속성을 배포하는 방법을 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 42024fc506de7befed7c44ebcc410756b6f43a35
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646425"
---
# <a name="deploying-dependencies-for-local-development"></a>로컬 개발을 위한 종속성 배포

> [!IMPORTANT]
> 이 문서를 업데이트하는 동안 최신 콘텐츠는 [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/)를 참조하세요.

이 문서에서는 로컬 개발 및 디버깅을 수행하는 데 필요한 Azure 플랫폼 서비스만 배포하는 방법을 설명합니다.   마지막으로 로컬 개발 및 디버깅에 필요한 모든 항목을 포함하는 리소스 그룹이 배포됩니다.

## <a name="deploy-azure-platform-services"></a>Azure 플랫폼 서비스 배포

1. PowerShell 및 [AzureRM PowerShell](/powershell/azure/azurerm/install-azurerm-ps) 확장이 설치되어 있는지 확인합니다.  명령 프롬프트 또는 터미널을 열고 다음을 실행합니다.

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 프롬프트에 따라 배포에 사용할 리소스 그룹에 이름을 할당합니다.  스크립트는 Azure 구독의 이 리소스 그룹에 대한 종속성만 배포하고 마이크로 서비스는 배포하지 않습니다.  또한 이 스크립트는 Azure AD에 애플리케이션을 등록합니다.  이는 OAUTH 기반 인증을 지원하는 데 필요합니다.  배포에는 몇 분 정도 걸릴 수 있습니다.

3. 스크립트가 완료되면 .env 파일을 저장하도록 선택할 수 있습니다.  .env 환경 파일은 개발 머신에서 실행하려는 모든 서비스 및 도구의 구성 파일입니다.  

## <a name="troubleshooting-deployment-failures"></a>배포 실패 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용해야 합니다.  이름은 리소스 이름 지정 요구 사항을 준수해야 하는 리소스의 이름을 지정하는 데도 사용됩니다.  

### <a name="azure-active-directory-ad-registration"></a>Azure AD(Active Directory) 등록

배포 스크립트는 Azure AD에서 Azure AD 애플리케이션을 등록하려고 합니다.  선택한 Azure AD 테넌트에 대한 권한에 따라 이 작업이 실패할 수 있습니다. 세 개의 옵션이 있습니다.

1. 테넌트 목록에서 Azure AD 테넌트를 선택한 경우 스크립트를 다시 시작하고 목록에서 다른 항목을 선택합니다.
2. 또는 프라이빗 Azure AD 테넌트를 배포하고 스크립트를 다시 시작한 다음, 사용하도록 선택합니다.
3. 인증 없이 계속합니다.  마이크로 서비스를 로컬에서 실행하고 있으므로 허용되지만, 프로덕션 환경을 모방하지는 않습니다.  

## <a name="next-steps"></a>다음 단계

이제 OPC Twin 서비스를 기존 프로젝트에 성공적으로 배포했으므로 다음 단계를 제안합니다.

> [!div class="nextstepaction"]
> [OPC Twin 모듈을 배포하는 방법에 관해 알아보기](howto-opc-twin-deploy-modules.md)