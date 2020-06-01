<template>
	<div>
    {{msg}}-{{name}}
    <p>
      <input
        type="text"
        placeholder="请输入特性名称"
        @keyup.enter="addFeature"
      >
    </p>
    <ul>
      <li
        v-for="f in features"
        :key="f.id"
      >{{f.name}}</li>
      <li>特性数量：{{featureCount}}</li>
    </ul>
	</div>
</template>

<script lang="ts">
	import {Component, Prop, Vue} from 'vue-property-decorator';

	// 类型注解
	let name = 'asher'; // 直接赋值触发类型推论
	let title: string;
	// 不被允许
	// title = 1
	title = '1';
	// 不被允许
	// name = 6

	// 数组类型
	let names: string[];
	// 不被允许
	// names = ['asher', 'tom',4]
	names = ['asher','tom'];
	// 多类型约束
	let datas: (string | number)[];
	datas = ['info', 3, 2];

	// 任意类型
	let list: any[];
	list = ['free', false, 2, {}];

  // 函数中使用类型注解
  // 约束形参类型、返回值类型
	function greeting (person: string): string {
    return `hello, ${person}`;
  }
  greeting('tom');
  // 不被允许
  // greeting(123)

  // void类型 函数没有返回值
  function warn(): void {
    alert('warning!!!');
  }

  // 常见内置类型：string、number、boolean、void、any

  // ts函数中参数如果声明了，就是必选参数
  function sayHello1 (name: string,age: number): string {
    return `name age`
  }
  sayHello1('tom', 20)
  // 不被允许，已声明参数必填
  // sayHello1('tom')

  // 函数声明age为可选参
  function sayHello2 (name: string,age?: number): string {
    return `name age`
  }
  // age可选
  sayHello2('tom')

  // 函数声明age参数有默认值，即age不传参时值为20，返回值类型为string | number，
  function sayHello3 (name: string,age: number = 20): string | number {
    return `name age`
  }
  sayHello3('tom')

  // 函数重载，多个同名函数，通过参数数量或者类型不同或者返回值不同区分
  // ts的重载，先声明，再实现
  function info(a: {name: string}): string
  function info(a: string): object
  function info(a: any): any{
    if(typeof a === 'object') {
      return a.name;
    }else {
      return {name: a}
    }
  }
  info({name: 'jerry'})

	@Component
	export default class Hello extends Vue {
    // private 仅当前类可以使用
    // protected 子类可用、派生类可用
    // public 公共性质
    // 装饰器
    /*
      类似：
      props: {
        msg: {
          type: String,
          required: true
        }
      }
    */
    // !表示属性msg为必填项，约定为string类型
    @Prop() private msg!: string;
    // ?表示属性为可选属性
    @Prop({default: 'asher'}) private name?: string;
    // obj约定为对象类型，必须包含foo属性
    // @Prop() private obj: {foo:string};

    // 声明的普通属性，相当于组件的data
    // private features = ['静态类型','编译']

    // 声明普通属性的时候，如果数据结构符合类的数据结构，则默认允许声明
    private features: Feature[] = [
      {id: 1, name: '静态类型', version: '1.0'},
      {id: 2, name: '编译', version: '1.0'}
    ]

    // 生命周期函数直接调用即可
    created () {
      // ...
    }

    // 计算属性，可理解为computed
    get featureCount () {
      return this.features.length
    }

    // 事件方法直接写就可以，这里就相当于原先的methods
    // addFeature(event: any){
    //   this.features.push(event.target.value);
    //   event.target.value = ''
    // }

    addFeature(event: any){
      this.features.push(
        {
          id: this.features.length + 1, 
          name: event.target.value, 
          version: '1.0'
        }
      );
      event.target.value = ''
    }
  } 

  class Shape {
    // readonly的变量要求必须初始化，可以在定义的时候赋值，也可以在构造器传参的时候赋值
    readonly foo: string = 'foo'

    // 参数属性：给构造函数的参数加上修饰符，能够定义并初始化一个成员属性，也就是说不需要另外定义声明

    // 写法1：（繁琐）
    // area: number
    // protected color: string

    // constructor(color: string, width: number, height: number){
    //   this.area = width * height
    //   this.color = color
    // }

    // 写法2：（省略color单独的定义、声明）
    area: number
    constructor(public color: string, width: number, height: number){
      this.area = width * height
    }

    shoutout () {
      return `i am ${this.color} with area of ${this.area} cm squared`
    }
  }

  // 从Shape类继承来的
  class Square extends Shape {

    constructor(color: string, side: number) {
      super(color, side, side)
      console.log(this.color)
    }

    shoutout () {
      return `我是${this.color}面积是${this.area}平方厘米`
    }
  }

  // 存取器 私有变量 
  class Employee {
    // private _fullName: string = "Mike James"
    private firstName: string = "Mike James"
    private lastName: string = "Mike James"

    getFullName (): string {
      return `${this.firstName} ${this.lastName}`
    }

    setFullName (newName: string) {
      console.log('您修改了用户名！')
      this.firstName = newName.split(' ')[0]
      this.lastName = newName.split(' ')[1]
    }
  }

  const exployee = new Employee()
  // exployee.fullName = 'Bob Smith'

  class Feature {
    constructor(public id: number, public name: string, public version: string){}
  }

  // 接口约束结构
  // 当使用class的时候，要初始化等等，单纯的只需要约束数据类型的话使用接口约束比较方便
  interface Person {
    firstName: string;
    lastName: string;
    sayHello(): string
  }
  // 传参的时候要求必须传和Person一样的结构
  function greeting1 (person: Person) {
    return `Hello ${person.firstName} ${person.lastName}`
  }
  // 只要满足包含Person内部的结构就ok
  const user1 = {firstName:'Jane',lastName: 'User',foo:123}
  // 不被允许
  // const user2 = {lastName: 'User',foo:123}
  const user3 = {firstName:'Jane',lastName: 'User',sayHello: () => {return 'lsls'}}
  // 没有按数据结构传sayHello方法，报错
  // greeting1(user)
  greeting1(user3)

  // 类实现接口
  class Greeter implements Person {
    constructor(public firstName='', public lastName=''){}
    sayHello () {
      return `Hello ${this.firstName} ${this.lastName}`
    }
  }
</script>

<style scoped>

</style>