# Vue
## Vue 인스턴스
### 인스턴스 생성
````
new Vue({
  ...
});
````

### 인스턴스 옵션 속성
인스턴스를 생성할때 재정의할 data, el, template등의 속성을 말한다.

````
<div id="app>
  {{ msg }}
</div>

<script>
  new Vue({
    el: '#app',
    data: {
      msg: 'hello'
    }
  });
  ````

  이 외에도 template, methods, created등 미리 정의되어 있는 속성이 있음
  template: 화면에 표시할 html, css등의 마크업 요소를 정의하는 속성
  methods: 화면 로직 제어와 관계된 메서드를 정의하는 속성
  created: 뷰 인스턴스가 생성되자마자 샐행할 로직을 정의할 수 있는 속성

  ### Vue인스턴스 유효범위
  뷰 인스턴스를 생성하면 html의 특정 범위안에서만 옵션 속성들이 적용된다.

  ### 인스턴스가 화면에 적용되는 과정

  1. new Vue() - 2. 뷰 라이브러리 파일 로딩 - 3. 인스턴스 객체 생성(옵션 속성 포함) - 4. 특정 화면 요소에 인스턴스를 붙임 - 5. 인스턴스 내용이 화면 요소로 변환 - 6. 변환된 화면 요소를 사용자가 최종 확인

### 라이프 사이클

new Vue() - (이벤트 및 라이프 사이클 초기화)
- beforeCreate - (화면에 반응성 주입)
- created - (el, template속성 확인) - (template속성 내용을 render()로 변환)
- beforeMount - ($el생성 후 el속성 값을 대입)
- mounted [데이터가 변경되는 경우에만 거침] (인스턴스를 화면에  부착) - (인스턴스의 데이터 변경) - beforeUpdate - (화면 재 렌더링 및 데이터 갱신) - updated
- (인스턴스 내용 갱신) - (인스턴스 접근 가능) - beforeDestroy - (컴포넌트, 인스턴스, 디렉티브 등 모두 해제) - destroyed - 인스턴스 소멸

** beforeCreate **
- 인스턴스가 실행되고 가장 처음 실행.
- data, methods가 아직 인스턴스에 정의되지 않음.
- dom과 같은 화면 요소에도 접근 안됨

** created **
- data, methods가 정의됨 (this.data, this.method()와 같은 로직 실행 가능)
- 아직 인스턴스가 화면 요소에 부착되기 전이어서 template속성에 정의된 돔 요소로 접근 불가능
- 서버에서 데이터를 요청하여 받아오는 로직을 수행하기 좋음

** beforeMount **
- template에 지정한 마크업 속성을 render()함수로 변환한 후 el속성에 지정한 화면 요소에 인스턴스를 부착하기 전에 호출되는 단계
- render()가 호출 되기 전의 로직을 추가하기 좋음

** mounted **
- el 속성에 지정한 화면 요소에 인스턴스가 부착되고 호출 됨
- template속성에 정의 한 화면 dom에 접근할 수 있음
- 다만 dom인스턴스가 부착되자마자 바로 호출되었으므로 하위 컴포넌트나 외부 라이브러리에 의해 추가된 화면 요소들이 최종 html코드로 변횐되는 시점과 다를 수 있다.
(변환되는 시점이 다를 경우 $nextTick() api를 이용해 html코드로 최종 바싱 될때까지 기다린 후 돔 제어 로직을 추가)

** beforeUpdate **
- el 속성에 지정한 화면 요소에 인스턴스가 부착 후 인스턴스에 정의한 속성들이 화면에 치환
- 치환된 값은 뷰의 반응성을 제공하기 위해 $watch속성으로 감시
- 관찰하고 있는 데이터가 변경되면 가상 돔으로 화면을 다시 그리기 전에 호출되는 단계
- 변경 예정인 새 데이터에 접근 가능
- 여기에 값을 변경하는 로직을 넣어도 화면이 다시 그려지지는 않는다.

