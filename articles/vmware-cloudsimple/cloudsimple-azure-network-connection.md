---
title: VMware 솔루션 CloudSimple-Azure 네트워크 연결
description: Azure virtual network CloudSimple 지역 네트워크에 연결 하는 방법에 대 한에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577002"
---
# <a name="azure-network-connection-overview"></a>Azure 네트워크 연결 개요

특정 지역의 CloudSimple 서비스를 만들면 해당:

* Azure ExpressRoute 회로 만들고 해당 지역에서 서비스에 연결 합니다.
* Azure 가상 네트워크 또는 Azure ExpressRoute를 사용 하 여 온-프레미스 네트워크에 CloudSimple 지역 네트워크 연결
* Azure 구독 또는 사설 클라우드 환경에서 온-프레미스 네트워크에서 실행 되는 액세스 서비스를 제공 합니다.

이 연결이 짧은 대기 시간으로 높은 대역폭으로 구성 합니다.

## <a name="benefits"></a>이점

Azure 네트워크 연결을 사용 하면 수 있습니다.

* 백업 대상으로 Azure를 사용 하 여 사설 클라우드에 가상 컴퓨터에 대 한 합니다.
* 사설 클라우드 vSAN 데이터 저장소를 암호화 하기 위해 Azure 구독에 KMS 서버를 배포 합니다.
* 여기서 응용 프로그램 하는 동안 공용 클라우드에서 실행 되는 응용 프로그램의 웹 계층 및 데이터베이스 계층을 실행할 사설 클라우드에서 하이브리드 응용 프로그램을 사용 합니다.

## <a name="expressroute-connection-to-on-premises-network"></a>온-프레미스 네트워크를 ExpressRoute 연결

기존 Azure ExpressRoute 회로 CloudSimple 지역을 연결할 수 있습니다. ExpressRoute 글로벌 환경 기능 두 회로 서로 연결 됩니다.  온-프레미스 CloudSimple ExpressRoute 회로와 연결 됩니다.

이 메서드는 두 환경 간의 연결을 설정 합니다.

* 보안
* 개인
* 높은 대역폭
* 짧은 대기 시간

온-프레미스 네트워크에 ExpressRoute 연결을 만들려면 [지원에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)합니다.

## <a name="next-steps"></a>다음 단계

* [가상 네트워크 연결 설정](https://docs.azure.cloudsimple.com/virtual-network-connection)