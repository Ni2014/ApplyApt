## ApplyAPT

1. 基本思路：自定义注解并自定义注解处理器，编译期生成代码；
2. 使用场景：很多库或framework都大量用了反射，这些都是会在runtime影响性能的点，用编译器提前生成代码即可调用，规避了这个问题；
3. 问题：

* javax
    要定义为javaLib而不是AndroidLib
* how to GenCode
    自己手写或用成熟的**javapoet库**
* 依赖注解库or配置
```java
    implementation 'com.google.auto.service:auto-service:1.0-rc2'
    implementation 'com.squareup:javapoet:1.9.0'
    
    implementation project(':process')
    annotationProcessor project(':process')
```
4. 核心代码：

```java

            MethodSpec creaedMethod = MethodSpec.methodBuilder("method1")
                    .addModifiers(Modifier.PUBLIC, Modifier.FINAL)
                    .returns(void.class)
                    .addParameter(String[].class, "parameters")
                    .addStatement("System.out.println($S)", "this`s java source is created by dynamic")
                    .build();


            TypeSpec createdClass = TypeSpec.classBuilder("CodeGeneratorTemp")
                    .addModifiers(Modifier.PUBLIC, Modifier.FINAL)
                    .addMethod(creaedMethod)
                    .build();
            JavaFile javaFile = JavaFile.builder("nothing.advance.apt", createdClass).build();

            try {
                javaFile.writeTo(processingEnv.getFiler());
            } catch (IOException e) {
                e.printStackTrace();
            }
        }


```

5. 生成代码效果：



![image](http://bmob-cdn-20286.b0.upaiyun.com/2018/12/21/e42c751640c3e43880231d091e1761b7.png)
