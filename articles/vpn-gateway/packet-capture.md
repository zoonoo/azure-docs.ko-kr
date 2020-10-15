---
title: 'Azure VPN Gateway: 패킷 캡처 구성'
description: VPN gateway에서 사용 하 여 문제의 원인을 좁힐 수 있는 패킷 캡처 기능에 대해 알아봅니다.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: radwiv
ms.openlocfilehash: 3be01f6d8e1fb1f6ba541f8d1cb0c92d2a43b0da
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073107"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>VPN gateway에 대 한 패킷 캡처 구성

연결 및 성능 관련 문제는 종종 복잡 합니다. 문제의 원인을 좁히기 위해 상당한 시간과 노력이 소요 될 수 있습니다. 패킷 캡처를 통해 네트워크의 특정 부분에 대 한 문제 범위를 좁힐 수 있습니다. 이를 통해 문제가 네트워크의 고객 측, 네트워크의 Azure 측 또는 그 사이에 있는지 여부를 확인할 수 있습니다. 문제의 범위를 좁히는 후에는 디버그 하 고 수정 작업을 수행 하는 것이 더 효율적입니다.

몇 가지 일반적인 패킷 캡처 도구가 있습니다. 이러한 도구를 사용 하 여 관련 패킷 캡처를 가져오는 것은 특히 대용량 트래픽 시나리오에서 번거로울 수 있습니다. Azure VPN Gateway 패킷 캡처에서 제공 하는 필터링 기능은 중요 한 차이점입니다. 일반적으로 사용 가능한 패킷 캡처 도구와 함께 VPN Gateway 패킷 캡처를 사용할 수 있습니다.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN Gateway 패킷 캡처 필터링 기능

사용자의 요구에 따라 게이트웨이 또는 특정 연결에서 VPN Gateway 패킷 캡처를 실행할 수 있습니다. 동시에 여러 터널에서 패킷 캡처를 실행할 수도 있습니다. VPN 게이트웨이에서 필터링과 함께 단방향 또는 양방향 트래픽, IKE 및 ESP 트래픽, 내부 패킷을 캡처할 수 있습니다.

대량 트래픽에서 문제를 격리 하는 경우 5 개 튜플 필터 (원본 서브넷, 대상 서브넷, 원본 포트, 대상 포트, 프로토콜) 및 TCP 플래그 (SYN, ACK, FIN, URG, PSH, RST)를 사용 하는 것이 좋습니다.

다음 JSON 및 JSON 스키마 예제에서는 각 속성에 대 한 설명을 제공 합니다. 패킷 캡처를 실행할 때 염두에 두어야 할 몇 가지 제한 사항은 다음과 같습니다.
- 여기에 표시 된 스키마에서 필터는 배열 이지만 현재 한 번에 하나의 필터만 사용할 수 있습니다.
- 여러 게이트웨이 차원의 패킷 캡처를 동시에 실행할 수 없습니다.
- 단일 연결에서 동시에 여러 패킷 캡처를 실행할 수 없습니다. 서로 다른 연결에서 여러 패킷 캡처를 동시에 실행할 수 있습니다.
- 게이트웨이 당 최대 5 개의 패킷 캡처를 병렬로 실행할 수 있습니다. 이러한 패킷 캡처는 게이트웨이 차원의 패킷 캡처 및 연결당 패킷 캡처의 조합일 수 있습니다.

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

## <a name="set-up-packet-capture-by-using-powershell"></a>PowerShell을 사용 하 여 패킷 캡처 설정

다음 예제에서는 패킷 캡처를 시작 및 중지 하는 PowerShell 명령을 보여 줍니다. 매개 변수 옵션에 대 한 자세한 내용은 [AzVirtualnetworkGatewayPacketCapture](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)를 참조 하세요.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN gateway에 대 한 패킷 캡처 시작

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

선택적 매개 변수를 사용 하 여 필터를 적용할 수 있습니다 `-FilterData` .

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN gateway에 대 한 패킷 캡처 중지

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN gateway 연결에 대 한 패킷 캡처 시작

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

선택적 매개 변수를 사용 하 여 필터를 적용할 수 있습니다 `-FilterData` .

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN gateway 연결에서 패킷 캡처 중지

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>주요 고려 사항

- 패킷 캡처 실행은 성능에 영향을 줄 수 있습니다. 필요 하지 않은 경우 패킷 캡처를 중지 해야 합니다.
- 제안 된 최소 패킷 캡처 기간은 600 초입니다. 경로의 여러 구성 요소 간 동기화 문제로 인해 짧은 패킷 캡처가 전체 데이터를 제공 하지 않을 수 있습니다.
- 패킷 캡처 데이터 파일은 PCAP 형식으로 생성 됩니다. Wireshark 또는 기타 일반적으로 사용할 수 있는 응용 프로그램을 사용 하 여 PCAP 파일을 엽니다.
- 패킷 캡처는 정책 기반 게이트웨이에서 지원 되지 않습니다.
- `SASurl`매개 변수가 올바르게 구성 되지 않은 경우 추적에 저장소 오류가 발생 했을 수 있습니다. 매개 변수를 올바르게 생성 하는 방법에 대 한 예제는 `SASurl` [AzVirtualNetworkGatewayPacketCapture](https://docs.microsoft.com/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture)를 참조 하세요.



## <a name="next-steps"></a>다음 단계

VPN Gateway에 대 한 자세한 내용은 [VPN Gateway 무엇입니까?](vpn-gateway-about-vpngateways.md)를 참조 하세요.
