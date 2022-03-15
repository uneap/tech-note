## Repository

Entity에 있는 데이터를 조회, 저장, 변경, 삭제 할 때 Repository 인터페이스를 정의해 데이터를 사용.

Repository는 내부적으로 EntityManager가 직접 Entity의 데이터를 관리하고 있음.

따라서 Repository를 사용한다면, EntityManager를 사용해 데이터를 처리하지 않아도 됨.