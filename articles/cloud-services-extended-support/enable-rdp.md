---
title: Cloud Services에서 원격 데스크톱 적용 (확장 지원)
description: Cloud Services에 대 한 원격 데스크톱 사용 (확장 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 94827916f28c9028d46bf7b5461a4fbd941b2a96
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773405"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Azure Cloud Services에 원격 데스크톱 확장 적용 (연장 지원)

Azure Portal은 원격 데스크톱 확장을 사용 하 여 응용 프로그램이 배포 된 후에도 원격 데스크톱을 사용 하도록 설정 합니다. 클라우드 서비스에 대 한 원격 데스크톱 설정을 사용 하면 원격 데스크톱을 사용 하도록 설정 하 고, 로컬 관리자 계정을 업데이트 하 고, 인증에 사용 되는 인증서를 선택 하 고, 해당 인증서의 만료 날짜를 설정할 수 있습니다. 

## <a name="apply-remote-desktop--extension"></a>원격 데스크톱 확장 적용
1. 원격 데스크톱을 사용 하도록 설정 하려는 클라우드 서비스로 이동 하 여 왼쪽 탐색 창에서 **"원격 데스크톱"** 을 선택 합니다.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Azure Portal에서 원격 데스크톱 옵션을 선택 하는 이미지가 표시 됩니다.":::

2. **추가** 를 선택합니다.
3. 원격 데스크톱을 사용 하도록 설정할 역할을 선택 합니다.
4. 사용자 이름, 암호, 만료 및 인증서 (필수 아님)에 대 한 필수 필드를 입력 합니다.
> 두고 원격 데스크톱에 대 한 암호는 8-123 자 사이 여야 하 고, 다음에서 3 개 이상의 암호 복잡성 요구 사항을 충족 해야 합니다. 1) 3)에 대문자 3이 포함 되어 있고, 숫자 숫자 4를 포함) 제어 문자는 허용 되지 않습니다.

   :::image type="content" source="media/remote-desktop-2.png" alt-text="원격 데스크톱에 연결 하는 데 필요한 정보를 입력 하는 이미지가 표시 됩니다.":::

5. 작업을 마쳤으면 **저장** 을 선택합니다. 역할 인스턴스가 연결을 수신할 준비가 되기까지 몇 분 정도 걸립니다.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>원격 데스크톱을 사용 하 여 역할 인스턴스에 연결
역할에 대해 원격 데스크톱이 사용 하도록 설정 되 면 Azure Portal에서 직접 연결을 시작할 수 있습니다.

1. **역할 및 인스턴스** 를 클릭 하 여 인스턴스 설정을 엽니다.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="이미지 구성 블레이드에서 역할 및 인스턴스 옵션을 선택 하는 방법을 보여 줍니다.":::

2. 구성 된 원격 데스크톱이 있는 역할 인스턴스를 선택 합니다.
3. **연결** 을 클릭 하 여 원격 데스크톱 연결 파일을 다운로드 합니다.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="이미지는 Azure Portal에서 작업자 역할 인스턴스를 선택 하는 방법을 보여 줍니다.":::
    
4. 파일을 열어 역할 인스턴스에 연결 합니다.


## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
