- ORM
    
    **정의:** SQL 쿼리를 직접 작성하는 대신, 익숙한 프로그래밍 언어(예: JavaScript)의 객체와 메소드를 사용하여 데이터베이스를 조작할 수 있도록 매핑해주는 도구(라이브러리).
    
    - **사용 예시:**
        - **Raw SQL:** `SELECT * FROM User WHERE id = 1;`
        - **ORM (Prisma):** `await prisma.user.findUnique({ where: { id: 1 } });`
- Prisma 문서 살펴보기
    - ex. Prisma의 Connection Pool 관리 방법
        
        
    - ex. Prisma의 Migration 관리 방법
        
        **개념:** `schema.prisma` 파일의 변경 이력을 **SQL 파일로 자동 생성**하고, 이력을 기반으로 DB 스키마를 **자동 적용/관리**하는 도구.
        
        **주요 명령어:**
        
        - **`npm exec prisma migrate dev`**: **(개발용)**
            - `schema.prisma`와 실제 DB를 비교하여 변경 사항으로 **새로운 Migration 파일(SQL)을 생성**합니다.
            - 생성된 Migration을 DB에 **즉시 적용**합니다.
            - (주의: 기존 테이블/데이터 삭제 경고가 뜰 수 있음!)
        - **`npm exec prisma migrate deploy`**: **(운영/배포용)**
            - **새로운 Migration을 생성하지 않습니다.**
            - `migrations` 폴더에 이미 존재하는 **모든 Migration 이력**을 순서대로 DB에 적용합니다.
        - **`npm exec prisma migrate reset`**: **(개발용/초기화)**
            - DB를 **초기화(Drop & Create)**합니다. (모든 데이터 삭제!)
            - `migrations` 폴더의 모든 이력을 처음부터 다시 적용
- ORM(Prisma)을 사용하여 좋은 점과 나쁜 점
    
    **장점**
    
    - **생산성 및 가독성:** SQL 문자열보다 객체 기반 코드가 더 **직관적**이고 **가독성**이 높으며, **유지보수**가 쉬워집니다.
    - **개발 편의성:** IDE(편집기)의 **자동 완성(Auto-completion)** 지원을 받을 수 있어 오타를 줄이고 개발 속도를 높일 수 있습니다.
    - **데이터 가공 용이:** DB에서는 최소한의 조회/정렬만 수행하고, 복잡한 데이터 가공 및 조합은 JavaScript **코드 레벨**에서 처리하는 최근 트렌드에 적합합니다.
    
    **단점**
    
    - **학습 곡선:** ORM 자체의 사용법, 개념, 문법(예: `schema.prisma`)을 별도로 학습해야 합니다.
    - **복잡성 증가:**
        - 복잡하고 세밀하게 튜닝된 SQL 쿼리를 ORM으로 표현하기 어려울 수 있습니다.
        - 문제가 발생했을 때 ORM이 생성하는 실제 SQL을 파악하고 디버깅하기 어려울 수 있습니다. (Prisma는 `log: ["query"]` 옵션으로 생성 SQL 확인 가능)
    - **협업 복잡성:** 특히 **Migration** 기능을 팀 단위로 사용할 때, 변경 이력이 꼬이거나 충돌할 위험이 있어 명확한 **팀 규칙**이 필요합니다.