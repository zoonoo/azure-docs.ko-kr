---
title: 프로그래밍 방식으로 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 프로그래밍 방식으로 IoT Central을 만들고 관리하는 방법을 설명합니다. JavaScript, Python, C#, Ruby 및 Go와 같은 다중 언어 SDK를 사용하여 애플리케이션을 보고, 수정하고, 제거할 수 있습니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/19/2020
ms.topic: how-to
ms.openlocfilehash: ba0ee0a610299bbe6b7e550f204cd2fd50d6d71a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748632"
---
# <a name="manage-iot-central-programmatically"></a>프로그래밍 방식으로 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 애플리케이션을 만들고 관리하는 대신 Azure SDK를 사용하여 프로그래밍 방식으로 애플리케이션을 관리할 수 있습니다. 지원되는 언어는 JavaScript, Python, C#, Ruby 및 Go입니다.

## <a name="install-the-sdk"></a>SDK 설치

다음 표에서는 SDK 리포지토리 및 패키지 설치 명령을 보여 줍니다.

| SDK 리포지토리 | 패키지 설치 |
| -------------- | ------------ |
| [JavaScript용 Azure IotCentralClient SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Python용 Microsoft Azure SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Azure SDK for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Ruby용 Microsoft Azure SDK - 리소스 관리(미리 보기)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Maven 패키지](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [패키지 릴리스](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>샘플

[Azure IoT Central ARM SDK 샘플](https://docs.microsoft.com/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) 리포지토리에는 Azure IoT Central 애플리케이션 만들기, 업데이트, 나열 및 삭제하는 방법을 보여 주는 여러 프로그래밍 언어에 대한 코드 샘플이 있습니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션을 프로그래밍 방식으로 관리하는 방법을 배웠으므로 제안할 다음 단계는 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 서비스에 대해 자세히 알아보는 것입니다.
