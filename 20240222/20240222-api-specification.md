# API 명세

#### History
2022.02.20 - 은빈 작성

## DB 스키마

![DB 스키마 이미지]()

## 로그인

- [카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)

## 사용자

### 회원가입

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/users      | JSON   | 회원가입      |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| client_id | String | Y | - SNS 로그인 후 받은 client_id |
| refresh_token | String | Y | - SNS 로그인 후 받은 refresh_token |
| name | String | Y | - 사용자 닉네임 <br> - 최대 25자 |
| birthday | Datetime | Y | - 사용자 생일 <br> - "YYYYMMDD" 형식으로 KST 기준 |

#### 요청 헤더

#### 출력 결과

#### 에러 코드

### 사용자 정보 조회

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET   |{base-url}/users/{user-id}| JSON | 사용자 정보 조회|

#### 요청 변수

#### 요청 헤더

#### 출력 결과

```json
{
	"result": "success",
	"data": {
		"name": "세리",
		"birthday": "19960123",
	}
}
```

#### 에러 코드

## 기부 상자

### 새 상자 만들기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/donation-boxes| JSON   | 새 상자 만들기 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| user_id | Int | Y | - 상자를 생성할 사용자의 id |
| name | String | Y | - 상자 이름 <br> - 최대 50자 |
| url | String | Y | - 기부처 링크 |
| description | String | Y | - 상자 설명 <br> - 최대 100자 |
| amount | Int | Y | - 인당 기부할 금액 |
| color | String | Y | - 상자 색깔 |

#### 요청 헤더

#### 출력 결과

#### 에러 코드


### 상자 리스트 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET    |{base-url}/donation-boxes/{user-id}/all| JSON   | 해당 사용자의 상자 리스트를 모두 가져오기 |

**Q. user-id를 파라미터로 넣는게 맞는지 헤더에 Authorization 필드를 추가하는게 맞는지?**

#### 요청 변수

#### 요청 헤더

#### 출력 결과

```json
// 상자 리스트가 비어있을 때
{
	"result": "success",
	"data": {
		"donation_box_list": [],
	}
}

// 상자 리스트에 요소가 하나라도 있을 때
{
	"result": "success",
	"data": {
		"donation_box_list": [
			{
				"id": "1",
				"color": "pink",
			},
			{
				"id": "2",
				"color": "blue",
			},
			{
				"id": "5",
				"color": "red",
			},
			...
		]
	}
}
```

#### 에러 코드



### 상자 정보 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET    |{base-url}/donation-boxes/{user-id}/{box-id}| JSON   | 해당 사용자의 선택된 상자에 대한 세부 내용 가져오기|

**Q. user-id를 파라미터로 넣는게 맞는지 헤더에 Authorization 필드를 추가하는게 맞는지?**

#### 요청 변수

#### 요청 헤더

#### 출력 결과

```json
{
	"result": "success",
	"data": {
		"name": "그린피스",
		"url": "https://....",
		"description": "카카오톡 선물하기 대신 함께 기부해요!",
		"amount": 5000,
		"color": "pink",
		"is_donated": false,
		"cert_img_url": "",
	},
}
```

**Q. 상자의 개봉 가능 날짜를 user-id를 통해 GET 해오는게 맞는지 data에 같이 실어 보내는게 맞는지?**

#### 에러 코드

### 상자 기부 여부 변경하기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/donation-boxes/{box-id}| JSON   | 새 상자 만들기 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| is_donated | Boolean | Y | - 기부 여부에 대한 불리언 값 |

#### 요청 헤더

#### 출력 결과

#### 에러 코드

## 쪽지

### 새 쪽지 만들기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/messages | JSON   | 쪽지 쓰기   |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| box_id | Int | Y | - 쪽지를 넣을 상자의 id |
| created_by | String | Y | - 쪽지 보내는 사람 이름 <br> - 최대 10자 |
| tag | String | Y | - 쪽지의 태그 |
| contents | String | Y | - 쪽지 내용 <br> - 최대 300자 |

#### 요청 헤더

#### 출력 결과

#### 에러 코드

### 쪽지 리스트 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET |{base-url}/messages/{user-id}/all | JSON | 쪽지 리스트 가져오기 |

**Q. user-id를 파라미터로 넣는게 맞는지 헤더에 Authorization 필드를 추가하는게 맞는지?**

#### 요청 변수

#### 요청 헤더

#### 출력 결과

```json
// 쪽지 리스트가 비어있을 때
{
	"result": "success",
	"data": {
		"message_list": [],
	}
}

// 쪽지 리스트에 요소가 하나라도 있을 때
{
	"result": "success",
	"data": {
		"message_list": [
			{
				"id": "1",
				"created_by": "박민주",
				"tag": "happiness",
			},
			{
				"id": "2",
				"created_by": "복예린",
				"tag": "peace",
			},
			{
				"id": "7",
				"created_by": "민주똥꼬불난다",
				"tag": "health",
			},
			...
		]
	}
}
```

**!! 쪽지 리스트를 어떻게 보여줄것인지 UI에 따라 결과 달라짐**

#### 에러 코드

### 쪽지 정보 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET  |{base-url}/messages/{user-id}/{message-id} | JSON   | 쪽지에 대한 상세 정보 가져오기 |

**Q. user-id를 파라미터로 넣는게 맞는지 헤더에 Authorization 필드를 추가하는게 맞는지?**

**Q. 페이지네이션 할 때 페이지는 요청변수로 넣어 보내면 될지?**

#### 요청 변수

#### 요청 헤더

#### 출력 결과

```json
// 논의 필요
```

#### 에러 코드

## 증명서

### 사진 업로드하기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/certifications | JSON | 증명서에 첨부될 사진 업로드하기 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| image | String | Y | - 증명서에 첨부될 사진 <br> - Base64 인코딩 필요 |

#### 요청 헤더

#### 출력 결과

#### 에러 코드


# References

[RESTful API 네이밍 컨벤션](https://restfulapi.net/resource-naming/)

[네이버 카페 API 문서](https://developers.naver.com/docs/login/cafe-api/cafe-api.md)