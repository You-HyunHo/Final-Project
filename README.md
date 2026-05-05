# FunSpot

- 目的：旅行コース推薦およびコミュニティプラットフォームの開発
- 参加人数：6名
- 開発期間: 2024.11.20. ~ 2025.01.08
- 担当部分: ユーザーのカスタムコース掲示板における閲覧・登録・修正・削除機能および人気コースの取得機能

## 目次

- [技術スタック](#技術スタック)
- [DBモデリング](#DBモデリング)
- [機能画面](#機能画面)
- [シーケンスダイアグラム](#シーケンスダイアグラム)
- [主要機能](#主要機能)
- [トラブルシューティングおよび解決](#トラブルシューティングおよび解決)

## 技術スタック

### Frontend
- React
- JavaScript (ES6+)
- Tailwind CSS

### Backend
- Java 17
- Spring Boot
- Spring Security
- JPA
- WebSocket
- JWT

### Database
- MySQL 8.4

### API
- 公共データポータルAPI
- Naver Map API
- Kakao Map API

### Infrastructure / Tools
- AWS
- Gabia
- Figma
- Lombok

## DBモデリング
![image](https://github.com/user-attachments/assets/7dcccd75-465d-4aee-9d8e-afb1d26c183a)</br>

## 機能画面

<details>
<summary><b>기능 화면</b></summary>
<div markdown="1">

### 1. ユーザーカスタムコース投稿画面
![Image](https://github.com/user-attachments/assets/35a35b0c-9d40-4e72-9f57-3d40b042100d)</br>
![Image](https://github.com/user-attachments/assets/abca6661-0b5a-4ecf-b732-a5e7dcd22392)</br>

### 2. コース一覧画面
![Image](https://github.com/user-attachments/assets/b27048a6-0292-4d41-bf8d-0b5502e11adf)</br>


### 3. コース詳細画面
![Image](https://github.com/user-attachments/assets/61e2e7bc-f83e-4dea-be5a-8ff2410595a9)</br>


### 4. コース編集画面
![Image](https://github.com/user-attachments/assets/10ff1aae-25c2-440f-9754-2ae894476996)</br>
![Image](https://github.com/user-attachments/assets/49b011b9-4189-469b-9663-96526d666093)</br>

### 5. 人気コース一覧画面
![Image](https://github.com/user-attachments/assets/9642ceba-02e1-4918-b592-73de1bca6c4a)</br>

</div>
</details>

## シーケンスダイアグラム
-[Sequence Diagrams](./README_img/SEQUENCE.md)


## 主要機能

### 1. コース作成機能

ユーザーが選択した複数の場所を基に、順序付きのコースを作成する機能です。
各場所は `orderIndex` を用いて順序を保持し、意図したルート通りに保存されるよう設計しました。
また、コース（Custom）と場所（Place）の多対多関係を解決するために、中間テーブル（CustomPlace）を導入し、柔軟なデータ構造を実現しました。

### 2. 人気コース取得機能

ユーザーの「いいね（WishList）」データを基に、人気のコースを抽出する機能です。
まず、WishListテーブルから人気コースのIDを取得し、その後コース情報を再取得することで、効率的にデータを構築しました。
また、取得したIDの順序を維持するために、並び替え処理を行い、ランキング順で表示されるよう工夫しました。

### 3. コース詳細取得機能

コースの詳細情報を取得する際に、関連する場所データを順序通りに整形し、DTOとして返却する機能です。
さらに、ユーザーごとの「いいね」状態を判定し、個人化された情報としてレスポンスに含めることで、ユーザー体験の向上を図りました。

## トラブルシューティングおよび解決

### 1. ページング処理の改善

【問題】
コース一覧画面において、当初はページ番号ベースのページング（下部のページボタン）を実装していたが、データの追加や更新が発生した際に、表示内容の重複や抜けが発生する問題があった。

【原因】
ページ番号ベースの方式では、データの変動によって取得範囲がずれ、ユーザーが意図しない結果を受け取る可能性があった。

【解決】
問題の改善方法を検討する中で、チームメンバーが以前に実装したスクロールベースのページング機能を提案してくれた。
その仕組みを理解し、自分の機能に適用することで、データの取得基準を固定し、重複や欠損のない安定したページング処理を実現した。

【結果】
スクロールベースのページングに変更したことで、データの整合性が保たれるようになり、重複表示や欠損の問題を解消することができた。
また、ユーザーが自然に次のデータを閲覧できるようになり、UXの向上にもつながった。


<details>
<summary><b>韓国語版(한국어 버전)</b></summary>

# Final-Project
더조은 컴퓨터 학원 파이널 프로젝트

# FunSpot

### 프로젝트 주제 및 선정 배경,기획의도
>"여행 코스 추천 및 커뮤니티 플랫폼 개발"
사용자가 맞춤형 여행 코스를 설계하고 맛집, 관광 명소, 숙박 정보를 공유하며 소통할 수 있는 웹사이트를 제작. 
분산된 여행 정보를 통합 제공하고, 커뮤니티 활성화를 통해 사용자 경험과 만족도를 높이는 것이 목표.

### 프로젝트 내용
>사용자 커스텀 데이트코스를 통해 사용자들끼리 공유하고 피드를통해 여행 커뮤니티를 활성화하고 기존의 유명한 장소뿐만 아니라
숨겨진 명소들을 공유하고 여행할 수 있도록 함

### 활용방안 및 기대 효과
>코로나19이후 여행자들이 급증함에 따라 각 지역의 숨겨진 명소를 발견하고 수도권뿐만 아니라 각 지역의 관광산업의 발전을
기대해 볼 수 있음

### 벤치마킹 앱
> 완벽한 하루: https://xn--2s2b33eb3kgvpta.com/

> 데이트 팝: https://datepop.co.kr/
</br>

## 제작 기간 & 참여 인원 & 참여부분
- 제작 기간 : 2024년 11월 20일 ~ 2025년 1월 8일

- 참여 인원 : 6명

- 참여 부분 : 사용자 커스텀 코스 게시판 등록,수정,삭제, 게시판 코스 조회, 인기 코스 조회
  </br>

## 사용 기술
####
- Java 17
- REACT
- MySQL 8.4
- Spring Boot
- JWT
- TailWindCSS
- 공공데이터포탈 API
- 네이버 지도 API
- 카카오 맵
- Spring Security
- lombok
- WebSocket
- JPA
- Figma
- AWS
- Gabia
  </br>

## ERD 설계
![image](https://github.com/user-attachments/assets/7dcccd75-465d-4aee-9d8e-afb1d26c183a)</br>

## 참여 기능 페이지
<details>
<summary><b>참여기능 페이지 보기</b></summary>
<div markdown="1">

### 1. 사용자 커스텀 코스 게시판 등록
![Image](https://github.com/user-attachments/assets/35a35b0c-9d40-4e72-9f57-3d40b042100d)</br>
- 2단계로 나누어져 있으며 장소검색 및 선택 단계와 코스 정보 입력 단계로 구분
- 장소 검색은 지역 및 상세 지역 또는 장소 이름으로 검색 가능
- 검색 결과는 각 장소를 나타내는 카드 형식으로 조회
- 장소의 이미지와 지역 주소, 장소 이름, 코스 추가 버튼 (클릭 시 선택된 장소에 추가)으로 구성
- 장소는 최소 2개에서 최대 5개까지 장소 선택 가능
- 선택된 장소는 원형 이미지와 장소 이름으로 구성, “x” 표시 버튼으로 삭제 가능

![Image](https://github.com/user-attachments/assets/abca6661-0b5a-4ecf-b732-a5e7dcd22392)</br>
- 코스 제목 및 설명 입력
- 누구와 가는 코스인지, 무엇을 하는 코스인지 선택
- 이전 단계 버튼 클릭 시 앞 단계인 장소 선택 단계로 이동
- 등록하기 버튼 클릭 시 코스가 등록 되며 코스 목록 화면으로 이동

### 2. 코스 목록 조회 페이지
![Image](https://github.com/user-attachments/assets/b27048a6-0292-4d41-bf8d-0b5502e11adf)</br>
- 상단에 코스 등록 페이지로 이동하는 버튼
- 등록된 코스 목록이 최신순으로 조회
- 처음에는 최근 등록된 3개의 코스만 조회, 스크롤을 끝까지 내리면 다음 3개 코스 조회(무한 스크롤링)
- 각 코스에 해당하는 장소 이미지, 장소의 지역 및 주소, 코스 순서 번호, 장소 이름, 코스 제목, 코스에 필요 예상 금액과 예상 소요 시간, 코스 태그
- 코스 상세 페이지로 이동하는 버튼

### 3. 코스 상세 페이지
![Image](https://github.com/user-attachments/assets/61e2e7bc-f83e-4dea-be5a-8ff2410595a9)</br>
- 각 장소의 위치와 코스 경로를 알려주는 지도 네이버 지도 API 활용)
- 코스 설명문, 각 위치를 마커로 표현, 마커 중앙에 장소 카테고리에 따른 이미지, 코스 순서 번호
- 코스 제목과 코스 태그, 수정,삭제 버튼은 작성자에게만 표현(토큰 활용)
- 수정 버튼 클릭 시 코스 수정 페이지로 이동, 삭제 버튼 클릭 시 코스 삭제 이후 코스 목록 화면으로 이동
- 찜 하기 버튼 클릭 시 마이 페이지의 위시리스트에 추가, 코스 목록 버튼 클릭 시 코스 목록 조회 화면으로 이동
- 각 장소의 정보가 담긴 카드 조회, 장소 이미지 지역을 나타내는 주소, 코스 순서 번호 및 장소 이름, 길 찾기 버튼 (카카오 맵 활용), 각 장소의 예상 비용 및 소요시간

### 4. 코스 수정 페이지
![Image](https://github.com/user-attachments/assets/10ff1aae-25c2-440f-9754-2ae894476996)</br>
![Image](https://github.com/user-attachments/assets/49b011b9-4189-469b-9663-96526d666093)</br>
- 코스 등록 페이지와 형식은 동일
- 기존 선택된 장소 표시, 장소 검색 및 선택 기능은 등록과 동일
- 기존에 입력된 제목,설명 표시
- 선택된 태그 활성화

### 5. 인기 코스 목록 조회 페이지
![Image](https://github.com/user-attachments/assets/9642ceba-02e1-4918-b592-73de1bca6c4a)</br>
- 사용자들이 위시리스트에 많이 추가된 코스 순으로 조회
- 그 외 기능은 목록 조회 화면과 동일

</div>
</details>

## 기능 코드

### 1. 장소 정보 조회

> - 백엔드코드</br>
    > [Controller 코드](https://github.com/You-HyunHo/Final-Project/blob/master/backend/src/main/java/com/spot/fun/usr/custom/controller/PlaceController.java)</br>
    > [Service 코드](https://github.com/You-HyunHo/Final-Project/blob/master/backend/src/main/java/com/spot/fun/usr/custom/service/PlaceServiceImpl.java)</br>

### 2. 사용자 커스텀 게시판(등록, 조회, 상세조회, 수정, 삭제)

> - 백엔드코드</br>
    > [Controller 코드](https://github.com/You-HyunHo/Final-Project/blob/master/backend/src/main/java/com/spot/fun/usr/custom/controller/CustomController.java)</br>
    > [Service 코드](https://github.com/You-HyunHo/Final-Project/blob/master/backend/src/main/java/com/spot/fun/usr/custom/service/CustomServiceImpl.java)</br>
> - 프론트엔드코드</br>
    > [Component 코드(등록)](https://github.com/You-HyunHo/Final-Project/blob/master/frontend/src/usr/custom/components/AddComponent.js)</br>
    > [Component 코드(조회)](https://github.com/You-HyunHo/Final-Project/blob/master/frontend/src/usr/custom/components/ListComponent.js)</br>
    > [Component 코드(상세조회)](https://github.com/You-HyunHo/Final-Project/blob/master/frontend/src/usr/custom/components/ReadComponent.js)</br>
    > [Component 코드(수정)](https://github.com/You-HyunHo/Final-Project/blob/master/frontend/src/usr/custom/components/UpdateComponent.js)</br>

### 3. 위시리스트(추가, 취소, 인기코스 조회)

> - 백엔드코드</br>
    > [Controller 코드](https://github.com/You-HyunHo/Final-Project/blob/master/backend/src/main/java/com/spot/fun/usr/custom/controller/WishListController.java)</br>
    > [Service 코드](https://github.com/You-HyunHo/Final-Project/blob/master/backend/src/main/java/com/spot/fun/usr/custom/service/WishListServiceImpl.java)</br>
> - 프론트엔드코드</br>
    > [Component 코드(인기 코스 조회)](https://github.com/You-HyunHo/Final-Project/blob/master/frontend/src/usr/custom/components/PopularComponent.js)</br>


## 프로젝트에서 잘한 점 & 부족한 점 & 개선 사항

### 잘한 점

#### 프론트엔드
- 디자이너 협업 디자인 구현
- React 레이아웃 구조 통일
- customHook 적용
- TailwindCSS 사용

#### 백엔드
- REST API 구현
- WebSocket 채팅 구현
- 자체와 OAuth2 간편 로그인 구현

### 부족한 점

#### 프론트엔드
- 관리자 페이지 미구현
- 상태관리 라이브러리 미적용
- 반응형 웹 미구현

#### 백엔드
- ADMIN API 미구현(일반유저와 구분 X)
- 일관성 없는 Package 구조
- DB 구조 설계 미흡


### 개선 사항
- 관리자 페이지 구현
- 결제 시스템 도입
- 실명 인증 도입
- 패키지 구조 통일
- 반응형 디자인 적용

</details>


