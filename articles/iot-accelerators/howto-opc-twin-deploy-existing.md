---
title: 기존 Azure 프로젝트에 OPC Twin 모듈을 배포하는 방법 | 마이크로 소프트 문서
description: 이 문서에서는 기존 프로젝트에 OPC Twin을 배포하는 방법에 대해 설명합니다. 또한 배포 실패 문제를 해결하는 방법을 배울 수 있습니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824114"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>기존 프로젝트에 OPC 트윈 배포

OPC Twin 모듈은 IoT Edge에서 실행되며 OPC 트윈 및 레지스트리 서비스에 여러 에지 서비스를 제공합니다.

OPC Twin 마이크로 서비스는 OPC 트윈 IoT 에지 모듈을 통해 공장 현장의 공장 운영자와 OPC UA 서버 장치 간의 통신을 용이하게 합니다. 마이크로 서비스는 REST API를 통해 OPC UA 서비스(찾아보기, 읽기, 쓰기 및 실행)를 노출합니다. 

OPC UA 장치 레지스트리 마이크로 서비스는 등록된 OPC UA 응용 프로그램 및 해당 끝점에 대한 액세스를 제공합니다. 운영자와 관리자는 새 OPC UA 응용 프로그램을 등록 및 등록 취소하고 끝점을 포함하여 기존 응용 프로그램을 찾아볼 수 있습니다. 레지스트리 서비스는 애플리케이션 및 엔드포인트 관리 외에도 등록된 OPC Twin IoT Edge 모듈을 카탈로그화합니다. 서비스 API를 사용하면 서버 검색(검색 서비스) 시작 또는 중지 또는 OPC Twin 마이크로 서비스를 사용하여 액세스할 수 있는 새 엔드포인트 트윈 활성화와 같은 에지 모듈 기능을 제어할 수 있습니다.

모듈의 핵심은 감독자 ID입니다. 감독자는 해당 OPC UA 레지스트리 API를 사용하여 활성화되는 OPC UA 서버 끝점에 해당하는 엔드포인트 트윈을 관리합니다. 이 끝점 쌍 변환 OPC UA JSON OPC 트윈 마이크로 서비스에서 클라우드에서 실행 되 고 OPC UA 이진 메시지로 변환, 관리 되는 끝점에 상태 보호 채널을 통해 전송 되는. 또한 감독자는 처리를 위해 장치 검색 이벤트를 OPC UA 장치 온보딩 서비스로 보내는 검색 서비스를 제공하며, 이 경우 이러한 이벤트로 인해 OPC UA 레지스트리가 업데이트됩니다.  이 문서에서는 OPC Twin 모듈을 기존 프로젝트에 배포하는 방법을 보여 주며 있습니다.

