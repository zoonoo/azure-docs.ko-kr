
## <a name="what-is"> </a>Blob 저장소 정의

Azure Blob 저장소는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다량의 구조화되지 않은 데이터를 저장하기 위한 서비스입니다. 단일 Blob의 크기는 수백 GB일 수 있습니다. 2012년 6월 8일 이후에 만들어진 경우 단일 저장소 계정에 최대 200TB의 Blob이 포함될 수 있고, 해당 날짜 전에 만들어진 저장소 계정에는 최대 100TB의 Blob이 포함될 수 있습니다. 저장소 계정 용량에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표][1](영문)를 참조하십시오.

Blob 저장소의 일반적인 사용은 다음과 같습니다.

* 브라우저에 직접 이미지 또는 문서 제공
* 분산 액세스를 위해 파일 저장
* 동영상 및 오디오 스트리밍
* 보안 백업 및 재해 복구 수행
* 온-프레미스 또는 Azure 호스티드 서비스에서 분석하기 위해 데이터 저장

Blob 저장소를 사용하여 세상에 공개적으로 또는 내부 응용 프로그램 저장소에 비공개적으로 데이터를 표시할 수 있습니다.

## <a name="concepts"> </a>개념

Blob 서비스에는 다음 구성 요소가 포함됩니다.

![Blob1](./media/howto-blob-storage/blob1.jpg)

* **저장소 계정:** Azure 저장소에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 저장소 계정 용량에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표][1](영문)를 참조하십시오.

* **컨테이너:** 컨테이너는 Blob 집합 그룹화를 제공합니다. 모든 Blob은 컨테이너에 있어야 합니다. 한 계정에 포함될 수 있는 컨테이너 수에는 제한이 없습니다. 한 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다.

* **Blob:** 임의 형식 및 크기의 파일입니다. Azure 저장소에 저장할 수 있는 Blob 유형에는 블록 Blob과 페이지 Blob의 두 가지가 있습니다. 대부분의 파일은 블록 Blob입니다. 단일 블록 Blob의 크기는 최대 200GB일 수 있습니다. 이 자습서에서는 블록 Blob을 사용합니다. 다른 Blob 유형인 페이지 Blob의 크기는 최대 1TB일 수 있으며, 파일의 바이트 범위가 자주 수정되는 경우 더 효율적입니다. Blob에 대한 자세한 내용은 [블록 Blob 및 페이지 Blob 이해][2]를 참조하십시오.

* **URL 형식:** 다음 URL 형식을 사용하여 Blob에 주소를 지정할 수 있습니다.   
   http://`<storage account>`.blob.core.windows.net/`<container>`/`<blob>`
  
  다음 예제 URL을 사용하여 위 다이어그램에 있는 Blob 중 하나의 주소를 지정할 수 있습니다.  
   `http://sally.blob.core.windows.net/movies/MOV1.AVI`



[1]: http://msdn.microsoft.com/ko-kr/library/dn249410.aspx
[2]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee691964.aspx