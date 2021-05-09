---
title: 포함 파일
description: 포함 파일
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016388"
---
## <a name="overview-of-ssh-and-keys"></a>SSH 및 키에 대한 개요

[SSH](https://www.ssh.com/ssh/)는 안전하지 않은 연결을 통해 보안 로그인을 제공하는 암호화된 연결 프로토콜입니다. SSH는 Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. SSH는 암호화된 연결을 제공하지만 SSH 연결에 암호를 사용하면 VM이 여전히 무차별 암호 대입 공격(brute force attack)에 취약해집니다. *SSH 키* 라고도 하는 공개-프라이빗 키 쌍을 사용하여 SSH를 통해 VM에 연결하는 것이 좋습니다. 

- *공개 키* 는 Linux VM에 배치됩니다.

- *프라이빗 키* 는 로컬 시스템에 남아 있습니다. 이 프라이빗 키는 보호해야 하는 한편, 공유하면 안됩니다.

SSH 클라이언트를 사용하여 공개 키가 있는 Linux VM에 연결하는 경우 원격 VM이 클라이언트를 테스트하여 올바른 프라이빗 키가 있는지 확인합니다. 클라이언트에 프라이빗 키가 있으면 VM에 액세스할 수 있습니다. 

조직의 보안 정책에 따라 여러 Azure VM 및 서비스에 액세스하는 단일퍼블릭-프라이빗 키 쌍을 다시 사용할 수 있습니다. 각 VM 또는 서비스에 액세스하기 위해 별도의 키 쌍이 반드시 필요한 것은 아닙니다. 

공개 키는 모두와 공유할 수 있지만, 사용자(또는 로컬 보안 인프라)만 프라이빗 키에 액세스할 수 있어야 합니다.