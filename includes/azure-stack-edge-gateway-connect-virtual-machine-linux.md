---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 05/13/2021
ms.author: alkohli
ms.openlocfilehash: 45a82d85510bd432efa93211ffc9dba8b911c557
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070750"
---
VM을 만드는 동안 전달된 개인 IP를 사용하여 VM에 연결합니다.

1. SSH 세션을 열어 IP 주소와 연결합니다.

   `ssh -l <username> <ip address>`

1. 프롬프트에서 VM을 만들 때 사용한 암호를 제공합니다.

   SSH 키를 제공해야 하는 경우 다음 명령을 사용합니다.

   `ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236`

   다음은 VM에 연결하는 경우의 출력 예입니다.

    ```powershell
    PS C:\07-30-2020\linux> ssh -l Administrator 10.126.68.186
    Administrator@10.126.68.186's password:
    Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 5.0.0-1027-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      System information as of Thu Jul 30 22:56:11 UTC 2020
    
      System load:  0.0               Processes:           105
      Usage of /:   5.6% of 28.90GB   Users logged in:     0
      Memory usage: 12%               IP address for eth0: 10.126.68.186
      Swap usage:   0%
    
     * Are you ready for Kubernetes 1.19? It's nearly here! Try RC3 with
       sudo snap install microk8s --channel=1.19/candidate --classic
    
       https://www.microk8s.io/ has docs and details.
    
    68 packages can be updated.
    0 updates are security updates.
    
    
    *** System restart required ***
    
    The programs included with the Ubuntu system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.
    
    Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
    applicable law.
    
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@mylinuxvm:
    ```
