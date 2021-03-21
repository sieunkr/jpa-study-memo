

# JPA 엔티티매핑

## 참고자료 

1. 김영한님 인프런 강의
2. JPA 퀵스타트



 - 데이터베이스 스키마 자동 생성
	 - 개요
		 - DDL 을 애플리케이션 실행 시점에서 자동으로 생성해주는 기능
	 - 속성
		 - create : 기존 테이블 삭제 후 다시 생성 (DROP + CREATE)
		 - create-drop : create 와 같으나 종료 시점에 테이블 DROP
		 - update : 변경된 내용만 반영
		 - validate : 엔티티와 테이블이 정상적으로 매핑되었는지 확인
		 - none : 자동생성 기능을 사용하지 않음
	 - best practice
		 - JPA 에서 생성해준 쿼리를 그대로 운영에서 사용하진 않는다.
			 - 회사 규정에 맞게 적절하게 수정해서 사용
		 - 운영환경에서는 create, create-drop, update 를 사용하지 않는다.
	 - DDL 생성 기능
		 - name, length, unique... 
		 - DDL 을 자동생성할때만 사용되고, JPA 의 실행 로직에는 영향을 주지 않는다.
		 - unique 제약조건은 애플리케이션에 영향을 주진 않음
			 - DDL 생성할때만 영향을 줌
		 - column name 을 설정하는 것도 애플리케이션에 영향을 주지 않음
 - 필드와 컬럼 매핑
	 - 기본 
		 - DDL 생성 시 현재 의존하고 있는 DB 에서 사용가능한 타입중에서, 자바로 선언해준 필드의 타입의 과장 유사한 타입으로, 데이터베이스에 생성을 하는 듯..
			 - 운영에 적용할 때는, 적절하게 DDL z쿼리를 수정해서 사용
	 - @Column
		 - name
			 - DB 컬럼의 이름을 지정
			 - 별도로 지정하지 않으면, 필드 네이빙을 참고해서 설정 됨
		 - insertable, updatable
			 - 등록, 변경 가능여부 설정
			 - updatable = false 로 설정하면, 이 컬럼은 절대 변경되지 않는다. (데이터베이스에서 직접 업데이트는 가능)
		 - unique = true
			 - 한 컬럼에 간단하게 유니크 제약조건을 주고 싶을 때 사용
			 - 단, 제약 조건의 이름이 랜덤으로 생성됨..
				 - uniqueConstrainst 어노테이션 사용 추천
		 - length
		 - columnDefinition
			 - 데이터베이스 컬럼 정보를 직접 설정하는 경우 사용
			 - 예) varchar(100 default 'NORMAL'... 
	 - @Enumerated
		 - 자바 Enum 타입 매핑할 때 사용
		 - 타입
			 - ORDINAL
				 - 기본 값
				 - ENUM 의 순서를 저장 (0, 1, 2 이런 값을 저장..)
			 - STRING
				 - Enum 의 이름을 데이터베이스에 저장
				 - mysql 에 생성하는 DDL 에서는 varchar 으로 저장되는 듯..
				 - ORDINAL 보다는 STRING 사용을 추천함
	 - @Temporal
		 - 날짜 타입을 매핑할 때 사용
		 - LocalDate, LocalDateTime 을 사용할 때는 생략 가능
			 - 최신 하이버네이트에서 지원
	 - @Lob
		 - 큰 컨텐츠를 저장해야하는 경우 사용
		 - 매핑하는 필드의 타입이 문자이면, clob, 그 외에는 brob
	 - @Transient
		 - 필드 매핑이 필요 없는 경우 사용
		 - 데이터베이스에 저장 X, 조회 X
		 - 주로, 메모리에서 임시로 값을 보관해야 하는 경우 사용
 - 기본키 매핑
	 - 방법
		 - 직접 할당
			 - @Id 어노테이션만 사용
		 - 자동 생성
			 - @GeneratedValud 어노테이션 같이 사용
		 - 자동 생성 방법
			 - IDENTITY : 데이터베이스에 위임
			 - SEQUENCE : 데이터베이스 시퀀스 오브젝트 사용 (오라클?)
			 - TABLE : 키 생성용 테이블 사용
			 - AUTO : dialect 설정에 따라서 자동으로 기본값 설정
		 - 권장하는 식별자 전략
			 - 기본키 제약 조건 : null 아님, 유일, 변하지 않는다.
			 - 미래까지 이 조건을 만족시키는 자연키는 거의 없다. 대체키 사용하는 것을 추천
				 - 시퀀스
				 - UUID
				 - AUTO_INCREMENT
				 - 랜덤값 조합
			 - 영속성 컨텍스트에서 관리할려면 무조건PK 값이 있어야 함..
				 - 플러시 하기 전에, 데이터베이스에 저장하는 듯..??
