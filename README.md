# Redux Packish

Sensible**less** promise handling and middleware for redux

`redux-pack` is a library that introduces promise-based middleware that allows async actions based on the lifecycle of a promise to be declarative.

And `redux-packish` is a library that cloned `redux-pack` allowing anyone dispatch actions from side-effects!

:warning: **NOW STOP USING THUNKS WITH PACK TO DISPATCH YOUR SIDE-EFFECTS!**

~~Async actions in redux are often done using `redux-thunk` or other middlewares. The problem with this approach is that it makes it too easy to use `dispatch` sequentially, and dispatch multiple "actions" as the result of the same interaction/event, where they probably should have just been a single action dispatch.~~

~~This can be problematic because we are treating several dispatches as all part of a single transaction, but in reality, each dispatch causes a separate rerender of the entire component tree, where we not only pay a huge performance penalty, but also risk the redux store being in an inconsistent state.~~

~~`redux-pack` helps prevent us from making these mistakes, as it doesn't give us the power of a `dispatch` function, but allows us to do all of the things we were doing before.~~

To give you some more context into the changes, here are some examples/information about the old way and new way of doing things below:

Ready to use it? Jump straight to the [How-To and API OFFICIAL DOC](https://github.com/lelandrichardson/redux-pack/#how-to)

### Adding side-effects with event hooks

You might want to add side effects (like sending analytics events or navigate to different views) based on promise results.

`redux-pack` lets you do that through event hooks functions. These are functions attached to the `meta` attribute of the original action. They are called with ~~two~~ THREE!! parameters:

1. the matching step payload (varies based on the step, details below)
2. **the `dispatch` function**
3. the `getState` function

Here are the available hooks and their associated payload:

* `onStart`, called with the initial action `payload` value
* `onFinish`, called with `true` if the promise resolved, `false` otherwise
* `onSuccess`, called with the promise resolution value
* `onFailure`, called with the promise error

Here is an example usage to send analytics event when the user `doesFoo`:

```js
import { sendAnalytics } from '../analytics';
import { doFoo } from '../api/foo';

export function userDoesFoo() {
  return {
    type: DO_FOO,
    promise: doFoo(),
    meta: {
      onSuccess: (result, dispatch, getState) => {
        const userId = getState().currentUser.id;
        const fooId = result.id;
        sendAnalytics('USER_DID_FOO', {
          userId,
          fooId,
        });

        dispatch({
          type: 'HOLY_MOTHER_OF_GOD_WE_CAN_DISPATCH_HERE_NOW',
        });
      }
    }
  }
}
```

### Notes

This project is based on [redux-pack](https://github.com/lelandrichardson/redux-pack) with some **A W E S O M E** changes! You can check the official docs at their repository!
