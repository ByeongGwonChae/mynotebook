---
title: "python과 COM"
category:
  - python
tag:
  - python
toc: true
toc_sticky: true
---

# COM(Component Object Model)
마이크로소프트에서 개발한 기술. 여러 컴포넌트 객체를 이용해 프로그램을 개발하는 모델을 의미한다. COM이라는 기술의 개념은 기존의 객체지향 프로그래밍과 크게 다르지 않으나, 거기에 더해서 프로그래밍 언어와 상관없이 개발된 객체를 사용할 수 있게 해준다.\\
python에서 다른 프로그래밍 언어로 작성된 COM 객체를 생성하려면 win32com.client라는 모듈의 Dispatch메서드를 사용하면 된다.

# OCX(Object Linking and Embedding Custom Control)
PyQt패키지의 QAxContainer 모듈을 통해 OCX 다루기
