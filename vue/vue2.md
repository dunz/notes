# Vuejs2 summary

### 프로젝트 생성
``` bash
vue init webpack vue-tuto
cd vue-tuto
npm start or npm run dev
```

### 사용법

##### 바인딩 표현식
`{{message}}`

##### 양방향 바인딩
``` js
data: () => ({
    message: 'Greetings your Majesty!'
})
```
``` html
<input v-model="message">
```

##### 개발 시 data 확인
``` html
{{$data}}
```
> 콘솔을 통해 지속적으로 바라보는 것보다 데이터를 만들고 조작하는데 훨씬 더 편리한 방법

##### 조건별 바인딩
###### vue-if
- <template>에서 사용가능
- 렌더링 안함
- 토글 비용이 높음
- 자주 변경할 필요가 없는경우 사용

###### vue-show
- <template>에서는 사용불가
- 렌더링 함 display:none 처리
- 초기 렌더링 비용이 높음
- 매우 자주 전환해야 하는 경우 사용

###### vue-else
- vue-if 바로 아래 형제노드에 사용해야 함
- vue 2.0 부터 vue-show 와 함께 사용할 수 없음

##### 마운트
``` js
new Vue({
    el: '.container'
})
```

> 클래스로 엘리먼트를 지정할때 클래스가 1개 이상 존재하면 Vue.js는 첫번째 엘리먼트에만 마운트된다.

##### v-pre
> 데이터를 컴파일 하지 않고 문자열을 그대로 출력한다.
```
<div v-pre>
    {{message}}
</div>
```

##### v-for
``` html
<li v-for="i in 11">
    {{ i-1 }} times 4 equals {{ (i-1) * 4 }}.
</li>
```

``` html
// 배열
<li v-for="(story, index) in stories">
    {{index}} {{ story.writer }} said "{{ story.plot }}"
</li>
```

``` html
// 객체
<li v-for="(value, key, index) in story">
    {{index}} : {{key}} : {{value}}
</li>
```

##### 여러요소를 template으로 묶어서 루프 처리
```
<template v-for="(contact, index) in contacts">
<tr>
    ...
</tr>
<tr class="divider" v-if="index % 5 === 4">
    <td colspan="4"></td>
</tr>
</template>
```

##### 이벤트 핸들링
###### 인라인 핸들링
``` html
<button v-on:click="upvotes++">
    Upvote! {{upvotes}}
</button>
```

###### 메소드 핸들링
``` html
<button v-on:click="upvote">
    Upvot1e! {{upvotes}}
</button>
```
``` js
methods: {
    upvote: function(){
    // this는 메소드 안에서 Vue인스턴스를 가리킵니다
        this.upvotes++;
    }
}
```

###### v-on 축약형 @ 사용
``` html
<button @click="upvote">
    Upvote! {{upvotes}}
</button>
```

##### 바인딩 수식어
``` html
<input v-model.number="a">
```
> a를 숫자로 파싱

##### 이벤트 수식어
1. .prevent
2. .stop
3. .capture
4. .self

``` html
<button type="submit" @click.prevent="calculate">Calculate</button>
```
> 이벤트의 기본동작 방지 (submit 되지 않음)

##### 키 수식어

``` html
<input v-model="a" @keyup.13="calculate">
```
> 이벤트 키코드가 13일 경우 호출 (13: enter)

모든 키코드를 외울 수 없기 때문에 일반적으로 사용되는 키코드의 별칭 제공

- enter
- tab
- delete
- esc
- space
- up
- down
- left
- right

``` html
<input v-model="b" @keyup.enter="calculate">
```

##### 계산된 속성
> 다른 변수의 변화에 따라 값이 바뀌는 변수

- 인라인 표현식은 편리하지만 좀더 복잡한 로직이 필요해지면 반드시 계산된 속성을 사용해야함
- 함수처럼 작동하고 객체의 속성처럼 사용 가능
- 계산된 속성의 종속되는 속성이 변경될 때마다 계산된 속성의 값은 다시 계산됨
- computed 내에 설정

``` js
computed: {
    c: function () {
        return this.a + this.b
    }
}
```

##### 필터
> 렌더링 된 결과를 리턴하는 메소드를 만들어 해결<br />
필터 로직을 자바스크립트로 옮겨 컴포넌트 전체에서 재사용하는 것을 권장

###### 기본 computed 로 구성한 필터 sample

``` js
computed: {
    famous: function() {
        return this.stories.filter(function(item){
            return item.upvotes > 25;
        });
    }
}
```

