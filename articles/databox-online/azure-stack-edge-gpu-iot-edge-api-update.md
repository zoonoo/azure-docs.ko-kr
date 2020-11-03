---
title: Azure Stack Edge 2021년 1월 업데이트 영향 | Microsoft Docs
description: 2021년 1월 업데이트를 설치한 후 IoT Edge 역할 관리가 Azure Stack Edge 디바이스에 미치는 영향을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: a86c4c9c32f78cda1b5183a8651783fdcbb20e85
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754677"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge"></a>Azure Stack Edge용 IoT Edge 역할 관리 변경 내용

Azure Stack Edge 디바이스용 IoT Edge 역할 관리에서는 2021년 1월에 릴리스되는 최신 버전의 API, SDK 및 Azure PowerShell을 사용합니다. 이 문서에서는 Azure Stack Edge에서 최신 버전의 API, SDK 및 PowerShell cmdlet을 IoT Edge 역할 관리에 사용할 때 필요한 변경 내용에 대해 자세히 설명합니다.

2021년 1월 업데이트는 Azure Stack Edge Pro - GPU, Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 디바이스에만 사용할 수 있으며, 이 문서의 정보는 이러한 디바이스에만 적용됩니다. 

## <a name="iot-edge-role-management-changes"></a>IoT Edge 역할 관리 변경 내용

선택적인 2021년 1월 디바이스 소프트웨어 버전이 Azure Stack Edge 디바이스에 설치되면 최신 버전의 API, SDK 및 PowerShell cmdlet을 IoT Edge 역할 관리에 사용해야 합니다.

- 2019-08-01 버전의 역할 관리 API를 사용하는 경우 2021년 1월에 릴리스되는 API 버전으로 업그레이드합니다. 
- SDK 버전 1.0.0을 통해 역할 관리를 사용하는 경우 2021년 1월에 릴리스되는 버전으로 업그레이드합니다.
- `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` 또는 `Remove-AzStackEdgeRole`과 같은 Azure PowerShell cmdlet(미리 보기)에서 역할 관리를 사용하는 경우 2021년 2월에 릴리스되는 새 cmdlet을 기다려야 합니다.

위의 변경 내용은 2021년 1월 업데이트를 적용하는 경우에만 필요합니다. 기존 디바이스 소프트웨어 버전을 유지하면 IoT Edge 역할 관리에 영향을 주지 않습니다. 그러나 새로운 기능 및 향상된 보안을 위해 디바이스를 업데이트하는 것이 좋습니다. 


## <a name="api-usage"></a>API 사용

API를 통해 IoT Edge 역할 관리를 수행한 경우 나중에 게시되는 새 API 버전(2020-12-01)을 사용해야 합니다. 현재 역할 API를 사용하고 있고 예정된 디바이스 소프트웨어 버전을 설치한 경우 PUT, GET, DELETE Kubernetes 역할로 이동한 다음, PUT IoT Addon API로 이동해야 합니다.


### <a name="for-put-method"></a>PUT 메서드의 경우

#### <a name="current-http-request"></a>현재 http 요청 

- API 호출은 다음 URI에서 수행됩니다. [https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01](https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01)

- 요청 본문은 다음과 같습니다.

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="upcoming-http-request"></a>예정된 http 요청 

- Kubernetes 역할에 대한 API 호출은 다음 URI에서 수행됩니다. [https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01](https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01)

    요청 본문은 다음과 같습니다.

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- IoT Edge 추가 기능에 대한 API 호출은 다음 URI에서 수행됩니다. [https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01](https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01)


    요청 본문은 다음과 같습니다.

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-get-method"></a>GET 메서드의 경우

#### <a name="current-http-response"></a>현재 http 응답

- API 호출은 다음 URI에서 수행됩니다. [https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01](https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01)


- 응답 본문은 다음과 같습니다.

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="upcoming-http-response"></a>예정된 http 응답

- API 호출은 다음 URI에서 수행됩니다. [https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01](https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01)
- 응답 본문은 다음과 같습니다. 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-delete-method"></a>DELETE 메서드의 경우

### <a name="current"></a>현재

API 호출은 다음 URI에서 수행됩니다. [https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01](https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01)

### <a name="upcoming"></a>예정

API 호출은 다음 URI에서 수행됩니다. [https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01](https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01)


## <a name="sdk-usage"></a>SDK 사용

SDK를 사용하고 있고 2021년 1월 디바이스 업데이트를 설치한 경우 다음 표와 같이 IoT Edge 역할을 설정하는 방법을 변경해야 합니다. 그런 다음, 예정된 NuGet 패키지를 다운로드하고 설치하여 다음 샘플과 같이 새 SDK로 이동해야 합니다.

### <a name="current-sdk-sample"></a>현재 SDK 샘플

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="new-sdk-sample"></a>새 SDK 샘플

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>cmdlet 사용

`Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` 또는 `Remove-AzStackEdgeRole` cmdlet을 사용하는 경우 2021년 2월에 계획된 새 버전을 기다려야 합니다.

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

#### <a name="i-am-using-azure-stack-edge-pro--fpga-does-the-january-2021-update-affect-the-fpga-model"></a>Azure Stack Edge Pro – FPGA를 사용하고 있습니다. 2021년 1월 업데이트가 FPGA 모델에 영향을 주나요?

아니요. 2021년 1월 업데이트는 Azure Stack Edge – GPU, Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 디바이스에만 적용됩니다. Azure Stack Edge Pro – FPGA는 이 업데이트의 영향을 받지 않으며, IoT Edge 역할 관리를 변경할 필요가 없습니다.

#### <a name="after-i-update-my-azure-stack-edge-pro---gpu-to-the-new-device-software-in-january-2021-are-any-of-the-existing-services-affected"></a>2021년 1월에 Azure Stack Edge Pro - GPU를 새 디바이스 소프트웨어로 업데이트하면 기존 서비스 중 어떤 것이 영향을 받나요?

아니요. 구성된 서비스는 2021년 1월 디바이스 업데이트를 설치한 후 영향을 받지 않습니다. <!--check w/ Anoob, what existing services you are talking about in this question-->

#### <a name="what-are-the-high-level-changes-to-iot-edge-management-api-sdk-or-cmdlet"></a>IoT Edge 관리 API, SDK 또는 cmdlet에 대한 주요 변경 내용은 무엇인가요?

IoT Edge는 Kubernetes 역할의 추가 기능입니다. 이는 먼저 Kubernetes가 구성되었는지 확인한 다음, IoT Edge 구성을 수행해야 한다는 것을 의미합니다.


## <a name="next-steps"></a>다음 단계

- [업데이트를 적용](azure-stack-edge-gpu-install-update.md)하는 방법에 대해 알아봅니다.

