---
title: Azure Stack 배포 네트워크 트래픽을 | Microsoft Docs
description: 이 문서에서는 Azure Stack 배포 네트워킹 프로세스에 대 한 예상 결과를 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 5f4f76f87718ddcc81f8fae8b043b73a4dbd6b0a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189279"
---
# <a name="about-deployment-network-traffic"></a>배포 네트워크 트래픽에 대 한
Azure Stack 하는 동안 네트워크 트래픽이 흐르는 방식을 이해 배포는 성공적인 배포를 위한 중요 합니다. 이 문서에서는 예상 되는 상황을 이해 하도록 배포 프로세스 중 네트워크 트래픽이 예상된을 통해 설명 합니다.

이 그림과 모든 구성 요소 및 연결 배포 프로세스와 관련:

![Azure Stack 배포 네트워크 토폴로지](media/deployment-networking/figure1.png)

> [!NOTE]
> 이 문서에서는 연결 된 배포를 위한 요구 사항 설명, 다른 배포 방법에 알아보려면 [Azure Stack 배포 연결 모델](azure-stack-connection-models.md)합니다.

### <a name="the-deployment-vm"></a>VM 배포
Azure Stack 솔루션은 Azure Stack 구성 요소를 호스트 하는 데 사용 되는 서버 및 하드웨어 수명 주기 호스트 (HLH)를 호출 하는 추가 서버 그룹을 포함 합니다. 배포 하 고 솔루션의 수명 주기를 관리 하는 데 사용 됩니다 하 고 배포 하는 동안 배포 VM (dvm이)를 호스트 하는이 서버.

## <a name="deployment-requirements"></a>배포 요구 사항
배포를 시작 하기 전에 배포를 성공적으로 완료 되도록 oem 유효성을 검사할 수 있는 최소 요구 사항이 있습니다. 다음은 이러한 요구 사항에서는 환경을 준비 하 고 유효성 검사에 성공 했는지 이해 합니다.

-   [Certificates](azure-stack-pki-certs.md)
-   [Azure 구독](https://azure.microsoft.com/free/?b=17.06)
-   인터넷 액세스
-   DNS
-   NTP

> [!NOTE]
> 이 문서는 마지막으로 세 가지 요구 사항에 중점을 둡니다. 처음 두 개에 대 한 자세한 내용은 위의 링크를 참조 하세요.

## <a name="deployment-network-traffic"></a>네트워크 트래픽 배포
dvm이 BMC 네트워크에서 IP를 사용 하 여 구성 되 고 인터넷에 대 한 네트워크 액세스가 필요 합니다. BMC 네트워크 구성 요소 중 일부만 필요한 외부 라우팅 또는 인터넷에 액세스할 수 있지만 일부 OEM 관련 구성 요소를이 네트워크에서 Ip를 활용 하 여이 필요할 수도 수 있습니다.

배포 하는 동안는 dvm이 Azure Active Directory (Azure AD) 구독에서 Azure 계정에 대해 인증 합니다. 이렇게 하려면는 dvm이 특정 포트 및 Url 목록에 대 한 인터넷 액세스가 필요 합니다. 전체 목록을 찾을 수 있습니다 합니다 [게시 끝점](azure-stack-integrate-endpoints.md) 설명서. dvm이 내부 구성 요소에서 외부 Url에 대 한 DNS 요청을 전달 하도록 DNS 서버를 사용 합니다. 내부 DNS 배포 하기 전에 oem 제공 하는 DNS 전달자 주소에 대 한 이러한 요청을 전달 합니다. NTP 서버에 대 한 마찬가지입니다, 그리고 신뢰할 수 있는 시간 서버는 모든 Azure Stack 구성 요소에 대 한 일관성 및 시간 동기화를 유지 관리 해야 합니다.

Dvm이 배포 하는 동안 필요한 인터넷 액세스는 아웃 바운드 전용, 인바운드 호출 하지 않고 배포 하는 동안 이루어집니다. 해당 IP를 사용 하 여 원본으로 하는 Azure Stack에 프록시 구성을 지원 하지 않습니다 염두에서에 둡니다. 따라서 필요한 경우 투명 프록시 또는 NAT 인터넷 액세스를 제공 해야 합니다. 배포 하는 동안 일부 내부 구성 요소는 공용 Vip를 사용 하 여 외부 네트워크를 통해 인터넷 액세스를 시작 합니다. 배포가 완료 되 면 Azure 및 Azure Stack 간의 모든 통신이 이루어집니다 외부 네트워크를 통해 공용 Vip를 사용 하 여.

Azure Stack 스위치에서 네트워크 구성 액세스 제어 목록 (Acl) 특정 네트워크 원본과 대상 간에 트래픽을 제한 하는 것을 포함 합니다. dvm이 무제한 액세스를 사용 하 여 유일한 구성 요소 HLH도 매우 제한 되어 있습니다. 손쉬운 관리 및 네트워크에서 액세스 하는 사용자 지정 옵션에 대 한 OEM을 요청할 수 있습니다. 이러한 Acl 때문에 반드시 배포 시 DNS 및 NTP 서버 주소를 변경 하지 마십시오. 이렇게 하면 모든 솔루션에 대 한 스위치를 다시 구성 해야 합니다.

배포가 완료 되 면 제공 된 DNS 및 NTP 서버 주소를 직접적으로 사용할 시스템의 구성 요소에서 계속 됩니다. 예를 들어, 배포가 완료 된 후 DNS 요청을 확인 하면 원본 dvm이 ip 주소로 외부 네트워크 범위에서 변경 됩니다.

배포가 완료 된 후 외부 네트워크를 사용 하 여 SDN 통해 시스템의 구성 요소에서 사용할 제공 된 DNS 및 NTP 서버 주소를 계속 합니다. 예를 들어, 배포가 완료 된 후 DNS 요청을 확인 하면 원본 dvm이 IP에서 공용 VIP에 변경 됩니다.

## <a name="next-steps"></a>다음 단계
[Azure 등록 유효성 검사](azure-stack-validate-registration.md)
