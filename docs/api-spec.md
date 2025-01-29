# API Spec

---

- [검색 API](#%EA%B2%80%EC%83%89-api)
  - [상품 검색](#%EC%83%81%ED%92%88-%EA%B2%80%EC%83%89)
- [상품 API](#%EC%83%81%ED%92%88-api)
  - [상품 상세 조회](#%EC%83%81%ED%92%88-%EB%8B%A8%EA%B1%B4-%EC%A1%B0%ED%9A%8C)
  - [인기 상품 조회](#%EC%9D%B8%EA%B8%B0-%EC%83%81%ED%92%88-%EC%A1%B0%ED%9A%8C)
- [주문 / 결제 API](#%EC%A3%BC%EB%AC%B8--%EA%B2%B0%EC%A0%9C-api)
  - [주문 생성](#%EC%A3%BC%EB%AC%B8-%EC%83%9D%EC%84%B1)
  - [결제 요청](#%EA%B2%B0%EC%A0%9C-%EC%9A%94%EC%B2%AD)
  - [주문 취소](#%EC%A3%BC%EB%AC%B8-%EC%B7%A8%EC%86%8C)
- [사용자 API](#%EC%82%AC%EC%9A%A9%EC%9E%90-api)
  - [잔액 충전](#%EC%9E%94%EC%95%A1-%EC%B6%A9%EC%A0%84)
  - [잔액 조회](#%EC%9E%94%EC%95%A1-%EC%A1%B0%ED%9A%8C)
  - [쿠폰 발급](#%EC%BF%A0%ED%8F%B0-%EB%B0%9C%EA%B8%89)
  - [보유 쿠폰 목록 조회](#%EB%B3%B4%EC%9C%A0-%EC%BF%A0%ED%8F%B0-%EB%AA%A9%EB%A1%9D-%EC%A1%B0%ED%9A%8C)
  - [장바구니에 상품 추가](#%EC%9E%A5%EB%B0%94%EA%B5%AC%EB%8B%88%EC%97%90-%EC%83%81%ED%92%88-%EC%B6%94%EA%B0%80)
  - [장바구니 조회](#%EC%9E%A5%EB%B0%94%EA%B5%AC%EB%8B%88-%EC%A1%B0%ED%9A%8C)
  - [장바구니 상품 삭제](#%EC%9E%A5%EB%B0%94%EA%B5%AC%EB%8B%88-%EC%83%81%ED%92%88-%EC%82%AD%EC%A0%9C)
  - [비회원 장바구니 병합](#%EB%B9%84%ED%9A%8C%EC%9B%90-%EC%9E%A5%EB%B0%94%EA%B5%AC%EB%8B%88-%EB%B3%91%ED%95%A9)

---  

## 검색 API

### 상품 검색

- **Endpoint**: `GET /search/products`
- **Description**:
  - 상품명, 카테고리, 가격 범위, 인기순 등의 조건으로 상품을 검색한다.
  - 오타 보정, 자동완성, 추천검색어, 초성검색 등의 기능 구현

#### Query Parameters

| 파라미터     | 타입     | 설명                |  
|----------|--------|-------------------|  
| query    | string | 검색어 (상품명, 키워드 등)  |  
| category | string | 카테고리(선택)          |  
| minPrice | number | 최소 가격(선택)         |  
| maxPrice | number | 최대 가격(선택)         |  
| sort     | string | 정렬 기준(인기순, 가격순 등) |  

#### Response Body 예시

```json  
[
  {
    "productId": 101,
    "name": "게이밍 마우스",
    "price": 45000,
    "category": "컴퓨터/주변기기"
  },
  
  {
    "productId": 202,
    "name": "무선 키보드",
    "price": 32000,
    "category": "컴퓨터/주변기기"
  }
]  
```  

| 필드명       | 타입     | 설명      |  
|-----------|--------|---------|  
| productId | number | 상품 식별자  |  
| name      | string | 상품명     |  
| price     | number | 상품 가격   |  
| category  | string | 상품 카테고리 |  

## 상품 API

### 상품 상세 조회

- **Endpoint**: `GET /products/{productId}`
- **Description**: 특정 상품의 상세 정보를 조회한다.

#### Path Parameter

| 파라미터명     | 타입     | 설명         |  
|-----------|--------|------------|  
| productId | number | 상품 식별자(필수) |  

#### Response Body 예시

```json  
{
  "productId": 1,
  "name": "노트북",
  "price": 1500000,
  "stock": 10
}  
```  

| 필드명       | 타입     | 설명       |  
|-----------|--------|----------|  
| productId | number | 상품 식별자   |  
| name      | string | 상품명      |  
| price     | number | 상품 가격    |  
| stock     | number | 재고(잔여수량) |  

### 인기 상품 조회

- **Endpoint**: `GET /products/popular`
- **Description**: 최근 3일간 주문 내역을 기준으로, 판매량이 높은 상품 상위 5개를 조회한다.

#### Response Body 예시

```json  
[
  {
    "productId": 1,
    "name": "노트북",
    "price": 1500000,
    "totalSold": 25
  },
  {
    "productId": 5,
    "name": "키보드",
    "price": 30000,
    "totalSold": 20
  }
]  
```  

| 필드명       | 타입     | 설명              |  
|-----------|--------|-----------------|  
| productId | number | 상품 식별자          |  
| name      | string | 상품명             |  
| price     | number | 상품 가격           |  
| totalSold | number | 최근 3일간 판매된 총 수량 |  

---  

## 주문 / 결제 API

### 주문 생성

- **Endpoint**: `POST /orders`
- **Description**: 사용자 식별자, 상품 목록(상품 ID, 수량), (선택) 쿠폰 ID 등을 입력받아 주문을 생성한다.
    - 결제 성공 시, 사용자 잔액에서 주문 금액 차감
    - 결제 성공 시, 주문 정보를 외부 데이터 플랫폼으로 전송(가정)
    - 결제 실패 시 주문 취소

#### Header

| 필드명       | 타입     | 설명          |  
|-----------|--------|-------------|  
| X-USER-ID | number | 사용자 식별자(필수) |  

#### Request Body 예시

```json  
{
  "orderItems": [
    {
      "productId": 1,
      "quantity": 2
    },
    {
      "productId": 2,
      "quantity": 1
    }
  ],
  "couponId": 101
}  
```  

| 필드명         | 타입     | 설명            |  
|-------------|--------|---------------|  
| orderItems  | array  | 주문할 상품 목록(필수) |  
| └ productId | number | 상품 식별자(필수)    |  
| └ quantity  | number | 주문 상품 수량 (필수) |  
| couponId    | number | 쿠폰 식별자(선택)    |  

#### Response Body 예시

```json  
{
  "orderId": 10001,
  "userId": 123,
  "orderItems": [
    {
      "productId": 1,
      "quantity": 2,
      "price": 1500000
    },
    {
      "productId": 2,
      "quantity": 1,
      "price": 30000
    }
  ],
  "discountAmount": 300000,
  "totalAmount": 3000000,
  "balance": 0,
  "orderedAt": "2025-01-27T15:00:00Z"
}  
```  

| 필드명            | 타입     | 설명                         |  
|----------------|--------|----------------------------|  
| orderId        | number | 주문 식별자                     |  
| userId         | number | 사용자 식별자                    |  
| orderItems     | array  | 주문 상품 목록 (상품 ID, 수량, 단가 등) |  
| └ productId    | number | 상품 식별자                     |  
| └ quantity     | number | 주문 상품 수량                   |  
| └ price        | number | 상품 가격                      |  
| discountAmount | number | 할인 금액 (쿠폰 사용 시 계산 결과)      |  
| totalAmount    | number | 최종 결제 금액                   |  
| balance        | number | 결제 후 사용자 잔액                |  
| orderedAt      | string | 주문 일시                      |  

### 결제 요청

- **Endpoint**: `POST /orders/{orderId}/payments`
- **Description**: 사용자 식별자, 주문 식별자를 받아 결제를 요청한다.
    - 결제 성공 시, 사용자 잔액에서 주문 금액 차감
    - 결제 성공 시, 주문 정보를 외부 데이터 플랫폼으로 전송(가정)

#### Header

| 필드명       | 타입     | 설명          |  
|-----------|--------|-------------|  
| X-USER-ID | number | 사용자 식별자(필수) |  

#### Path Parameter

| 필드명     | 타입     | 설명         |  
|---------|--------|------------|  
| orderId | number | 주문 식별자(필수) |  

#### Response Body 예시

```json  
{
  "orderId": 10001,
  "userId": 123,
  "amount": 0
}  
```  

| 필드명     | 타입     | 설명          |  
|---------|--------|-------------|  
| orderId | number | 주문 식별자      |  
| userId  | number | 사용자 식별자     |  
| amount  | number | 결제 후 사용자 잔액 |  

### 주문 취소

- **Endpoint**: `POST /orders/{orderId}/cancel`
- **Description**: 진행 중인 결제를 취소하고 싶을 경우(혹은 환불 등의 시나리오), 주문도 함께 취소하는 API

#### Header

| 필드명       | 타입     | 설명          |  
|-----------|--------|-------------|  
| X-USER-ID | number | 사용자 식별자(필수) |  

#### Path Parameter

| 파라미터명   | 타입     | 설명         |  
|---------|--------|------------|  
| orderId | number | 주문 식별자(필수) |
  
---  

## 사용자 API

### 잔액 충전

- **Endpoint**: `PATCH /users/charge`
- **Description**: 사용자 식별자와 충전 금액을 받아서 잔액을 충전한다.

#### Header

| 필드명       | 타입     | 설명          |  
|-----------|--------|-------------|  
| X-USER-ID | number | 사용자 식별자(필수) |  

#### Request Body 예시

```json  
{
  "amount": 10000
}  
```  

| 필드명    | 타입     | 설명         |  
|--------|--------|------------|  
| amount | number | 충전할 금액(필수) |  

#### Response Body 예시

```json  
{
  "userId": 123,
  "amount": 30000
}  
```  

| 필드명    | 타입     | 설명          |  
|--------|--------|-------------|  
| userId | number | 사용자 식별자     |  
| amount | number | 충전 후 사용자 잔액 |  

### 잔액 조회

- **Endpoint**: `GET /users/amount`
- **Description**: 사용자 식별자를 받아 해당 사용자의 현재 잔액을 조회한다.

#### Header

| 필드명       | 타입     | 설명          |  
|-----------|--------|-------------|  
| X-USER-ID | number | 사용자 식별자(필수) |  

#### Response Body 예시

```json  
{
  "userId": 123,
  "amount": 30000
}  
```  

| 필드명    | 타입     | 설명        |  
|--------|--------|-----------|  
| userId | number | 사용자 식별자   |  
| amount | number | 현재 사용자 잔액 |  

### 쿠폰 발급

- **Endpoint**: `POST /users/coupons/{couponId}`
- **Description**: 사용자가 선착순으로 할인 쿠폰을 발급받는다. (금액 할인/정률 할인 구분은 `couponType` 등으로 처리)

#### Header

| 필드명       | 타입     | 설명          |  
|-----------|--------|-------------|  
| X-USER-ID | number | 사용자 식별자(필수) |  

#### Response Body 예시

```json  
{
  "userId": 123,
  "couponId": 101,
  "couponType": "RATE",
  "discountValue": 10,
  "issuedAt": "2025-01-27T14:00:00Z"
}  
```  

| 필드명           | 타입     | 설명            |  
|---------------|--------|---------------|  
| userId        | number | 사용자 식별자       |  
| couponId      | number | 쿠폰 식별자        |  
| couponType    | string | 쿠폰 유형         |  
| discountValue | number | 할인 값(금액 or %) |  
| issuedAt      | string | 발급 일시         |  

### 보유 쿠폰 목록 조회

- **Endpoint**: `GET /users/coupons`
- **Description**: 특정 사용자가 보유한 쿠폰 목록을 조회한다.

#### Header

| 필드명       | 타입     | 설명          |  
|-----------|--------|-------------|  
| X-USER-ID | number | 사용자 식별자(필수) |  

#### Response Body 예시

```json  
[
  {
    "couponId": 101,
    "couponType": "RATE",
    "discountValue": 10,
    "issuedAt": "2025-01-27T14:00:00Z",
    "valid": true
  },
  {
    "couponId": 102,
    "couponType": "AMOUNT",
    "discountValue": 5000,
    "issuedAt": "2025-01-27T14:05:00Z",
    "valid": false
  }
]  
```  

| 필드명           | 타입     | 설명                         |  
|---------------|--------|----------------------------|  
| couponId      | number | 쿠폰 식별자                     |  
| couponType    | string | 쿠폰 유형 ("AMOUNT" or "RATE") |  
| discountValue | number | 할인 값(금액 or %)              |  
| issuedAt      | string | 발급 일시                      |  
| valid         | bool   | 사용 가능 여부                   |  

### 장바구니에 상품 추가

- **Endpoint**: `POST /users/cart`
- **Description**:
    - 사용자/비회원 식별자와 상품 Id, 수량을 받아 장바구니에 추가한다.
    - 비회원일 경우 session 정보를 받아 처리 한다.

#### Header

| 필드명          | 타입     | 설명             |  
|--------------|--------|----------------|  
| X-USER-ID    | number | 사용자 식별자(선택)    |  
| X-SESSION-ID | string | 비회원 세션 아이디(선택) |  

#### Request Body 예시

```json  
{
  "productId": 1,
  "quantity": 2
}  
```  

| 필드명       | 타입     | 설명             |  
|-----------|--------|----------------|  
| productId | number | 상품 식별자(필수)     |  
| quantity  | number | 장바구니 추가 수량(필수) |  

#### Response Body 예시

```json  
{
  "cartItemId": 5001,
  "productId": 1,
  "quantity": 2,
  "createdAt": "2025-01-27T15:20:00Z"
}  
```  

| 필드명        | 타입     | 설명             |  
|------------|--------|----------------|  
| cartId     | number | 장바구니 식별자       |  
| cartItemId | number | 장바구니 상품 식별자    |  
| productId  | number | 상품 식별자         |  
| quantity   | number | 장바구니에 담긴 상품 수량 |  
| createdAt  | string | 추가된 시각         |  

### 장바구니 조회

- **Endpoint**: `GET /users/cart`
- **Description**: 특정 사용자/비회원 의 장바구니 목록을 조회한다.

#### Header

| 필드명          | 타입     | 설명             |  
|--------------|--------|----------------|  
| X-USER-ID    | number | 사용자 식별자(선택)    |  
| X-SESSION-ID | string | 비회원 세션 아이디(선택) |  

#### Response Body 예시

```json  
{
  "cartId": 1,
  "cartItem": [
    {
      "cartItemId": 5001,
      "productId": 1,
      "name": "노트북",
      "price": 1500000,
      "quantity": 2,
      "createdAt": "2025-01-27T15:20:00Z"
    },
    {
      "cartItemId": 5002,
      "productId": 2,
      "name": "무선 마우스",
      "price": 30000,
      "quantity": 1,
      "createdAt": "2025-01-27T15:25:00Z"
    }
  ]
}  
```  

| 필드명        | 타입     | 설명             |  
|------------|--------|----------------|  
| cartItemId | number | 장바구니 상품 식별자    |  
| productId  | number | 상품 식별자         |  
| name       | string | 상품명            |  
| price      | number | 상품 가격          |  
| quantity   | number | 장바구니에 담긴 상품 수량 |  
| createdAt  | string | 장바구니에 추가된 시각   |  

### 장바구니 상품 삭제

- **Endpoint**: `DELETE /users/cart`
- **Description**: 장바구니에 담긴 특정 상품을 제거한다.

#### Header

| 필드명          | 타입     | 설명             |  
|--------------|--------|----------------|  
| X-USER-ID    | number | 사용자 식별자(선택)    |  
| X-SESSION-ID | string | 비회원 세션 아이디(선택) |  

#### Response Body 예시

```json  
[
  {
    "cartItemId": 5001
  },
  {
    "cartItemId": 5002
  }
]  
```  

| 필드명        | 타입     | 설명          |  
|------------|--------|-------------|  
| cartItemId | number | 장바구니 상품 식별자 |  

---  

### 비회원 장바구니 병합

- **Endpoint**: `PATCH /users/cart/associate`
- **Description**: 회원 로그인 시 비회원 장바구니 목록을 병합 한다.

#### Header

| 필드명          | 타입     | 설명             |  
|--------------|--------|----------------|  
| X-USER-ID    | number | 사용자 식별자(필수)    |  
| X-SESSION-ID | string | 비회원 세션 아이디(필수) |