``` html
<h1>Let’s hear some famous stories! ({{famous.length}})</h1>
<li v-for="story in famous">
    {{ story.writer }} said "{{ story.plot }}" and upvoted {{ story.upvotes }} times.
</li>
```

###### 검색 필터 sample

``` js
search: function () {
    var query = this.query
    return this.stories.filter(function (story) {
      return story.plot.includes(query)
    })
}
```

``` html
<label for="query">What are you looking for?</label>        // ng-model 에 바인딩된 data를 for에서 사용 가능
<input v-model="query" class="form-control">
<li v-for="story in search">
    {{ story.writer }} said "{{ story.plot }}"
</li>
```

###### 정렬 sample (오름차순 내림차순 토글 가능)

``` js
data: () => ({
    stories: [...],
    order : -1
}),
computed: {
    orderedStories: function () {
        var order = this.order;
        return this.stories.sort(function(a, b) {
            return (a.upvotes - b.upvotes) * order;
        })
    }
}
```

``` html
<li v-for="story in orderedStories">
    {{ story.writer }} said "{{ story.plot }}" and upvoted {{ story.upvotes }} times.
</li>
<button @click="order = order * -1">Reverse Order</button>
```

##### 사용자 정의 필터
``` js
Vue.filter('snitch', function (hero) {
    return hero.secretId + ' is '
        hero.firstname + ' '
        hero.lastname + ' in real life!'
})

...

data: () => ({
    heroes: [
        { firstname: 'Bruce', lastname: 'Wayne', secretId: 'Batman'},
        { firstname: 'Clark', lastname: 'Kent', secretId: 'Superman'},
        { firstname: 'Jay', lastname: 'Garrick', secretId: 'Flash'},
        { firstname: 'Peter', lastname: 'Parker', secretId: 'Spider-Man'}
    ]
})
```

``` html
<li v-for="hero in heroes">
    {{ hero | snitch }}
</li>
```ㅈㅈㅈ

##### Lodash 활용한 정렬
``` js
// _.orderBy(collection, [iteratees=[_.identity]], [orders])
_.orderBy(kids, [’intelligence’, ’strength’], [’desc’, ’asc’])
```

##### 컴포넌트
``` html
// parent
<story v-bind:story="{plot: 'My horse is amazing.', writer: 'Mr. Weebl'}"></story>
<story v-bind:story="{plot: 'Narwhals invented Shish Kebab.', writer: 'Mr. Weebl'}"></story>
<story v-bind:story="{plot: 'The dark side of the Force is stronger.', writer: 'Darth Vader'}"></story>
```

```html
// story component
<template id='story-template'>
    <h1>{{ story.writer }} said "{{ story.plot }}"</h1>
</template>
```

``` js
Vue.component('story', {
    props: ['story'],
    template: "#story-template"
});
```
> v-bind 축약형 : 사용가능 ex) <story v-for="story in stories" :story="story"></story>

#### prop
```
props: {
        fixedPosition: {
            type: Number,       // Number 값    Number값을 지정할 경우 prop 데이터를 넘길 때 v-bind or : 를 사용하여 넘겨주어야 한다. (원시 attribute는 항상 문자열로 인식)
            default: 445        // default 설정
            default () {
                return {} or    //  객체나
                return []       //  배열일 경우 default함수를 지정하여 return 해주어야 한다.
            },
            required: false or true
        }
    },
```

##### 사용자 정의 이벤트
- $on(event) - 이벤트 리스너
- $once(event) - 한번만 이벤트 청취
- $off(event) - 이벤트 리스너 중지
- $emit(event) - 이벤트 발생

##### 생명주기 훅
- beforeCreate: 인스턴스 초기화 후 데이터 감시 및 이벤트/감시자 설정 전
- created: 인스턴스가 생성된 직후.
- beforeMount: 마운트 시작 직전.
- mounted: 이제 막 DOM에 마운트 된 직후.
- beforeUpdate: 데이터 변경시, 가상 DOM이 다시 렌더링되고 패치되기 전
- updated: 데이터 변경 후 가상 DOM이 다시 렌더링되고 된 후
- activated: keep-alive 상태의 컴포넌트가 활성화 될 때
- deactivated: keep-alive 상태의 컴포넌트가 비활성화 될 때
- beforeDestroy: Vue 인스턴스가 파괴되기 직전
- destroyed: Vue 인스턴스가 파괴된 후

