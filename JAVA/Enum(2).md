# Enum(2)

업무 중에 enum을 사용하여 기능을 구현하는 부분이 있었다. 데이터베이스에서 가져온 값을 enum 클래스 내부에 valueOf 메서드를 만들어
해당 값이 enum 데이터에 있는지 확인하여 enum값을 받는 기능을 수행하는 부분이었다. 

~~~java 

public enum ProductCategory {
    SPORTS("스포츠"), MUSIC("음악"), COOK("요리");
    
    private String productCategoryName;
    
    ProductCategory(String productCategoryName){
        this.productCategoryName = productCategoryName;
    }
    
    public String getProductCategoryName(){
        return productCategoryName;
    }
    
    public static ProductCategory productCategoryValueOf(String productCategoryName){
        for(ProductCategory p : ProductCategory.values()){
            if(p.getProductCategoryName().equals(productCategoryName)){
                return p;
            }
        }
        throw new IllegalArgumentException();
    }
}
~~~

이렇게 productCategoryValueOf 라는 메서드를 정의하여 enum을 리턴하는 형식으로 처음에 만들었는데,
이렇게 for문으로 구현하고 나니, 데이터베이스에서 값을 가져올 때마다 enum의 데이터 크기 만큼 비교해서 
성능이 저하 될 것 같은 생각이 들었다.

이를 해결하기위해 static 구문을 사용하여 캐시할 Map을 형성 한 뒤 해당 Map에서 한번에 가져올 수 있게끔 
수정하였다.  

~~~java
public enum ProductCategory {
    SPORTS("스포츠"), MUSIC("음악"), COOK("요리");
    
    private String productCategoryName;
    
    private static final Map CACHE_MAP = new HashMap<>();

    static{
        for(ProductCategoryEnum p : ProductCategoryEnum.values()){
            CACHE_MAP.put(p.getProductCategoryName(), p);
        }
    }

    //생략.... 
    
    public static ProductCategoryEnum productCategoryValueOf(String productCategoryName){
        return CACHE_MAP.get(productCategoryName);
    }
}
~~~

이렇게 static 구문을 사용하여 static Map 자료구조를 만들어 `valueOf`를 사용시에 단 한번의 비교로
`enum` 데이터를 가져올 수 있도록 하였다. 


