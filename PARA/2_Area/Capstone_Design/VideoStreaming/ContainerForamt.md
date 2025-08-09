# Video Container Format

## Container Format이란

서로 다른 데이터 구성요소들과 메타데이터들이 컴퓨터 파일 안에서 같이 있을 수 있게 하는 방법을 기술

![[640px-Informatik-Containerformate-Beispiele.svg.png]]





## **MP4** Container Format

MP4 컨테이너는 디지털 비디오, 디지털 오디오 데이터 뿐만 아니라 다양한 메타파일을 담는다

- 일반 파일 메타데이터
	- 파일의 종류, 호환성 정보
- 트랙 정보
	- 비디오, 오디오, 자막 트랙의 종류와 코덱 정보
- 미디어 상세 정보
	- 영상의 길이, 시간 단위, 비트레이트, 해상도 등
- 실제 미디어 데이터
	- 인코딩된 비디오 프레임이나 오디오 샘플 데이터

```
video.mp4
├───general file metadata
├───movie data
├───tracks
│   ├───video
│   │   ├───video metadata
│   │   └───video sample data
│   └───audio
│       ├───audio metadata
│       └───audio sample data
└───more metadata
```


### MP4 **Box** 구조

mp4 파일은 여러 박스가 계층적으로 구성된 **Tree** 구조

각 박스마다 4글자로 된 고유이름 (**FourCC**)을 가지고 있다.

- **ftyp (File Type Box)**
	- 파일 종류와 호환되는 브랜드를 정의
- **moov (Movie Box)** 
	- 미디어 트랙에 대한 모든 메타데이터
- **mdat (Movie Data Box)**
	- 실제 인코딩된 비디오, 오디오 데이터가 저장되는 곳
	- `moov` 정보를 바탕으로 플레이어가 `mdat`에서 데이터 읽어와 재생
- **trak (Trak Box)**
	- `moov` 박스 안에 포함, 개별 트랙에 대한 정보

```
video.mp4
├───ftyp -------------------> FileType Box
├───mdat -------------------> Movie Data Box
├───moov -------------------> Movie Boxes
│   ├───trak ---------------> Track Box
│   │   ├─── tkhd ----------> Track Header
│   │   └─── mdia ----------> Media Box
│   │        └─── ...
│   └───trak
│   │   ├─── tkhd ----------> Track Header
│   │   └─── mdia ----------> Media Box
│   │        └─── ...
└───udta -------------------> Userdata Box
```


### Box 구성

```
┌─────────────────────┐
|      Box Header     |
| Size (4) | Type (4) | Box Header = 8 Bytes
| --------------------|
|     Box Data (N)    | Box Data = N Bytes
└─────────────────────┘
           └─────────── Box Size = 8 + N bytes
```

- **Header**
	- size - 해당 박스 전체의 크기 (헤더 + 데이터)
	- type - 박스의 이름을 나타내는 4글자 문자열
- **Data**
	- 실제 메타 데이터나 미디어 데이터가 들어가는 부분