# API 명세

#### History
2022.02.20 - 은빈 작성
2022.02.22 - 은빈, 민주 작성

## DB 스키마

![DB 스키마 이미지]()

## 로그인

- [카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)

## 사용자

### 회원가입

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/sign-up    | JSON   | 회원가입      |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| name | String | Y | - 사용자 닉네임 <br> - 최대 25자 |
| birthday | Datetime | Y | - 사용자 생일 <br> - "YYYYMMDD" 형식으로 KST 기준 |

#### 요청 헤더

- 없음

#### 응답 헤더

- access_token, refresh_token 과 같은 토큰류들을 Set-cookie 헤더에 넣어서 보냄

#### 출력 결과

```json
// 성공 - 200~
{
	"result": "success",
	"message": "성공",
	"data": {
		"user_id": 1,
	}
}

// 실패 - 400 ~ 5??
{
	"result": "failure",
	"message": "회원가입에 실패했습니다.",
}
```

### 사용자 정보 조회

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET   |{base-url}/users/{user-id}| JSON | 사용자 정보 조회|

#### 요청 변수

- 없음

#### 요청 헤더

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 성공
{
	"result": "success",
	"message": "성공",
	"data": {
		"name": "세리",
		"birthday": "19960123",
	}
}

// 실패
{
	"result": "failure",
	"message": "사용자가 없습니다.",
}
```

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

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 성공
{
	"result": "success",
	"message": "성공",
	"data": {
		"name": "그린피스",
		"color": "pink",
	}
}

// 실패
{
	"result": "failure",
	"message": "상자 만드는 데 실패했습니다.",
}
```

### 상자 리스트 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET    |{base-url}/donation-boxes/{user-id}/all| JSON   | 해당 사용자의 상자 리스트를 모두 가져오기 |

#### 요청 변수

- 없음

#### 요청 헤더

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 상자 리스트가 비어있을 때
{
	"result": "success",
	"message": "성공",
	"data": {
		"donation_box_list": [],
	}
}

// 상자 리스트에 요소가 하나라도 있을 때
{
	"result": "success",
	"message": "성공",
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

// 실패
{
	"result": "failure",
	"message": "상자 리스트를 가져오는데 실패했습니다.",
}
```

### 상자 정보 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET    |{base-url}/donation-boxes/{user-id}/{box-id}| JSON   | 해당 사용자의 선택된 상자에 대한 세부 내용 가져오기|

#### 요청 변수

- 없음

#### 요청 헤더

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 성공
{
	"result": "success",
	"message": "성공",
	"data": {
		"name": "그린피스",
		"url": "https://....",
		"description": "카카오톡 선물하기 대신 함께 기부해요!",
		"amount": 5000,
		"color": "pink",
		"is_donated": false,
		"cert_img_url": "",
		"open_date": "20230123",
		"message_count": 10,
	},
}

// 실패
{
	"result": "failure",
	"message": "상자 정보를 가져올 수 없습니다."
}
```

- open_date는 저장된 형식대로 전송
- message_count는 상자에 담긴 쪽지 개수, 없을 땐 0으로 보내주기

### 상자 기부 여부 변경하기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|PUT    |{base-url}/donation-boxes/{box-id}| JSON   | 기부 여부 변경하기 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| is_donated | Boolean | Y | - 기부 여부에 대한 불리언 값 |

#### 요청 헤더

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 성공
{
	"result": "success",
	"message": "성공",
}

// 실패
{
	"result": "failure",
	"message": "수정에 실패했습니다.",
}
```

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

- 없음

#### 출력 결과

> ‼️ 고민! <br>
> 쪽지가 보내졌는지 안 보내졌는지 피드백을 좀 더 상세히 해야 할 필요는?

```json
// 성공
{
	"result": "success",
	"message": "성공",
}

// 실패
{
	"result": "failure",
	"message": "메시지 전송에 실패했습니다.",
}
```

### 쪽지 리스트 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET |{base-url}/messages/{user-id}/all | JSON | 쪽지 리스트 가져오기 |

#### 요청 변수

- 없음

#### 요청 헤더

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 쪽지 리스트가 비어있을 때
{
	"result": "success",
	"message": "성공",
	"data": {
		"message_list": [],
	}
}

// 쪽지 리스트에 요소가 하나라도 있을 때
{
	"result": "success",
	"message": "성공",
	"data": {
		"message_list": [
			{
				"id": "1",
				"created_by": "박민주",
				"contents": "축하합니다축하합니다축하합니다축하합니다",
				"tag": "happiness",
			},
			{
				"id": "2",
				"created_by": "복예린",
				"contents": "축하합니다축하합니다축하합니다축하합니다",
				"tag": "happiness",
			},
			{
				"id": "7",
				"created_by": "민주똥꼬불난다",
				"contents": "축하합니다축하합니다축하합니다축하합니다",
				"tag": "happiness",
			},
			...
		]
	}
}

// 실패
{
	"result": "failure",
	"message": "쪽지 리스트를 가져오는데 실패했습니다.",
}
```

- 전체 쪽지 전송

> ‼️ 나중에 쪽지 개수가 많아지고 복잡해지면 페이지네이션 필요할 수 있음!

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

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 성공
{
	"result": "success",
	"message": "성공",
}

// 실패
{
	"result": "failure",
	"message": "이미지 저장에 실패했습니다.",
}
```

### 증명서 정보 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET    |{base-url}/certifications/{box-id} | JSON | 증명서에 들어갈 내용 가져오기 |

#### 요청 변수

- 없음

#### 요청 헤더

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 성공
{
	"result": "success",
	"message": "성공",
	"data": {
		"box_name": "그린피스",
		"box_created_by": "권은빈",
		"donors_name": ["박민주", "복예린", ...],
		"cert_img_url": "s3_image_url",
		"cert_created_at": "20240124",
	}
}

// 실패
{
	"result": "failure",
	"message": "증명서 정보 가져오기에 실패했습니다.",
}
```

- cert_created_at 타입 Datetime, KST, YYYYMMDD

# References

[RESTful API 네이밍 컨벤션](https://restfulapi.net/resource-naming/)

[네이버 카페 API 문서](https://developers.naver.com/docs/login/cafe-api/cafe-api.md)