---
title: Azure Stack Edge Pro GPU 디바이스에서 로컬 Azure Resource Manager란?
description: Azure Stack Edge 디바이스에서 로컬 Azure Resource Manager란 무엇인지에 대한 개요를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 8d200195540f92c88ae3dc93737ea09a461e6c26
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113114562"
---
# <a name="what-is-local-azure-resource-manager-on-azure-stack-edge"></a>Azure Stack Edge에서 로컬 Azure Resource Manager란?

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. Azure Stack Edge 디바이스는 로컬 구독에서 VM 만들기 업데이트 및 삭제를 수행하는 동일한 Azure Resource Manager API를 지원합니다. 이 지원을 통해 클라우드와 일치하는 방식으로 디바이스를 관리할 수 있습니다. 

이 문서에서는 Azure Stack Edge 디바이스에서 로컬 API에 연결하는 데 사용할 수 있는 로컬 Azure Resource Manager의 개요를 제공합니다.

## <a name="about-local-azure-resource-manager"></a>로컬 Azure Resource Manager 정보

로컬 Azure Resource Manager는 Resource Manager 템플릿 사용을 통해 Azure Stack Edge 디바이스에 대한 모든 호출에 일관된 관리 계층을 제공합니다. 로컬 Azure Resource Manager의 이점은 다음 섹션에 설명되어 있습니다.

#### <a name="consistent-management-layer"></a>일관적인 관리 계층

로컬 Azure Resource Manager는 Azure Stack Edge 디바이스 API를 호출하고 VM 만들기, 업데이트 및 삭제와 같은 작업을 수행하는 일관된 관리 계층을 제공합니다. 

1. REST API 또는 SDK의 요청을 전송할 때 디바이스의 로컬 Azure Resource Manager는 요청을 수신합니다. 
1. 로컬 Azure Resource Manager는 STS(보안 토큰 서비스)를 사용하여 요청을 인증하고 권한을 부여합니다. STS는 보안 토큰의 생성, 유효성 검사, 갱신 및 취소를 담당합니다. STS는 액세스 토큰과 새로 고침 토큰 등 두 가지 유형의 보안 토큰을 만듭니다. 이러한 토큰은 로컬 Azure Resource Manager를 통해 디바이스에 액세스하는 클라이언트와 디바이스 간의 연속 통신에 사용됩니다.
1. 그런 다음, Resource Manager는 요청된 작업을 처리하는 리소스 공급자에게 요청을 전송합니다.   

    Azure Stack Edge를 사용하여 미리 등록된 리소스 공급자는 다음과 같습니다.

    - **컴퓨팅**: `Microsoft.Compute` 또는 컴퓨팅 리소스 공급자를 사용하여 Azure Stack Edge에 VM을 배포할 수 있습니다. 컴퓨팅 리소스 공급자에는 VM 및 VM 확장을 만들 수 있는 기능이 포함되어 있습니다. 

    - **네트워킹 리소스 공급자**: `Microsoft.Network` 또는 네트워킹 리소스 공급자를 사용하여 네트워크 인터페이스 및 가상 네트워크와 같은 리소스를 만들 수 있습니다.

    - **스토리지 리소스 공급자**: `Microsoft.Storage` 또는 스토리지 리소스 공급자는 Azure 일치 Blob Storage 서비스를 제공하고, 암호 및 인증서와 같은 비밀의 관리 및 감사를 제공하는 Key Vault 계정 관리를 제공합니다.  
    
    - **디스크 리소스 공급자**: `Microsoft.Disks` 또는 디스크 리소스 공급자를 사용하여 VM을 만드는 데 사용할 수 있는 관리 디스크를 만들 수 있습니다.

    리소스는 Azure Stack Edge를 통해 사용할 수 있는 관리 가능한 항목이며 리소스 공급자는 리소스를 제공하는 일을 담당합니다. 예를 들어 가상 머신, 스토리지 계정 및 가상 네트워크가 리소스의 예입니다. 그리고 컴퓨팅 리소스 공급자는 가상 머신 리소스를 제공합니다.    

모든 요청이 동일한 API를 통해 처리되므로 모든 여러 도구에서 일관적인 결과 및 기능을 볼 수 있습니다.

다음 이미지는 모든 API 요청을 처리하는 메커니즘과 해당 요청을 처리하기 위해 일관된 관리 계층을 제공하는 로컬 Azure Resource Manager의 역할을 보여 줍니다.

![Azure Resource Manager의 다이어그램](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


#### <a name="use-of-resource-manager-templates"></a>Resource Manager 템플릿 사용

Azure Resource Manager의 또 다른 주요 혜택은 Resource Manager 템플릿을 사용할 수 있다는 것입니다. 리소스를 일관되고 반복적으로 배포하는 데 사용할 수 있는 선언적 구문의 JSON(JavaScript Object Notation) 파일이 있습니다. 선언적 구문을 사용하면 항목을 만드는 프로그래밍 명령의 시퀀스를 작성하지 않고도 “만들려는 대상은 다음과 같습니다”라고 선언할 수 있습니다. 예를 들어 이러한 선언적 구문 템플릿을 사용하여 Azure Stack Edge 디바이스에 가상 머신을 배포할 수 있습니다. 자세한 내용은 [템플릿을 통해 Azure Stack Edge 디바이스에 가상 머신 배포](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)를 참조하세요.

## <a name="connect-to-the-local-azure-resource-manager"></a>로컬 Azure Resource Manager에 연결

Azure Stack Edge 디바이스에서 가상 머신 또는 공유나 스토리지 계정을 만들려면 해당하는 리소스를 만들어야 합니다. 예를 들어 가상 머신의 경우 네트워킹, 디스크 및 스토리지 리소스 공급자로부터 VM의 네트워크 인터페이스, OS 및 데이터 디스크와 같은 리소스가 필요합니다. 

리소스 공급자에서 리소스 생성을 요청하려면 먼저 로컬 Azure Resource Manager에 연결해야 합니다. 자세한 단계는 [Azure Stack Edge 디바이스에서 Azure Resource Manager에 연결](azure-stack-edge-gpu-connect-resource-manager.md)을 참조하세요.

Azure Resource Manager에 처음 연결할 때 암호를 다시 설정해야 합니다. 자세한 단계는 [Azure Resource Manager 암호 재설정](azure-stack-edge-gpu-set-azure-resource-manager-password.md)을 참조하세요.


## <a name="azure-resource-manager-endpoints"></a>Azure Resource Manager 엔드포인트

로컬 Azure Resource Manager 및 STS 서비스는 디바이스에서 실행되며 특정 엔드포인트에서 연결할 수 있습니다. 다음 표에서는 이러한 서비스에 의해 디바이스에 노출되는 다양한 엔드포인트, 지원되는 프로토콜 및 해당 엔드포인트에 액세스하는 포트를 요약하여 설명합니다. 

| # | 엔드포인트 | 지원되는 프로토콜 | 사용되는 포트 | 사용 대상 |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | 자동화를 위해 Azure Resource Manager에 연결 |
| 2. | 보안 토큰 서비스 | https | 443 | 액세스 및 새로 고침 토큰을 통해 인증 |


## <a name="next-steps"></a>다음 단계

[사용자 Azure Stack Edge Pro GPU 디바이스에서 로컬 Azure Resource Manager에 연결](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
