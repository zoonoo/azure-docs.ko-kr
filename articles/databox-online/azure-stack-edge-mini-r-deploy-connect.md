---
title: Azure Portal에서 Azure Stack Edge Mini R에 연결하기 위한 자습서
description: 로컬 웹 UI를 사용하여 Azure Stack Edge Mini R 디바이스에 연결하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464995"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>자습서: Azure Stack Edge Mini R에 연결

이 자습서에서는 로컬 웹 UI를 사용하여 Azure Stack Edge Mini R 디바이스에 연결하는 방법을 설명합니다.

연결 프로세스를 완료하는 데 5분 정도가 소요됩니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 물리적 디바이스에 연결



## <a name="prerequisites"></a>필수 구성 요소

Azure Stack Edge 디바이스를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge 설치](azure-stack-edge-mini-r-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.


## <a name="connect-to-the-local-web-ui-setup"></a>로컬 웹 UI 설정에 연결

1. 고정 IP 주소가 192.168.100.5이고 서브넷이 255.255.255.0인 Azure Stack Edge Pro 디바이스에 연결하도록 컴퓨터의 이더넷 어댑터를 구성합니다.

2. 디바이스의 포트 1에 컴퓨터를 연결합니다. 컴퓨터를 디바이스에 직접 연결하는 경우(스위치 없이) 크로스오버 케이블 또는 USB 이더넷 어댑터를 사용합니다. 다음 일러스트레이션을 사용하여 디바이스의 포트 1을 찾습니다.

    ![Wi-Fi용 케이블 연결](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * 사전 요구 사항
> * 물리적 디바이스에 연결


디바이스에서 네트워크 설정을 구성하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [네트워크 구성](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
