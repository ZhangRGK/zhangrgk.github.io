---
layout: post
title: Vuex 单元测试
category: program
---

终于又到了折腾测试的环节, 这回要写的是 vuex 中的单元测试.

### vuex

vuex 可以大体上分为3个部分:

- mutation
- action
- getter

[官方文档](https://vuex.vuejs.org/zh-cn/testing.html)上已经写了很成熟的例子, 我就只是根据我们的生产环境来描述实际的代码.

#### mutation

mutation 的测试完全按照官方文档推荐的方法写, 唯一需要注意的地方, 是在定义 mutation 的时候, 需要单独 export mutations

~~~
export const mutations = {};    // 需要单独 export

export default new Vuex.store({});
~~~

#### getter

由于 getter 也是纯函数, 所以测试代码与官方例子相同

~~~
const state = {}
const params = {}

const result = getters.getterMethod(state, params)

expect(result).to.be.equal({

})
~~~

#### action

根据官方测试用例的推荐, 可测试的 action 需要做一些特殊的处理:

- 所有异步操作独立到一个新的层中
- 依赖浏览器环境的代码通过 Promise 或者 callback 的方式放回到组件中

所以 action 的代码变成了这样

~~~
import myService from './myService';

export const myAction = () => myService.myMethod();    // return a Promise

// or

export const myAction = (params, callback) => {
    myService.myMethod()
    callback()
};
~~~

测试 action 需要用到 webpack 和 inject-loader

~~~
const actions = actionsInjector({
    './myService': {    // 注: 这里是写在 action 中 import 使用的 path
        login(username, password) {
            return new Promise((resolve, reject) => {
                if (username === 'admin' && password === 'admin') {
                    resolve(fakeResponse({
                        authorization: 'passed',
                        userInfo,
                    }));
                } else {
                    reject({
                        errorCode: '401',
                    });
                }
            });
        },
    },
}).default;
~~~

测试中需要用到两个帮助方法

~~~
// 返回模拟 VueResource 的 response
export const fakeResponse = (data) => Promise.resolve({
    json: () => new Promise((resolve, reject) => {
        try {
            const json = (typeof data === 'string') ? JSON.parse(data) : data;
            resolve(json);
        } catch (e) {
            reject({
                errorCode: 'unknow',
            });
        }
    }),
    text: () => new Promise((resolve, reject) => {
        try {
            resolve(data);
        } catch (e) {
            reject({
                errorCode: 'unknow',
            });
        }
    }),
});

/* action中 mutation 调用的断言方法
参数:
action: 需要测试的 action
args: 传递给 action 的参数
state: action 调用前的 state
expectedMutations: 用于断言的 mutations 数组, 顺序是有意义的
resolve, reject: 断言 Promise 的回调函数
done: mocha 的 done 回调直接传
*/
export function testAction( action, args, state, expectedMutations, resolve, reject, done ) {
    let count = 0;

    const commit = (type, payload) => {
        const mutation = expectedMutations[count];
        expect(mutation.type).to.equal(type);
        if (payload) {
            expect(mutation.payload).to.deep.equal(payload)
        }
        count++
        if (count >= expectedMutations.length) {
            done()
        }
    }

    action({ commit, state }, args)
    .then(resolve)
    .catch(reject);

    if (expectedMutations.length === 0) {
        expect(count).to.equal(0);
        done();
    }
}
~~~

然后直接写测试代码.

~~~
it('login success', done => {
    testAction(actions.login, { username: 'admin', password: 'admin' }, {}, [
        { type: LOGIN_SUCCESS, payload: userInfo },
    ], done);
});
~~~

完整的测试代码如下:

~~~
import { expect } from 'chai';
import { mutations } from './store';
import { LOGIN_SUCCESS, LOGIN_FAIL } from './mutation-types';
import { INVALID_USERNAME_OR_PASSWORD } from './strings';
import { fakeResponse, testAction } from './utils';

const actionsInjector = require('inject!./actions');
const userInfo = { name: 'keeeeeen', avatar: '/static/imgs/1.jpg' };

describe('vuex-login', () => {
    describe('mutations', () => {
        it('LOGIN_FAIL', () => {
            const state = { notice: null };
            mutations.LOGIN_FAIL(state, INVALID_USERNAME_OR_PASSWORD);
            expect(state.notice).to.equal(INVALID_USERNAME_OR_PASSWORD);
        });
    });

    describe('actions', () => {
        const actions = actionsInjector({
            './service/LoginService': {
                login(username, password) {
                    return new Promise((resolve, reject) => {
                        if (username === 'admin' && password === 'admin') {
                            resolve(fakeResponse({
                                authorization: 'passed',
                                userInfo,
                            }));
                        } else {
                            reject({
                                errorCode: '401',
                            });
                        }
                    });
                },
            },
        }).default;

        it('login success', done => {
            testAction(actions.login, { username: 'admin', password: 'admin' }, {}, [
                { type: LOGIN_SUCCESS, payload: userInfo },
            ], done);
        });

        it('login fail', done => {
            testAction(actions.login, { username: 'admin', password: 'invalidpassword' }, {}, [
                { type: LOGIN_FAIL, payload: INVALID_USERNAME_OR_PASSWORD },
            ], done);
        })
    });
});
~~~


#### 完