** updated **
- 데이터가 변경되고 나서 가상 돔으로 다시 화면을 그리고 나면 실행
- 데이터 변경 후 화면 요소 제어와 관련된 로직을 추가 할 수 있음
- 이 단계에서 데이터 값을 변경하면 무한 루프에 빠질 수 있기 때문에 값을 변경하려면 computed, watch와 같은 속성을 사용
- 따라서 데이터 값을 갱신하는 로직은 가급적 beforeUpdate에 추가, updated는 변경 데이터의 화면 요소와 관련된 로직을 추가 하는 것이 좋다.
(하위 컴포넌트의 화면 요소와 외부 라이브러리에 의해 주입된 요소의 최종 변환 시점이 다를 수 있으므로 $nextTick()을 사용하여 변환이 완료될 때까지 기다렸다 로직 추가)

** beforeDestroy **
- Vue인스턴스가 파괴되기 직전에 호출
- 아직 인스턴스에 접근 가능
- 뷰인스턴스의 데이터를 삭제하기 좋은 단계

** destroy **
- 뷰 인스턴스에 정의한 모든 속성이 제거되고 하위 인스턴스들 또한 모두 파괴됨

## 컴포넌트

조합하여 화면을 구성 할 수 있는 블럭

- 전역 컴포넌트
- 지역 컴포넌트

### 전역컴포넌트
````
Vue.component('컴포넌트 이름', {
  내용
});

--------------------------------------------

<div id="app">
  <my-component></my-component>
</div>
<script>

  Vue.component('my-component', {
    template: '<p>템플릿 입니다</p>'
  });

  new Vue({
    el: '#app'
  });

</script>
````

### 지역 컴포넌트
````
new Vue({
  el: '#app',
  components: {
    '컴포넌트 이름': '컴포넌트 내용'
  }
});

-----------------------------------------

var cmp = {
  template: '<p>컴포넌트 입니다</p>'
};

new Vue({
  el: '#app',
  components: {
    'my-components': cmp
  }
});
````
## 컴포넌트 통신

- 각 컴포넌트의 유효 범위기 독립적이기 때문에 다른 컴포넌트의 값을 직접 참조 할 수 없다.
- 상위 -> 하위 컴포넌트간에 데이터를 전달하는 것이 기본 데이터 전달 법.

상위에서 하위로는 **props전달**
하위에서 상위로는 **이벤트 발생**

### props

````
Vue.component('child-component', {
  props: ['props 속성 이름']
});

<child-component v-bind:props속성 이름="상위 컴포넌트 data 속성"></child-component>

--------------------------------------------------------------------------------

<div id="app">
  <my-comp v-bind:item="msg"></my-comp>
</div>

<script>
// 컴포넌트를 등록함과 동시에 Vue인스턴스의 하위 컴포넌트가 됨
  Vue.component('my-comp', {
    props: ['item'],
    template: '<p>{{ msg }}</p>'
  });

  new Vue({
    el: '#app',
    data: {
      msg: 'hello'
    }
  });
</script>
````

### $.emit()
하위 컴포넌트에서 상위 컴포넌트를 이벤트를 전달하기 위해 사용
````
// 이벤트 발생
this.$emit('이벤트명');

// 이벤트 수신
<my-comp v-on:이벤트명="상위 컴포넌트 메서드 명">

--------------------------------------------------------------------------------
// my-comp에서 compEvt이벤트를 보내면
// vue인스턴스의 display 메서드를 실행한다.
<div id="app">
  <my-comp v-bind:item="msg" v-on:compEvt="display"></my-comp>
</div>

<script>

  Vue.component('my-comp', {
    props: ['item'],
    template: '<p v-on:click="showLog">{{ msg }}</p>',
    methods: {
      showLog: function() {
        this.$emit('compEvt');
      }
    }
  });

  new Vue({
    el: '#app',
    data: {
      msg: 'hello'
    },
    methods: {
      display: function() {
        console.log('hello');
      }
    }
  });
