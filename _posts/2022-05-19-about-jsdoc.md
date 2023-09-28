---
title: "JSDoc으로 Javascript 문서를 좀 더 친절하게 만들어보자"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-05-19 11:58:00 +0900
categories: [Tech, Talk]
tags: [Javascript]
---
# JSDoc
**JSDoc**은 ```Javascript```로 작성 된 문서에서 각 변수나 함수 등에 의미를 부여하기 위한 마크업 언어입니다.  
이를 이용하면 VSCode와 같은 개발 툴에서 특정 함수를 사용할 때, 이 함수가 어떤 역할을 하는지 혹은 어떤 변수를 파라미터로 전달 받는지 등을 툴팁 형태로 띄워줄 수 있습니다.
# 태그
- ```@author``` 이 코드를 작성한 개발자를 나타낼 때 사용합니다.
- ```@constructor```	이 함수가 생성자의 역할을 할 때 사용합니다,
- ```@deprecated```	더 이상 사용되지 않는 함수에 사용합니다.
- ```@exception```	```@throws```와 동일하게 사용됩니다.
- ```@exports``` 어떤 모듈에서 추출 된 멤버인지를 나타냅니다.
- ```@param```	특정 함수에 넘겨야 하는 파라미터를 나타냅니다.
- ```@private```	```private```로 선언 된 함수를 표시할 때 사용합니다.
- ```@returns```	이 함수가 ```return```하는 값을 표현할 때 사용합니다.
- ```@return```	```@returns```와 동일하게 사용됩니다.
- ```@see``` 다른 개체와 연결 되어 있음을 명시적으로 표현합니다.
- ```@this```	이 함수 내에서 갖고있는 this가 어떤 값을 의미하는지 표현할 때 사용합니다.
- ```@throws```	이 함수 안에서 예외가 발생 되었을 때 어떤 값이 넘어오는지 명시적으로 표현합니다.
- ```@version``` 라이브러리의 버전 정보를 제공할 때 사용합니다.

# 문법
```@param``` 처럼 태그를 열고 해당 줄에 정보를 기입합니다.  
```javascript
/**
  * Creates a new Circle from a diameter.
  *
  * @param {number} d The desired diameter of the circle.
  * @return {Circle} The new Circle object.
  */
  const fromDiameter(d) = () => {
    return new Circle(d / 2)
  }
```
위 코드처럼 함수를 정의하시면 ```reates a new Circle from a diameter.```라는 설명과 함께 함수의 파라미터, 리턴값 등이 보여지게 됩니다.
# 예제
{%raw%}
```javascript
/** @class Circle representing a circle. */
class Circle {
/**
 * Creates an instance of Circle.
 *
 * @author: moi
 * @param {number} r The desired radius of the circle.
 */
  constructor(r) {
    /** @private */ this.radius = r
    /** @private */ this.circumference = 2 * Math.PI * r
  }

  /**
   * Creates a new Circle from a diameter.
   *
   * @param {number} d The desired diameter of the circle.
   * @return {Circle} The new Circle object.
   */
  static fromDiameter(d) {
    return new Circle(d / 2)
  }

  /**
   * Calculates the circumference of the Circle.
   *
   * @deprecated since 1.1.0; use getCircumference instead
   * @return {number} The circumference of the circle.
   */
  calculateCircumference() {
    return 2 * Math.PI * this.radius
  }

  /**
   * Returns the pre-computed circumference of the Circle.
   *
   * @return {number} The circumference of the circle.
   * @since 1.1.0
   */
  getCircumference() {
    return this.circumference
  }

  /**
   * Find a String representation of the Circle.
   *
   * @override
   * @return {string} Human-readable representation of this Circle.
   */
  toString() {
    return `[A Circle object with radius of ${this.radius}.]`
  }
}

/**
 * Prints a circle.
 *
 * @param {Circle} circle
 */
function printCircle(circle) {
    /** @this {Circle} */
    function bound() { console.log(this) }
    bound.apply(circle)
}
```
{%endraw%}