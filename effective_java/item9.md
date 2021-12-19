## 아이템 9. try-finally보다는 try-with-resources를 사용하라

- 자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다.
  - InputStream, OutputStream, java.sql.Connection 등
- 자원 닫기는 클라이언트가 놓치기 쉬워서 예측할 수 없는 성능 문제로 이어지기도 한다.



### 기존의 방식은 try-finally

```java
static String firstLineOfFile(String path) throws IOException {
	BufferedReader br = new BufferedReader(new FileReader(path));
	try {
		return br.readLine();
	} finally {
		br.close();
	}
}
```

- 자원을 하나 더 사용하는 경우에는 try-finally 방식은 지저분 해 질 수 있다.

```java
static void copy(String src, String dst) throws IOException {
	InputStream in = new FileInputStream(src);
	try {
		OutputStream out = new FileOutputStream(dst);
		try {
			byte[] buf = new byte[BUFFER_SIZE];
			int n;
			while ((n = in.read(buf) >= 0))
				out.write(buf, 0, n);
		} finally {
			out.close();
		}
	} finally {
    in.close();
  }
}
```



### try-with-resource

- AutoCloseble 인터페이스 구현하기
- Try-with-resource의 사용예시는 아래와 같다.

```java
static String firstLineOfFile(String path) throws IOException {
	try (BufferedReader br = new BufferedReader(
					new FileReader(path))) {
          return br.readLine();
	}
}
```



- 위에서 작성한 copy 메소드에 try-with-resources를 적용하면 아래와 같다.

```java
static void copy(String src, String dst) throws IOException {
	try (InputStream in = new FileInputStream(src);
			OutputStream out = new FileOutputStream(dst)) {
				byte[] buf = new byte[BUFFER_SIZE];
				int n;
				while ((n = in.read(buf)) >= 0)
					out.write(buf, 0, n);
			}
}
```



- catch절을 통해 try문을 중첩하지 않고도 다수의 예외처리가 가능하다.

```java
static String firstLineOfFile(String path, String defaultVal) {
	try (BufferedReader br = new BufferedReader(new FileReader(path))) {
			return br.readLine();
	} catch (IOException e) {
		return defaultVal;
	}
}
```

