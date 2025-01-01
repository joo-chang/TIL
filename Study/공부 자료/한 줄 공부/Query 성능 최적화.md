논문 제목: Query Optimization Techniques - Tips For Writing Efficient And Faster SQL Queries   
저자 : Jean HABIMANA   
출판된 저널/컨퍼런스명: INTERNATIONAL JOURNAL OF SCIENTIFIC & TECHNOLOGY RESEARCH

### **[ Tip #1: Use Column Names Instead of * in a SELECT Statement ]**

> SELECT 문 사용 시 전체 조회 보다 열 이름을 사용해라

테이블에서 몇 개의 컬럼만을 조회하는 경우라면, SELECT를 사용할 필요가 없다. 비록 이것이 적기 편할 수 있지만, 쿼리를 완료하기 위해서 더 많은 시간을 필요로 한다. 필요한 일부 컬럼만을 선택함으로써 결과 테이블의 크기를 줄이고 네트워크 트래픽을 감소시킴으로써 쿼리의 평균 속도를 높일 수 있다.

- Original: SELECT * FROM SH.Sales;
- Improved: SELECT s.PROD_ID FROM SH.Sales s;
- 27% Time Reduction

![[Pasted image 20240603233004.png]]

### **[ Tip #2: Avoid including HAVING clause in SELECT statements ]**

> SELECT 문에 HAVING 절을 포함하지 마라

Having 절은 모든 열이 선택된 이후에 필터를 위해 사용된다. SELECT 문에서는 HAVING절이 불필요하다. 이것은 최종 테이블에서 모든 열들을 파싱하면서 HAVING 조건에 충족되지 않는 열들을 필터링한다.

- Original: SELECT s.cust_id, count(s.cust_id) FROM SH.Sales s GROUP BY s.cust_id HAVING s.cust_id != '1660' AND s.cust_id != '2
- Improved: SELECT s.cust_id, count(s.cust_id) FROM SH.Sales s WHERE s.cust_id != '1660' AND s.cust_id != '2' GROUP BY s.cust_id;
- 31% Time Reduction

![[Pasted image 20240603233011.png]]

### **[ Tip #3: Eliminate Unnecessary DISTINCT Conditions ]**

> 불필요한 DISTINCT 는 제거해라

아래의 예제와 같이 테이블은 Primary Key를 가지고 있기 때문에 DISTINCT를 사용할 필요가 없으므로 제거한다. DISTINCT를 사용하면 정렬하는 과정이 들어가기 때문에, Query의 속도가 상당히 저하된다.

- Original: SELECT DISTINCT * FROM SH.Sales s JOIN SH.Customer c ON s.cust_id = c.cust_id WHERE c.cust_marital_status = 'single';
- Improved: SELECT * FROM SH.Sales s JOIN SH.Customer c ON s.cust_id = c.cust_id WHERE c.cust_marital_status = 'single';
- 85% Time Reduction

![[Pasted image 20240603233016.png]]

### **[ Tip #4: Un-nest subqueries ]**

> 중첩된 쿼리를 해제하는 것이 효과적이다.

중첩된 쿼리를 조인조건으로 재작성하는 것은 효율적인 실행과 효과적인 최적화를 불러일으킨다. 일반적으로 서브쿼리를 푸는 것은 ANY, ALL, EXISTS 등이 사용되는 한개의 테이블에서 처리된다.

- Original: SELECT * FROM SH.products p WHERE p.prod_id = (SELECT s.prod_id FROM SH.sales s WHERE s.cust_id = 100996 AND s.quantity_sold = 1);
- Improved: SELECT p.* FROM SH.products p, sales s WHERE p.prod_id = s.prod_id AND s.cust_id = 100996 AND s.quantity_sold = 1;
- 61% Time Reduction

![[Pasted image 20240603233023.png]]

### **[ Tip #5: Consider using an IN predicate when querying an indexed column ]**

> 인덱스가 지정된 열을 조회할 때 IN절을 사용해라

IN-list는 index된 검색을 위해 활용될 수 있으며, Optimizer는 Index의 정렬 순서와 일치하도록 IN-list를 정렬하여 보다 효율적인 검색을 수행할 수 있다. IN-list에는 상수 또는 쿼리 블록이 실행되는 동안 일정한 값만 포함해야 한다.

