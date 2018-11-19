---
layout: post
title:  "Android APT 动态注解处理笔记"
date:   2018-9-11
categories: notes tcp/ip
---

创建项目AnnotationTester,包名com.viator42    
由于Android对新版jdk的支持不完全,这里强制使用1.7版java    

## Module annotation

__新建一个名称为annotation的Java Library__

主要放置一些项目中需要使用到的Annotation和关联代码

    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.CLASS)
    public @interface TesterAnnotation {
        String name() default "undefined";
        String text() default "";
    }

配置build.gradle，主要是规定jdk版本

    apply plugin: 'java'

    sourceCompatibility = 1.7 
    targetCompatibility = 1.7 

    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
    }

## module compiler

__创建一个名为compiler的Java Library，这个类将会写代码生成的相关代码__    

配置build.gradle

    apply plugin: 'java'
    sourceCompatibility = 1.7 
    targetCompatibility = 1.7 
    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        compile 'com.google.auto.service:auto-service:1.0-rc2'
        compile 'com.squareup:javapoet:1.7.0'
        compile project(':annotation')
    }

1. 定义编译的jdk版本为1.7，这个很重要，不写会报错。    
2. AutoService 主要的作用是注解 processor 类，并对其生成 META-INF 的配置信息。    
3. JavaPoet 这个库的主要作用就是帮助我们通过类调用的形式来生成代码。    
4. 依赖上面创建的annotation Module。    

定义Processor类    
生成代码相关的逻辑就放在这里。    

    @AutoService(Processor.class)
    public class TestingAnnnotationProcessor extends AbstractProcessor {

        @Override
        public synchronized void init(ProcessingEnvironment processingEnv) {
            super.init(processingEnv);


        }

        @Override
        public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {

            MethodSpec main = MethodSpec.methodBuilder("main")
                    .addModifiers(Modifier.PUBLIC, Modifier.STATIC)
                    .returns(void.class)
                    .addParameter(String[].class, "args")
                    .addStatement("$T.out.println($S)", System.class, "Hello, JavaPoet!")
                    .build();
            TypeSpec helloWorld = TypeSpec.classBuilder("HelloWorld")
                    .addModifiers(Modifier.PUBLIC, Modifier.FINAL)
                    .addMethod(main)
                    .build();
            JavaFile javaFile = JavaFile.builder("com.viator42.annotationtester", helloWorld)
                    .build();
            try {
                javaFile.writeTo(processingEnv.getFiler());
            } catch (IOException e) {
                e.printStackTrace();
            }

            return false;
        }

        @Override
        public Set<String> getSupportedAnnotationTypes() {
            return Collections.singleton(TesterAnnotation.class.getCanonicalName());
        }

        @Override
        public SourceVersion getSupportedSourceVersion() {
            return SourceVersion.RELEASE_7;
        }
    }

build.gradle配置

    apply plugin: 'java-library'

    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation 'com.google.auto.service:auto-service:1.0-rc2'
        implementation 'com.squareup:javapoet:1.7.0'
        implementation project(':annotation')
    }

    sourceCompatibility = "7"
    targetCompatibility = "7"

## 在app中使用

配置项目根目录的build.gradle

    dependencies {
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
    }

app module的build.gradle配置

    android {
        ...
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_7
            targetCompatibility JavaVersion.VERSION_1_7
        }
    }

    dependencies {
        ...
        apply plugin: 'com.android.application'

        implementation project(':annotation')
        annotationProcessor project(':compiler')
    }

对一个类添加注解

    @TesterAnnotation(
            name="viator42",
            text="hello viator42"
    )
    public class MainActivity extends AppCompatActivity {

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
        }
    }

然后就能在以下路径找到生成的Java Class

    AnnotationTester\app\build\generated\source\apt\debug\com\viator42\annotationtester\HelloWorld.java

--------