> [!NOTE]
> 배포 세부 정보 및 지침에 대한 자세한 내용은 GitHub [리포지토리](https://github.com/Azure/azure-iiot-opc-twin-module)를 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

PowerShell 및 [AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) 확장이 설치되어 있는지 확인합니다. 아직 사용하지 않은 경우 이 GitHub 리포지토리를 복제합니다. PowerShell에서 다음 명령을 실행합니다.

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Azure에 산업용 IoT 서비스 배포

1. PowerShell 세션에서 다음을 실행합니다.

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. 프롬프트에 따라 배포의 리소스 그룹에 이름을 할당하고 웹 사이트에 이름을 지정합니다.   스크립트는 마이크로 서비스 및 해당 Azure 플랫폼 종속성을 Azure 구독의 리소스 그룹에 배포합니다.  또한 스크립트는 OAUTH 기반 인증을 지원하기 위해 AAD(Azure Active Directory) 테넌트에 응용 프로그램을 등록합니다.  배포에는 몇 분 정도 걸릴 수 있습니다.  솔루션이 성공적으로 배포되면 표시되는 내용의 예는 다음과 같습니다.

   ![산업용 IoT OPC 트윈이 기존 프로젝트에 배포됨](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   출력에는 공용 끝점의 URL이 포함됩니다. 

3. 스크립트가 성공적으로 완료되면 파일을 저장할지 여부를 `.env` 선택합니다.  콘솔과 `.env` 같은 도구를 사용하여 클라우드 엔드포인트에 연결하거나 개발 및 디버깅을 위한 모듈을 배포하려면 환경 파일이 필요합니다.

## <a name="troubleshooting-deployment-failures"></a>배포 실패 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용해야 합니다.  이름은 리소스 이름 지정 요구 사항을 준수해야 하는 리소스의 이름을 지정하는 데도 사용됩니다.  

### <a name="website-name-already-in-use"></a>이미 사용 중이신 웹 사이트 이름

웹 사이트의 이름이 이미 사용 중일 수 있습니다.  이 오류가 발생하면 다른 응용 프로그램 이름을 사용해야 합니다.

### <a name="azure-active-directory-aad-registration"></a>Azure 활성 디렉터리(AAD) 등록

배포 스크립트는 Azure Active Directory에서 두 개의 AAD 응용 프로그램을 등록하려고 시도합니다.  선택한 AAD 테넌트에 대한 권한에 따라 배포가 실패할 수 있습니다. 옵션에는

1. 테넌트 목록에서 AAD 테넌트를 선택한 경우 스크립트를 다시 시작하고 목록에서 다른 테넌트를 선택합니다.
2. 또는 다른 구독에 개인 AAD 테넌트를 배포하고 스크립트를 다시 시작한 다음 사용하도록 선택합니다.

> [!WARNING]
> 인증 없이 계속하지 마십시오.  이렇게 선택한 경우 누구나 인증되지 않은 인터넷에서 OPC Twin 끝점에 액세스할 수 있습니다.   항상 ["로컬" 배포 옵션을](howto-opc-twin-deploy-dependencies.md) 선택하여 타이어를 차게 할 수 있습니다.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>올인원 산업용 IoT 서비스 데모 배포

서비스 및 종속성 대신 올인원 데모를 배포할 수도 있습니다.  하나의 데모의 모든 세 OPC UA 서버, OPC 트윈 모듈, 모든 마이크로 서비스 및 샘플 웹 응용 프로그램이 포함되어 있습니다.  데모용입니다.

1. 리포지토리의 복제본이 있는지 확인합니다(위 참조). 리포지토리의 루트에서 PowerShell 프롬프트를 열고 다음을 실행합니다.

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. 프롬프트에 따라 리소스 그룹에 새 이름과 웹 사이트에 이름을 할당합니다.  성공적으로 배포되면 스크립트는 웹 응용 프로그램 끝점의 URL을 표시합니다.

## <a name="deployment-script-options"></a>배포 스크립트 옵션

스크립트는 다음과 같은 매개 변수를 사용합니다.

```powershell
-type
```

배포 유형(vm, 로컬, 데모)

```powershell
-resourceGroupName
```

기존 리소스 그룹의 이름또는 새 리소스 그룹의 이름일 수 있습니다.

```powershell
-subscriptionId
```

리소스가 배포되는 구독 ID는 선택 사항입니다.

```powershell
-subscriptionName
```

또는 구독 이름입니다.

```powershell
-resourceGroupLocation
```

선택 사항, 리소스 그룹 위치입니다. 이 지정한 경우 이 위치에 새 리소스 그룹을 만들려고 합니다.

```powershell
-aadApplicationName
```

아래에 등록할 AAD 응용 프로그램의 이름입니다.

```powershell
-tenantId
```

사용할 AAD 테넌트입니다.

```powershell
-credentials
```

## <a name="next-steps"></a>다음 단계

OPC Twin을 기존 프로젝트에 배포하는 방법을 배웠으므로 다음 단계로 제안했습니다.

> [!div class="nextstepaction"]
> [OPC UA 클라이언트 및 OPC UA PLC의 보안 통신](howto-opc-vault-secure.md)
