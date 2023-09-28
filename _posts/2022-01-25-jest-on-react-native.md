---
title: "[React Native] Jest를 이용한 단위 테스트 해보기 (feat. TDD)"
author:
    name: 박 찬영
    link: https://github.com/univdev
date: 2022-01-25 16:55:00 +0900
categories: [Tech, React Native]
tags: [React Native, Jest, Unittest]
---
# 흔한 소프트웨어 개발 방법
요즘 소프트웨어의 개발은 **정교함**보다는 간편함과 신속함에 초점이 맞춰져 있습니다.  
물론 일반화 하기에는 너무나 다양한 가치관과 개발 방식이 있겠지만 대부분의 기업에서는 아래와 같은 시나리오를 따릅니다.  

1. 소프트웨어의 요구스펙을 적은 기획서를 작성한다. (일부 기업에서는 이를 '유저스토리'라고 칭한다.)
2. 디자인을 제작한다.
3. 개발자가 디자인과 기획서를 보고 개발에 들어간다.
4. 개발자가 직접 테스트를 진행하면서 기획서와 교차 테스트를 진행한다.
5. 제품을 출시한다.
6. 이슈가 발견되면 수정한다.

# 문제점
위 개발 방식은 흔하디 흔한 Waterfall 시나리오입니다.
그런데 이러한 개발 방식에는 한가지 문제점이 있습니다.  
기능 추가 및 변경이 지속적으로 일어날수록, 다시 말해 프로젝트의 규모가 확대 될수록 **3번 항목이 제대로 지켜지지 않을 수 있다는 점입니다.**
예를 하나 들어보겠습니다.

1. **2017년**에 만들기 시작한 프로젝트가 있다. 이 때는 앱이 지원하는 기능이 간단한 게시판 정도밖에 없었다. 심지어 비회원도 게시물을 작성할 수 있는, 별도의 회원 인증 기능조차 없는 간단한 앱이다.
2. **2018년**에는 앱의 사용자 수가 늘어나서 수익을 창출해야만 한다. 광고를 추가했다.
3. **2019년**에는 멤버십 기능 추가를 위해 회원 기능을 만들었다.
4. **2020년**에는 구글 애널리틱스를 적용하여 비회원과 회원의 앱 사용 방식을 분석하기 시작했다.
5. **2021년**에는 비회원 제도를 폐지하여 회원만 앱 사용이 가능하도록 제작했다.

위와 같은 개발 절차를 거칠 경우, 1번과 5번이 상충됩니다. 새로운 기능이 생겨나면서 기존 기능의 영역을 침범하게 되는거죠.  
5번을 개발할 때 1번에 대한 내용을 기억하고 있다면 돌아가서 수정하면 되는 부분이겠지만 만약 1번의 비회원 게시글 작성 기능을 잊고 있는 상태라면 어떨까요?  
서비스의 코어 기능에 대해 심각한 이슈를 가진 채 릴리즈 될 수도 있는 부분입니다.

서비스 개발 과정에서 흔히 일어나는 인적 오류입니다. 기능 개발 후 교차 검증을 사람에게 전적으로 맡기다보면 테스트에 소모되는 시간적 비용도 증가되고, 기능에 대해서 확실히 동작한다는 보장을 받을 수 없습니다.
# 단위 테스트
## 개요
**단위 테스트**란, 개발에 사용되는 가장 작은 단위의 요소(컴포넌트)를 기능별로 분석하여 모든 기능이 개별적으로, 정상적으로 동작하는지를 확인하기 위한 테스트 기법입니다.
## 장점
테스트가 조밀하게 작성 된 소프트웨어는 컴포넌트와 페이지의 갯수만큼의 QA 직원을 데리고 있는 것과 마찬가지입니다. *(그것도 매우 성실한)*  
1. QA 직원 한명은 컴포넌트 하나만을 들여다보기 때문에 절대 실수하는 법이 없습니다.
2. 더 이상 쪼개질 수 없을 정도로 디테일하게 작성 된 테스트 코드는 추후 기능 추가 및 변경에 의한 대응도 간편합니다.
3. 사람이 진행하는 테스트가 아니기에 인적 실수가 적용되지 않습니다.
4. 진행해야 할 테스트를 덜 알려주게 되면 Coverage를 차감하는 방식으로 알려주기 때문에 기획 사항이 누락 될 위험이 없습니다.
5. 테스트를 진행하면서 실제로 소프트웨어를 돌려보기 때문에 어떤 과정에서 퍼포먼스가 안좋은지를 바로 알 수 있습니다.

