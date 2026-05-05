# 시퀀스 다이어그램

## 코스 생성 (register) 시퀀스
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as CustomService
participant DB
participant WL as WishListRepository

    FE->>BE: 코스 상세 조회 요청 (cno, userIdx)

    BE->>DB: Custom 조회
    DB-->>BE: Custom 반환

    BE->>BE: CustomDTO 변환

    BE->>BE: CustomPlace 정렬 (orderIndex)
    BE->>BE: Place → PlaceDTO 변환

    BE->>WL: 위시리스트 여부 조회
    WL->>DB: exists query
    DB-->>WL: 결과 반환
    WL-->>BE: true/false

    BE->>BE: DTO에 places, tags, wishlist 설정

    BE-->>FE: CustomDTO 반환
```

## 코스 상세 조회 (get)
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as CustomService
participant DB
participant WL as WishListRepository

    FE->>BE: 코스 상세 조회 요청 (cno, userIdx)

    BE->>DB: Custom 조회
    DB-->>BE: Custom 반환

    BE->>BE: CustomDTO 변환

    BE->>BE: CustomPlace 정렬 (orderIndex)
    BE->>BE: Place → PlaceDTO 변환

    BE->>WL: 위시리스트 여부 조회
    WL->>DB: exists query
    DB-->>WL: 결과 반환
    WL-->>BE: true/false

    BE->>BE: DTO에 places, tags, wishlist 설정

    BE-->>FE: CustomDTO 반환
```
## 리스트 조회 (무한 스크롤 포함)
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as CustomService
participant DB
participant WL as WishListRepository

    FE->>BE: 코스 리스트 요청 (scrollPaging, pageable)

    BE->>DB: 스크롤 페이징 조회
    DB-->>BE: Custom 리스트 반환

    loop 각 Custom 반복
        BE->>BE: DTO 변환
        BE->>BE: 장소 정렬 (orderIndex)
        BE->>BE: PlaceDTO 변환

        BE->>WL: 위시리스트 여부 확인
        WL->>DB: exists query
        DB-->>WL: 결과 반환
        WL-->>BE: true/false
    end

    BE->>BE: hasNext 계산

    BE-->>FE: CustomResponseDTO 반환
```
## 업데이트 흐름
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as CustomService
participant DB

    FE->>BE: 코스 수정 요청 (cno, CustomDTO)

    BE->>DB: 기존 Custom 조회
    DB-->>BE: Custom 반환

    BE->>BE: 기본 정보 수정 (title, desc, tags)

    BE->>BE: 기존 CustomPlaces 제거

    loop 장소 재생성
        BE->>BE: CustomPlace 생성 (orderIndex 포함)
    end

    BE->>DB: 저장
    DB-->>BE: 완료

    BE-->>FE: 완료 응답
```
## 장소 검색 (searchPlaces)
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as PlaceService
participant DB

    FE->>BE: 장소 검색 요청 (address, name)

    alt address 없음 && name 없음
        BE->>DB: 전체 조회 (findAll)
    else address + name 모두 있음
        BE->>DB: 주소 + 이름 검색
    else address만 있음
        BE->>DB: 주소 기준 검색
    else name만 있음
        BE->>DB: 이름 기준 검색
    end

    DB-->>BE: Place 리스트 반환

    BE->>BE: Place → PlaceDTO 변환

    BE-->>FE: PlaceDTO 리스트 반환
```
## 찜 추가 (addWishList)
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as WishListService
participant DB

    FE->>BE: 찜 추가 요청 (userIdx, customCno)

    BE->>DB: 중복 여부 확인
    DB-->>BE: exists 여부 반환

    alt 이미 존재함
        BE-->>FE: 예외 발생 (중복)
    else 없음
        BE->>BE: WishList 엔티티 생성
        BE->>DB: 저장
        DB-->>BE: 저장 완료
        BE-->>FE: 성공 응답
    end
```
## 찜 삭제 (removeWishList)
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as WishListService
participant DB

    FE->>BE: 찜 삭제 요청 (userIdx, customCno)

    BE->>DB: 해당 데이터 삭제
    DB-->>BE: 완료

    BE-->>FE: 성공 응답
```
## 내 찜 목록 조회 (getFavoritesByUser)
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as WishListService
participant DB

    FE->>BE: 찜 목록 조회 (pageSize)

    BE->>DB: 페이징 조회 (정렬 포함)
    DB-->>BE: WishList 리스트 반환

    BE->>BE: DTO 변환

    BE->>BE: hasNext 계산

    BE-->>FE: WishListDTO 반환
```
## 인기 코스 조회 (listPopular)
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as WishListService
participant DB

    FE->>BE: 인기 코스 요청 (topCount)

    BE->>DB: 인기 Custom ID 조회 (찜 기준)
    DB-->>BE: Custom ID 리스트

    BE->>DB: Custom + Place 함께 조회
    DB-->>BE: Custom 리스트 반환

    BE->>BE: ID 순서 기준 정렬

    loop 각 Custom
        BE->>BE: Place 정렬 (orderIndex)
        BE->>BE: DTO 변환
    end

    BE-->>FE: 인기 코스 리스트 반환
```
## 전체 인기 + 사용자 상태 (listPopularAll)
```mermaid
sequenceDiagram
autonumber
participant FE as Frontend
participant BE as WishListService
participant DB

    FE->>BE: 인기 전체 조회 (userIdx)

    BE->>DB: Custom 리스트 조회
    DB-->>BE: Custom 리스트 반환

    loop 각 Custom
        BE->>BE: Place 정렬 및 DTO 변환

        BE->>DB: 찜 여부 확인
        DB-->>BE: true/false
    end

    BE-->>FE: CustomDTO 리스트 반환
```
