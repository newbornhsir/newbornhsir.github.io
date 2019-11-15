---
title: react-navigation的使用
date: 2017-11-02 14:57:50
tags: react-native
---

## 安装

[官方文档](https://reactnavigation.org/docs/navigators/stack)

## 路由分类

- [StackNavigator](#stack)
- [TabNavigator](#tab)
- [DrawerNavigator](#drawer)

<h3 id="statck">StackNavigatorConfig</h3>

使用

```
import {StackNavigator} from 'react-navigation';

const App = StackNavigator(routerConfig,StackNavigatorConfig)

// routerConfig
{
    pathName: {
        screen: screenName,
        path: '',
        navigationOptions: ({navigator})=>({
                // navigationOptions config
            })
    }
}

//StackNavigatorConfig

{
    // 路由配置
    initialRouteName: '',//默认显示的路由，不配置则默认为路由配置中的第一个
    initialRouteParams: {},
    navigationOptions: ({navigator})=>({
        title: 'headetitle',
        // null/React Element/ function that given HeaderProps returns a React Element
        header: null,
        //react Element/ string
        headerTitle: ,
        headerLeft: ,//react element
        headerStyle: {},//style Obj
        headerTitleStyle: {}, // style Obj for title component
        headerTintColor: '#ccc', // tint color
        ....
    }),
    path: ,//A mapping of overrides for the paths set in the route configs
    // 视觉显示配置
    mode: 'card',//card/modal, default is card
    // 配置了一下headerMode,没看到太大差别，除了none的时候不现实header，文档说float在ios常见，screen在android常见
    headerMode: 'float',// float/screen/none
    cardStyle: ,//
    // 函数：传递一下三个参数,transitionProps,prevtransitionProps,isModal,返回一个和默认过渡效果配置的对象
    transitionConfig: ()=>{},
    onTransitionStart: ()=>{},
    onTransitionEnd; ()=>{}
}

 // Navigator Props 
// StackNavigator创建的导航可以通过screenProps向子视图传递额外的数据

const SomeStack = StackNavigator({
  // config
});

<SomeStack
  screenProps={/* this prop will get passed to the screen components as this.props.screenProps */}
/>

```

<h3 id="tab">TabNavigator</h3>

API : TabNavigator(routerconfig,TabNavigatorConfig)

#### routerconfig

和StackNavigator一致

#### TabNavigatorConfig

- tabBarComponent: default: TabBarBottom in ios, TabBarTop in android
- tabBarPosition: 'top' or 'bottom'
- swipeEnable: 是否可以滑动切换
- animationEnable: 切换是否有动画
- lazy: 是否lazy渲染
- initiallayout:
- tabBarOptions: 如下

    1. initialRouteName
    2. order
    3. paths
    4. backBehavior

    ##### tabBarBottom

    1. activeTintColor
    2. activebackgroundColor
    3. inactiveTintColor
    4. inactiveBackgroundColor
    5. showLabel
    6. style
    7. backgroundColor
    8. labelStyle
    9. tabStyle
    10. allowFontScaling

    ##### tabBarTop

    1. activeTintColor
    2. inactiveTintColor
    3. showIcon
    4. showLabel
    5. upperCaseLabel
    6. pressColor
    7. pressOpacity
    8. scrollEnable
    9. tabStyle
    10. indicatorStyle
    11. labelStyle
    12. iconStyle
    13. style
    14. allowFontScaling

- navigationOptions: 如下

    1. title
    2. tabBarVisible
    3. tabBaricon
    4. tabBarLabel
    5. tabBarOnPress


<h3 id="drawer">Drawernavigator</h3>

目前未用到，用法见官方文档。

## Screen Navigation Prop


props.navigation 接受一下4个prop

1. navigate(router,params,action)

路径，参数，The sub-action to run in the child router, if the screen is a navigator

2. state

Object: {routeName:'',key:'',params:{}}

3. setParams

更改路由中参数

4. goBack()

goBack("screen"||null)

5. dispatch

send an action to the router 

## Navigation Action

### Navigate

```

import { NavigationActions } from 'react-navigation'

const navigateAction = NavigationActions.navigate({

  routeName: 'Profile',// 在app中注册过的目标路由

  params: {}, // Object, 传递到目标路由的参数

  // Object: The sub-action to run in the child router, if the screen is a navigator

  action: NavigationActions.navigate({ routeName: 'SubProfileRoute'})
})

this.props.navigation.dispatch(navigateAction)


```

### Reset

- index - number - required - Index of the active route on routes array in navigation state.
- actions - array - required - Array of Navigation Actions that will replace the navigation state.
- key - string or null - optional - If set, the navigator with the given key will reset. If null, the root navigator will reset.

```

import { NavigationActions } from 'react-navigation'

const resetAction = NavigationActions.reset({
  index: 0,
  actions: [
    NavigationActions.navigate({ routeName: 'Profile'})
  ]
})
this.props.navigation.dispatch(resetAction)

```

### Back

```

import { NavigationActions } from 'react-navigation'

const backAction = NavigationActions.back({
    //string or null - optional - If set, navigation will go back from the given key. If null, navigation will go back anywhere.
  key: 'Profile'
})
this.props.navigation.dispatch(backAction)

```


### SetParams

```

import { NavigationActions } from 'react-navigation'

const setParamsAction = NavigationActions.setParams({
  params: { title: 'Hello' },//Object
  key: 'screen-123',//string - required - Route key that should get the new params

})
this.props.navigation.dispatch(setParamsAction)

```


## Router

routers 定义一个组件的navigation的state,允许开发者定义可处理的route和action.

### build-in routers

- StackRouter

api: StackRouter(routerconfig,stackconfig)

routerconfig:

1. path; Specify the screen component or child navigator

2. screen: Specify the screen component or child navigator

3. getScreen(): Set a lazy getter for a screen component (but not navigators)

stackconfig

1. initialRouteName
2. initialRouteParams
3. paths - 可更改routerconfig中的path

- TabRouter

api: StackRouter(routerconfig,tabconfig)

routerconfig:

1. path; Specify the screen component or child navigator

2. screen: Specify the screen component or child navigator

3. getScreen(): Set a lazy getter for a screen component (but not navigators)

tabconfig

1. initialRouteName
2. order - Array of routeNames which defines the order of the tabs
3. paths - 可更改routerconfig中的path
4. backbehavior - Should the back button cause a tab switch to the initial tab? If yes, set to initialRoute, otherwise none. Defaults to initialRoute behavior.


use

```
class MyNavigator extends React.Component {
    static router = StackRouter(routes, config);
    ...
}
```

### custom router API

<image src="https://reactnavigation.org/assets/routers-concept-map.png" /> 

#### getStateForAction(action, state)

定义所给action的state。函数在传入props.navigation.dispatch()的时候调用。返回

```
{
    index: 0,
    routers: [
        {
            routerName: 'name',
            ...
        }
        ...
    ]
}
```

#### getComponentForRouteName(routename)

返回所给路由的子组件或者导航

```
{
  index: 1,
  routes: [
    { key: 'A', routeName: 'Foo' },
    { key: 'B', routeName: 'Bar' },
  ],
}
```

#### getComponentForState(state)

Returns the active component for a deep navigation state.

#### getActionForPathAndParams(path, params)

Returns an optional navigation action that should be used when the user navigates to this path and provides optional query parameters.

#### getPathAndParamsForState(state)

Returns the path and params that can be put into the URL to link the user back to the same spot in the app.
The path/params that are output from this should form an action when passed back into the router's getActionForPathAndParams. That action should take you to a similar state once passed through getStateForAction.

#### getScreenOptions(navigation, screenProps)

Used to retrieve the navigation options for a screen. Must provide the screen's current navigation prop and optionally, other props that your navigation options may need to consume.

- navigation - This is the navigation prop that the screen will use, where the state refers to the screen's route/state. Dispatch will trigger actions in the context of that screen.
- screenProps - Other props that your navigation options may need to consume
- navigationOptions - The previous set of options that are default or provided by the previous configurer
Inside an example view, perhaps you need to fetch the configured title:

```
// First, prepare a navigation prop for your child, or re-use one if already available.
const screenNavigation = addNavigationHelpers({
  // In this case we use navigation.state.index because we want the title for the active route.
  state: navigation.state.routes[navigation.state.index],
  dispatch: navigation.dispatch,
});
const options = this.props.router.getScreenOptions(screenNavigation, {});
const title = options.title;
```


### custom navigation actions

```
const MyApp = StackNavigator({
  Home: { screen: HomeScreen },
  Profile: { screen: ProfileScreen },
}, {
  initialRouteName: 'Home',
})

const defaultGetStateForAction = MyApp.router.getStateForAction;

MyApp.router.getStateForAction = (action, state) => {
  if (state && action.type === 'PushTwoProfiles') {
    const routes = [
      ...state.routes,
      {key: 'A', routeName: 'Profile', params: { name: action.name1 }},
      {key: 'B', routeName: 'Profile', params: { name: action.name2 }},
    ];
    return {
      ...state,
      routes,
      index: routes.length - 1,
    };
  }
  return defaultGetStateForAction(action, state);
};
```


### 阻止 navigation action

```
import { NavigationActions } from 'react-navigation'

const MyStackRouter = StackRouter({
  Home: { screen: HomeScreen },
  Profile: { screen: ProfileScreen },
}, {
  initialRouteName: 'Home',
})

const defaultGetStateForAction = MyStackRouter.router.getStateForAction;

MyStackRouter.router.getStateForAction = (action, state) => {
  if (
    state &&
    action.type === NavigationActions.BACK &&
    state.routes[state.index].params.isEditing
  ) {
    // Returning null from getStateForAction means that the action
    // has been handled/blocked, but there is not a new state
    return null;
  }
  
  return defaultGetStateForAction(action, state);
};

```

### Handling Custom URIs

```

import { NavigationActions } from 'react-navigation'

const MyApp = StackNavigator({
  Home: { screen: HomeScreen },
  Profile: { screen: ProfileScreen },
}, {
  initialRouteName: 'Home',
})
const previousGetActionForPathAndParams = MyApp.router.getActionForPathAndParams;

Object.assign(MyApp.router, {
  getActionForPathAndParams(path, params) {
    if (
      path === 'my/custom/path' &&
      params.magic === 'yes'
    ) {
      // returns a profile navigate action for /my/custom/path?magic=yes
      return NavigationActions.navigate({
        routeName: 'Profile',
        action: NavigationActions.navigate({
          // This child action will get passed to the child router
          // ProfileScreen.router.getStateForAction to get the child
          // navigation state.
          routeName: 'Friends',
        }),
      });
    }
    return previousGetActionForPathAndParams(path, params);
  },
});

```

