- Original: SELECT s.* FROM SH.sales s WHERE s.prod_id = 14 OR s.prod_id = 17;
- Improved: SELECT s.* FROM SH.sales s WHERE s.prod_id IN (14, 17);
- 73% Time Reduction

![[Pasted image 20240603233031.png]]

### **[ Tip #6: Use EXISTS instead of DISTINCT when using table joins that involves tables having one-to-many relationships ]**

> 1대다 관계의 테이블을 조인 하는 경우 DISTINCT 대신 EXISTS를 사용해라

DISTINCT 키워드는 테이블의 모든 열을 선택한 후에 중복되는 것들을 파싱하는 형태로 작동한다. 만약 서브쿼리와 함께 EXISTS키워드를 사용한다면, 전체 테이블을 조회하는 것을 피할 수 있다.

- Original: SELECT DISTINCT c.country_id, c.country_name FROM SH.countries c, SH.customers e WHERE e.country_id = c.country_id;
- Improved: SELECT c.country_id, c.country_name FROM SH.countries c WHERE EXISTS (SELECT 'X' FROM SH.customers e WHERE e.country_id = c.country_id);
- 61% Time Reduction

![[Pasted image 20240603233035.png]]

### **[ Tip #7: Try to use UNION ALL in place of UNION ]**

> 중복 검사를 피하기 위해 UNION ALL을 사용해라

UNION 문은 중복된 열의 존재 유무에 상관없이 열을 선택할 때 중복 검사를 하지만 UNION ALL은 중복검사를 하지 않으므로, UNION 보다는 UNION ALL을 사용하는 것이 빠르다.

- Original: SELECT cust_id FROM SH.sales UNION SELECT cust_id FROM customers;
- Improved: SELECT cust_id FROM SH.sales UNION ALL SELECT cust_id FROM customers;
- 81% Time Reduction

![[Pasted image 20240603233041.png]]

### **[ Tip #8: Avoid using OR in join conditions ]**

> 조인 조건에서 OR 사용 피하기

조인 조건에 'OR'을 사용할 때마다 쿼리는 최소한 2배 이상 느려진다. OR문을 사용하는 경우에는 Index를 활용한 검색을 하지 못하고, Full-Scan을 하기 때문이다.

- Original: SELECT * FROM SH.costs c INNER JOIN SH.products p ON c.unit_price = p.prod_min_price OR c.unit_price = p.prod_list_price;
- Improved: SELECT * FROM SH.costs c INNER JOIN SH.products p ON c.unit_price = p.prod_min_price UNION ALL SELECT * FROM SH.costs c INNER JOIN SH.products p ON c.unit_price = p.prod_list_price;
- 70% Time Reduction

![[Pasted image 20240603233047.png]]

### **[ Tip #9: Avoid functions on the right hand side of the operator ]**

> 컬럼에 함수를 적용하거나 연산자를 통해 비교 시 함수를 사용하는 것을 지양

Functions 또는 Methods는 SQL 쿼리에서 자주 함께 사용된다. 집계 함수 기능을 제거하여 쿼리를 재작성하는 것은 성능을 상당히 높여줄 것이다.

- Original: SELECT * FROM SH.sales WHERE EXTRACT (YEAR FROM TO_DATE (time_id, 'DD-MON-RR')) = 2001 AND EXTRACT (MONTH FROM TO_DATE (time_id, 'DD-MON-RR')) = 12;
- Improved: SELECT * FROM SH.sales WHERE TRUNC (time_id) BETWEEN TRUNC(TO_DATE('12/01/2001', 'mm/dd/yyyy')) AND TRUNC (TO_DATE ('12/30/2001', 'mm/dd/yyyy'));
- 70% Time Reduction

![[Pasted image 20240603233056.png]]

### **[ Tip #10: Remove any redundant mathematics ]**

> 중복된 연산 제거하기

SQL 문을 작성하다 보면 수학 연산을 수행해야할 때가 있다. 수학연산은 매번 열을 찾아서 연산을 다시 하므로, 부적절하게 사용된다면 성능을 상당히 저하시킬 것이다. 따라서 SQL문에서 불필요한 수학 연산을 제거하는 것은 속도를 빠르게 할 것이다.

- Original: SELECT * FROM SH.sales s WHERE s.cust_id + 10000 < 35000;
- IImproved: SELECT * FROM SH.sales s WHERE s.cust_id < 25000;
- 11% Time Reduction

![[Pasted image 20240603233100.png]]
