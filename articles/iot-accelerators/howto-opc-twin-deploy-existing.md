---
title: OPC Twin 모듈을 기존 Azure 프로젝트에 배포하는 방법 | Microsoft Docs
description: 이 문서에서는 OPC Twin을 기존 프로젝트에 배포하는 방법을 설명합니다. 배포 실패 문제를 해결하는 방법에 관해서도 알아볼 수 있습니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4535818898459f1eb183026629bc338524c1efd9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646413"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>기존 프로젝트에 OPC Twin 배포

> [!IMPORTANT]
> 이 문서를 업데이트하는 동안 최신 콘텐츠는 [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/)를 참조하세요.

OPC Twin 모듈은 IoT Edge에서 실행되며 OPC Twin 및 레지스트리 서비스에 여러 에지 서비스를 제공합니다.

OPC Twin 마이크로 서비스는 OPC Twin IoT Edge 모듈을 통해 센터 운영자와 제조 현장의 OPC UA 서버 디바이스 간 통신을 용이하게 합니다. 마이크로 서비스는 REST API를 통해 OPC UA 서비스(찾아보기, 읽기, 쓰기 및 실행)를 노출합니다. 

OPC UA 디바이스 레지스트리 마이크로 서비스는 등록된 OPC UA 애플리케이션 및 해당 엔드포인트에 대한 액세스를 제공합니다. 운영자와 관리자는 새로운 OPC UA 애플리케이션을 등록 및 등록 해제하고 엔드포인트를 포함한 기존 애플리케이션을 검색할 수 있습니다. 애플리케이션 및 엔드포인트 관리 외에도 레지스트리 서비스는 등록된 OPC Twin IoT Edge 모듈도 카탈로그화합니다. 서비스 API를 사용하면 서버 검색(서비스 검사) 시작 또는 중지, OPC Twin 마이크로 서비스를 사용하여 액세스할 수 있는 새 엔드포인트 트윈 활성화와 같은 에지 모듈 기능을 제어할 수 있습니다.

모듈의 핵심은 감독자 ID입니다. 감독자는 해당 OPC UA 레지스트리 API를 사용하여 활성화된 OPC UA 서버 엔드포인트에 해당하는 엔드포인트 트윈을 관리합니다. 이 엔드포인트 트윈은 클라우드에서 실행되는 OPC Twin 마이크로 서비스에서 수신된 OPC UA JSON을 상태 저장 보안 채널을 통해 관리형 엔드포인트로 전송되는 OPC UA 이진 메시지로 변환합니다. 감독자는 처리를 위해 디바이스 검색 이벤트를 OPC UA 디바이스 온보딩 서비스로 보내는 검색 서비스도 제공하며, 해당 이벤트로 인해 OPC UA 레지스트리가 업데이트됩니다.  이 문서에서는 OPC Twin 모듈을 기존 프로젝트에 배포하는 방법을 보여 줍니다.