## 단점
1. 기능이 추가 / 변경 될 때마다 기존 테스트 코드에 반영을 해야하기 때문에 기능 개발 + a 의 시간이 들어갑니다.
2. 초반부터 테스트 코드를 작성하고 개발한게 아니라면 추후 테스트 코드를 도입하기 위해 거쳐야 하는 시간적 비용이 클 수도 있습니다.
3. 테스트 코드를 작성하기 위한 학습 시간이 필요할 수 있습니다.

# 실제로 도입해보기
## 설치해야 할 패키지
- @types/jest
- @testing-library/jest-native
- @testing-library/react-native
- react-test-renderer
- immer (단위 테스트에는 상관 없는데, 추후 Home 컴포넌트를 작성하기 위해서 사용합니다.)

```shell
yarn add --dev @types/jest @testing-library/jest-native @testing-library/react-native react-test-renderer immer
```

## 방법
> 이 섹션에서는 ```jest```의 ```expect```와 ```toBe```와 같은 함수의 사용법은 숙지하고 있다는 전제 하에 설명합니다.

### 기획
아래와 같은 앱을 만든다고 생각해봅시다.
1. 상단에는 타이틀이 보여진다.
2. 내가 가장 좋아하는 레스토랑의 이름을 입력할 수 있는 입력 필드가 있다.
3. 레스토랑을 추가할 수 있는 버튼이 존재한다.
4. 레스토랑의 이름을 입력하지 않고 추가 버튼을 누를 경우 경고창이 보여지며 입력을 막는다.
5. 레스토랑 이름을 입력하면 레스토랑이 추가가 되어 화면에 표시가 된다.

예상 실행 모습은 아래와 같습니다.

![My Favorite Restaurants][스크린샷]
## TDD
**TDD**개발 원칙에 따라 테스트 코드 먼저 작성해봅시다.
> TDD 개발이란, 테스트 코드부터 개발하고 실제 개발에 들어가는 방식을 말합니다.
> 최종 결과물을 제작할 때는 기존에 작성해둔 테스트 코드가 통과 되도록 제작해야 합니다.

1. ```__test__``` 폴더를 루트 경로에 배치합니다. *(```react-native CLI```를 이용하여 프로젝트를 생성했다면 이미 해당 폴더가 존재 할겁니다.*)
2. ```__test__``` 폴더 안에 ```Home-test.js``` 파일을 생성합니다.
3. ```Home-test.js``` 안에는 ```Home```이라는 이름의 스크린에서 유저가 행할 수 있는 모든 행동과, 유저가 얻을 수 있는 모든 기대값을 테스트하기 위한 로직을 작성합니다.
4. ```Home-test.js``` 최상단에서 패키지를 아래와 같이 ```import```합니다. *(각각의 패키지에서 불러온 메소드가 어떤 역할을 하는지는 나중에 설명합니다.)*
    ```javascript
    import React from 'react';
    import 'react-native';
    import { render, fireEvent } from '@testing-library/react-native';
    import Home from '../src/Home'; // Home 컴포넌트는 예시이다. 각자의 사정에 맞춰서 테스트를 할 컴포넌트를 불러오면 된다.
    ```
5. 현재 ```Home``` 컴포넌트를 테스트하고 있기 때문에, ```Home```이라는 대주제를 개설합니다.
    ```javascript
    describe('Home', () => {

    });
    ```
