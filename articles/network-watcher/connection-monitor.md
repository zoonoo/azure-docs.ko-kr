---
title: "Azure Network Watcher로 네트워크 연결 모니터링 - Azure Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure Network Watcher를 통해 네트워크 연결을 모니터링하는 방법을 알아봅니다."
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Network Watcher로 네트워크 연결 모니터링

연결 모니터를 사용하여 Azure 가상 머신과 IP 주소 간의 네트워크 연결을 모니터링 하는 방법에 알아봅니다. 다른 Azure 리소스, 인터넷 또는 온-프레미스 리소스에 IP 주소를 할당할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 아티클의 단계를 완료하기 전에 다음 필수 구성 요소를 충족해야 합니다.

* 연결을 모니터링하려는 지역의 Network Watcher 인스턴스. 아직 없는 경우 [Azure Network Watcher 인스턴스 만들기](network-watcher-create.md)의 단계를 완료하여 만듭니다.
* 모니터링 할 가상 머신.
* 연결을 모니터링 할 가상 머신에 `AzureNetworkWatcherExtension`을 설치합니다. Windows 가상 머신에서 확장을 설치하려면 [Windows용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)을 방문하고, Linux 가상 머신에서 확장을 설치하려면 [Linux용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)을 방문하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인 

[Azure 포털](http://portal.azure.com)에 로그인합니다.

## <a name="create-a-connection-monitor"></a>연결 모니터 만들기

1. 포털의 왼쪽에서 **추가 서비스**를 선택합니다.
2. *Network Watcher*를 입력합니다. 검색 결과에 **Network Watcher**가 나타나면 이를 선택합니다.
3. **모니터링** 아래에서 **연결 모니터(미리보기)**를 선택합니다. 미리 보기 릴리스의 기능은 일반 릴리스의 기능과 동일한 수준의 안정성 또는 지역 가용성을 제공하지 않습니다.
4. **+추가**를 선택합니다.
5. 모니터링하려는 연결에 대한 적절한 정보를 입력하거나 선택한 다음, **추가**를 선택합니다. 이 예제에서는 *myVmSource* 및 *myVmDestination* 가상 머신 간의 연결이 포트 80을 통해 모니터링됩니다.
    
    |  설정                                 |  값               |
    |  -------------------------------------   |  ------------------- |
    |  Name                                    |  myConnectionMonitor |
    |  원본 가상 머신                  |  myVmSource          |
    |  원본 포트                             |                      |
    |  대상에서 가상 머신을 선택합니다.   |  myVmDestination     |
    |  대상 포트                        |  80                  |

6. 모니터링을 시작합니다. 연결 모니터는 60초마다 프로브합니다.
7. 연결 모니터에 평균 왕복 시간과 실패한 프로브 백분율이 나타납니다. 그리드 또는 그래프로 모니터 데이터를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 생성하여 가상 머신 경고로 패킷 캡처를 자동화하는 방법을 알아봅니다.

- [IP 흐름 확인 검사](network-watcher-check-ip-flow-verify-portal.md)를 방문하여 가상 머신에서 또는 가상 머신으로 특정 트래픽 전송이 허용되는지 확인합니다.