> [!NOTE]
> 배포 세부 정보와 지침에 관한 자세한 내용은 GitHub [리포지토리](https://github.com/Azure/azure-iiot-opc-twin-module)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

PowerShell 및 [AzureRM PowerShell](/powershell/azure/azurerm/install-azurerm-ps) 확장이 설치되어 있는지 확인합니다. 아직 수행하지 않은 경우 이 GitHub 리포지토리를 복제합니다. PowerShell에서 다음 명령을 실행합니다.

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

2. 프롬프트에 따라 배포의 리소스 그룹에 이름을 할당하고 웹 사이트에 이름을 지정합니다.   이 스크립트는 마이크로 서비스와 해당 Azure 플랫폼 종속성을 Azure 구독의 리소스 그룹에 배포합니다.  또한 이 스크립트는 OAUTH 기반 인증을 지원하기 위해 AAD(Azure Active Directory) 테넌트에 애플리케이션을 등록합니다.  배포하는 데는 시간이 몇 분 정도 걸립니다.  솔루션이 성공적으로 배포되면 표시되는 항목의 예는 다음과 같습니다.

   ![기존 프로젝트에 산업용 IoT OPC Twin 배포](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   출력에는 퍼블릭 엔드포인트의 URL이 포함됩니다. 

3. 스크립트가 성공적으로 완료되면 `.env` 파일 저장 여부를 선택합니다.  콘솔과 같은 도구를 사용하여 클라우드 엔드포인트에 연결하거나 개발 및 디버깅을 위해 모듈을 배포하려면 `.env` 환경 파일이 필요합니다.

## <a name="troubleshooting-deployment-failures"></a>배포 실패 문제 해결

### <a name="resource-group-name"></a>리소스 그룹 이름

짧고 간단한 리소스 그룹 이름을 사용해야 합니다.  이름은 리소스 이름 지정 요구 사항을 준수해야 하는 리소스의 이름을 지정하는 데도 사용됩니다.  

### <a name="website-name-already-in-use"></a>웹 사이트 이름이 이미 사용 중임

웹 사이트 이름이 이미 사용 중일 수 있습니다.  이 오류가 발생하면 다른 애플리케이션 이름을 사용해야 합니다.

### <a name="azure-active-directory-aad-registration"></a>AAD(Azure Active Directory) 등록

배포 스크립트는 Azure Active Directory에서 두 AAD 애플리케이션을 등록하려고 시도합니다.  선택한 AAD 테넌트에 대한 권한에 따라 배포가 실패할 수 있습니다. 두 가지 옵션 중이 있습니다.

* 테넌트 목록에서 AAD 테넌트를 선택한 경우 스크립트를 다시 시작하고 목록에서 다른 항목을 선택합니다.
* 또는 다른 구독에 프라이빗 AAD 테넌트를 배포하고, 스크립트를 다시 시작한 다음, 사용하도록 선택합니다.

> [!WARNING]
> 인증 없이는 계속하지 않아야 합니다.  인증 없이 계속하도록 선택하면 누구나 인터넷에서 인증되지 않은 상태로 OPC Twin 엔드포인트에 액세스할 수 있습니다.   언제든지 [“로컬” 배포 옵션](howto-opc-twin-deploy-dependencies.md)을 선택하여 올바른지 확인할 수 있습니다.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>올인원 산업용 IoT 서비스 데모 배포

서비스와 종속성만 배포하는 것이 아니라 올인원 데모를 배포할 수도 있습니다.  올인원 데모에는 OPC UA 서버 3개, OPC Twin 모듈, 모든 마이크로 서비스 및 샘플 웹 애플리케이션이 포함되어 있습니다.  데모용으로만 제공됩니다.

1. 리포지토리의 복제본이 있는지 확인합니다(위 참조). 리포지토리의 루트에서 PowerShell 프롬프트를 열고 다음을 실행합니다.

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. 프롬프트에 따라 리소스 그룹에 새 이름을 할당하고 웹 사이트에 이름을 지정합니다.  성공적으로 배포되면 스크립트는 웹 애플리케이션 엔드포인트의 URL을 표시합니다.

## <a name="deployment-script-options"></a>배포 스크립트 옵션

이 스크립트는 다음 매개 변수를 사용합니다.

```powershell
-type
```

배포 유형(vm, 로컬, 데모)

```powershell
-resourceGroupName
```

기존 또는 새 리소스 그룹의 이름일 수 있습니다.

```powershell
-subscriptionId
```

리소스를 배포할 구독 ID(선택 사항)입니다.

```powershell
-subscriptionName
```

또는 구독 이름입니다.

```powershell
-resourceGroupLocation
```

리소스 그룹 위치(선택사항)입니다. 지정된 경우 이 위치에 새 리소스 그룹을 만들려고 합니다.

```powershell
-aadApplicationName
```

등록할 AAD 애플리케이션의 이름입니다.

```powershell
-tenantId
```

사용할 AAD 테넌트입니다.

```powershell
-credentials
```

## <a name="next-steps"></a>다음 단계

이제 기존 프로젝트에 OPC Twin을 배포하는 방법을 배웠으므로 다음 단계를 제안합니다.

> [!div class="nextstepaction"]
> [OPC UA 클라이언트 및 OPC UA PLC의 보안 통신](howto-opc-vault-secure.md)