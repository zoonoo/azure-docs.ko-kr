---
title: "알려진 문제 및 문제 해결 가이드 | Microsoft Docs"
description: "알려진 문제의 목록 및 문제를 해결하는 데 도움이 되는 가이드"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 알려진 문제 및 문제 해결 가이드 
이 문서는 Azure Machine Learning Workbench 응용 프로그램 사용의 일부로 발생하는 오류 또는 실패를 찾고 수정하는 데 도움을 줍니다. 

> [!NOTE]
> 전자 메일 또는 포럼 게시물을 통해 지원 팀과 의사 소통을 할 때 빌드 번호가 있는 것이 도움이 됩니다. **도움말** 메뉴를 클릭하여 앱의 빌드 번호를 찾을 수 있습니다. 빌드 번호를 클릭하면 클립보드에 복사됩니다. 전자 메일 또는 지원 포럼에 붙여넣어 문제를 보고하는 데 도움이 될 수 있습니다.

![버전 번호 확인](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows 및 Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>CentOS 기반 Azure 데이터 과학 가상 컴퓨터 
* CentOS 기반 Azure DSVM(데이터 과학 가상 컴퓨터)에 작업 제출은 지원되지 않습니다. [Ubuntu 기반 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)을 대상으로 지정할 수 있습니다.

### <a name="docker-error"></a>Docker 오류 
* 로컬 Docker 컨테이너에 대해 실행할 때 다음과 같은 오류가 표시되면 Docker DNS 서버를 자동에서 고정 8.8.8.8로 변경하여 해결할 수 있습니다. 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![이미지](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Linux VM 암호 
* Ubuntu Linux VM의 Azure Portal에서 암호를 변경하는 경우 작업을 실행할 때 다음 오류가 발생할 수 있습니다.
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  해결 방법은 다음 콘텐츠로 _/etc/sudoers.d_에 파일(예: _myDockerUsers_)을 추가하는 것입니다.
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>SSH 키 
* SSH를 통해 원격 컴퓨터 또는 Spark 클러스터에 연결할 때 SSH 키는 지원되지 않습니다. 사용자 이름/암호 모드만 지원됩니다.

## <a name="windows-only"></a>Windows만 해당 
### <a name="sharing-c-drive-in-docker"></a>Docker에서 C 드라이브 공유 
* 파일 탐색기를 사용하여 C 드라이브에서 공유 확인
* 네트워크 어댑터 설정을 열고 vEthernet에 대해 "Microsoft 네트워크용 파일 및 프린터 공유" 제거/다시 설치
* Docker 설정을 열고 Docker 설정 내에서 C 드라이브 공유
* Windows 암호로 변경은 공유에 영향을 줍니다. 파일 탐색기를 열고, C 드라이브를 다시 공유하고, 새 암호를 입력합니다.
* Docker로 C 드라이브를 공유하려고 할 때 방화벽 문제가 발생할 수 있습니다. 이 [Stack Overflow 게시물](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051)이 도움이 될 수 있습니다.
* 도메인 자격 증명을 사용하여 C 드라이브를 공유하는 경우 도메인 컨트롤러에 연결할 수 없는 네트워크에서 공유가 작동을 중지할 수 있습니다(예: 홈 네트워크, 공용 wifi 등). 자세한 내용은 [이 게시물](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/)을 참조하세요.



## <a name="mac-only"></a>Mac만 해당 
* 텍스트 클러스터링 변환은 Mac에서 지원되지 않습니다.
* RevoScalePy 라이브러리는 Mac에서 지원되지 않습니다.

## <a name="azure-machine-learning-service-limits"></a>Azure Machine Learning 서비스 제한

- 최대 허용 프로젝트 폴더 크기: 25MB
    >[!Note]
    >이 제한은 `.git`, `docs` 및 `outputs` 폴더에 적용되지 않습니다. 이러한 폴더 이름은 대/소문자를 구분합니다.

- 최대 허용 실험 실행 시간: 7일
- 실행 후 `outputs` 폴더에서 추적된 파일의 최대 크기: 512MB 

>[!NOTE]
>큰 파일을 사용하는 경우 [변경 내용 유지 및 큰 파일 처리](how-to-read-write-files.md)를 참조하세요.

## <a name="other-issues"></a>기타 문제
문서화되지 않은 다른 문제를 알고 있다면 _웃는 얼굴/찡그린 얼굴 보내기_를 통해 사용자 의견을 보내 주세요. 




