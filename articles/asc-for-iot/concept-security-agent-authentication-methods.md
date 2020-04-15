---
title: 보안 에이전트 인증 방법
description: IoT 서비스에 대한 Azure 보안 센터를 사용할 때 사용할 수 있는 다양한 인증 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0d9d51292c3cae9634af917819b558cdfd2fa04b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311509"
---
# <a name="security-agent-authentication-methods"></a>보안 에이전트 인증 방법

이 문서에서는 AzureIoTSecurity 에이전트와 함께 IoT Hub를 사용하여 인증하는 데 사용할 수 있는 다양한 인증 방법에 대해 설명합니다.

IoT Hub의 IoT용 Azure 보안 센터에 온보온된 각 장치에 대해 보안 모듈이 필요합니다. 장치를 인증하기 위해 IoT용 Azure 보안 센터는 두 가지 방법 중 하나를 사용할 수 있습니다. 기존 IoT 솔루션에 가장 적합한 방법을 선택하십시오.

> [!div class="checklist"]
> * 보안모듈 옵션
> * 장치 옵션

## <a name="authentication-methods"></a>인증 방법

AzureIoTSecurity 에이전트가 인증을 수행하는 두 가지 방법은 다음과 같은 것입니다.

- **보안모듈** 인증 모드<br>
에이전트는 장치 ID와 독립적으로 보안 모듈 ID를 사용하여 인증됩니다.
보안 에이전트가 보안 모듈(대칭 키만 해당)을 통해 전용 인증 방법을 사용하려면 이 인증 유형을 사용합니다.

- **장치** 인증 모드<br>
이 방법에서 보안 에이전트는 먼저 장치 ID를 인증합니다. 초기 인증 후 IoT 에이전트용 Azure 보안 센터는 장치의 인증 데이터와 함께 REST API를 사용하여 IoT Hub에 대한 **REST** 호출을 수행합니다. 그런 다음 IoT 에이전트용 Azure 보안 센터에서 IoT Hub에서 보안 모듈 인증 방법 및 데이터를 요청합니다. 마지막 단계에서 IoT 용 Azure 보안 센터 에이전트는 IoT용 Azure 보안 센터에 대한 인증을 수행합니다.

보안 에이전트가 기존 장치 인증 방법(자체 서명된 인증서 또는 대칭 키)을 재사용하려면 이 인증 유형을 사용합니다.

구성 하는 방법에 대 한 자세한 내용은 [보안 에이전트 설치 매개 변수를](#security-agent-installation-parameters) 참조 하십시오.

## <a name="authentication-methods-known-limitations"></a>알려진 제한 사항 인증 방법

- **보안모듈** 인증 모드는 대칭 키 인증만 지원합니다.
- CA 서명 된 인증서는 **장치** 인증 모드에서 지원되지 않습니다.

## <a name="security-agent-installation-parameters"></a>보안 에이전트 설치 매개 변수

[보안 에이전트를 배포할](how-to-deploy-agent.md)때 인증 세부 정보를 인수로 제공해야 합니다.
이러한 인수는 다음 표에 설명되어 있습니다.

|리눅스 매개 변수 이름 | 윈도우 매개 변수 이름 | 단축 매개 변수 |설명|옵션|
|---------------------|---------------|---------|---------------|---------------|
|인증-ID|인증 ID|오이 (오이)|인증 ID| **보안모듈** 또는 **장치**|
|authentication-method|인증 방법|아움 (것)에움 (|인증 방법|**대칭키** 또는 **자체 서명인증서**|
|파일 경로|FilePath|f|인증서 또는 대칭 키가 포함된 파일에 대한 절대 전체 경로| |
|호스트 이름|HostName|hn|IoT 허브의 FQDN|예: ContosoIotHub.azure-devices.net|
|장치 ID|deviceId|디|디바이스 ID|예: 마이디바이스1|
|인증서 위치 종류|인증서위치Kind|Cl|인증서 저장소 위치|**로컬 파일** 또는 **저장소**|
|

설치 보안 에이전트 스크립트를 사용하는 경우 다음 구성이 자동으로 수행됩니다. 보안 에이전트 인증을 수동으로 편집하려면 구성 파일을 편집합니다.

## <a name="change-authentication-method-after-deployment"></a>배포 후 인증 방법 변경

설치 스크립트를 통해 보안 에이전트를 배포할 때 구성 파일이 자동으로 생성됩니다.

배포 후 인증 방법을 변경하려면 구성 파일을 수동으로 편집해야 합니다.

### <a name="c-based-security-agent"></a>C#기반 보안 에이전트

다음 매개 변수로 _Authentication.config_ 편집:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C 기반 보안 에이전트

다음 매개 변수로 _LocalConfiguration.json_ 편집:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>참조

- [보안 에이전트 개요](security-agent-architecture.md)
- [보안 에이전트 배포](how-to-deploy-agent.md)
- [원시 보안 데이터 액세스](how-to-security-data-access.md)
