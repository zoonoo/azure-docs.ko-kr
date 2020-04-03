---
title: Azure 요새 문제 해결 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 요새 문제를 해결하는 방법을 알아봅니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619178"
---
# <a name="troubleshoot-azure-bastion"></a>Azure Bastion 문제 해결

이 문서에서는 Azure 요새 문제를 해결하는 방법을 보여 주며 있습니다.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>AzureBastionSubnet에서 NSG를 만들 수 없습니다.

**Q:** Azure Bastion 서브넷에서 NSG를 만들려고 할 때 *'네트워크 보안 <NSG name> 그룹에 Azure Bastion Subnet Azure BastionSubnet에 필요한 규칙이 없습니다.'* 오류가 발생합니다.

**A:** *AzureBastionSubnet에*NSG를 만들고 적용하는 경우 NSG에 다음 규칙을 추가했는지 확인합니다. 이러한 규칙을 추가하지 않으면 NSG 생성/업데이트가 실패합니다.

1. 제어 비행기 연결 – 게이트웨이Manager에서 443인바운드
2. 진단 로깅 및 기타 - 443에서 AzureCloud로 의 아웃바운드(이 서비스 태그 내의 지역 별 태그는 아직 지원되지 않습니다.)
3. 대상 VM – 3389 및 22에서 가상 네트워크로 아웃바운드

NSG 규칙의 예는 빠른 시작 [템플릿에서](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)참조할 수 있습니다.
자세한 내용은 [Azure 요새에 대한 NSG 지침을](bastion-nsg.md)참조하십시오.

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Azure 요새에서 내 SSH 키를 사용할 수 없음

**Q:** 내 SSH 키 파일을 찾아보려고 할 때 다음과 같은 오류가 발생합니다----- *---------- -----*.

**A:** Azure 요새는 이 시점에서 RSA SSH 키만 지원합니다. 대상 VM에 프로비전된 공개 키를 가지고 SSH의 RSA 개인 키인 키 파일을 찾아보세요. 

예를 들어 다음 명령을 사용하여 새 RSA SSH 키를 만들 수 있습니다.

**ssh-keygen -t rsa -b 4096 -C "email@domain.com**

출력:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Windows 도메인에 가입한 가상 컴퓨터에 로그인할 수 없음

**Q:** 도메인에 가입된 Windows 가상 컴퓨터에 연결할 수 없습니다.

**A:** Azure Bastion은 사용자 이름 암호 기반 도메인 로그인전용도메인 에 가입된 VM 로그인을 지원합니다. Azure 포털에서 도메인 자격 증명을 지정할 때username@domain *도메인\사용자 이름* 형식 대신 UPN () 형식을 사용하여 로그인합니다. 도메인 조인 또는 하이브리드 조인(도메인 조인및 Azure AD 조인) 가상 시스템에 대해 지원됩니다. Azure AD 조인 전용 가상 시스템에는 지원되지 않습니다.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>파일 전송 문제

**Q:** Azure 요새에서 파일 전송이 지원되고 있습니까?

**A:** 현재 파일 전송은 지원되지 않습니다. 우리는 지원을 추가하기 위해 노력하고 있습니다.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure 포털의 검은색 화면

**Q:** Azure 요새를 사용하여 연결을 시도하면 Azure 포털에서 검은색 화면이 표시됩니다.

**A:** 이 문제는 웹 브라우저와 Azure Bastion 간에 네트워크 연결 문제가 있거나(클라이언트 인터넷 방화벽이 WebSockets 트래픽 또는 이와 유사한 것을 차단할 수 있음) 또는 Azure Bastion과 대상 VM 사이에 발생하는 경우 발생합니다. 대부분의 경우 AzureBastionSubnet또는 가상 네트워크의 RDP/SSH 트래픽을 차단하는 대상 VM 서브넷에 적용된 NSG가 포함됩니다. 클라이언트 인터넷 방화벽에서 WebSockets 트래픽을 허용하고 대상 VM 서브넷에서 NSG를 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [바스티온 FAQ를](bastion-faq.md)참조하십시오.