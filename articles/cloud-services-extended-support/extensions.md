---
title: Cloud Services용 확장(추가 지원)
description: Cloud Services용 확장(추가 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 84b27bc4ae30e28a1d97fb8e9931602a1eb14504
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444789"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Cloud Services용 확장(추가 지원)

확장은 역할에 대한 배포 후 구성 및 Automation 작업을 제공하는 작은 애플리케이션입니다. 예를 들어 원격 데스크톱 확장을 사용하여 클라우드 서비스(추가 지원) 배포 중에 역할에서 원격 데스크톱 연결을 사용하도록 설정할 수 있습니다. 

## <a name="key-vault-extension"></a>Key Vault 확장

Key Vault VM 확장은 Azure Key Vault에 저장된 인증서의 자동 새로 고침을 제공합니다. 특히 확장은 키 자격 증명 모음에 저장된 인증서에 대해 관찰된 목록을 모니터링하고, 변경 내용이 검색되면 해당 인증서를 검색 및 설치합니다. 또한 클라우드 서비스에 대한 인증서의 지역 간/구독 간 참조를 허용합니다(추가 지원).

자세한 내용은 [클라우드 서비스용 키 자격 증명 모음 확장 구성(추가 지원)](./enable-key-vault-virtual-machine.md)을 참조하세요.

## <a name="remote-desktop-extension"></a>원격 데스크톱 확장

원격 데스크톱을 사용하면 Azure에서 실행 중인 역할의 데스크톱에 액세스할 수 있습니다. 원격 데스크톱 연결을 사용하여 애플리케이션 실행 중에 애플리케이션 문제를 진단하고 해결할 수 있습니다.

서비스 정의에 원격 데스크톱 모듈을 포함하거나 원격 데스크톱 확장을 통해 개발 중에 역할에서 원격 데스크톱을 사용하도록 설정할 수 있습니다. 

자세한 내용은 [Azure Portal에서 원격 데스크톱 구성](enable-rdp.md)을 참조하세요.

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure Diagnostics 확장

클라우드 서비스의 주요 성능 메트릭을 모니터링할 수 있습니다. 모든 클라우드 서비스 역할은 최소 데이터에 해당하는 CPU 사용량, 네트워크 사용량 및 디스크 사용률을 수집합니다. Microsoft.Azure.Diagnostics 확장이 적용된 클라우드 서비스 역할은 추가 데이터 요소를 수집할 수 있습니다. 

기본 모니터링을 사용할 경우 역할 인스턴스의 성능 카운터 데이터는 3분 간격으로 샘플링되고 수집됩니다. 이 기본 모니터링 데이터는 스토리지 계정에 저장되지 않으며, 추가 비용도 부과되지 않습니다. 

고급 모니터링을 사용할 경우 5분, 1시간 및 12시간 간격으로 추가 메트릭이 샘플링되고 수집됩니다. 집계된 데이터는 스토리지 계정에 테이블로 저장되고, 10일 후에 삭제됩니다. 사용되는 스토리지 계정은 역할별로 구성됩니다. 따라서 역할마다 다른 스토리지 계정을 사용할 수 있습니다. 

자세한 내용은 [Cloud Services(추가 지원)에서 Windows Azure Diagnostics 확장 적용](enable-wad.md)을 참조하세요.

## <a name="anti-malware-extension"></a>맬웨어 방지 확장
Azure 애플리케이션 또는 서비스는 PowerShell cmdlet을 사용하여 Azure Cloud Services에 대해 Microsoft 맬웨어 방지를 활성화하고 구성할 수 있습니다. Microsoft Antimalware는 Windows Server 2012 R2 및 이전 버전을 실행하는 Cloud Services 플랫폼에서 비활성화된 상태로 설치되고 Azure 애플리케이션에서 동작을 필요로 하여 활성화됩니다. Windows Server 2016 이상에서는 Windows Defender가 기본적으로 활성화되어 있으므로 이러한 cmdlet을 사용하여 맬웨어 방지를 구성할 수 있습니다.

자세한 내용은 [추가 지원(CS-ES)을 사용하여 Azure Cloud Service에 Microsoft Antimalware 추가](../security/fundamentals/antimalware-code-samples.md#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support)를 참조하세요.

Azure Antimalware에 대해 자세히 알아보려면 [여기](../security/fundamentals/antimalware.md)를 방문하세요.



## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토하고 관련 리소스를 만듭니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
