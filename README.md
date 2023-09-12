# MyJNI
JNI usage and example
  
@참고
- [JAVA - JNI 사용하기](https://mommoo.tistory.com/71)
- [Java JNI 사용법 및 예제](https://hbase.tistory.com/82)

## JNI 란
Java Native Interface(자바 네이티브 인터페이스)의 약어.  
자바 가상 머신위에서 실행되고 있는 자바코드와 운영체제의 고유기능(Native)인 네이티브 코드(C/C++로 작성됨)가 상호작용하는 방법을 정의한 프로그래밍 프레임워크.

## 구현 방법
- 필요한 것 : IDE. C, C++ 을 작성하기 위한 Visual Studio. -> Eclipse, Visual Studio 2022 Community
1. Java 코드 작성하기
```MyJNIProject/jni.JNI.java
package jni;

public class JNI {
	static {
		System.loadLibrary("JNI");
	}
	
	private native int getNumber();
	private native void printHelloWorld();
	
	public static void main(String[] args) {
		JNI jni = new JNI();
		jni.printHelloWorld();
		System.out.println(jni.getNumber());
	}
}
```
2. Java 코드 파일 컴파일, 헤더파일 만들기
- Java 코드 파일 컴파일하기  
Java 코드 파일을 실행해 컴파일 하고, 이때 dll 파일을 찾지 못한다고 오류가 뜰 수 있다.
- 헤더파일 만들기  
프로젝트 폴더의 bin 폴더에서 terminal 을 켜주고 아래 명령어를 실행한다.
``` 
E:\workspace\eclipse\MyJNIProject\bin>javah jni.JNI
```
제대로 컴파일이 됐다면, bin 폴더 어딘가에 패키지명_클래스명.h 파일이 생성된다.

3. Visual Studio 에서 C, C++ 코드로 함수 구현부 작성하기  
      1. Visual Studio 에서 빈 프로젝트를 생성하는데, 추후 dll 파일을 만들 시, 프로젝트 명으로 dll 파일이 만들어진다. JNI 라고 지었다.
      2. 앞에서 생성된 jni_JNI.h 를 헤더파일 폴더에 오른쪽 마우스 클릭을 통해서 추가한다. 나중에 제대로 참조가 안되어 c++ 파일 위치에 복붙도 해주었다.
      3. 프로젝트 속성 변경  
       - JNI 프로젝트의 속성 페이지에서  
       구성 속성 > 일반 > 구성 형식을 동적 라이브러리(.dll) 로 변경한다.  
       소스 파일에 아래의 c++ 파일을 추가한 후,  
      구성 속성 > C/C++ 에서 추가 포함 디렉터리에 {jdk_path}\include 와 {jdk_path}\include\win32 를 추가한다.
      4. c++ 코드 작성  
      이때, 헤더 파일에 있는 함수정보를 복사해서 파라미터를 JNIEnv *  -> JNIEnv *env , jObject -> jObject 로 맞춰주면 된다.
```MyJNI.cpp
#include <stdio.h>
#include <jni.h>
#include "jni_JNI.h"

JNIEXPORT jint JNICALL Java_jni_JNI_getNumber
(JNIEnv *env, jobject jobj) {
  return 3;
}

JNIEXPORT void JNICALL Java_jni_JNI_printHelloWorld
(JNIEnv *env, jobject jobj) {
  printf("Hello World!");
}
```
4. Java 프로젝트에 dll 파일을 넣고 컴파일 한다.
만든 dll파일을 Java 프로젝트 폴더 바로 아래 넣고 컴파일한다.
  
  
  
