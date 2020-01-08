---
title: Linux 클러스터에서 암호화 인증서 설정
description: Linux 클러스터에서 암호화 인증서를 설정하고 비밀을 암호화하는 방법을 알아봅니다.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 350718e4ce890fcbfaa7f2b10cc4c47dfac4da90
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614709"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Linux 클러스터에서 암호화 인증서 설정 및 비밀 암호화
이 문서에서는 Linux 클러스터에서 암호화 인증서를 설정하고 이를 사용하여 비밀을 암호화하는 방법을 알아봅니다. Windows 클러스터의 경우 [windows 클러스터에서 암호화 인증서 설정 및 암호 암호화][secret-management-windows-specific-link]를 참조 하세요.

## <a name="obtain-a-data-encipherment-certificate"></a>데이터 암호화 인증서 가져오기
데이터 암호화 인증서는 서비스의 Servicemanifest.xml에 있는 서비스의 설정 .xml 및 [환경 변수][environment-variables-link] 에서 [매개 변수의][parameters-link] 암호화 및 암호 해독에 엄격 하 게 사용 됩니다. 인증 또는 암호화 텍스트의 서명에는 사용되지 않습니다. 인증서는 다음 요구 사항을 충족해야 합니다.

* 인증서에 프라이빗 키가 포함되어 있어야 합니다.
* 인증서 키 사용에는 데이터 암호화(10)가 포함되어야 하며, 서버 인증 또는 클라이언트 인증은 포함되면 안 됩니다.

  예를 들어, OpenSSL을 사용하여 필요한 인증서를 생성하려면 다음 명령을 사용할 수 있습니다.
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>클러스터에 인증서 설치
`/var/lib/sfcerts` 아래에 있는 클러스터의 각 노드에 인증서를 설치해야 합니다. 서비스가 실행 중인 사용자 계정(기본적으로 sfuser)에 설치된 인증서(즉, 현재 예제의 경우 `/var/lib/sfcerts/TestCert.pem`)에 대한 **읽기 권한이 있어야 합니다**.

## <a name="encrypt-secrets"></a>비밀 암호화
다음 코드 조각은 비밀을 암호화하는 데 사용할 수 있습니다. 이 코드 조각은 값만 암호화하며, 암호화 텍스트에 서명하지 **않습니다**. 비밀 값의 암호 텍스트를 생성하려면 클러스터에 설치된 것과 동일한 암호화 인증서를 **사용해야 합니다**.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
그 결과 encrypted.txt에 대한 기본 64로 인코딩된 문자열 출력에는 비밀 암호 텍스트와 암호화에 사용된 인증서에 대한 정보가 모두 포함되어 있습니다. OpenSSL을 사용하여 암호를 해독하여 유효성을 확인할 수 있습니다.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>다음 단계
[응용 프로그램에서 암호화 된 암호를 지정][secret-management-specify-encrypted-secrets-link] 하는 방법을 알아봅니다.

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