6. **스냅샷 촬영 및 컴포넌트 렌더링이 제대로 되었는지에 대한** 테스트 로직을 작성합니다. (위에서 생성한 ```describe```의 ```callback``` 내부에 작성해야 한다.)
    ```javascript
    it('Home 화면이 정상적으로 렌더링 되는가?', () => { // 현재 테스트 하고자 하는 목적을 적고, 두번째 매개변수로 callback을 전달한다. 실제 테스트는 callback 내부에서 진행한다.
        const screen = render(<Home />); // react-native 컴포넌트를 @testing-library/react-native 패키지의 render 함수를 통해 렌더링한다.
        const json = screen.toJSON(); // 렌더링 된 컴포넌트를 json 구조로 변환한다.
        expect(json).toMatchSnapshot(); // 만들어진 json으로 스냅샷을 촬영한다.
    });
    ```
    > 스냅샷을 촬영하는 이유는, 추후 컴포넌트가 무언가에 의해 변경됨을 감지하기 위해서 앱 실행 당시 초기의 모습을 기억해두기 위해서입니다.

    > 한 번 촬영해둔 스냅샷은 파일의 형태로 ```__snapshots__``` 폴더에 저장되기 때문에 추후에 컴포넌트를 직접 변경했을 경우 기존에 촬영된 스냅샷과 일치하지가 않아 에러가 발생되는 경우가 있는데, 그럴 경우에는 ```yarn test --u```를 한 번 실행해서 스냅샷을 다시 촬영해주면 됩니다.
7. **제대로 타이틀이 보여지는지**에 관한 테스트를 작성합니다.
    ```javascript
    it('타이틀이 정상적으로 보여지는가?', () => { // 마찬가지로 현재 테스트하고자 하는 목적을 적는다.
        const screen = render(<Home />); // Home 컴포넌트를 렌더링한다.
        const title = screen.getByText('Your Favorite Restaurants!'); // 해당 screen에서 'Your Favorite Restaurants!' 라는 내용을 갖는 노드를 찾는다.
        expect(title).toBeDefined(); // 만약 노드가 있다면 toBeDefined()로 해당 객체가 정의 되었는지 테스트한다. (toBeDefined는 undefined가 넘어가면 실패를 반환하는 메소드이다.)
    });
    ```
8. **내가 가장 좋아하는 레스토랑의 이름을 입력할 수 있는 입력 필드가 노출** 되었는지에 관한 테스트를 작성합니다.
    ```javascript
    it('레스토랑 이름을 입력할 수 있는 텍스트 필드가 존재하는가?', () => {
        const screen = render(<Home />);
        expect(screen.getByTestId('restaurantNameField')).toBeDefined(); // testID 속성에 'restaurantNameField'가 들어가있는 요소가 있는지 찾고, 정의 되었는지를 식별한다.
    });
    ```
    > ```testID```란, ```React Native```에서 순전히 테스트를 위해 존재하는 ```Attribute```입니다.
    
    > ```React Native```환경은 웹과 다르게 ```className```이나 ```id```를 사용하지 않기 때문에 ```Element```를 선택하려면 ```testId```를 활용해야 합니다.

9. **레스토랑을 추가할 수 있는 버튼이 노출** 되었는지에 관한 테스트를 진행합니다.
    ```javascript
    // 레스토랑 이름 입력 필드 검사 로직과 testId만 다르기 때문에 설명은 생략한다.
    it('레스토랑을 추가할 수 있는 추가 버튼이 존재하는가?', () => {
        const screen = render(<Home />);
        expect(screen.getByTestId('restaurantAddButton')).toBeDefined();
    });
    ```
10. **레스토랑 이름을 입력하지 않고 추가 버튼을 누를 경우**에 대한 테스트를 작성하기 전에, 패키지 ```import```라인에 해당 코드를 추가합니다.
    ```javascript
    import { Alert } from 'react-native'; // react-native에서 지원하는 경고창이다.

    jest.spyOn(Alert, 'alert'); // jest의 spy를 Alert 메소드에 심어놓는다. 테스트를 할 때는 실제 Alert를 실행하는게 아닌, '실행이 되었나?'에 대한 여부만을 확인하기 위한 mocking 작업을 진행하게 된다.
    ```
