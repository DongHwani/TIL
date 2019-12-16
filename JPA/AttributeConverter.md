# AttributeConverter

### 1. AttributeConverter란

javax.persistence 패키지에 있는 interface이다. Jpa에서 AttributeConverter를 사용하여 
Entity의 필드 데이터타입과 DB 데이터타입을 맵핑할 때 유용하게 사용 할 수 있다.


~~~Java
package javax.persistence;

public interface AttributeConverter<X, Y> {
    Y convertToDatabaseColumn(X var1);

    X convertToEntityAttribute(Y var1);
}
~~~

위의 인터페이스를 확인해보면 Entity 타입을 Column 타입으로, Column타입을 Entity 필드 타입으로 할 수 있게 메서드가 정의 되어 있다. 

### 2. 구현

~~~JAVA
public class RoleAttributeConvertor implements AttributeConverter<List<String>, String> {

    @Override
    public String convertToDatabaseColumn(List<String> attribute) {
        return JsonMappingUtils.toJson(attribute);
    }

    @Override
    public List<String> convertToEntityAttribute(String dbData) {
        if (StringUtils.isBlank(dbData)) {
            throw new IllegalArgumentException("Role Attribute is not founded.");
        }
        return JsonMappingUtils.toObject(dbData, List.class);
    }
}
~~~

~~~JAVA
@Entity
@Table(schema = "base", name = "members")
public class Member{
    
    @Convert(converter= RoleAttributeConvertor.class)
    private List<String> roles;

}
~~~
AttributeConverter 인터페이스를 상속받아 재정의해서 구현한 클래스를 Entity 클래스에 적용할 필드에 **@Convert**를 사용하고 재정의한 converter를 Class 타입으로 명시해주면, 
DB 칼럼과 Entity 필드간의 맵핑이 완료된다. 




