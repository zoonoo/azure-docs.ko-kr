---
title: Azure VM에서 Service Fabric 클러스터에 대한 인프라 만들기 자습서 - Azure Service Fabric | Microsoft Docs
description: 이 자습서에서는 Service Fabric 클러스터를 실행하는 Azure VM 인프라를 설정하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: jpconnock
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: jeconnoc
ms.custom: mvc
ms.openlocfilehash: b24b4d95827dbd398c0eba43dcbad9fbfeb51469
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166268"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>자습서: Azure VM 인프라를 만들어 Service Fabric 클러스터 호스팅하기

Service Fabric 독립 실행형 클러스터는 사용자 자신의 환경을 선택하고 Service Fabric이 수행하는 "모든 OS 및 모든 클라우드" 접근 방법의 일부로서 클러스터를 만드는 옵션을 제공합니다. 이 자습서 시리즈에서는 Azure VM에서 호스팅되는 독립 실행형 클러스터를 만들고 여기에 애플리케이션을 설치합니다.

이 자습서는 시리즈의 1부입니다. 이 문서에서는 Service Fabric의 독립 실행형 클러스터를 호스팅하는 데 필요한 Azure VM 리소스를 생성합니다. 후속 문서에서는 Service Fabric 독립 실행형 도무 모음을 설치하고 클러스터에 샘플 애플리케이션을 설치하고 마지막으로 클러스터를 정리해야 합니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure VM 인스턴스 세트 만들기
> * 보안 그룹 수정
> * 인스턴스 중 하나에 로그인
> * Service Fabric에 대한 인스턴스 준비

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure 구독이 필요합니다.  계정이 아직 없는 경우 [Azure Portal](https://portal.azure.com)로 이동해 계정을 만듭니다.

## <a name="create-azure-virtual-machine-instances"></a>Azure Virtual Machine 인스턴스 만들기

1. Azure Portal에 로그인하고 **가상 머신**(가상 머신(클래식) 아님)을 선택합니다.

   ![Azure Portal VM][az-console]

2. **추가** 단추를 선택합니다. 그러면 **가상 머신 만들기** 양식이 열립니다.

3. **기본** 탭에서 원하는 구독 및 리소스 그룹을 선택합니다(새 리소스 그룹을 사용할 것을 권장).

4. **이미지** 유형을 **Windows Server 2016 Datacenter**로 변경합니다. 
 
5. 인스턴스 **크기**를 **표준 DS2 v2**로 변경합니다. 관리자 **사용자 이름**과 **암호**를 설정하고 메모해 둡니다.

6. **인바운드 포트 규칙**은 일단 차단된 상태로 둡니다. 이는 다음 섹션에서 구성합니다.

7. **네트워킹** 탭에서 새 **Virtual Network**를 만들고 해당 이름을 메모해 둡니다.

8. 다음으로, **NIC 네트워크 보안 그룹**을 **고급**으로 설정합니다. 새 보안 그룹을 만들고 이름을 메모해 둔 후 모든 소스의 TCP 트래픽을 허용하는 다음 규칙을 만듭니다.

   ![sf-inbound][sf-inbound]

   * 포트 `3389` - RDP 및 ICMP용(기본 연결)
   * 포트 `19000-19003` - Service Fabric용
   * 포트 `19080-19081` - Service Fabric용
   * 포트 `8080` - 웹 브라우저 요청용

   > [!TIP]
   > Service Fabric에서 가상 머신을 함께 연결하려면 인프라를 호스팅하는 VM에는 동일한 자격 증명이 있어야 합니다.  일관된 자격 증명을 가져오려면 동일한 도메인에 모두 조인하거나 각 VM에서 동일한 관리자 암호를 설정하는 등의 두 가지 일반적인 방법이 있습니다. 하지만 Azure는 동일한 **가상 네트워크**의 모든 가상 머신이 손쉽게 연결할 수 있게 지원하므로 모든 인스턴스가 동일한 네트워크에 있어야 합니다.

9. 다른 규칙을 추가합니다. 소스를 **서비스 태그**로 설정하고 소스 서비스 태그를 **VirtualNetwork**로 설정합니다. Service Fabric이 클러스터 내에서 통신하려면 다음 포트가 열려 있어야 합니다. 135,137-139,445,20001-20031,20606-20861.

   ![vnet-inbound][vnet-inbound]

10. 옵션의 나머지 부분은 기본 상태로 적용됩니다. 원한다면 검토한 후 가상 머신을 시작하세요.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Service Fabric 클러스터의 추가 인스턴스 만들기

2개 이상의 **Virtual Machines**를 시작하고, 이전 섹션에서 간략히 설명한 것과 동일한 설정을 유지하도록 합니다. 특히 동일한 관리자 사용자 이름 및 암호를 유지합니다. **Virtual Network** 및 **NIC 네트워크 보안 그룹**을 다시 만들면 안 됩니다. 드롭다운 메뉴에서 이미 생성된 항목을 선택하세요. 각 인스턴스가 배포될 때까지 몇 분 정도 걸릴 수 있습니다.

## <a name="connect-to-your-instances"></a>인스턴스에 연결

1. **Virtual Machine** 섹션에서 인스턴스 중 하나를 선택합니다.

2. **개요** 탭에서 *개인* IP 주소를 기록해 둡니다. 그런 다음, **연결**을 클릭합니다.

3. **RDP** 탭에서 이전에 명시적으로 열었던 포트 3389와 공용 IP 주소를 사용 중임을 확인합니다. RDP 파일을 다운로드합니다.
 
4. RDP 파일을 열고 메시지가 표시되면 및 VM 설정에서 지정한 사용자 이름 및 암호를 입력합니다.

5. 인스턴스에 연결되면 원격 레지스트리가 실행 중인지 유효성을 검사하고, SMB를 사용하고, SMB 및 원격 레지스트리에 대한 필수 포트를 열어 두어야 합니다.

   SMB를 사용하려면 다음 PowerShell 명령을 사용합니다.

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. 방화벽에서 포트를 열려면 여기에 PowerShell 명령이 있습니다.

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. 다른 인스턴스에 이 프로세스를 반복하고 개인 IP 주소도 메모해 둡니다.

## <a name="verify-your-settings"></a>설정 확인

1. 기본 연결의 유효성을 검사하려면 RDP를 사용하여 VM 중 하나에 연결합니다.

2. 해당 VM 내에서 **명령 프롬프트**를 연 후 ping 명령을 사용하여 한 VM에서 다른 VM으로 연결하고, 아래의 IP 주소를 이전에 메모해 둔 개인 IP 주소(이미 연결한 VM의 IP 아님) 중 하나로 바꿉니다.

   ```
   ping 172.31.20.163
   ```

   출력이 `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`과 같은 경우 인스턴스 간의 연결이 4차례 반복됩니다.

3. 다음 명령으로 SMB 공유가 작동하는지 유효성을 검사합니다.

   ```
   net use * \\172.31.20.163\c$
   ```

   출력으로 `Drive Z: is now connected to \\172.31.20.163\c$.`을 반환해야 합니다.


   이제 Service Fabric에 사용할 인스턴스가 올바르게 준비되었습니다.

## <a name="next-steps"></a>다음 단계

시리즈의 1부에서는 Azure VM 인스턴스 3개를 시작하고 Service Fabric 설치를 위해 이를 구성하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure VM 인스턴스 세트 만들기
> * 보안 그룹 수정
> * 인스턴스 중 하나에 로그인
> * Service Fabric에 대한 인스턴스 준비

클러스터에서 Service Fabric을 구성하려면 시리즈의 2부로 이동합니다.

> [!div class="nextstepaction"]
> [Service Fabric 설치](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