##### 자식에서 부모로 이벤트 전달
######자식 컴포넌트
``` js
Vue.component('food', {
    template: '#food',
    props: ['name'],
    methods: {
        vote: function () {
            this.$emit('voted')
        }
    }
})
```

``` html
<template id="food">
    <button class="btn btn-default" @click="vote">{{ name }}</button>
</template>
```

###### 부모 컴포넌트
``` js
new Vue({
    el: ".container",
    data: {
        votes: 0
    },
    methods: {
        countVote: function () {
            this.votes++
        }
    }
})
```

```html
<div class="container text-center">
    <p style="font-size: 140px;">
        {{ votes }}
    </p>
    <food @voted="countVote" name="Cheeseburger"></food>
</div>
```

##### 부모 자식간의 버스를 통한 이벤트 통신
###### 부모 컴포넌트
``` html
<button @click="reset">Reset votes</button>
<li v-for="vote in votes.log"> {{ vote }} </li>
```
``` js
var bus = new Vue()

methods: {
    countVote: function (food) {
        this.votes.count++
        this.votes.log.push(food + ' received a vote.')
    },
    reset: function () {
        this.votes = {
            count: 0,
            log: []
        }
        bus.$emit('reset')      // 버스로 reset 이벤트 전달
    }
},
created () {
    bus.$on('voted', this.countVote)    // 버스에서 voted 이벤트 수신
}
```

###### 자식 컴포넌트
``` html
<button @click="vote">{{ name }}</button>
```
``` js
methods: {
    vote: function (event) {
        var food = event.srcElement.textContent;
        this.votes++
        bus.$emit('voted', food)        // 버스로 voted 이벤트 전달
    },
    reset: function () {
        this.votes = 0
    }
},
created () {
    bus.$on('reset', this.reset)    // 버스에서 reset 이벤트 수신
}
```

##### 클래스 바인딩
###### 객체
``` js
elClass: {
    'classA': true,
    'classB': false,
    'classC': true
}
```
``` html
<div v-bind:class="elClasses"></div>        // true 인 클래스만 적용
```

###### 배열
``` html
<div v-bind:class="['classA', 'classsB', anotherClass]"></div>
<div v-bind:class="['classA', condition ? 'classsB' : '']"></div>
```

##### v-bind
> 축약어 :로 사용가능 

##### 스타일 바인딩
``` js
niceStyle: {
    color: 'blue',
    fontSize: '20px'
}
```
``` html
<div v-bind:style="niceStyle"></div>
<div :style="niceStyle"></div>
<div :style="{'color': 'blue', fontSize: '20px'}">...</div>
<div :style="[niceStyle, badStyle]"></div>      // 배열 바인딩
```
> v-bind:style 을 이용하는 경우 css속성에 접두어가 필요한 속성일 경우 Vue.js에서 자동으로 붙여준다.

##### DOM보장
``` js
mounted: function () {
    this.$nextTick(function () {
        // this.$el이 문서 안에 있을 때 작동합니다.
    })
}
```

##### api 호출
``` js
var vm = new Vue({
    el: '#app',
    data: {
    stories: []
    },
    mounted: function(){
        $.get('/api/stories', function(data){
            vm.stories = data;
        })
    }
})
```
> 콜백에서는 this가 Vue를 가리키지 않기 때문에 vm변수를 만들어 vue 전역에서 사용할 수 있다.

##### html 제한이 있는곳의 컴포넌트 사용
``` html
<table class="table table-striped">
<tr>
    <th>#</th>
    <th>Plot</th>
    <th>Writer</th>
    <th>Upvotes</th>
    <th>Actions</th>
</tr>
<tr v-for="story in stories" is="story" :story="story"></tr>
</table>
```

> table 같은 일부 HTML 엘리먼트는 내부에 사용할수 있는 엘리먼트가 정해져 있다. 그곳에 컴포넌트를 바인딩 할 경우 렌더링 되지 않는데 이럴경우 is를 사용하여
사용자 정의 엘리먼트임을 알려줌으로써 해결할 수 있다.

##### disable
``` html
<button @click="fetchStories(pagination.next_page_url)" :disabled="!pagination.next_page_url">
```

##### 예약어 component와 is를 활용한 동적 컴포넌트 변경
``` html
<keep-alive include="Hello, Greet" exclude="Hello, Greet">
    <component :is="currentComponent"O>
</keep-alive
<!-- this.currentComponent가 변경되면 컴포넌트가 바뀝니다 -->
</component>
<a href="#" @click="currentComponent = 'hello'">Show Hello</a>
<a href="#" @click="currentComponent = 'greet'">Show Greet</a>
```

