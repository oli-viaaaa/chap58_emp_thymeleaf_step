[chap58_emp_thymeleaf_step 특이사항]

 1. 기존 프로젝트 복사
  - chap56_emp_thymeleaf 프로젝트를 복사해서 만듦.
  - 기존 프로젝트를 복사해서 하나씩 관련 내용을 추가해서 제작
  - 이 프로젝트의 완성본은 chap57_thymeleaf_layout 
  
 2. 의존성
  - 타임리프 템플릿 사용 의존성 추가
  
	<!-- 타임리프 템플릿 사용 의존성 thymeleaf-layout-dialect -->
	<dependency>
	    <groupId>nz.net.ultraq.thymeleaf</groupId>
	    <artifactId>thymeleaf-layout-dialect</artifactId>
	</dependency> 	
 
 3. 기존 프로젝트의 vo 다음 세개 내용 수정
  - EmployeeCommonDto
  - Criteria
  - PageDto
  
 4. 매퍼 수정
 <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
	 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<!-- 매퍼XML : Employees 에 대한 모든 쿼리문 보관 -->
	<mapper namespace="com.javalab.board.dao.EmployeeDao">

	<!-- 사원 목록 조회(페이징 + 검색 기능 추가) -->
	<select id="getEmployeesList" parameterType="EmployeeCommonDto" 
        						resultType="EmployeeCommonDto">
		select e.employee_id, 
			e.first_name, 
			e.last_name, 
			e.email, 
			e.phone_number, 
			e.hire_date, 
			e.salary,
			e.job_id, 
			j.job_title, 
			e.department_id, 
			d.department_name,
			e1.first_name as manager_name,
			d.location_id, 
			l.state_province, 
			l.city,
			l.country_id, 
			c.country_name
		from employees e
		left outer join jobs j on e.job_id = j.job_id
		left outer join departments d on e.department_id = d.department_id
		left outer join locations l on d.location_id = l.location_id
		left outer join countries c on l.country_id = c.country_id
		left outer join employees e1 on e.manager_id = e1.employee_id
		where 1=1
	    <if test="searchText != null">
	        and (e.first_name like concat('%', #{searchText}, '%')
	            or e.last_name like concat('%', #{searchText}, '%')
	            or d.department_name like concat('%', #{searchText}, '%')
	        )
	    </if>
	    order by e.employee_id
	    limit #{offset}, #{amount}
	</select>

	 <!-- 사원한명 조회 -->
	 <select id="getEmployees" parameterType="int" 
	 						resultType="EmployeeCommonDto">
	 	select employee_id, 
			first_name, 
			last_name, 
			email, 
			phone_number, 
			hire_date, 
			job_id, 
			salary, 
			commission_pct, 
			manager_id, 
			department_id  
	 	from employees 
 		where employee_id = #{param1}		 
	 </select>



	<!-- 사원 등록 -->
	<!-- MariaDB는 자동증가 컬럼에 값을 저장할 경우 
		.useGeneratedKeys, keyProperty 옵션 설정해야
		.날짜 저장시 STR_TO_DATE(#{hireDate}, '%Y-%m-%d') -->
    <insert id="register" parameterType="Employees" 
    		useGeneratedKeys="true" keyProperty="employeeId">
    <![CDATA[
        insert into employees(
        	first_name,
        	last_name,
        	email,
        	phone_number,
        	hire_date,
        	job_id,
        	salary,
        	commission_pct,
        	manager_id,
        	department_id) 
		values (
			  #{firstName, jdbcType=VARCHAR}
			, #{lastName, jdbcType=VARCHAR}
			, #{email, jdbcType=VARCHAR}
			, #{phoneNumber, jdbcType=VARCHAR}
			, STR_TO_DATE(#{hireDate}, '%Y-%m-%d')
			, #{jobId, jdbcType=VARCHAR}
			, #{salary, jdbcType=NUMERIC }
			, #{commissionPct, jdbcType=NUMERIC }
			, #{managerId, jdbcType=NUMERIC}
			, #{departmentId, jdbcType=NUMERIC}
		)
	]]>	 
    </insert>	 

	<!-- 전체 사원수 -->
	<select id = "getTotalEmployees" parameterType="Criteria" resultType="int">
		select count(*)
		from employees e
		where 1=1
		<if test="searchText != null">
			and (e.first_name like '%'||#{searchText}||'%' 
				 or e.last_name like '%'||#{searchText}||'%')
		</if>		
	</select>
	
	<!-- 전체 사원수 -->
	<select id = "getTotalPages" parameterType="Criteria" resultType="int">
		select count(*)
		from employees e
		where 1=1
		<if test="searchText != null">
			and (e.first_name like '%'||#{searchText}||'%' 
				 or e.last_name like '%'||#{searchText}||'%')
		</if>		
	</select>

	 <!-- 부서 목록 조회 -->
	 <select id="getDepartmentList" resultType="Department">
	 <![CDATA[
		select department_id, department_name
		from departments	
	]]>	 
	 </select>

	 <!-- 직업(job) 목록 조회 -->
	 <select id="getJobList" resultType="Job">
	 <![CDATA[
		select 	
			job_id, 
			job_titlE
		FROM JOBS	
	]]>	 
	 </select>
</mapper>

5. 부트스트랩 사용을 위한 정적자원(CSS, JS)추가
  - static.zip 파일 전송
 - 위 파일을 압축 해제하고 static 폴더 아래에 복사
 
6. templates 추가
 1) 템플릿으로 사용할 layout.html
 2) 컨텐츠 페이지로 사용할 qlist.html
 3) 상세보기는 미구현  
 
7. 컨트롤러 부분
 - HomeController
 - EmployeeController

8. Service/Dao Layer

 [1차 파편화 끝]
 
1. 서비스 변경
 

2. Dao 