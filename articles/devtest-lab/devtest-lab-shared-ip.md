---
title: "Azure DevTest Labs에서 공유 IP 주소 이해 | Microsoft Docs"
description: "Azure DevTest Labs에서 공유 IP 주소를 사용하여 랩 VM에 액세스하는 데 필요한 공용 IP 주소를 최소화하는 방법에 대해 알아봅니다."
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 905357b9e2262b86cde31874287cc0b89eef4815
ms.lasthandoff: 03/17/2017

---

# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공유 IP 주소 이해

Azure DevTest Labs에서 공유 IP 주소를 사용하여 개별 랩 VM에 액세스하는 데 필요한 공용 IP 주소 수를 최소화합니다.  이 문서에서는 공유 IP의 작동 방식과 관련 구성 옵션에 대해 설명합니다.

## <a name="shared-ip-setting"></a>공유 IP 설정

랩을 만들면 가상 네트워크의 서브넷에 상주합니다.  기본적으로 이 서브넷은 **Enable shared public IP**(공유 공용 IP 사용)을 *예*로 설정하면 생성됩니다.  이 구성은 전체 서브넷에 하나의 공용 IP 주소를 만듭니다.  가상 네트워크 및 서브넷 구성에 대한 자세한 내용은 [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)을 참조하세요.

![새 랩 서브넷](media/devtest-lab-shared-ip/lab-subnet.png)

이 랩에 생성된 모든 VM은 기본적으로 공유 IP를 사용합니다.  VM을 만들 때 **IP 주소 구성** 아래 **고급 설정** 블레이드에서 이 설정을 확인할 수 있습니다.

![새 VM](media/devtest-lab-shared-ip/new-vm.png)

공유 IP가 사용된 VM이 서브넷에 추가될 때마다 TCP 포트가 VM의 RDP 포트에 전달되는 공용 IP 주소에 할당됩니다.  

## <a name="using-the-shared-ip"></a>공유 IP 사용

IP 주소 또는 정규화된 도메인 이름, 콜론, 포트를 순서대로 사용하여 RDP 클라이언트에서 VM의 원격 데스크톱에 연결합니다.  예를 들어 아래 이미지에서 VM에 연결되는 RDP 주소는 `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`입니다.  또는 Azure Portal에서 **연결** 단추를 선택하여 미리 구성된 RDP 파일을 다운로드합니다.

![VM 예](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>다음 단계

* [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)
* [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)






