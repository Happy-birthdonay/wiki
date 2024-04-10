# API 명세

#### History
2022.02.20 - 은빈 작성

2022.02.22 - 은빈, 민주 작성

2022.02.28 - 은빈, 민주 수정

2022.03.06 - 은빈, 민주 수정

2022.03.14 - 은빈 수정

2022.03.20 - 은빈, 민주 수정 (Last updated)

## DB 스키마

![이미지](/images/ERD/20240320-v5.png)

## 사용자

### OAuth

- [카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)

- [참고한 벨로그 글](https://velog.io/@max9106/OAuth)

![Velog Image](https://velog.velcdn.com/images%2Fmax9106%2Fpost%2F5620524a-4359-4abd-b90c-07b65359b3ca%2F%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-07-12%20%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB%204.16.43.png)


#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/oauth/token| JSON   | 카카오에서 발급한 인증 코드 보내기 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| code | String | Y | - 카카오 로그인 요청에 따라 발급받은 인증 코드 |

#### 요청 헤더

없음

#### 응답 헤더

- access_token, refresh_token 과 같은 **우리 서버 토큰**을 Set-cookie 헤더에 넣어서 보냄

#### 출력 결과

```json
// 성공 - 200~
{
	"result": "success",
	"message": "성공",
	"data": {
		"userId": 1,
		"name": "권은빈", 
		"birthday": "0123"
	}
}

// 실패 - 400 ~ 5??
{
	"result": "failure",
	"message": "카카오 회원가입/로그인에 실패했습니다.",
}
```

> check: 카카오 인증 코드가 유효하지 않은지, 카카오 API 요청에 실패했는지 등 에러 메시지 변경될 수 있음.


### access token 리프레쉬


#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/refresh| -   | refresh token 보내기 |

#### 요청 변수

없음

#### 요청 헤더

- Authorization에 Bearer {refresh_token}

```bash
# example
$ http POST :8000/refresh Authorization:"Bearer $REFRESH_TOKEN"
```

#### 응답 헤더

- access_token, refresh_token 과 같은 **우리 서버 토큰**을 Set-cookie 헤더에 넣어서 보냄

#### 출력 결과

```json
// 성공 - 200~
{
	"result": "success",
	"message": "성공",
	"data": {
		"access_token": "token..."
	}
}

// 실패 - 400 ~ 5??
{
	"result": "failure",
	"message": "access token 재발급에 실패했습니다.",
}
```

### 회원가입

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|PATCH   |{base-url}/sign-up| JSON   | 회원가입      |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| name | String | Y | - 사용자 닉네임 <br> - 최대 25자 |
| birthday | String | Y | - 사용자 생일 <br> - "mmdd" 형식 |

#### 요청 헤더

- Authorization 에 Bearer 토큰
- 토큰을 통해 user_id 추출

#### 응답 헤더

없음

#### 출력 결과

```json
// 성공 - 200~
{
	"result": "success",
	"message": "성공",
	"data": {
		"userId": 1,
		"name": "세리",
		"birthday": "0123"
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
|GET   |{base-url}/users| JSON | 사용자 정보 조회|

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
		"userId": 1,
		"name": "세리",
		"birthday": "0123",
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
| name | String | Y | - 기부처 이름 <br> - 최대 50자 |
| url | String | Y | - 기부처 링크 |
| boxTitle | String | Y | - 상자 이름 <br> - 최대 50자 |
| boxDescription | String | Y | - 상자 설명 <br> - 최대 100자 |
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
		"boxId": 1
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
|GET    |{base-url}/donation-boxes| JSON   | 해당 사용자의 상자 리스트를 모두 가져오기 |

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
		"donationBoxList": [],
	}
}

// 상자 리스트에 요소가 하나라도 있을 때
{
	"result": "success",
	"message": "성공",
	"data": {
		"donationBoxList": [
			{
				"box_id": "1",
				"color": "pink",
			},
			{
				"box_id": "2",
				"color": "blue",
			},
			{
				"box_id": "5",
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
|GET    |{base-url}/donation-boxes/{box-id}| JSON   | 해당 사용자의 선택된 상자에 대한 세부 내용 가져오기|

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
		"boxId": 1,
		"name": "그린피스",
		"url": "https://....",
		"boxTitle": "그린피스 짱짱맨",
		"boxDescription": "카카오톡 선물하기 대신 함께 기부해요!",
		"amount": 5000,
		"color": "pink",
		"isDonated": false,
		"certImgUrl": "",
		"openDate": "20230123",
		"messageCount": 10,
	},
}

// 실패
{
	"result": "failure",
	"message": "상자 정보를 가져올 수 없습니다."
}
```

- openDate는 yyyymmdd 형식의 String으로 전송
- messageCount는 상자에 담긴 쪽지 개수, 없을 땐 0으로 보내주기

### 상자 기부 여부 변경하기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|PATCH  |{base-url}/donation-boxes/{box-id}| JSON   | 기부 여부 변경하기 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| isDonated | Boolean | Y | - 기부 여부에 대한 불리언 값 |

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
| boxId | Int | Y | - 쪽지를 넣을 상자의 id |
| createdBy | String | Y | - 쪽지 보내는 사람 이름 <br> - 최대 10자 |
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
|GET |{base-url}/messages | JSON | 쪽지 리스트 가져오기 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| boxId | Int | Y | - 쪽지가 담긴 상자의 id |

- 쿼리 파라미터로 전달

#### 요청 헤더

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 쪽지 리스트가 비어있을 때
{
	"result": "success",
	"message": "성공",
	"data": {
		"messageList": [],
	}
}

// 쪽지 리스트에 요소가 하나라도 있을 때
{
	"result": "success",
	"message": "성공",
	"data": {
		"messageList": [
			{
				"messageId": "1",
				"createdBy": "박민주",
				"contents": "축하합니다축하합니다축하합니다축하합니다",
				"tag": "happiness",
			},
			{
				"messageId": "2",
				"createdBy": "복예린",
				"contents": "축하합니다축하합니다축하합니다축하합니다",
				"tag": "happiness",
			},
			{
				"messageId": "7",
				"createdBy": "민주똥꼬불난다",
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

> ‼️ 나중에 쪽지 개수가 많아지고 복잡해지면 페이지네이션 필요할 수 있음! <br>
> 이 때 페이지별 개수 등 논의 후 Query Parameter 추가

## 증명서

### 사진 URL 업로드하기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|POST    |{base-url}/certifications | JSON | S3에 업로드된 이미지 URL 전송 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| boxId | Int | Y | - 증명서에 해당하는 선물상자 ID |
| imageUrl | String | Y | - 증명서에 첨부될 사진 URL <br> - S3 버킷에 업로드 |

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

### 증명서 이미지 가져오기

#### 기본 정보
| method | request URL          | format | description |
|--------|----------------------|--------|-------------|
|GET    |{base-url}/certifications | JSON | 증명서에 들어갈 내용 가져오기 |

#### 요청 변수

| name | type | required | description |
|------|------|----------|-------------|
| boxId | Int | Y | - 증명서에 해당하는 선물상자 ID |

- 쿼리 파라미터로 전달

#### 요청 헤더

- Authorization 에 Bearer 토큰

#### 출력 결과

```json
// 성공
{
	"result": "success",
	"message": "성공",
	"data": {
		"boxId": 1,
		"name": "그린피스",
		"boxCreatedBy": "권은빈",
		"donorsNameList": ["박민주", "복예린", ...],
		"certImgUrl": "s3_image_url",
		"certCreatedAt": "2024-01-24",
	}
}

// 실패
{
	"result": "failure",
	"message": "증명서 정보 가져오기에 실패했습니다.",
}
```

- certCreatedAt 타입 String yyyy-mm-dd

# References

[RESTful API 네이밍 컨벤션](https://restfulapi.net/resource-naming/)

[네이버 카페 API 문서](https://developers.naver.com/docs/login/cafe-api/cafe-api.md)

[Patch](https://www.qu3vipon.com/idempotence-of-patch), [Query Param vs Path Variable에 대한 아티클 번역문](https://ryan-han.com/post/translated/pathvariable_queryparam/)