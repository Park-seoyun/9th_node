https://github.com/Park-seoyun/umc-workbook/tree/feature/chapter-08

# 미션 기록

## 변경 사항

### CORS 설정 추가

**Before:**

```jsx
app.use(cors()); *// 모든 출처 허용*
```

**After:**

```jsx
app.use(cors({
  origin: ["http://localhost:3000", "http://127.0.0.1:5500"],
  credentials: true,  *// 쿠키 허용*
}));
```

### Swagger 엔드포인트 추가

```jsx
*// Swagger UI: http://localhost:3000/docs*
app.use("/docs", swaggerUiExpress.serve, swaggerUiExpress.setup(...));

*// OpenAPI JSON: http://localhost:3000/openapi.json*
app.get("/openapi.json", async (req, res, next) => { ... });
```

## Swagger 주석 구조

### 기본 구조

```jsx
export const handleAPI = async (req, res, next) => {
  */**
    #swagger.summary = 'API 제목';
    #swagger.tags = ['태그명']
    
    // Path 파라미터
    #swagger.parameters['id'] = {
      in: 'path',
      required: true,
      type: 'integer',
      description: '설명',
      example: 1
    }
    
    // Request Body
    #swagger.requestBody = { ... };
    
    // 성공 응답
    #swagger.responses[200] = { ... };
    
    // 실패 응답
    #swagger.responses[404] = { ... };
  **/*
  
  *// 실제 로직...*
};
```

### RequestBody 예시

```jsx
#swagger.requestBody = {
  required: true,
  content: {
    "application/json": {
      schema: {
        type: "object",
        required: ["필수필드1", "필수필드2"],
        properties: {
          email: { 
            type: "string", 
            format: "email", 
            example: "user@example.com" 
          },
          password: { 
            type: "string", 
            format: "password", 
            example: "pass123" 
          }
        }
      }
    }
  }
};
```

### 성공 응답 예시

```jsx
#swagger.responses[200] = {
  description: "성공 설명",
  content: {
    "application/json": {
      schema: {
        type: "object",
        properties: {
          resultType: { type: "string", example: "SUCCESS" },
          error: { type: "object", nullable: true, example: null },
          success: {
            type: "object",
            properties: {
              data: { type: "string", example: "응답 데이터" }
            }
          }
        }
      }
    }
  }
};
```

### 실패 응답 예시

```jsx
#swagger.responses[404] = {
  description: "리소스를 찾을 수 없음",
  content: {
    "application/json": {
      schema: {
        type: "object",
        properties: {
          resultType: { type: "string", example: "FAIL" },
          error: {
            type: "object",
            properties: {
              errorCode: { type: "string", example: "U001" },
              reason: { type: "string", example: "오류 메시지" },
              data: { type: "object" }
            }
          },
          success: { type: "object", nullable: true, example: null }
        }
      }
    }
  }
};
```

## Components (재사용 스키마)

index.js의 doc.components.schemas에 정의:

```jsx
components: {
  schemas: {
    SuccessResponse: {
      type: "object",
      properties: {
        resultType: { type: "string", example: "SUCCESS" },
        error: { type: "object", nullable: true, example: null },
        success: { type: "object" }
      }
    },
    ErrorResponse: {
      type: "object",
      properties: {
        resultType: { type: "string", example: "FAIL" },
        error: { type: "object" },
        success: { type: "object", nullable: true, example: null }
      }
    }
  }
}
```

**사용 방법:**

```jsx
#swagger.responses[500] = {
  description: "서버 오류",
  content: {
    "application/json": {
      schema: {
        $ref: "#/components/schemas/ErrorResponse"
      }
    }
  }
};
```

## API별 적용

### 1. 회원가입 (POST /api/v1/users/signup)

![image.png](attachment:2d36c792-002d-4068-a1da-09c9768169b3:image.png)

![image.png](attachment:5a9f45be-719d-4a28-a459-2daa9ac94295:image.png)

- Request Body: email, password, nickname, gender, birth_date, address_main, preferences
- 200 Success: 회원가입 성공
- 409 Conflict: 이메일/닉네임 중복 (U001, U002)
- 500 Error: 서버 오류

### 2. 가게 추가 (POST /api/v1/regions/:regionId/stores)

![image.png](attachment:842f21c9-edb3-48dc-8c84-055a49bc583e:image.png)

![image.png](attachment:ef16fb13-3806-433a-bccb-3c8a25f75d4e:image.png)

- Path Parameter: regionId
- Request Body: name, address
- 201 Created: 가게 추가 성공
- 404 Not Found: 지역 없음 (R001)

### 3. 리뷰 추가 (POST /api/v1/stores/:storeId/reviews)

![image.png](attachment:ca25b182-7ebb-411e-8354-8333e497a937:image.png)

![image.png](attachment:04d0f6df-0f54-4ee2-8e92-05e747abe55e:image.png)

- Path Parameter: storeId
- Request Body: rating (1-5), content
- 201 Created: 리뷰 추가 성공
- 404 Not Found: 가게 없음 (S001)

### 4. 미션 추가 (POST /api/v1/stores/:storeId/missions)

![image.png](attachment:9ae212ff-72bf-45a9-bfe4-089a15d2e450:image.png)

![image.png](attachment:fad9fb9a-23b7-4e1d-9020-ed5ff44399c2:image.png)

- Path Parameter: storeId
- Request Body: title, description, reward_points
- 201 Created: 미션 추가 성공
- 404 Not Found: 가게 없음 (S001)

### 5. 미션 도전 (POST /api/v1/missions/:missionId/challenge)

![image.png](attachment:00b6ff7b-a6fb-475a-90aa-dd353b1e3245:image.png)

![image.png](attachment:b8a0e43c-57b7-40f1-aa86-3b2fce2e6a1c:image.png)

- Path Parameter: missionId
- 201 Created: 미션 도전 시작
- 404 Not Found: 미션 없음 (M001)
- 409 Conflict: 이미 도전 중 (M002)