``` js
<script>
import Hello from './components/Hello'
import Greet from './components/Greet'

export default {
    components: {
        Hello,
        Greet
    },
    data: () => ({
        currentComponent: 'hello'
    })
}
</script>
```
> 컴포넌트간 동적 전환이 필요한 경우 유용할 수 있다.<br/>
keep-alive 를 감싸서 적용하면 한번 로드된 component 를 새로 로드하지 않고 캐싱한다.<br/>
캐싱하거나 캐싱하고 싶지 않읂 컴포넌트들은 include와 exclude 에 포함시켜 지정하면 된다.

##### 재귀 컴포넌트
```vue
<template>
<ul>
    <li v-for="sub in subs">
        {{sub.name}}
        <tree :subs="sub"></tree>
    </li>
</ul>
</template>
<script>
export default {
    name: 'tree'
}
</script>
```
> 컴포넌트 내에서 자기 자신을 참조
> 재귀 참조를 위해 name 옵션이 필수이다.

##### vue-router name 설정
``` html
<router-link :to="{ name: 'home'}">Home</router-link>
<router-link :to="{ name: 'login'}">Login</router-link>
```

``` js
const routes = [
    {
        path: '/',
        name: 'home',
        component: Hello
    },
    {
        path: '/login',
        name: 'login',
        component: Login
    }
]
```

##### vue-router 히스토리모드 및 base 설정
``` js
const router = new VueRouter({
    mode: 'history',
    base: '/',
    routes
})
```

##### vue-router 중첩 라우팅
``` js
const routes = [
    {
        path: '/'',
        component: Hello
    },
    {
        path: '/login',
        component: Login
    },
    {
        path: '/stories',
        component: StoriesPage,
        children: [
            {
                path: '',
                name: 'stories.all',
                component: StoriesAll
                },
                {
                path: 'famous',
                name: 'stories.famous',
                component: StoriesFamous
            }
        ]
    }
]
```

##### vue-router 활성링크 스타일 적용
1. 전역 활성 클래스 사용

``` css
.router-link-active {
    color: green;
}
```

2. 지역범위 사용자 정의 active 클래스사용

``` html
<router-link :to="{ name: 'hello'}" active-class="my-active-class" exact>Home</router-link>
```

3. 전역범위 사용자 정의 active 클래스 사용

``` js
const router = new VueRouter({
    mode: 'history',
    base: '/,
    linkActiveClass: 'my-active-class',
    routes
})
```

##### route 객체
- path: 현재 경로와 동일한 문자열로 항상 절대 경로로 해석됩니다 예를 들어 /foo/bar.
- params: 동적 세그먼트와 스타 세그먼트의 키/값을 포함하는 객체입니다.
- query: 쿼리 문자열의 키/값 쌍을 포함하는 객체입니다. 예를 들어, /foo?user=1 라우트의 경우, $route.query.user == 1를 사용할 수 있습니다
- hash: 해시가 존재하는 경우 #, 존재하지 않는 경우 값은 빈 문자열이 됩니다.
- fullPath: 쿼리 및 해시를 포함한 전체 URL 입니다.
- matched: 현재 경로의 모든 중첩 경로 세그먼트에 대한 라우트가 포함된 배열입니다. 라우트 레코드는 라우트 배열(및 하위 배열)에 있는 객체의 복사본입니다.
- name: 현재 라우트의 이름입니다. (존재하는 경우)

##### 동적 세그먼트
- /user/:id/posts 에서 user, :id, posts는 각각 하나의 세그먼트이고 :id는 동적 세그먼트이다.
- 동적 세그먼트는 $route.params를 통해 접근가능

동적 세그먼트를 포함한 route-link
``` html
<router-link :to="{ name: 'stories.edit', params: { id: story.id }}" tag="button" exact>
    Edit
</router-link>
```
> to 객체 내에 params를 지정해주며 tag를 통해 렌더링할 태그 지정 가능 ( default a 태그 )

##### 동적 세그먼트 사용
###### route.params에서 접근
``` js
isTheOne (story) {
    return story.id === this.id
},
mounted () {
    this.id = Number(this.$route.params.id)
    this.story = store.stories.find(this.isTheOne)
}
```
> 주소창에서 url을 변경하여 접근시 동적세그먼트의 타입이 string으로 반환되기 때문에 Number를 이용하여 타입을 변환해주어야 한다.

