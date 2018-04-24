#vuex 和 redux 的使用
###Vue + Vuex
######接入vuex:
- 安装 vuex
- 在构建最顶层组件js文件中（一般是index.js 或则 app.js）注入store
    ```
    import Vue from 'vue';
    import store from './store/store';
    new Vue({
        el: '#app',
        store,
        router,
        components: { App },
        template: '<App/>'
    })
    ```
    - 直接使用mutations
        ```
        import Vuex from 'vuex';
        const store = new Vuex.Store({
            state: {  // 初始化 defaultState
                isLogin: false
            },
            mutations: {
                login(state, preload){
                    state.isLogin = preload.isLogin;
                }
            }
        })
        ```
        使用方法 (如在Login.vue中)
        ```
        this.$store.commit('login', {isLogin: true});
    - 结合action使用
        ```
        import Vuex from 'vuex';
        const store = new Vuex.Store({
            state: {  // 初始化 defaultState
                isLogin: false
            },
            mutations: {
                login(state, preload){
                    state.isLogin = preload.isLogin;
                }
            },
            actions: {
                login(context, preload){
                    fecth('/api/login',{
                        method: 'post',
                        data: JOSN.stringify({
                            name: 'wen',
                            password: '123'
                        })
                    }).then((response) => {
                        return response.json();
                    }).catch( err => {
                        console.log(err);
                    }).then( res => {
                        if (res.code === 1) {
                            context.commit('login',res.data)
                        }
                    })
                }
            }
        })
        ```
        使用方法（Login.vue）
        ```
        this.$store.dispatch('login', {});
        ```
    - 改造actions实现回调
        ```
        actions: {
            login(context, preload) {
                new Promise( (resovle, reject) => {
                    fecth('/logo.png')
                    .then( response => response.json())
                    .catch( err => console.log(err) )
                    .then( res => {
                        commit('login', res.data);
                        resovle(res.data);
                    })
                } )
            }
        }
        ```
        使用
        ```
        this.$store.dispatch('login',{}).then( res => {
            // res 就是 res.data
            this.$router.push('/'); // 登录成功返回首页.
        })
        

###React + Redux
######接入redux
- 安装 redux , react-redux
- 在最顶层组件（一般是index.js）中注入store
    ```
    import { Provider } from 'react-redux';
    import store from './store/store';
    ReactDOM.render(
        <Provider store={store}>
            <YouRouterConfig />
        </Provider>
    , document.getElementById('root'));
    ```
- store 创建
    - 创建action
        ```
        const LOGIN = {
            type: 'LOGIN',
            isLogin: false
        };
        export default LOGIN;
        ```
        或者使用函数构建action:
        ```
        const login = (isLogin) => {
            return {
                type: 'LOGIN',
                isLogin
            }
        };
        export default login;
        ```
    - 创建reducer
        ```
        const loginReducer = (preState = defaultState, action) => {
            switch(action.type) {
                case 'LOGIN':
                    return { ...preState, ...action };
                case 'OTHER_ACTION':
                    /* some code...; */
                    return { ...preState, ...action };
                default:
                    return preState;
            }
        }
        export default loginReducer;
        ```
    - 创建store
        ```
        import reducer from './reducer';
        import { createStore } from 'redux';

        export default createStore(reducer);
        ```
- Container组件连接store 
    ```
    import login_action from '../store/action';
    import { connect } from 'react-redux';
    const Container = () => {
        return (
            <div>
                { some elements }
            </div>
        )
    }
    const mapStateToProps = (state) => {
        /* some code to create a new state*/
        return { ...state }
    }
    const mapDispatchToProps = (dispatch) => {
        return {
            onLogin(){
                dispatch(login_action(true));
                /* do some logic
                    such as :
                    history.push('/') // back to home page.
                 */
            }
        }
    }
    export default connect(
        mapStateToProps,
        mapDispatchToProps
        )(Container)
    ```
- Presentational组件使用store
    ```
    class Presentational extends React.Component {
        constructor(props) { // 在Presentational组件中尽量不用设置自身的state。
            super(props);   // props 中包含了从Container组件中传入的state
        }
        componentDidMount (){
            if(this.props.isLogin) {
                history.push('/')
            }
        }
        render() {}
    }
    ```
- history创建
    ```
    import createBrowserHistory from 'history/createBrowserHistory';
    export default createBrowserHistory();
