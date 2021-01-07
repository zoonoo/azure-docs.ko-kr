---
title: Azure Portal에서 GPU가 있는 Azure Stack Edge Pro 디바이스에 연결, 구성, 활성화하는 자습서 | Microsoft Docs
description: 로컬 웹 UI를 사용하여 온보드 GPU가 있는 Azure Stack Edge 디바이스에 연결하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: f069f711ccf5672041ea6f3b15a52fa2b3ba2022
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900039"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-with-gpu"></a>자습서: GPU가 있는 Azure Stack Edge Pro에 연결

이 자습서에서는 로컬 웹 UI를 사용하여 온보드 GPU가 있는 Azure Stack Edge Pro 디바이스에 연결하는 방법을 설명합니다.

연결 프로세스를 완료하는 데 5분 정도가 소요됩니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 물리적 디바이스에 연결


## <a name="prerequisites"></a>사전 요구 사항

Azure Stack Edge Pro device with GPU 디바이스를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge Pro 설치](azure-stack-edge-gpu-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.


## <a name="connect-to-the-local-web-ui-setup"></a>로컬 웹 UI 설정에 연결

1. 고정 IP 주소가 192.168.100.5이고 서브넷이 255.255.255.0인 Azure Stack Edge Pro 디바이스에 연결하도록 컴퓨터의 이더넷 어댑터를 구성합니다.

2. 디바이스의 포트 1에 컴퓨터를 연결합니다. 컴퓨터를 디바이스에 직접 연결하는 경우(스위치 없이) 크로스오버 케이블 또는 USB 이더넷 어댑터를 사용합니다. 다음 일러스트레이션을 사용하여 디바이스의 포트 1을 찾습니다.

    ![케이블로 연결된 디바이스의 백플레인](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    디바이스의 백플레인은 사용자가 받은 정확한 모델에 따라 약간 다를 수 있습니다. 자세한 내용은 [디바이스 케이블 연결](azure-stack-edge-gpu-deploy-install.md#cable-the-device)을 참조하세요.


3. 브라우저 창을 열고 `https://192.168.100.10`에서 디바이스의 로컬 웹 UI에 액세스합니다.  
    이 작업은 디바이스를 켠 후 몇 분 정도 걸릴 수 있습니다.

    웹 사이트의 보안 인증서에 문제가 있음을 나타내는 오류 또는 경고가 표시됩니다. 
   
    ![웹 사이트 보안 인증서 오류 메시지](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. **이 웹 페이지에서 계속 진행**을 선택합니다.  
    이러한 단계는 사용 중인 브라우저에 따라 달라질 수 있습니다.

5. 디바이스의 웹 UI에 로그인합니다. 기본 암호는 *Password1*입니다. 
   
    ![Azure Stack Edge Pro 디바이스 로그인 페이지](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. 디바이스 관리자 암호를 변경하라는 메시지가 표시되면 변경합니다.  
    새 암호는 8-16자여야 합니다. 암호에 대문자, 소문자, 숫자, 특수 문자 중 3가지가 포함되어야 합니다.

이제 디바이스의 **개요** 페이지에 있습니다. 다음 단계는 디바이스의 네트워크 설정을 구성하는 것입니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * 사전 요구 사항
> * 물리적 디바이스에 연결


Azure Stack Edge Pro 디바이스에서 네트워크 설정을 구성하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [네트워크 구성](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