</script>
````
### 이벤트 버스
이벤트 버스를 이용하면 상 - 하 간의 통신하지 않고 통신이 가능하다

````
var evtbus = new Vue();

// 이벤트를 보낸다
methods: {
  send: function() {
    evtbus.$emit('sendEv', data);
  }
}

// 이벤트를 받는다
methods: {
  receive: function() {
    evtbus.$on('sendEv', function(data) {

    });
  }
}

-------------------------------------------------------

<div id="app">
  <my-comp></my-comp>
</div>

// 이벤트 버스
var evtbus = new Vue();

  Vue.component('my-comp', {
    template: '<p>하위 컴포넌트 <button type="button" v-on:click="showLog">click</button></p>',
    methods: {
      showLog: function() {
        evtbus.$emit('triggerEvtbus', 100);
      }
    }
  });

  new Vue({
    el: '#app',
    created: function() {
      evtbus.$on('triggerEvtbus', function(data) {
        console.log('받은값: ', data);
      });
    }
  });

````
[예제](https://github.com/fireworks80/study/blob/master/event-bus.html)
컴포넌트가 많아지면 어디서 어디로 보내는지 관리가 되지 않는다.
이 문제를 해결 하기 위해 vuex라는 상태 관리 도구가 필요하다.

## 라우팅
웹페이지 간의 이동 방법
spa에서 주로 사용하고 있다

### 뷰라우터
뷰에서 라우팅을 구현하기 위한 공식 라이브러리

````
** 마크업 **

// to prop을 사용
// 페이지 이동태그 <a>로 표시
<router-link to="url">이동</router-link>

// 페이지 표시 태그
<router-view></router-view>

**스크립트**
// 라우트 컴포넌트 정의
const foo = {template: '컴포넌트'};
const bar = {template: '컴포넌트'};

// 라우트 정의
const routes = [
  {path: '/foo', component: foo }
  {path: '/bar', component: bar }
];

// 라우트 인스턴스 생성
const router = new VueRouter({ routes });

// 루트 인스턴스에 마운트
new Vue({ router }).$mount('#app');

````
[예제](https://github.com/fireworks80/study/blob/master/router.html)

기본적으로 해쉬값이 붙는다

**해쉬값 없애기**
````
var router = new VueRouter({
  mode: 'history', // 모드 추가
  routes
});
````

### 중첩 라우터
````
+-------------------------+                     +-------------------------+
| user                    |                     | user                    |
| +---------------------+ |                     | +---------------------+ |
| | profile             | |                     | | Posts               | |
| |                     | | + -------------- >  | |                     | |
| +---------------------+ |                     | +---------------------+ |
+-------------------------+                     +-------------------------+


````
[예제](https://github.com/fireworks80/study/blob/master/nested-router.html)

## 뷰프로젝트 구성하기

### 싱글파일 컴포넌트
.vue파일로 프로젝트 구조를 구성하는 방식
.vue 1개 파일은 1개의 컴포넌트와 동일

````
// 화면에 표시될 요소들을 정의 하는 영역
<template>
</template>

// vue 컴포넌트의 내용을 정의
// template, data, methods 등`
<script>
</script>

// 템플릿에 추가된 html 태그의 css 스타일링
<style>
</style>
````
## Vue CLI

  기본 템플릿은 babel, eslint, unit-mocha를 포함한다.
````
vue create my-project
````

### 디렉토리 구조
````
- dist         # 빌드 결과물, yarn build 전까지 없음
- node_modules # yarn 또는 npm으로 설치한 의존성
- public       # 공용으로 접근 가능한 파일이 위치함
- src          # 애플리케이션 소스코드
- test         # 테스트 코드
.gitignore
package.json

````

### package.json
**scripts**
- serve: 개발용 버전으로 앱을 실행
- build: 배포용 버전으로 앱을 빌드
- test: 테스트 코드를 실행
- lint: 코드 스타일을 lint