11. **레스토랑 이름을 입력하지 않고 추가 버튼을 누를 경우**에 대한 테스트를 작성합니다.
    ```javascript
    it('레스토랑 이름을 추가하지 않고 추가 버튼을 누를 경우 예외 문구가 표시 되는가?', () => {
        const screen = render(<Home />);
        const title = '레스토랑 추가 실패'; // Alert의 제목
        const message = '레스토랑 이름을 입력해주세요!'; // Alert의 내용
        const button = screen.getByTestId('restaurantAddButton'); // 버튼을 직접 눌러보기 위해서 버튼을 탐색
        fireEvent(button, 'press'); // fireEvent를 통해 윗 줄에서 선택한 버튼에 'press'라는 이벤트를 실행하는 trigger이다.
        const json = screen.toJSON(); // 버튼을 누른 이후의 모습을 json으로 만든다.
        expect(Alert.alert).toHaveBeenCalledWith(title, message); // react-native의 Alert 메소드가 title, message과 같은 매개변수를 넘겨받고 실행 된 적이 있는지 테스트한다.
        expect(json).toMatchSnapshot(); // 초기에 촬영 된 스냅샷과 현재 스크린을 비교한다. 만약 경고창은 띄웠지만 실제 작성을 막지 않은 경우, 엉뚱한 노드가 하나 더 생겼을테니 초기 스냅샷과 달라졌기 때문에 오류가 난다.
    });
    ```
12. **레스토랑을 추가할 수 있는지**에 대한 테스트를 작성합니다.
    ```javascript
    it('레스토랑을 추가할 수 있는가?', () => {
        const names = [ // 추가할 레스토랑의 이름들을 배열에 적어둔다.
        '1st gourmet',
        'sushi daisuki',
        'udonya',
        ];
        const screen = render(<Home />);
        const input = screen.getByTestId('restaurantNameField');
        const button = screen.getByTestId('restaurantAddButton');
        for (const name of names) { // 이름들을 반복한다.
        fireEvent(input, 'changeText', name); // 위에서 찾은 input 노드에 'changeText'라는 이벤트를 보내는데, 매개변수로 name을 보낸다.
        fireEvent(button, 'press'); // 위에서 찾은 button 노드에 'press'라는 이벤트를 보낸다.
        expect(screen.getByText(name)).toBeDefined(); // name 텍스트를 가진 요소가 정의 되었는지 탐색한다. 만약 레스토랑 이름이 표기되지 않았다면 실패한다.
        }
    });
    ```
13. 위 모든 과정을 제대로 따라왔다면 최종 테스트 코드의 모습은 아래와 같이 나올 것입니다.
    ```javascript
    import React from 'react';
    import { Alert } from 'react-native';
    import { render, fireEvent } from '@testing-library/react-native';
    import Home from '../src/pages/Home';

    jest.spyOn(Alert, 'alert');

    describe('Home', () => {
    it('Home 화면이 정상적으로 렌더링 되는가?', () => {
        const screen = render(<Home />);
        const json = screen.toJSON();
        expect(json).toMatchSnapshot();
    });
    it('타이틀이 정상적으로 보여지는가?', () => {
        const screen = render(<Home />);
        const title = screen.getByText('Your Favorite Restaurants!');
        expect(title).toBeDefined();
    });
    it('레스토랑 이름을 입력할 수 있는 텍스트 필드가 존재하는가?', () => {
        const screen = render(<Home />);
        expect(screen.getByTestId('restaurantNameField')).toBeDefined();
    });
    it('레스토랑을 추가할 수 있는 추가 버튼이 존재하는가?', () => {
        const screen = render(<Home />);
        expect(screen.getByTestId('restaurantAddButton')).toBeDefined();
    });
    it('레스토랑 이름을 추가하지 않고 추가 버튼을 누를 경우 예외 문구가 표시 되는가?', () => {
        const screen = render(<Home />);
        const title = '레스토랑 추가 실패';
        const message = '레스토랑 이름을 입력해주세요!';
        const button = screen.getByTestId('restaurantAddButton');
        fireEvent(button, 'press');
        const json = screen.toJSON();
        expect(Alert.alert).toHaveBeenCalledWith(title, message);
        expect(json).toMatchSnapshot();
    });
    it('레스토랑을 추가할 수 있는가?', () => {
        const names = [
        '1st gourmet',
        'sushi daisuki',
        'udonya',
        ];
        const screen = render(<Home />);
        const input = screen.getByTestId('restaurantNameField');
        const button = screen.getByTestId('restaurantAddButton');
        for (const name of names) {
        fireEvent(input, 'changeText', name);
        fireEvent(button, 'press');
        expect(screen.getByText(name)).toBeDefined();
        }
    });
    });
    ```
