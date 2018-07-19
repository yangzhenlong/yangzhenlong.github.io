# base64转换
## 字节码转base64
```java
Base64.Encoder encoder = Base64.getEncoder();
String base64Str = encoder.encodeToString(bytes);
```
## base64转字节码
```java
Base64.Decoder decoder = Base64.getDecoder();
byte[] bytes = decoder.decode(base64Str);
```