###### prop을 사용하여 라우트에서 분리
``` js
const routes = [
...
    {
        path: ':id/edit',
        props: (route) => ({ id: Number(route.params.id) }),
        name: 'stories.edit',
        component: StoriesEdit
    },
...
]
```
> 매개변수를 라우트 설정 안에서 숫자로 캐스팅하고 사용하는 곳에서 prop으로 사용할 수 있다.

``` js
export default {
    props: ['id'],
    data () {
        return {
            story: {}
        }
    },
    methods: {
        isTheOne (story) {
            return story.id === this.id
        }
    },
    mounted () {
        this.story = store.stories.find(this.isTheOne)
    }
}
```

##### vue-router alias
``` js
{
    path: 'famous',
    name: 'stories.famous',
    // '/stories/famous'와 같이 '/famous'와 일치시킵니다.
    alias: '/famous',
    component: StoriesFamous
}
```

##### vue-router push
- 링크를 이용하지 않고 스크립트 상에서 라우트를 탐색하려면 router.push(path)를 사용
- 문자열 또는 객체 사용가능
- 문자열의 경우 동적 세그먼트를 포함할 수 없다.

``` js
this.$router.push('/stories/11/edit')
this.$router.push({ path: '/stories/11/edit' })
this.$router.push({ name: 'stories.edit', params: {id: '11'} })
```

##### vue-router back
``` js
this.$router.back() or
this.$router.go(-1)     // 히스토리 스택에서 앞 또는 뒤로 이동하는 단계수를 나타내는 정수 속성의 전달인자를 사용
```
> $router.back()의 경우 다른 라우터에 연결하게 되는데 연결 해제를 원하면 window.history.back()을 사용할 수 있다.

##### 라우트 기본 룰
```md
/items/                     - this is for listing items
/items/{id:[0-9]{1,4}}      - this is for displaying one item in detail
/items/add                  - for displaying new item form
/items/:id/edit             - for displaying existing item in Form for editing
/items/:id/delete
```

##### 트랜지션
> 컴포넌트 진출입시 애니메이션을 적용할 수 있다.

<img src="http://www.soreply.com/images/transition.png">
- transition의 이름을 정의하면 모든 클래스는 v대신 이름을 사용한다. (ex: fade-enter, fade-leave-to 등)

``` html
<transition name="fade">
    <router-view></router-view>
</transition>
```

``` css
.fade-enter, .fade-leave-to{
    opacity: 0
}
.fade-enter-active, .fade-leave-active {
    transition: opacity 1s
}
.fade-enter-to, .fade-leave {
    opacity: 0.8
}
```

##### 외부 라이브러리 클래스 지정
``` html
<transition enter-active-class="animated rollIn">
    <router-view></router-view>
</transition>
```
> 클래스를 미리 지정 가능

##### 네비게이션 가드
> 네비게이션 보호기능은 전역수준, 라우트정보 수준, 컴포넌트 수준에서 사용할 수 있다.

###### 1. 전역수준
- vue-router는 라우트 변경을 필터링 하기 위한 편리한 메커니즘을 제공한다.
- router.beforeEach(): 각 변경 전에 트리거 됨, 인증과 관련된 시나리오에서 유용하게 사용 가능 ( ex: 엑세스 권한이 없으면 로그인 페이지로 이동 등 )
- router.afterEach(): 탐색에 영향을 미치지 않음

``` js
// 더미 사용자 객체 생성
let User = {
    isAdmin: false
}

router.beforeEach((to, from, next) => {
    if (to.path !== '/login' && !User.isAdmin) {
        // 관리자가 아니면 login 페이지로 리다이렉트
        next('/login')
    } else {
        // 인증된 사용자는 계속 진행합니다
        next()
    }
}),
router.afterEach((to, from) => {
    //...
})
```
> 항상 next()함수를 호출해야 합니다. 그렇지 않으면 훅이 절대 해결되지 않습니다.

- next(): 다음 이벤트 훅으로 이동
- next(false): 현재 네비게이션 중단, from 라우트 객체의 url 로 재설정
- next(경로): 지정된 경로로 리디렉션, 현재 네비게이션이 중단되고 새로운 네비게이션이 시작
- next(error): next에 전달된 인자가 error라면 네비게이션이 중단되고 router.onError()를 이용해 등록된 콜백에 에러가 전달됨.

###### 2. 라우트정보 수준
```vue
routes:[
    {
        path:'/contacts/:no',
        component: ContactByNo,
        beforeEnter: (to, from, next) => {
            // 전역수준의 beforeEach와 동일
        }
    }
]
```

