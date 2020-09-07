---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89080563"
---
1. 디바이스의 로컬 웹 UI로 이동하여 디바이스에 로그인합니다. 디바이스가 잠금 해제되었는지 확인합니다.

2. **네트워크 설정** 페이지로 이동합니다. 클라이언트에 연결하는 데 사용된 네트워크 인터페이스의 디바이스 IP 주소를 기록해 둡니다.

3. 원격 Windows 클라이언트를 사용하는 경우 관리자 권한으로 **메모장**을 시작한 다음, `C:\Windows\System32\Drivers\etc`에 있는 호스트 파일을 엽니다.

4. 다음 항목을 호스트 파일에 추가합니다. `<Device IP address> <Blob service endpoint>`

    Azure Portal에서 만든 Edge 스토리지 계정에서 Blob 서비스 엔드포인트를 가져왔습니다. Blob 서비스 엔드포인트의 접미사만 사용합니다.

    참조를 위해 다음 이미지를 사용합니다. `hosts` 파일을 저장합니다.

    ![Windows 클라이언트에서 호스트 파일 수정](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)