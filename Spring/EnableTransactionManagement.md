
# @EnableTransactionManagement


### 1. @EnableTransactionManagement란?
 Spring의 Annotation 기반의 트랜잭션 관리 기능을 사용할 때 쓰인다. 
 xml 기반의 **tx:annotation-driven'**과 유사한 기능이다.

proxy, aspectJ, aop동작기반 등을 지원하여 @Transactional을 선언한 곳에서 이러한 기능들을 지원해준다.


@EnableTransactionManagement 소스
~~~java 
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import({TransactionManagementConfigurationSelector.class})
public @interface EnableTransactionManagement {
    boolean proxyTargetClass() default false;

    AdviceMode mode() default AdviceMode.PROXY;

    int order() default 2147483647;
}
~~~

### 2. tx:annotation-driven기반과의 차이점
tx:annotation은 default로 bean 이름이 transactionManager를 찾도록 되어있다. 반면에 @EnableTransactionManagement는 이런 default bean 이름과 상관없이 사용자가 설정한 name에 따라 설정이 가능하다. 

### 3. 설정

~~~java
@Configuration
@EnableJpaRepositories(basePackageClasses = MemberManagementJpaRepo.class)
@EnableTransactionManagement
@PropertySource("classpath:jpa.properties")
public class JpaConfig  {
    
    @Autowired
    private Environment env;

    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource dataSource) {
        LocalContainerEntityManagerFactoryBean emf = new LocalContainerEntityManagerFactoryBean();
        emf.setJpaVendorAdapter(jpaVendorAdapter());
        emf.setDataSource(dataSource);
        emf.setPersistenceUnitName("persistenceJpa");
        emf.setPackagesToScan("com.domain.*");
        emf.setJpaProperties(additionalProperties());
        return emf;
    }

    private JpaVendorAdapter jpaVendorAdapter(){
        HibernateJpaVendorAdapter jpaVendorAdapter = new HibernateJpaVendorAdapter();
        jpaVendorAdapter.setShowSql(true);
        return jpaVendorAdapter;
    }


    @Bean
    public PlatformTransactionManager jpaTransactionManager(EntityManagerFactory entityManagerFactory) {
        JpaTransactionManager jpaTransactionManager = new JpaTransactionManager();
        jpaTransactionManager.setEntityManagerFactory(entityManagerFactory);
        return jpaTransactionManager;
    }



    private Properties additionalProperties() {
        Properties properties = new Properties();
        properties.setProperty("spring.jpa.hibernate.ddl-auto", env.getProperty("ddl-auto"));
        properties.setProperty("spring.jpa.show-sql", env.getProperty("show-sql"));
        properties.setProperty("spring.jpa.properties.hibernate.format_sql",  env.getProperty("format_sql"));
        return properties;
    }
~~~

~~~java
@Service
@Transactional
public class MemberManagementImpl implements MemberManagementService {
        //생략....
}

~~~
 

### 3. 테스트 결과 
 Bean DI 테스트 : 성공

~~~JAVA
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = RootAppConfig.class)
public class ApplicationContextConfigTest {

    @Autowired
     private PlatformTransactionManager jpaTransactionManager;

      @Test
    public void rootContextDependencyInjection(){
        Assert.assertEquals(jpaTransactionManager.getClass(), new JpaTransactionManager().getClass());

    }
}
~~~



