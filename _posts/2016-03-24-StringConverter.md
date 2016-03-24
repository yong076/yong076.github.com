---
published: false
---

## Retrofit2 에서의 String Converter

Retrofit을 사용 하면서. Json데이타들을 위한 DataModel을 만들고.. 그 만든 모델을 전부 이어 붙여 파싱하는 과정에서의 불필요한 데이터. (예를들어 나는 몇몇개의 데이터가 필요 하지 않는데에도 모델을 만들어 써야하는 불편함..)들 모두 파싱해야 한다는 부담이 있어서.

Gson이나 다른 JsonParsing을 위한 라이브러리들을 그렇게 좋아하진 않는 편이다.

직접 Raw Response를 Json으로 바꾸어, 직접 한땀한땀 파싱하는 것을 더 좋아 하는 편이라. 
Retrofit 같은 Gson에 최적화된것들을 사용 할때에 힘든 과정을 겪었었다.

Retrofit 2.0 베타 버전 까지는 만들어 두었던 String Converter가 정상 작동하여 잘 사용 하였지만.

Stable버전이 나온 뒤로는 Converter.Factory의 변경으로 약간 수정을 가하였다.

``` {.java}

public class StringConverter extends Converter.Factory {
    private static final MediaType MEDIA_TYPE = MediaType.parse("text/plain");

    @Override
    public Converter<?, RequestBody> requestBodyConverter(Type type, Annotation[] parameterAnnotations, Annotation[] methodAnnotations, Retrofit retrofit) {
        if (String.class.equals(type)) {
            return new Converter<String, RequestBody>() {
                @Override
                public RequestBody convert(String value) throws IOException {
                    return RequestBody.create(MEDIA_TYPE, value);
                }
            };
        }
        return super.requestBodyConverter(type, parameterAnnotations, methodAnnotations, retrofit);
    }

    @Override
    public Converter<ResponseBody, ?> responseBodyConverter(Type type, Annotation[] annotations, Retrofit retrofit) {
        if (String.class.equals(type)) {
            return new Converter<ResponseBody, String>() {
                @Override
                public String convert(ResponseBody value) throws IOException {
                    return value.string();
                }
            };
        }
        return super.responseBodyConverter(type, annotations, retrofit);
    }
}
```