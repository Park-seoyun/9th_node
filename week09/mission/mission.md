### 미션 1: 하드코딩 제거

**Before (기존):**

```jsx
*// review.controller.js*
const userId = 1; *// 하드코딩// mission.controller.js*
const userId = 1; *// 하드코딩*
```

**After (개선):**

```jsx
*// review.controller.js*
const userId = req.user.id; *// JWT에서 가져옴// mission.controller.js*
const userId = req.user.id; *// JWT에서 가져옴*
```

### 미션 2: 사용자 정보 수정 API 추가

**새 API 추가:**

PATCH /api/v1/users/me

**Request:**

```json
{
  "nickname": "새닉네임",
  "gender": "MALE",
  "birth_date": "1995-05-15",
  "address_main": "서울시 강남구"
}
```

**Response:**

```json
{
  "resultType": "SUCCESS",
  "error": null,
  "success": {
    "id": 1,
    "email": "user@example.com",
    "nickname": "새닉네임",
    "gender": "MALE",
    "birth_date": "1995-05-15",
    "address_main": "서울시 강남구"
  }
}
```

### 미션 3: JWT 인증 미들웨어 적용 (auth.config.js)

**isLogin 미들웨어:**

```json
const isLogin = passport.authenticate('jwt', { session: false });
```

**보호된 API 목록:**
API인증 필요
POST /api/v1/users/signup 로그인 불필요
공개GET /api/v1/users/me 로그인 필요
PATCH /api/v1/users/me 로그인 필요
POST /api/v1/regions/:regionId/stores 로그인 필요
POST /api/v1/stores/:storeId/reviews 로그인 필요
POST /api/v1/stores/:storeId/missions 로그인 필요
POST /api/v1/missions/:missionId/challenge 로그인 필요