## Spring Boot - Spring Data REST
**參考網址：https://juejin.cn/post/6858416358678331405**
### 什麼是Spring Data REST
>Springboot + Spring MVC大大簡化了Web應用的RESTful開發，而Spring Data REST更簡單。Spring Data REST是建立在Data Repository之上的，它能直接把resository以HATEOAS風格暴露成Web服務，而不需要再手寫Controller層
>>`HATEOAS，即Hypermedia as the Engine of Application State，它是一種更成熟的REST模型，在資源的表達中包含了鏈接信息，客戶端可以根據鏈接來發現可執行的動作`
### RESTful API
>REST是一種設計風格(與具體的語言無關)，它的URL主體是資源，是個名詞。而且也僅支持HTTP協議，規定了使用HTTP Method表達本次要做的動作，類型一般也不超過那四五種。這些動作表達了對資源僅有的幾種轉化方式
>>常用的HTTP動詞有下面五個（括號裡是對應的SQL命令）。
>>
>>* GET（SELECT）：從服務器取出資源（一項或多項）。
>>* POST（CREATE）：在服務器新建一個資源。
>>* PUT（UPDATE）：在服務器更新資源（客戶端提供改變後的完整資源）。
>>* PATCH（UPDATE）：在服務器更新資源（客戶端提供改變的屬性）。
>>* DELETE（DELETE）：從服務器刪除資源。

**添加相關依賴項，`spring-boot-starter-data-rest`為本次演示重點項目**
```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- Add dependency for Spring Data REST -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-rest</artifactId>
</dependency>
		
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-devtools</artifactId>
	<scope>runtime</scope>
	<optional>true</optional>
</dependency>
		
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<scope>runtime</scope>
</dependency>
		
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<scope>test</scope>
</dependency>
```

**Employee.java程式碼，創建實體類別**
```
@Entity
@Table(name="employee")
public class Employee {

	// define fields
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="first_name")
	private String firstName;
	
	@Column(name="last_name")
	private String lastName;
	
	@Column(name="email")
	private String email;
	
	
	// define constructors
	public Employee() {
		
	}


	public Employee(String firstName, String lastName, String email) {
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
	}
	
	// define getter/setter
	以下省略...
}
```

**application.properties屬性配置文件，可於此文件中配置例如：根結點名稱、Page頁面大小等等，詳細可參考官方文檔**
>https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.data
```
#
# JDBC properties
#
spring.datasource.url=jdbc:mysql://localhost:3306/employee_directory?useSSL=false&serverTimezone=UTC
spring.datasource.username=hbstudent
spring.datasource.password=hbstudent

#
# Spring Data REST properties
#
spring.data.rest.base-path=/magic-api
spring.data.rest.default-page-size=20
```

**EmployeeRepository.java，創建DAO層，透過擴展JpaRepository類別實現**
>Spring Data REST提供的公開端點為複數型式(給定的實體名稱+s以及第一個字母小寫)，但僅支持一般複數英文單字，非一般複數型式可透過`@RepositoryRestResource`自訂端點名稱
>
>註解RepositoryRestResource是Data REST用於暴露Repository，path為訪問路徑，設置為user，則訪問地址為http://localhost:8080/user
```
@RepositoryRestResource(path="members")
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {

	// that's it ... no need to write any code
}
```
![image](https://user-images.githubusercontent.com/101872264/223433648-4c14f670-a92b-4839-96f8-db0d8373915c.png)