14. 위 테스트 코드를 기반으로 ```Home```을 구현해보면 실제 ```Home```의 코드는 아래와 같이 될 것입니다.
    ```jsx
    import produce from 'immer';
    import React, { useReducer } from 'react';
    import { Text, View, TextInput, Button, Alert, SafeAreaView, } from 'react-native';

    const reducer = (state, action) => {
    switch(action.type) {
        case 'SET_NAME':
        return produce(state, (draft) => {
            draft.name = action.payload;
            return draft;
        });
        case 'ADD_ITEM':
        return produce(state, (draft) => {
            draft.items.push(action.payload);
            return draft;
        });
        default:
        return state;
    }
    };

    const Home = () => {
    const [state, dispatch] = useReducer(reducer, {
        name: '',
        items: [],
    });
    const handleAddRestaurant = () => {
        try {
        if (!state.name) throw new Error('레스토랑 이름을 입력해주세요!');
        const { name } = state;
        dispatch({ type: 'ADD_ITEM', payload: name });
        dispatch({ type: 'SET_NAME', payload: '' });
        } catch (e) {
        Alert.alert('레스토랑 추가 실패', e.message);
        }
    };
    const handleChangeName = (e) => {
        dispatch({ type: 'SET_NAME', payload: e });
    };
    return (
        <SafeAreaView>
        <Text>Your Favorite Restaurants!</Text>
        <View>
            {
            state.items.map((item, index) => {
                return (
                <Restaurant
                    key={index}
                    name={item}
                />
                );
            })
            }
        </View>
        <TextInput
            value={state.name}
            testID="restaurantNameField"
            onChangeText={handleChangeName}
        />
        <Button
            title="추가"
            testID="restaurantAddButton"
            onPress={handleAddRestaurant}
        />
        </SafeAreaView>
    );
    };

    const Restaurant = (props) => {
    return (
        <View>
        <Text>{props.name}</Text>
        </View>
    );
    };

    export default Home;
    ```

## 테스트 실행하기
### 기본
jest는 기본적으로 ```__test__``` 폴더 내부의 모든 파일 혹은 ```*.test``` 명칭을 가진 파일들을 실행하기 때문에 아래 커맨드로 프로젝트내 포함 된 모든 테스트를 별도의 설정 없이 읽어낼 수 있습니다.
```shell
yarn test
```
### 특정 파일만 테스트 해보기
만약 특정 테스트 파일만 읽고 싶다면 아래 명령어를 입력합니다.
```shell
yarn test home
// home은 파일의 이름이다. Home-test.js일 경우, 앞의 Home만 따서 쓰는 형태이다.
```
### 스냅샷 갱신
만약 스냅샷을 재촬영하고 싶다면 아래 명령어를 입력합니다.
```shell
yarn test --u
```
## Awesome!
![Result][결과]

[스크린샷]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/jest_screen.gif?alt=media&token=38da110e-7bc3-4c13-b15c-e7a76533f803
[결과]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/jest_result.gif?alt=media&token=2a9717ac-356f-4971-a3ba-d0c7aebbb9f3