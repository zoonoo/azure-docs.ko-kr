---
title: VPN Gateway용 패킷 캡처 구성
titleSuffix: Azure VPN Gateway
description: VPN Gateway에서 사용하여 문제의 원인을 좁힐 수 있는 패킷 캡처 기능에 대해 알아 봅니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alzam
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7a781a497ce6b3e4511eff3ba5818bab1a1a2b8
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110681628"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>VPN Gateway용 패킷 캡처 구성

연결 및 성능과 관련된 문제는 복잡한 경우가 많습니다. 문제의 구체적인 원인을 파악하는 데 상당한 시간과 노력이 소요될 수 있습니다. 패킷 캡처를 수행하면 네트워크의 특정 부분으로 문제의 범위를 좁힐 수 있습니다. 이를 통해 문제가 네트워크의 고객 측에 있는지, 네트워크의 Azure 측에 있는지, 그 사이에 있는지 파악할 수 있습니다. 문제의 범위를 좁힌 후에 디버그와 수정 작업을 수행하는 것이 더 효율적입니다.

몇 가지 일반적인 패킷 캡처 도구가 있습니다. 이러한 도구로 관련 패킷 캡처를 가져오면 번거로울 수 있는데, 특히 대용량 트래픽 상황에서는 더욱 그러합니다. Azure VPN Gateway 패킷 캡처에서 제공하는 필터링 기능에는 큰 차이점이 있습니다. 일반적으로 사용 가능한 패킷 캡처 도구와 함께 VPN Gateway 패킷 캡처를 사용할 수 있습니다.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN Gateway 패킷 캡처 필터링 기능

사용자의 요구에 따라 게이트웨이 또는 특정 연결에서 VPN Gateway 패킷 캡처를 실행할 수 있습니다. 동시에 여러 터널에서 패킷 캡처를 실행할 수도 있습니다. VPN Gateway에서 필터링과 함께 단방향 또는 양방향 트래픽, IKE 및 ESP 트래픽, 내부 패킷을 캡처할 수 있습니다.

대량 트래픽에서 문제를 격리하는 경우 5개 튜플 필터(원본 서브넷, 대상 서브넷, 원본 포트, 대상 포트, 프로토콜) 및 TCP 플래그(SYN, ACK, FIN, URG, PSH, RST)를 사용하면 편리합니다.

다음 JSON 및 JSON 스키마 예제에서는 각 속성에 대한 설명을 제공합니다. 패킷 캡처를 실행할 때 염두에 두어야 할 몇 가지 제한 사항은 다음과 같습니다.

- 여기에 표시된 스키마에서 필터는 배열되어 있지만 현재는 한 번에 하나의 필터만 사용할 수 있습니다.
- 여러 게이트웨이 차원의 패킷 캡처를 동시에 실행할 수 없습니다.
- 단일 연결에서 동시에 여러 패킷 캡처를 실행할 수 없습니다. 서로 다른 연결에서 여러 패킷 캡처를 동시에 실행할 수 있습니다.
- 게이트웨이당 최대 5개의 패킷 캡처를 병렬로 실행할 수 있습니다. 이러한 패킷 캡처는 게이트웨이 차원의 패킷 캡처 및 연결당 패킷 캡처의 조합일 수 있습니다.
- MaxPacketBufferSize의 단위는 바이트이고 MaxFileSize는 메가바이트입니다.

### <a name="example-json"></a>예제 JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON 스키마
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="packet-capture---portal"></a>패킷 캡처 - 포털

Azure Portal에서 패킷 캡처를 설정할 수 있습니다.

:::image type="content" source="./media/packet-capture/portal.jpg" alt-text="포털의 패킷 캡처 스크린샷" lightbox="./media/packet-capture/portal.jpg":::

## <a name="packet-capture---powershell"></a>패킷 캡처 - PowerShell

다음 예제에서는 패킷 캡처를 시작하고 중지하는 PowerShell 명령을 보여줍니다. 매개 변수 옵션에 대한 자세한 정보는 [Start-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)를 참조하세요.

>
### <a name="prerequisite"></a>필수 조건

* 패킷 캡처 데이터는 구독의 스토리지 계정에 로그인해야 합니다. 다음 항목 [스토리지 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.
* 패킷 캡처를 중지하려면 스토리지 계정에 대한 `SASUrl`를 생성해야 합니다. 다음 항목 [사용자 위임 SAS 만들기](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md)를 참조하세요.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN Gateway에 대한 패킷 캡처 시작

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

선택적 매개 변수 `-FilterData`를 사용하여 필터를 적용할 수 있습니다.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN Gateway에 대한 패킷 캡처 중지

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

매개 변수 옵션에 대한 자세한 정보는 [Stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture)를 참조하세요.

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN Gateway 연결에 대한 패킷 캡처 시작

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

선택적 매개 변수 `-FilterData`를 사용하여 필터를 적용할 수 있습니다.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN Gateway 연결에서 패킷 캡처 중지

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

매개 변수 옵션에 대한 자세한 정보는 [Stop-AzVirtualNetworkGatewayConnectionPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewayconnectionpacketcapture)를 참조하세요.

## <a name="key-considerations"></a>주요 고려 사항

- 패킷 캡처를 실행하면 성능에 영향을 줄 수 있습니다. 패킷 캡처가 필요하지 않은 경우 이를 중지해야 합니다.
- 제안된 최소 패킷 캡처 기간은 600초입니다. 경로에 있는 여러 구성 요소 간 동기화 문제로 인해 짧은 시간 동안 패킷 캡처를 수행하면 전체 데이터가 제공되지 않을 수 있습니다.
- 패킷 캡처 데이터 파일은 PCAP 형식으로 생성됩니다. Wireshark 또는 일반적으로 사용 가능한 기타 애플리케이션을 사용하여 PCAP 파일을 엽니다.
- 패킷 캡처는 정책 기반 게이트웨이에서 지원되지 않습니다.
- 만약 `SASurl` 매개 변수가 올바르게 구성되지 않은 경우 스토리지 오류로 추적이 실패할 수 있습니다. `SASurl` 매개 변수를 올바르게 생성하는 방법에 대한 예제는 [AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture)를 참조하세요.



## <a name="next-steps"></a>다음 단계

VPN Gateway에 대한 자세한 정보는 [VPN Gateway란?](vpn-gateway-about-vpngateways.md)을 참조하세요.
