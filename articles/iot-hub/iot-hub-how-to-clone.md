---
title: Azure IoT 허브를 복제하는 방법
description: Azure IoT 허브를 복제하는 방법
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429145"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Azure IoT 허브를 다른 지역으로 복제하는 방법

이 문서에서는 IoT Hub를 복제하는 방법을 살펴보고 시작하기 전에 답변해야 하는 몇 가지 질문을 제공합니다. 다음은 IoT 허브를 복제하려는 몇 가지 이유입니다.
 
* 유럽에서 북미(또는 그 반대)와 같이 회사를 한 리전에서 다른 리전으로 옮기고 있으며 리소스와 데이터가 새 위치에 지리적으로 가깝게 유지되기를 원하므로 허브를 이동해야 합니다.

* 개발 과 프로덕션 환경을 위한 허브를 설정하는 것입니다.

* 다중 허브 고가용성의 사용자 지정 구현을 수행하려고 합니다. 자세한 내용은 [IoT Hub 고가용성 및 재해 복구의 교차 영역 HA 섹션을 달성하는 방법을](iot-hub-ha-dr.md#achieve-cross-region-ha)참조하십시오.

* 허브에 대해 구성된 [파티션](iot-hub-scaling.md#partitions) 수를 늘리려고 합니다. 이 설정은 허브를 처음 만들 때 설정되며 변경할 수 없습니다. 이 문서의 정보를 사용하여 허브를 복제하고 복제본을 만들 때 파티션 수를 늘릴 수 있습니다.

허브를 복제하려면 원래 허브에 대한 관리 액세스 권한이 있는 구독이 필요합니다. 새 허브를 새 리소스 그룹 및 리전에 배치하거나 원래 허브와 동일한 구독또는 새 구독에 넣을 수 있습니다. 허브 이름은 전역적으로 고유해야 하므로 동일한 이름을 사용할 수 없습니다.

> [!NOTE]
> 현재 IoT 허브를 자동으로 복제할 수 있는 기능은 없습니다. 주로 수동 프로세스이므로 오류가 발생하기 쉽습니다. 허브 복제의 복잡성은 허브의 복잡성에 정비례합니다. 예를 들어 메시지 라우팅없이 IoT 허브를 복제하는 것은 매우 간단합니다. 메시지 라우팅을 하나의 복잡성으로 추가하면 허브를 복제하는 것이 최소한 더 복잡해집니다. 또한 라우팅 끝점에 사용되는 리소스를 이동하는 경우, 그것은 더 복잡한 돋보기의 또 다른 순서입니다. 

## <a name="things-to-consider"></a>고려할 사항

IoT 허브를 복제하기 전에 고려해야 할 몇 가지 사항이 있습니다.

* 원래 위치에서 사용할 수 있는 모든 피쳐도 새 위치에서 사용할 수 있는지 확인합니다. 일부 서비스는 미리 보기 중이며 모든 기능을 사용할 수 있는 것은 아닙니다.

* 복제된 버전을 만들고 확인하기 전에 원래 리소스를 제거하지 마십시오. 허브를 제거하면 영원히 사라졌으며 허브가 올바르게 복제되었는지 확인하기 위해 설정이나 데이터를 확인하기 위해 복구할 방법이 없습니다.

* 많은 리소스에는 전역적으로 고유한 이름이 필요하므로 복제된 버전에 대해 다른 이름을 사용해야 합니다. 또한 복제된 허브가 속한 리소스 그룹에 대해 다른 이름을 사용해야 합니다. 

* 원래 IoT 허브에 대한 데이터는 마이그레이션되지 않습니다. 여기에는 원격 분석 메시지, C2D(클라우드-장치) 명령 및 일정 및 기록과 같은 작업 관련 정보가 포함됩니다. 메트릭 및 로깅 결과도 마이그레이션되지 않습니다. 

* Azure Storage로 라우팅된 데이터 또는 메시지의 경우 원래 저장소 계정에 데이터를 그대로 두거나, 해당 데이터를 새 리전의 새 저장소 계정으로 전송하거나, 이전 데이터를 그대로 두고 새 데이터의 새 위치에 새 저장소 계정을 만들 수 있습니다. Blob 저장소의 데이터 이동에 대한 자세한 내용은 [AzCopy 를 사용하여 시작하기 를](../storage/common/storage-use-azcopy-v10.md)참조하십시오.

* 이벤트 허브 및 서비스 버스 토픽 및 큐에 대한 데이터는 마이그레이션할 수 없습니다. 이는 시점 데이터이며 메시지가 처리된 후에는 저장되지 않습니다.

* 마이그레이션에 대한 가동 중지 시간을 예약해야 합니다. 장치를 새 허브로 복제하는 데는 시간이 걸립니다. 가져오기/내보내기 방법을 사용하는 경우 벤치마크 테스트를 통해 500,000대의 장치를 이동하는 데 약 2시간, 100만 대의 장치를 이동하는 데 4시간이 걸릴 수 있음을 테스트했습니다. 

* 장치를 종료하거나 변경하지 않고 새 허브에 장치를 복사할 수 있습니다. 

    * 장치가 원래 DPS를 사용하여 프로비전된 경우 장치를 다시 프로비전하면 각 장치에 저장된 연결 정보가 업데이트됩니다. 
    
    * 그렇지 않으면 가져오기/내보내기 메서드를 사용하여 장치를 이동한 다음 새 허브를 사용하려면 장치를 수정해야 합니다. 예를 들어 장치를 설정하여 원하는 두 개의 속성에서 IoT Hub 호스트 이름을 사용할 수 있습니다. 장치는 해당 IoT Hub 호스트 이름을 지정하고 이전 허브에서 장치를 분리한 다음 새 허브에 다시 연결합니다.
    
* 새 리소스와 함께 사용할 수 있도록 사용 중인 인증서를 업데이트해야 합니다. 또한 DNS 테이블 어딘가에 허브가 정의되어 있을 수 있으므로 해당 DNS 정보를 업데이트해야 합니다.

## <a name="methodology"></a>방법

이것은 IoT 허브를 한 리전에서 다른 리전으로 이동하는 데 권장하는 일반적인 방법입니다. 메시지 라우팅의 경우 리소스가 새 지역으로 이동되지 않는다고 가정합니다. 자세한 내용은 메시지 [라우팅의 섹션을](#how-to-handle-message-routing)참조하십시오.

   1. 허브 및 해당 설정을 리소스 관리자 템플릿으로 내보냅니다. 
   
   1. 이름과 복제된 허브의 위치를 모두 업데이트하는 등 템플릿을 변경합니다. 메시지 라우팅 끝점에 사용되는 템플릿의 리소스에 대해 해당 리소스에 대한 템플릿의 키를 업데이트합니다.
   
   1. 새 위치에서 템플릿을 새 리소스 그룹으로 가져옵니다. 이렇게 하면 복제가 만들어집니다.

   1. 필요에 따라 디버그합니다. 
   
   1. 템플릿에 내보내지 않은 모든 것을 추가합니다. 
   
       예를 들어 소비자 그룹은 템플릿으로 내보내지않습니다. 허브를 만든 후 소비자 그룹을 템플릿에 수동으로 추가하거나 [Azure 포털을](https://portal.azure.com) 사용해야 합니다. 문서에서 템플릿에 하나의 소비자 그룹을 추가하는 예가 있습니다 [Azure 리소스 관리자 템플릿을 사용하여 IoT Hub 메시지 라우팅을 구성합니다.](tutorial-routing-config-message-routing-rm-template.md)
       
   1. 원래 허브에서 복제본으로 장치를 복사합니다. IoT [허브에 등록된 장치 관리](#managing-the-devices-registered-to-the-iot-hub)섹션에서 다룹니다.

## <a name="how-to-handle-message-routing"></a>메시지 라우팅을 처리하는 방법

허브에서 [사용자 지정 라우팅을](iot-hub-devguide-messages-read-custom.md)사용하는 경우 허브에 대한 템플릿 내보내기에는 라우팅 구성이 포함되지만 리소스 자체는 포함되지 않습니다. 라우팅 리소스를 새 위치로 이동할지 아니면 그대로 두고 "있는 그대로" 계속 사용할지 선택해야 합니다. 

예를 들어 미국 서부에 메시지를 저장소 계정(미국 서부)으로 라우팅하는 허브가 있고 허브를 미국 동부로 이동하려고 한다고 가정해 보겠습니다. 허브를 이동하여 메시지를 미국 서부의 저장소 계정으로 계속 라우팅하도록 하거나 허브를 이동하고 저장소 계정을 이동할 수도 있습니다. 다른 리전의 엔드포인트 리소스에 메시지를 라우팅하는 데서 성능이 작을 수 있습니다.

라우팅 끝점에 사용되는 리소스도 이동하지 않는 경우 메시지 라우팅을 사용하는 허브를 쉽게 이동할 수 있습니다. 

허브에서 메시지 라우팅을 사용하는 경우 두 가지 선택 사항이 있습니다. 

1. 라우팅 끝점에 사용되는 리소스를 새 위치로 이동합니다.

    * [Azure 포털에서](https://portal.azure.com) 수동으로 또는 리소스 관리자 템플릿을 사용하여 새 리소스를 직접 만들어야 합니다. 

    * 전역적으로 고유한 이름이 있으므로 새 위치에 리소스를 만들 때 모든 리소스의 이름을 바여야 합니다. 
     
    * 새 허브를 만들기 전에 새 허브의 템플릿에서 리소스 이름과 리소스 키를 업데이트해야 합니다. 새 허브를 만들 때 리소스가 있어야 합니다.

1. 라우팅 끝점에 사용되는 리소스를 이동하지 마십시오. "제자리에"그들을 사용합니다.

   * 템플릿을 편집하는 단계에서는 각 라우팅 리소스에 대한 키를 검색하고 새 허브를 만들기 전에 템플릿에 넣어야 합니다. 

   * 허브는 여전히 원래 라우팅 리소스를 참조하고 구성된 대로 메시지를 라우팅합니다.

   * 허브와 라우팅 끝점 리소스가 같은 위치에 있지 않기 때문에 성능이 작아집니다.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>허브를 다른 지역으로 마이그레이션할 준비

이 섹션에서는 허브 마이그레이션에 대한 구체적인 지침을 제공합니다.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>원래 허브를 찾아 리소스 템플릿으로 내보냅니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 

1. 리소스 **그룹으로** 이동하여 이동하려는 허브가 포함된 리소스 그룹을 선택합니다. 리소스로 이동하여 **Resources** 그런 식으로 허브를 찾을 수도 있습니다. 허브를 선택합니다.

1. 허브의 속성 및 설정 목록에서 **템플릿 내보내기를** 선택합니다. 

   ![IoT Hub의 템플릿내보내기 명령을 보여주는 스크린샷입니다.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. 템플릿을 다운로드하려면 **다운로드를** 선택합니다. 파일을 다시 찾을 수 있는 곳에 저장합니다. 

   ![IoT Hub용 템플릿을 다운로드하는 명령을 보여주는 스크린샷입니다.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>템플릿 보기 

1. 다운로드 폴더(또는 템플릿을 내보낼 때 사용한 폴더)로 이동하여 zip 파일을 찾습니다. zip 파일을 열고 라는 `template.json`파일을 찾습니다. 을 선택한 다음 Ctrl+C를 선택하여 템플릿을 복사합니다. zip 파일에 없는 다른 폴더로 이동하여 파일을 붙여넣습니다(Ctrl+V). 이제 편집할 수 있습니다.
 
    다음 예제는 라우팅 구성이 없는 일반 허브에 대한 것입니다. S1 계층 허브(1단위)인 **ContosoTestHub29358지역** **서쪽**지역입니다. 내보낸 템플릿은 다음과 같습니다.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>템플릿 편집 

템플릿을 사용하여 새 리전에서 새 허브를 만들려면 몇 가지 사항을 변경해야 합니다. [VS 코드](https://code.visualstudio.com) 또는 텍스트 편집기를 사용하여 템플릿을 편집합니다.

#### <a name="edit-the-hub-name-and-location"></a>허브 이름 및 위치 편집

1. 맨 위에 있는 매개 변수 섹션을 제거하면 여러 매개 변수가 없을 것이기 때문에 허브 이름을 사용하는 것이 훨씬 간단합니다. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. 이전 단계에서 제거한 매개 변수에서 검색하는 대신 실제(새) 이름을 사용하도록 이름을 변경합니다. 

    새 허브의 경우 원래 허브의 이름과 문자열 *복제를* 사용하여 새 이름을 구성합니다. 허브 이름과 위치를 정리하여 시작합니다.
    
    이전 버전: 

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    새 버전:  

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    다음으로 **경로의** 값에 이전 허브 이름이 포함되어 있음을 알 수 있습니다. 새 것을 사용하도록 변경합니다. 이벤트라는 **eventHubEnd포인트** **아래의** 경로 값및 **운영 모니터링이벤트입니다.**

    작업이 완료되면 이벤트 허브 끝점 섹션은 다음과 같아야 합니다.

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>이동되지 않는 라우팅 리소스의 키 업데이트

라우팅이 구성된 허브에 대한 Resource Manager 템플릿을 내보낼 때 해당 리소스의 키가 내보낸 템플릿에 제공되지 않고 해당 위치가 별표로 표시됩니다. 새 허브의 템플릿을 가져오고 허브를 만들기 **전에** 포털의 해당 리소스로 이동하여 키를 검색하여 채워야 합니다. 

1. 라우팅 리소스에 필요한 키를 검색하여 템플릿에 넣습니다. [Azure 포털의](https://portal.azure.com)리소스에서 키를 검색할 수 있습니다. 

   예를 들어 메시지를 저장소 컨테이너로 라우팅하는 경우 포털에서 저장소 계정을 찾습니다. 설정 섹션에서 액세스 **키를**선택한 다음 키 중 하나를 복사합니다. 템플릿을 처음 내보낼 때 키모양은 다음과 같습니다.

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. 저장소 계정의 계정 키를 검색한 후 별표 대신 `AccountKey=****` 절의 템플릿에 넣습니다. 

1. 서비스 버스 큐의 경우 SharedAccessKeyName과 일치하는 공유 액세스 키를 가져옵니다. 다음은 키와 `SharedAccessKeyName` json의 키입니다.

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. 서비스 버스 토픽 및 이벤트 허브 연결도 마찬가지입니다.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>새 위치에 새 라우팅 리소스 만들기

이 섹션은 라우팅 끝점에 대해 허브에서 사용하는 리소스를 이동하는 경우에만 적용됩니다.

라우팅 리소스를 이동하려면 새 위치에 리소스를 수동으로 설정해야 합니다. [Azure 포털을](https://portal.azure.com)사용하거나 메시지 라우팅에 사용되는 각 리소스에 대해 리소스 관리자 템플릿을 내보내서 라우팅 리소스를 만들고 편집하고 가져올 수 있습니다. 리소스를 설정한 후 허브의 템플릿(라우팅 구성 포함)을 가져올 수 있습니다.

1. 라우팅에 사용되는 각 리소스를 만듭니다. [Azure 포털을](https://portal.azure.com)사용하여 수동으로 이 작업을 수행하거나 리소스 관리자 템플릿을 사용하여 리소스를 만들 수 있습니다. 템플릿을 사용하려는 경우 다음 단계를 따릅니다.

    1. 라우팅에서 사용되는 각 리소스에 대해 리소스 관리자 템플릿으로 내보냅니다.
    
    1. 리소스의 이름과 위치를 업데이트합니다. 

    1. 리소스 간의 상호 참조를 업데이트합니다. 예를 들어 새 저장소 계정에 대한 템플릿을 만드는 경우 해당 템플릿의 저장소 계정 이름과 해당 템플릿을 참조하는 다른 템플릿을 업데이트해야 합니다. 대부분의 경우 허브에 대한 템플릿의 라우팅 섹션은 리소스를 참조하는 유일한 다른 템플릿입니다. 

    1. 각 리소스를 배포하는 각 템플릿을 가져옵니다.

    라우팅에 사용되는 리소스가 설정되고 실행되면 계속진행할 수 있습니다.

1. IoT 허브의 템플릿에서 각 라우팅 리소스의 이름을 새 이름으로 변경하고 필요한 경우 위치를 업데이트합니다. 

이제 라우팅을 처리하기로 결정한 방법에 따라 이전 허브와 거의 똑같이 보이는 새 허브를 만드는 템플릿이 있습니다.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>이동 - 템플릿을 로드하여 새 영역에 새 허브 만들기

템플릿을 사용하여 새 위치에 새 허브를 만듭니다. 이동하려는 라우팅 리소스가 있는 경우 새 위치에 리소스를 설정하고 템플릿의 참조가 일치하도록 업데이트되어야 합니다. 라우팅 리소스를 이동하지 않는 경우 업데이트된 키가 있는 템플릿에 있어야 합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. **리소스 만들기를 선택합니다.** 

1. 검색 상자에서 "템플릿 배포"를 입력하고 Enter를 선택합니다.

1. **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)를 선택합니다.** 이렇게 하면 템플릿 배포화면으로 이동합니다. **만들기**를 선택합니다. 다음 화면이 표시됩니다.

   ![자신의 템플릿을 구축하기위한 명령을 보여주는 스크린 샷](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. 파일에서 템플릿을 업로드할 수 있는 **편집기에서 사용자 고유의 템플릿 빌드를 선택합니다.** 

1. **파일 로드를**선택합니다. 

   ![템플릿 파일을 업로드하는 명령을 보여주는 스크린샷](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. 편집한 새 템플릿을 찾아서 선택한 다음 **열기를**선택합니다. 편집 창에 템플릿을 로드합니다. **저장**을 선택합니다. 

   ![템플릿 로드를 보여주는 스크린샷](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. 다음 필드를 입력합니다.

   **구독**: 사용할 구독을 선택합니다.

   **리소스 그룹**: 새 위치에 새 리소스 그룹을 만듭니다. 이미 새 설정이 있는 경우 새 설정을 만드는 대신 선택할 수 있습니다.

   **위치**: 기존 리소스 그룹을 선택한 경우 리소스 그룹의 위치와 일치하도록 채워져 있습니다. 새 리소스 그룹을 만든 경우 해당 위치가 됩니다.

   **나는 확인란에 동의합니다**: 이것은 기본적으로 당신이 만드는 자원 (들)에 대한 지불에 동의한다는 것을 말합니다.

1. **구매** 단추를 선택합니다.

이제 포털에서 템플릿의 유효성을 검사하고 복제된 허브를 배포합니다. 라우팅 구성 데이터가 있는 경우 새 허브에 포함되지만 이전 위치의 리소스를 가리킵니다.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>IoT 허브에 등록된 장치 관리

이제 복제를 실행하고 있으므로 원래 허브에서 복제로 모든 장치를 복사해야 합니다. 

이 작업을 수행하는 방법에는 여러 가지가 있습니다. 원래 [DPS(장치 프로비저닝 서비스)를](/azure/iot-dps/about-iot-dps)사용하여 장치를 프로비전하거나 사용하지 않았습니다. 당신이 한 경우, 이것은 어렵지 않다. 그렇지 않은 경우 매우 복잡할 수 있습니다. 

DPS를 사용하여 장치를 프로비전하지 않은 경우 다음 섹션을 건너뛰고 [가져오기/내보내기를 사용하여 장치를 새 허브로 이동할](#using-import-export-to-move-the-devices-to-the-new-hub)수 있습니다.

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>DPS를 사용하여 새 허브에서 장치를 다시 프로비전

DPS를 사용하여 장치를 새 위치로 이동하려면 [장치를 다시 프로비전하는 방법을](../iot-dps/how-to-reprovision.md)참조하세요. 완료되면 [Azure 포털에서](https://portal.azure.com) 장치를 보고 새 위치에 있는지 확인할 수 있습니다.

[Azure 포털을](https://portal.azure.com)사용하여 새 허브로 이동합니다. 허브를 선택한 다음 **IoT 장치를**선택합니다. 복제된 허브에 다시 프로비전된 장치가 표시됩니다. 복제된 허브의 속성을 볼 수도 있습니다. 

라우팅을 구현한 경우 메시지를 리소스로 올바르게 라우팅하는지 테스트하고 확인합니다.

### <a name="committing-the-changes-after-using-dps"></a>DPS 사용 후 변경 내용 커밋

이 변경 사항은 DPS 서비스에서 커밋되었습니다.

### <a name="rolling-back-the-changes-after-using-dps"></a>DPS를 사용한 후 변경 내용을 롤백합니다. 

변경 내용을 롤백하려면 새 허브에서 이전 허브로 장치를 다시 프로비전합니다.

이제 허브 및 해당 장치 마이그레이션이 완료되었습니다. [정리로](#clean-up)건너뛸 수 있습니다.

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>가져오기-내보내기를 사용하여 장치를 새 허브로 이동

응용 프로그램은 .NET Core를 대상으로 하므로 Windows 또는 Linux에서 실행할 수 있습니다. 샘플을 다운로드하고, 연결 문자열을 검색하고, 실행할 비트에 대한 플래그를 설정하고 실행할 수 있습니다. 코드를 열지 않고도 이 작업을 수행할 수 있습니다.

### <a name="downloading-the-sample"></a>샘플 다운로드

1. 이 페이지의 IoT C# 샘플 사용: C# 에 [대한 Azure IoT 샘플.](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/) zip 파일을 다운로드하고 컴퓨터에서 압축을 풀십시오. 

1. 관련 코드는 ./iot 허브/샘플/서비스/ImportExportDeviceSample에 있습니다. 응용 프로그램을 실행하기 위해 코드를 보거나 편집할 필요가 없습니다.

1. 응용 프로그램을 실행하려면 세 개의 연결 문자열과 5개의 옵션을 지정합니다. 이 데이터를 명령줄 인수로 전달하거나 환경 변수를 사용하거나 이 둘의 조합을 사용합니다. 옵션을 명령줄 인수로 전달하고 연결 문자열을 환경 변수로 전달합니다. 

   그 이유는 연결 문자열이 길고 불안정하며 변경될 가능성이 낮지만 옵션을 변경하고 응용 프로그램을 두 번 이상 실행할 수 있기 때문입니다. 환경 변수의 값을 변경하려면 사용 중인 명령 창과 Visual Studio 또는 VS 코드를 닫아야 합니다. 

### <a name="options"></a>옵션

다음은 응용 프로그램을 실행할 때 지정한 다섯 가지 옵션입니다. 잠시 후 명령줄에 넣겠습니다.

*   **addDevices** (인수 1) -- 당신을 위해 생성 된 가상 장치를 추가하려는 경우 true로 설정합니다. 이러한 원본 허브에 추가 됩니다. 또한 **numToAdd(인수** 2)를 설정하여 추가할 장치 수를 지정합니다. 허브에 등록할 수 있는 최대 장치 수는 백만 개입니다. 이 옵션의 목적은 테스트용으로 특정 수의 장치를 생성한 다음 다른 허브로 복사할 수 있습니다.

*   **copyDevice** (인수 3) -- 한 허브에서 다른 허브로 장치를 복사하려면 이 것을 true로 설정합니다. 

*   **deleteSourceDevices** (인수 4) -- 소스 허브에 등록 된 모든 장치를 삭제하려면 true로 설정합니다. 이 장치를 실행하기 전에 모든 장치가 전송되었는지 확인할 때까지 기다리는 것이 좋습니다. 기기를 삭제하면 기기를 다시 사용할 수 없습니다.

*   **deleteDestDevices** (인수 5) -- 대상 허브 (복제)에 등록 된 모든 장치를 삭제하려면 true로 설정합니다. 장치를 두 번 이상 복사하려는 경우 이 작업을 수행할 수 있습니다. 

기본 명령은 *dotnet 실행이* 됩니다.NET 로컬 csproj 파일을 빌드 하 고 실행 합니다. 명령줄 인수를 실행하기 전에 끝에 추가합니다. 

명령줄은 다음과 같습니다.

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>연결 문자열에 환경 변수 사용

1. 샘플을 실행하려면 이전 및 새 IoT 허브에 대한 연결 문자열이 필요하며 임시 작업 파일에 사용할 수 있는 저장소 계정에 연결해야 합니다. 이러한 값에 대 한 값을 환경 변수에 저장 합니다.

1. 연결 문자열 값을 얻으려면 [Azure 포털에](https://portal.azure.com)로그인합니다. 

1. 메모장과 같이 검색할 수 있는 곳에 연결 문자열을 배치합니다. 다음을 복사하면 연결 문자열을 이동 위치에 직접 붙여 넣을 수 있습니다. 등호자 주위에 공백을 추가하거나 변수 이름을 변경하지 마십시오. 또한 연결 문자열 주위에 큰따옴표가 필요하지 않습니다. 저장소 계정 연결 문자열 주위에 따옴표를 넣으면 작동하지 않습니다.

   Windows의 경우 환경 변수를 설정하는 방법은 다음과 같은 것입니다.

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Linux의 경우 환경 변수를 정의하는 방법은 다음과 같은 것입니다.

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. IoT 허브 연결 문자열의 경우 포털의 각 허브로 이동합니다. 허브에 대한 **리소스를** 검색할 수 있습니다. 리소스 그룹을 알고 있는 경우 **리소스 그룹으로**이동하여 리소스 그룹을 선택한 다음 해당 리소스 그룹의 자산 목록에서 허브를 선택할 수 있습니다. 

1. 허브에 대한 설정에서 **공유 액세스 정책을** 선택한 다음 **iothubowner를** 선택하고 연결 문자열 중 하나를 복사합니다. 대상 허브에 대해동일한 작업을 수행합니다. 적절한 SET 명령에 추가합니다.

1. 저장소 계정 연결 문자열의 경우 리소스 또는 **리소스** **그룹** 아래에서 저장소 계정을 찾아 엽니다. 
   
1. 설정 섹션에서 액세스 **키를** 선택하고 연결 문자열 중 하나를 복사합니다. 적절한 SET 명령에 대한 연결 문자열을 텍스트 파일에 넣습니다. 

이제 SET 명령이 있는 파일에 환경 변수가 있으며 명령줄 인수가 무엇인지 알 수 있습니다. 샘플을 실행해 보겠습니다.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>샘플 응용 프로그램 실행 및 명령줄 인수 사용

1. 명령 프롬프트 창을 엽니다. Windows를 선택하고 `command prompt` 입력하여 명령 프롬프트 창을 가져옵니다.

1. 환경 변수를 한 번에 하나씩 설정하는 명령을 복사하고 명령 프롬프트 창에 붙여넣고 Enter를 선택합니다. 작업이 완료되면 명령 프롬프트 창을 입력하여 `SET` 환경 변수와 해당 값을 확인합니다. 명령 프롬프트 창에 복사한 후에는 새 명령 프롬프트 창을 열지 않는 한 다시 복사할 필요가 없습니다.

1. 명령 프롬프트 창에서 ./ImportExportDevicesSample(ImportExportDevicesSample.csproj 파일이 있는 위치)에 있을 때까지 디렉토리를 변경합니다. 그런 다음 다음을 입력하고 명령줄 인수를 포함합니다.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    dotnet 명령은 응용 프로그램을 빌드하고 실행합니다. 응용 프로그램을 실행할 때 옵션을 전달하기 때문에 응용 프로그램을 실행할 때마다 값을 변경할 수 있습니다. 예를 들어 한 번 실행하고 새 장치를 만든 다음 다시 실행하고 해당 장치를 새 허브에 복사하는 등의 작업이 가능합니다. 복제가 완료될 때까지 장치를 삭제하지 않는 것이 좋지만 동일한 실행에서 모든 단계를 수행할 수도 있습니다. 다음은 1000개의 장치를 생성한 다음 다른 허브로 복사하는 예제입니다.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    장치가 성공적으로 복사되었는지 확인한 후 다음과 같이 소스 허브에서 장치를 제거할 수 있습니다.

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Visual Studio를 사용하여 샘플 응용 프로그램 실행

1. Visual Studio에서 응용 프로그램을 실행하려면 현재 디렉터리를 IoTHubServiceSamples.sln 파일이 있는 폴더로 변경합니다. 그런 다음 명령 프롬프트 창에서 이 명령을 실행하여 Visual Studio에서 솔루션을 엽니다. 환경 변수를 설정하는 동일한 명령 창에서 이 작업을 수행해야 하므로 해당 변수를 알 수 있습니다.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로** *집합을* 선택합니다.    
    
1. 다섯 가지 옵션에 대한 ImportExportDevicesSample 폴더의 Program.cs 맨 위에 변수를 설정합니다.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. 응용 프로그램을 실행하려면 F5를 선택합니다. 실행이 끝나면 결과를 볼 수 있습니다.

### <a name="view-the-results"></a>결과 보기 

[Azure 포털에서](https://portal.azure.com) 장치를 보고 새 위치에 있는지 확인할 수 있습니다.

1. [Azure 포털을](https://portal.azure.com)사용하여 새 허브로 이동합니다. 허브를 선택한 다음 **IoT 장치를**선택합니다. 이전 허브에서 복제된 허브로 방금 복사한 장치가 표시됩니다. 복제된 허브의 속성을 볼 수도 있습니다. 

1. [Azure 포털의](https://portal.azure.com) Azure 저장소 계정으로 이동하여 `devicefiles` `ImportErrors.log`에 대한 컨테이너를 찾아서 가져오기/내보내기 오류를 확인합니다. 이 파일이 비어 있으면(크기가 0) 오류가 없습니다. 동일한 장치를 두 번 이상 가져오려고 하면 장치를 두 번째로 거부하고 로그 파일에 오류 메시지를 추가합니다.

### <a name="committing-the-changes"></a>변경 내용 커밋 

이 시점에서 허브를 새 위치로 복사하고 장치를 새 복제본으로 마이그레이션했습니다. 이제 장치가 복제된 허브에서 작동하도록 변경해야 합니다.

변경 내용을 커밋하려면 수행해야 하는 단계는 다음과 같습니다. 

* 각 장치를 업데이트하여 IoT Hub 호스트 이름을 변경하여 IoT Hub 호스트 이름을 새 허브로 가리킵니다. 장치를 처음 프로비전할 때 사용한 방법과 동일한 방법을 사용하여 이 작업을 수행해야 합니다.

* 새 허브를 가리키도록 이전 허브를 참조하는 응용 프로그램을 변경합니다.

* 완료되면 새 허브가 실행되고 실행되어야 합니다. 이전 허브에는 활성 장치가 없어야 하며 연결이 끊어진 상태입니다. 

### <a name="rolling-back-the-changes"></a>변경 내용 롤백

변경 내용을 롤백하기로 결정한 경우 수행할 단계는 다음과 같습니다.

* 각 장치를 업데이트하여 이전 허브의 IoT 허브 호스트 이름을 가리키도록 IoT 허브 호스트 이름을 변경합니다. 장치를 처음 프로비전할 때 사용한 방법과 동일한 방법을 사용하여 이 작업을 수행해야 합니다.

* 이전 허브를 가리키도록 새 허브를 참조하는 응용 프로그램을 변경합니다. 예를 들어 Azure 애널리틱스를 사용하는 경우 Azure [Stream Analytics 입력을](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)다시 구성해야 할 수 있습니다.

* 새 허브를 삭제합니다. 

* 라우팅 리소스가 있는 경우 이전 허브의 구성은 여전히 올바른 라우팅 구성을 가리키며 허브를 다시 시작한 후에도 해당 리소스와 함께 작동해야 합니다.

### <a name="checking-the-results"></a>결과 확인 

결과를 확인하려면 IoT 솔루션을 변경하여 새 위치의 허브를 가리키고 실행합니다. 즉, 이전 허브에서 수행한 새 허브에서 동일한 작업을 수행하고 제대로 작동하는지 확인합니다. 

라우팅을 구현한 경우 메시지를 리소스로 올바르게 라우팅하는지 테스트하고 확인합니다.

## <a name="clean-up"></a>정리

새 허브가 실행되고 장치가 올바르게 작동하고 있는지 확신할 때까지 정리하지 마십시오. 또한 해당 기능을 사용하는 경우 라우팅을 테스트해야 합니다. 준비가 되면 다음 단계를 수행하여 이전 리소스를 정리합니다.

* 아직 하지 않은 경우 이전 허브를 삭제합니다. 이렇게 하면 허브에서 모든 활성 장치가 제거됩니다.

* 새 위치로 이동한 라우팅 리소스가 있는 경우 이전 라우팅 리소스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT 허브를 새 지역의 새 허브로 복제하고 장치가 완성되었습니다. IoT Hub의 ID 레지스트리에 대해 대량 작업을 수행하는 자세한 내용은 [IoT Hub 장치 ID 가져오기 및 내보내기를 참조하세요.](iot-hub-bulk-identity-mgmt.md)

허브의 IoT 허브 및 개발에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [IoT 허브 개발자 가이드](iot-hub-devguide.md)

* [IoT 허브 라우팅 자습서](tutorial-routing.md)

* [IoT 허브 장치 관리 개요](iot-hub-device-management-overview.md)

* 샘플 응용 프로그램을 배포하려면 [.NET Core 응용 프로그램 배포를](https://docs.microsoft.com/dotnet/core/deploying/index)참조하십시오.