###### 3. 컴포넌트 수준
- beforeRouteEnter(to, from, next): 렌더링하는 라우트 이전에 호출되는 훅. 이시점에서 Vue인스턴스가 만들어지지 않았기 때문에 this를 이용할 수 없다. 
- beforeRouteLeave(to, from ,next): 현재 경로에서 다른 경로로 빠져나갈 때 호출되는 훅
- beforeRouteUpdate(to, from ,next): 이미 렌더링된 컴포넌트의 경로가 변경될 때 호출되는 훅, 이미 현재 컴포넌트의 Vue 인스턴스가 만들어져 있어서 재사용 될 때는 beforeRouteEnter 훅은 호출되지 않고 beforeRouteUpdate훅이 호출된다.

```vue
beforeRouteEnter(to, from, next) {
    next(vm=>{
        //vm이 생성된 vue 인스턴스를 참조한다.
    })
}
```

###### 4. 네비게이션 가드 실행 흐름
1. 네비게이션 시작
2. 비활성화된 컴포넌트가 있다면 보호 기능 호출
3. 전역 수준의 beforeEach호출
4. 재사용되는 컴포넌트의 beforeRouteUpdate 훅 호출
5. 라우트 정보 수준의 beforeEnter 호출
6. 활성화된 컴포넌트에서 beforeRouteEnter 훅 호출
7. 네비게이션 완료
8. 전역 afterEach 훅 호출
9. DOM 갱신 트리거됨
10. 인스턴스들의 beforeRouteEnter 훅에서 next에 전달된 콜백 호출(콜백 사용시에만) 

##### 1부터 숫자 n까지의 합
```
if (Number.isNaN(n) || n<1) return 0;
return (1+n)*n/2;
```

##### 스타일
###### Shadow style 적용
1. scoped style 방식

```vue
<style scoped>
    .bor {border:3px solid red;}
</style>
```
- 템플릿에서 일반 css 사용하듯이 사용
- 내부적으로 attribute 가 생성되고 attribute selector 를 이용하기 때문에 스타일 적용이 느리다.
- scoped가 자식 컴포넌트까지 전파된다.

2. module style 방식

```vue
<style module>
    .bor {border:3px solid red;}
    .bor2 {border:4px solid red;}
</style>
<template>
    <div :class="$style.bor"></div>
    <div :class="[$style.bor, $style.bor2]"></div>   // 복수의 class 적용
</template>
```
- $style computed 속성을 이용해서 템플릿에 class로 해당 스타일을 바인딩 시킨다.
- 내부적으로 임의의 class 명을 만든다.

##### 슬롯
1. 기본슬롯

```vue
//---child
    <tmeplate>
        <slot></slot>
    </template>
//---parent
    <template>
        <child>
            // slot 에 주입할 html
        </child
    </template>
```
> prop과는 별개로 html이나 스타일적용을 위한 클래스 등등을 prop으로 일일이 넘기지 않고 바로 html로 주입할수 있다는 게 장점


2. Named 슬롯
```vue
//---child
    <tmeplate>
        <slot name="a"></slot>
        <slot name="b"></slot>
    </template>
//---parent
    <template>
        <child>
            <div slot="a">
            </div>
            <p slot="b">
            </p>
        </child
    </template>
```
> 여러개의 슬롯을 주입하고 싶을때 Named 슬롯을 이용할 수 있다.

3. scoped 슬롯
```vue
//---child
    <tmeplate>
        <slot name="a" :px="x" :py="y"></slot>
        <slot name="b" :px="x" :py="y"></slot>
    </template>
    <script>
        data: vm => ({x:1, y:2})
    </script>
//---parent
    <template>
        <child>
            <div slot="a" slot-scope="p1">
                {{p1.x}} + {{p1.y}} = {{p1.x + p1.y}}
            </div>
            <p slot="b" slot-scope="p2">
                {{p2.x}} 더하기 {{p2.y}} 는 {{p2.x + p2.y}} 이다.
            </p>
        </child
    </template>
```
> 자식에서 부모로 해당 슬롯의 범위내에서 사용할 수 있는 데이터를 넘길수 있다.

### Custom Code
##### Directive: v-click-outside
```js
// const stopProp = event => event.stopPropagation();
let event = null;

export default {
    bind(el, binding) {
        event = binding.value;
        // el.addEventListener('click', stopProp);
        document.body.addEventListener('click', event);
    },
    unbind(el) {
        // el.removeEventListener('click', stopProp);
        document.body.removeEventListener('click', event);
    }
};
```